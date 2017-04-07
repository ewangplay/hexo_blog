title: 为什么对于thrift不建议使用长连接
date: 2015-02-10 11:27:43
tags: [Thrift,Golang]
categories: 编程技术
---
我们后端的服务采用Micro-Service的架构，按照功能切分成各种细小的服务，对于每个服务不限制编程语言，比如我们的团队成员就使用了三种编程语言——golang、python、nodejs。服务与服务之间使用了Facebook开源的thrift通讯框架来做RPC调用，灵活、简单、功能上也基本满足了需求。关于thrift的使用，可以参考[它的官网](http://thrift.apache.org/)。

前期为了使用简单，thrift调用的client端都是采用短连接的方式，每次调用API都创建了一个新的连接，调用完成后关闭这个连接。代码演示如下：


        {
            trans, err := thrift.NewTSocket(networkAddr)
            if err != nil {
                return "", err
            }
            defer trans.Close()

            protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()
            client := smssender.NewSMSSenderClientFactory(trans, protocolFactory)

            if err := trans.Open(); err != nil {
                return "", err
            }

            r, err = client.SendMessage(request_id, serial_number, receiver, content, signature, service_number)
            if err != nil {
                return "", err
            }
        }

后来在重构这部分代码时，就想着能不能采用长连接的模式呢？原先这种每次调用API都新建连接的方式，是不是性能上太低效了呢？于是鼓捣鼓捣，就把前面新建连接、新建thrift协议、新建thrift客户端的部分提取成公用的，让直接调用API的代码瘦身。

理想很美好，现实很残酷！这样重构了之后，一些莫名其妙的问题就随之而来了。有时运行良好，有时出现莫名的错误。Oh！My God! 这是为何呀？

穷途末路之际，只有痛定思痛，下定决心，决定往根儿上刨一刨，也许会有意想不到的收获的。下面就是翻看thrift源码（Go语言版本）的过程，对应的源码在[Github](https://github.com/apache/thrift)上可以找到。

针对Go语言版本的thrift库文件还是蛮多的，我查了一下，算上单元测试的文件有55个go文件，所以我并没有从头到尾把源码看一遍，也没这个必要，因为我是有目的性的查找问题，所以在大致浏览了一下工程的整体框架后，专注在一些重点的源码上即可。

首先我们先看一下在Server端thrift的典型用法：

        func main() {

            ......

            transportFactory := thrift.NewTBufferedTransportFactory(1024)
            protocolFactory := thrift.NewTBinaryProtocolFactoryDefault()
            serverTransport, err := thrift.NewTServerSocket(networkAddr)
            handler := &SNSSchedulerImpl{}
            processor := snsscheduler.NewSNSSchedulerProcessor(handler)
            server := thrift.NewTSimpleServer4(processor, serverTransport, transportFactory, protocolFactory)

            server.Serve()
        }

这里关键的几个步骤如下：
1. 创建一个TTransport对象工厂用来后续生成TTransport对象

        type Flusher interface {
            Flush() (err error)
        }

        // Encapsulates the I/O layer
        type TTransport interface {
            io.ReadWriteCloser
            Flusher

            // Opens the transport for communication
            Open() error

            // Returns true if the transport is open
            IsOpen() bool
        }

    Transport层实际上提供了对网络Socket进行读/写的一个抽象。

    对于Server端而言，在基础的Transport抽象层之上，又提供了一个针对服务端的抽象层：TServerTransport

        type TServerTransport interface {
            Listen() error
            Accept() (TTransport, error)
            Close() error

            // Optional method implementation. This signals to the server transport
            // that it should break out of any accept() or listen() that it is currently
            // blocked on. This method, if implemented, MUST be thread safe, as it may
            // be called from a different thread context than the other TServerTransport
            // methods.
            Interrupt() error
        }

    下面我们看到的TServerSocket实现，实际上就是对这个interface的实现。

        type TServerSocket struct {
            listener      net.Listener
            addr          net.Addr
            clientTimeout time.Duration

            // Protects the interrupted value to make it thread safe.
            mu          sync.RWMutex
            interrupted bool
        }

        func NewTServerSocket(listenAddr string) (*TServerSocket, error) {
            return NewTServerSocketTimeout(listenAddr, 0)
        }

        func NewTServerSocketTimeout(listenAddr string, clientTimeout time.Duration) (*TServerSocket, error) {
            addr, err := net.ResolveTCPAddr("tcp", listenAddr)
            if err != nil {
                return nil, err
            }
            return &TServerSocket{addr: addr, clientTimeout: clientTimeout}, nil
        }

        func (p *TServerSocket) Listen() error {
            if p.IsListening() {
                return nil
            }
            l, err := net.Listen(p.addr.Network(), p.addr.String())
            if err != nil {
                return err
            }
            p.listener = l
            return nil
        }

        func (p *TServerSocket) Accept() (TTransport, error) {
            p.mu.RLock()
            interrupted := p.interrupted
            p.mu.RUnlock()

            if interrupted {
                return nil, errTransportInterrupted
            }
            if p.listener == nil {
                return nil, NewTTransportException(NOT_OPEN, "No underlying server socket")
            }
            conn, err := p.listener.Accept()
            if err != nil {
                return nil, NewTTransportExceptionFromError(err)
            }
            return NewTSocketFromConnTimeout(conn, p.clientTimeout), nil
        }

        // Checks whether the socket is listening.
        func (p *TServerSocket) IsListening() bool {
            return p.listener != nil
        }

        // Connects the socket, creating a new socket object if necessary.
        func (p *TServerSocket) Open() error {
            if p.IsListening() {
                return NewTTransportException(ALREADY_OPEN, "Server socket already open")
            }
            if l, err := net.Listen(p.addr.Network(), p.addr.String()); err != nil {
                return err
            } else {
                p.listener = l
            }
            return nil
        }

        func (p *TServerSocket) Addr() net.Addr {
            if p.listener != nil {
                return p.listener.Addr()
            }
            return p.addr
        }

        func (p *TServerSocket) Close() error {
            defer func() {
                p.listener = nil
            }()
            if p.IsListening() {
                return p.listener.Close()
            }
            return nil
        }

        func (p *TServerSocket) Interrupt() error {
            p.mu.Lock()
            p.interrupted = true
            p.mu.Unlock()

            return nil
        }
2. 创建一个TProtocol对象工厂用来后续生成TProtocol对象

        type TProtocol interface {
            WriteMessageBegin(name string, typeId TMessageType, seqid int32) error
            WriteMessageEnd() error
            WriteStructBegin(name string) error
            WriteStructEnd() error
            WriteFieldBegin(name string, typeId TType, id int16) error
            WriteFieldEnd() error
            WriteFieldStop() error
            WriteMapBegin(keyType TType, valueType TType, size int) error
            WriteMapEnd() error
            WriteListBegin(elemType TType, size int) error
            WriteListEnd() error
            WriteSetBegin(elemType TType, size int) error
            WriteSetEnd() error
            WriteBool(value bool) error
            WriteByte(value byte) error
            WriteI16(value int16) error
            WriteI32(value int32) error
            WriteI64(value int64) error
            WriteDouble(value float64) error
            WriteString(value string) error
            WriteBinary(value []byte) error

            ReadMessageBegin() (name string, typeId TMessageType, seqid int32, err error)
            ReadMessageEnd() error
            ReadStructBegin() (name string, err error)
            ReadStructEnd() error
            ReadFieldBegin() (name string, typeId TType, id int16, err error)
            ReadFieldEnd() error
            ReadMapBegin() (keyType TType, valueType TType, size int, err error)
            ReadMapEnd() error
            ReadListBegin() (elemType TType, size int, err error)
            ReadListEnd() error
            ReadSetBegin() (elemType TType, size int, err error)
            ReadSetEnd() error
            ReadBool() (value bool, err error)
            ReadByte() (value byte, err error)
            ReadI16() (value int16, err error)
            ReadI32() (value int32, err error)
            ReadI64() (value int64, err error)
            ReadDouble() (value float64, err error)
            ReadString() (value string, err error)
            ReadBinary() (value []byte, err error)

            Skip(fieldType TType) (err error)
            Flush() (err error)

            Transport() TTransport
        }

    TProtocol层实际上提供了对传输的各种数据类型的一个编码/解码过程的封装，相当于对数据的序列化和反序列化过程。提供的协议格式有多种：json, xml, 纯文本，压缩二进制等。

3. 创建一个自定义的跟业务相关的TProcessor对象

        type TProcessor interface {
            Process(in, out TProtocol) (bool, TException)
        }

    TProcessor层实际上定义了一个处理框架，从Input TProtocol对象读取数据，委托给服务开发者实际编写的业务接口进行处理，然后把处理后的结果输出到Output TProtocol对象。Input TProtocol对象中的数据代表了服务API调用者传递过来的调用信息（一般而言就是调用API的名称和对应的参数），而输出到Output TProtocol对象的数据代表了服务API经过内部处理后返回的结果数据。

    对于thrift框架的使用者来说，实现自定义的TProcessor对象接口，就是要全部关注的内容，其它的通讯细节都被thrift封装了起来，对开发者来说是透明的。

4. 创建一个Server对象把上面的对象模型整合到一起，进入工作流程；

+ 创建一个TTransport对象
+ 针对这个TTransport对象创建Input/Output TProtocol对象
+ 基于Input/Output TProtocol对象创建一个业务相关的TProcessor对象
+ 等待incomming的连接请求，并把传递给TProcessor对象进行处理

    我们看一下实际的源码流程：

        func (p *TSimpleServer) Listen() error {
            return p.serverTransport.Listen()
        }

        func (p *TSimpleServer) AcceptLoop() error {
            for {
                client, err := p.serverTransport.Accept()
                if err != nil {
                    select {
                    case <-p.quit:
                        return nil
                    default:
                    }
                    return err
                }
                if client != nil {
                    go func() {
                        if err := p.processRequests(client); err != nil {
                            log.Println("error processing request:", err)
                        }
                    }()
                }
            }
        }

        func (p *TSimpleServer) Serve() error {
            err := p.Listen()
            if err != nil {
                return err
            }
            p.AcceptLoop()
            return nil
        }

        func (p *TSimpleServer) processRequests(client TTransport) error {
            processor := p.processorFactory.GetProcessor(client)
            inputTransport := p.inputTransportFactory.GetTransport(client)
            outputTransport := p.outputTransportFactory.GetTransport(client)
            inputProtocol := p.inputProtocolFactory.GetProtocol(inputTransport)
            outputProtocol := p.outputProtocolFactory.GetProtocol(outputTransport)
            defer func() {
                if e := recover(); e != nil {
                    log.Printf("panic in processor: %s: %s", e, debug.Stack())
                }
            }()
            if inputTransport != nil {
                defer inputTransport.Close()
            }
            if outputTransport != nil {
                defer outputTransport.Close()
            }
            for {
                ok, err := processor.Process(inputProtocol, outputProtocol)
                if err, ok := err.(TTransportException); ok && err.TypeId() == END_OF_FILE {
                    return nil
                } else if err != nil {
                    log.Printf("error processing request: %s", err)
                    return err
                }
                if !ok {
                    break
                }
            }
            return nil
        }

从这段源码，我们已经可以分析的很清楚了，对于thrift的服务器端来说，启动Listen后，就进入一个循环等待thrift客户端连接请求的过程，一旦有客户端新的连接请求过来，就Accept这个连接请求，生成一个新的封装底层网络通讯的TTransport对象，然后把这个代表客户端请求的TTransport对象传递一个独立的goroutine来处理。在这个goroutine里面，又启动了一个for循环，循环里面就是调用了自定义的TProcessor->Process过程来处理本次的请求并返回结果。如果这次处理过程是正常的，即返回的两个状态值ok=true并且err=nil，那么这个循环继续，开始对下一次请求的处理；如果这次处理过程发生了异常，即返回的两个状态值ok=false或者err!=nil，那么这个循环就退出了，对应的底层连接也随之关闭。

所以，对于thrift客户端来说，采用短连接的模式来调用API，在使用上比较简单清晰，在程序的容错性上也比较好处理，如果对性能不是特别要求的话，还是建议使用短连接的模式。如果很在乎程序的性能，希望在性能上有高追求的话，也可以使用长连接的模式，但一定要处理好容错的问题，因为针对上面的分析，一旦本次API调用返回error，那么对应的连接就会被断开，如果在上面继续调用API就会报错，所以就需要添加重连机制，保证在之前连接被断开的情况下，重新建立连接。

