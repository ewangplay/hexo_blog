title: 并发编程中的一个陷阱
date: 2015-01-08 16:56:53
tags: [Golang,并发]
categories: 编程技术
---
最近两个多月都在使用golang编写服务器端程序，大量使用到了golang中的协程(goroutine)来处理各种并发事务，非常的方便快捷，的确比原先使用c/c++编写程序效率上提升了很多。goalng提供的闭包也很强大，很容易让人不由自主使用闭包。所谓闭包，就是一种特殊的匿名函数，在闭包中可以直接使用上下文中有效范围内的常量或变量，这些常量或变量一旦被闭包所引用，那么就会延长其生命周期，即使其常规意义上的生命周期已经到来，在闭包中这些值仍然是有效的。闭包使用起来非常方便，但不小心也会掉入陷阱中。就好比一柄利剑，使用得当，就可以所向披靡；使用不当，就会伤及自身。

前两天，在跟前端的同学联调一个刚写完的golang微服务时，发现了一个莫名其妙的问题，对于多个发送人的情况，只有其中一个人能接收到，查看了一下代码，貌似也没有问题啊。

        for {
            select {
            case msg := <- this.smsQueue:
                outputStr = fmt.Sprintf("活动[%v]的短信在发送队列中被调度，准备发送...", msg.RequestId)
                g_logger.Debug(outputStr)

                //发送短信
                for _, entry := range msg.Entries {
                    receiverList := strings.FieldsFunc(entry.Receiver, func (char rune) bool {
                        switch char {
                        case ',':
                            return true
                        }
                        return false
                    })

                    for _, receiver := range receiverList {
                        go func() {
                            g_snsClient.SendSMSMessage(msg.RequestId, strconv.FormatUint(g_smsSerialNumber, 10), receiver, entry.Content, entry.Signature)
                        }()
                    }
                }
            }
        }

看似没有问题的代码，经不起推敲啊，稍微思考一下并发的运行机制，就会发现上面的代码存在很大的问题，这里面有一个很典型的并发编程的陷阱：在一个循环体中启动goroutine，而goroutine的执行体又通过闭包引用了循环体的参数，就必然会导致问题。比如上面的代码中内层循环体参数receiver被goroutine的闭包引用，但receiver的值却是随着循环体的执行在变换，这样就导致前面启动的goroutine里的receiver值已经不是期望中的值，都随着外面receiver的改变在改变，这就是为什么多个接收人只有最后一个接收到的原因。同样的问题，外层循环体中的参数entry在闭包中也被直接引用，所以也会产生问题，如果现在还没出现问题，那只是运气好而已，早晚会出现问题的。

这个问题的解决其实也很简单，就是不要依赖于闭包的共享作用域变量的特性，而是通过参数传递来生成Copy的本地变量，让闭包里的值跟外层的循环参数隔离开来，就不会出现这个问题了。

        for {
            select {
            case msg := <- this.smsQueue:
                outputStr = fmt.Sprintf("活动[%v]的短信在发送队列中被调度，准备发送...", msg.RequestId)
                g_logger.Debug(outputStr)

                //发送短信
                for _, entry := range msg.Entries {
                    receiverList := strings.FieldsFunc(entry.Receiver, func (char rune) bool {
                        switch char {
                        case ',':
                            return true
                        }
                        return false
                    })

                    for _, receiver := range receiverList {
                        go func(requestid string, smsSerialNumber uint64, phone string, content string, signature string) {
                            g_snsClient.SendSMSMessage(requestid, strconv.FormatUint(smsSerialNumber, 10), phone, content, signature)
                        }(msg.RequestId, g_smsSerialNumber, receiver, entry.Content, entry.Signature)
                    }
                }
            }
        }


这种并发编程的问题并不限于某种语言，只要是支持并发和闭包的高级语言都可能遇到。碰巧的是，我刚刚修改了该问题后不久，团队里一个前端的小盆友就遇到了相同的问题，拉着我一起分析时，我一下就匹配上了这个问题模式，一模一样，她使用的javascript语言。所以在这里记录一下，给自己也给各位同学提个醒。只要意识到了这个问题，还是很容易避免的。

