title: Go语言中如何捕获系统信号
date: 2015-01-13 14:32:55
tags: [Golang]
categories: 编程技术
---
熟悉C语言的同学可能都知道在标准C的编程中，我们通常使用标准C库提供的signal函数来捕获系统信号并执行我们注册的回调函数。对于进行服务端编程的同学来说，这样一个需求是很普遍的。我最近一直在使用Go语言写服务端程序，很自然也碰到了这个需求。那么在Go语言中如何实现捕获系统的信号并处理我们自己的流程呢？

大致了解了一下，在Go语言中要实现这个需求，需要用到两个package：os/signal和syscall，其中os/signal提供了实现捕获系统信号的接口，syscall提供了常规的信号量定义。

下面我们看一下os/signal提供的接口函数：

1. 通知在指定的channel上接收相应的系统信号

        func Notify(c chan<- os.Signal, sig ...os.Signal)

> 针对该接口的注意点：
+ 捕获到的系统信号会被放入channel中，如果指定了sig列表，那么指定的系统信号会被放入channel中；如果sig列表为空，那么所有的系统信号会被放入channel中。
+ signal包并不保证在channel没有存放空间时阻塞捕获的系统信号的放入，所以如果使用者自己要保证make的channel有足够大的空间来接收多个系统信号。
+ 允许在同一个channel上调用Notify多次，这样每次调用相当于扩充了在channel上接收到的系统信号的数量。
+ 允许在不同的channel上针对同一个系统信号调用多次，这样当系统信号到来时，针对每一个channel都会接收到系统信号的一个拷贝。

2. 停止在指定的channel上接收系统信号

        func Stop(c chan<- os.Signal)

> 针对该接口的注意点：
+ Stop调用会保证针对channel不再接收到任何的系统信号。不管之前针对该channel调用了多少次Notify，一次Stop足矣。

Go语言中捕获系统信号的机制跟之前C语言的方式有很大的改变，不再是针对特定的系统信号注册自定义函数，而是把设定要捕获的系统信号放入一个channel中，至于使用者怎么操作channel来处理这个系统信号，完全由使用者说了算，给了开发者很大的灵活性。

鉴于捕获系统信号的处理通常跟主流程分离，所以在使用中通常需要启动一个单独的goroutine来处理。下面我以实际项目中的一个小需求来演示一下捕获系统信号的使用方法。

我们有一个服务在维护一个短信发送的流水号，每次发送一批短信，该流水号会加1。那么很自然的需求，当我们Stop该服务时，就需要把当前的流水号给保存起来，服务Restart时重新加载进来，在原有数值的基础上继续累加。而我们的服务通常Stop的方式就是使用kill或者killall来结束，就需要捕获到相应的系统信号，然后执行保存操作后再结束程序。


        var g_smsSerialNumber uint64

        func readSMSSerialNumber() (uint64, error) {
            var smsSerialNumber uint64

            filename, ok := g_config.Get("service.smsSerialNumber")
            if !ok || filename == "" {
                return 0, fmt.Errorf("没有指定短信流水号的存储文件")
            }

            f, err := os.Open(filename)
            if err != nil {
                return 0, err
            }
            defer f.Close()

            data, err := ioutil.ReadAll(f)
            if err != nil {
                return 0, err
            }

            strNumber := string(data)
            smsSerialNumber, err = strconv.ParseUint(strNumber, 0, 0)
            if err != nil {
                return 0, err
            }

            return smsSerialNumber, nil
        }

        func saveSMSSerialNumber(smsSerialNumber uint64) error {

            filename, ok := g_config.Get("service.smsSerialNumber")
            if !ok || filename == "" {
                return fmt.Errorf("没有指定短信流水号的存储文件")
            }

            f, err := os.Create(filename)
            if err != nil {
                return err
            }
            defer f.Close()

            strNumber := strconv.FormatUint(smsSerialNumber, 10)

            _, err = f.WriteString(strNumber)
            if err != nil {
                return err
            }

            return nil
        }

        func sigHandler() {
            var outputStr string

            c := make(chan os.Signal)
            //拦截指定的系统信号
            signal.Notify(c, syscall.SIGINT, syscall.SIGTERM)

            for {
                select {
                case s := <-c:
                    outputStr = fmt.Sprintf("接收到系统信号: %v", s)
                    g_logger.Info(outputStr)

                    err := saveSMSSerialNumber(g_smsSerialNumber)
                    if err != nil {
                        outputStr = fmt.Sprintf("保存短信流水号smsSerialNumber失败, 失败原因: %v", err)
                        g_logger.Error(outputStr)
                    } else {
                        outputStr = fmt.Sprintf("保存短信流水号smsSerialNumber成功")
                        g_logger.Info(outputStr)

                        os.Exit(0)
                    }
                }
            }
        }

        func main() {

            g_smsSerialNumber, err = readSMSSerialNumber()
            if err != nil {
                //如果失败，流水号从0开始计算
                g_smsSerialNumber = 0
            }
            go sigHandler()

            //服务主流程
            //......
        }

