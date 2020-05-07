title: Go语言中的异常处理机制
date: 2015-02-05 17:54:32
tags: [Golang]
categories: 编程技术
---
正如在前面的文章[《Go语言基础——为什么选择Go》](/2014/10/13/Go语言基础——为什么选择Go？)中提到的，Go语言采用了独创的新的错误处理机制，抛弃了之前主流语言中使用的try-catch模式的错误处理机制。因为传统的try-catch结构会破坏程序的可读性和维护性，让开发者仅仅为了程序安全性而添加大量一层套一层的try-catch语句，这个跟Go语言奉行的简单精致的设计哲学背道而驰，所以Go语言的设计者抛弃了这种做法。

Go语言引入了3个关键字用于异常的处理，这3个关键字是`defer`、`panic`和`recover`。

但是值得一提的是，虽然Go语言的设计者们规划了这种异常处理机制，但是并不建议大量使用该机制，而是推荐采用传统的判断函数返回状态的方法来保证程序执行的流程安全，而这种新的错误处理机制只有在特定的场景下才使用。

要想确定在什么场景下使用，就必须明确地区分开错误和异常。错误指的是可能出现问题的地方出现了问题，比如打开一个文件时失败，这种情况在人们的意料之中；而异常指的是不应该出现问题的地方出现了问题，这种情况在人们的意料之外。

对于错误的场景，在Go语言中惯用的处理方法是通过函数或方法的最后一个返回参数返回error类型的状态，而调用者需要总是检查函数或方法的返回状态，来决定程序的执行流程。而可以忽略这些返回状态检查的情况是那些对程序的正常执行逻辑不造成影响的函数或方法调用，比如只是单纯的向控制台打印日志。

对于异常的场景，在Go语言中惯用的处理方式是调用panic来终止程序的运行，我们可以传递给panic任何希望传递的值，这个信息会在随后dump出来的程序调用栈中显示出来，以提供给程序开发者来修复这个问题。所以panic的使用场景类似于其它语言(比如C语言)中的assert断言，其含义是保证程序不要执行到这里，执行到这里就表示程序出现了不可接受的异常，需要终止程序的运行，引起开发者的注意。但是这种使用模式通常只适用于程序的开发阶段，出现异常后程序终止，开发者关注该问题并修复，然后让程序继续运行起来。一旦我们的程序发布出去，成为在线的使用系统，那么就不再希望如此。因为程序碰到异常后Down掉，无论对于用户还是开发者来说，都是不可接受的，这个时候就需要我们的程序即使遭遇了异常也不至于Over掉，那么recover的就派上了用场。使用recover可以在程序遭遇异常Down掉之前捕获到这个异常，然后做出适当的处理，比如转换成一个error类型的错误返回给调用者，让程序优雅的运行下去，而不是直接嗝屁。但recover的使用必须跟defer相配合，这在下面会具体的讲解到。

在多级函数调用的场景下，底层的一个函数调用了panic抛出一个异常，这个异常就会向上层调用者传递。如果上层调用者在defer调用中使用了recover捕获这个异常，就可以对这个异常进行处理；如果上层调用者没有在defer中使用recover捕获这个异常，那么这个异常不会到此终止，而是继续向更上层调用者传递，以此类推，直到到达main函数。如果main函数在defer调用中使用recover捕获了这个异常，就可以对这个异常进行处理；如果main函数没有在defer调用中使用recover捕获这个异常，那么这个异常就到此终止，程序也随之终止运行。

一旦在defer中使用recover捕获到了一个异常，我们通常应对的策略有哪些呢？一般来说有三种：
1. 忽略这个异常。这种应对策略通常是不推荐的，因为这相当于小猫把自己的便便掩盖起来，不然自己的主人看到，但迟早还是会被发现的。最起码要用日志把这个异常记录下来。
2. 使用panic继续向上传递这个异常。如果不希望在这一层次来处理该异常，那么我们就可以针对该异常做一些清理工作之后，使用panic继续向上层传递，最终汇总到一处来统一处理这些异常。
3. 转换这个异常为error类型的返回值。这种应对策略是被通常推荐的一种方法，捕获到异常后，封装成一个error，然后通过函数或方法的返回值返回到调用者，调用者根据错误的类型做出合适的程序控制，这是最自然的一种做法。

通常，Go语言的标准库中更多的使用错误的处理方法，而非使用异常的处理方法。那么在我们自己开发的Go语言库中也推荐这样做，即使内部使用了panic抛出了异常，也最好在该异常脱离本库的控制之前使用recover捕获它，并转换成一个error类型的错误。这样对库的使用者来说，也是相对友好的做法。

接下来我们用几个具体的例子来了解一下这种机制是怎么运作的。

        package main

        import (
            "fmt"
            "math"
            "os"
            "strconv"
        )

        func main() {
            if len(os.Args) != 2 {
                fmt.Printf("usage: %v <number>\n", os.Args[0])
                os.Exit(1)
            }

            strN := os.Args[1]
            n, err := strconv.ParseInt(strN, 0, 0)
            if err != nil {
                fmt.Printf("input number invalid. %v\n", err)
                os.Exit(1)
            }

            r, err := Int8FromInt64(n)
            if err != nil {
                fmt.Printf("转换%v到类型int8失败：%v\n", n, err)
                os.Exit(1)
            }
            fmt.Printf("转换%v到类型int8成功: %v\n", n, r)
        }

        func Int8FromInt64(num int64) (r int8, err error) {
            defer func() {
                if e := recover(); e != nil {
                    err = fmt.Errorf("%v", e)
                }
            }()

            r = ConvertInt64ToInt8(num)

            return r, nil
        }

        func ConvertInt64ToInt8(num int64) int8 {
            if math.MinInt8 <= num && num <= math.MaxInt8 {
                return int8(num)
            }
            panic("out of the int8 range")
        }

这个例子只是为了演示Go语言的异常处理的使用方法，在ConvertInt64ToInt8函数我们使用panic制造了一个异常，但实际的生产代码中我们可能只需要直接返回一个error错误。上述例子是一个标准的异常处理流程，在Convertint64toint8中抛出一个异常，在它的调用者Int8FromInt64里面定义了一个defer调用，使用recover捕获这个异常，然后转换成一个error返回值。最终对于main函数中的调用流程来说，这个异常是透明的，是不被感知的。


        func Int8FromInt64(num int64) (r int8, err error) {
            defer func() {
                err = fmt.Errorf("这是一个修改后error")
            }()

            defer func() {
                if e := recover(); e != nil {
                    err = fmt.Errorf("%v", e)
                }
            }()

            r = ConvertInt64ToInt8(num)

            return r, nil
        }

这个例子对sample1中的Int8fromint64做了一些修改，其它的没有变化。在Int8fromint64中定义了两个defer调用，主要为了演示defer调用的LIFO（后进先出）的特性。一个函数体中如果先后定义了多个defer调用，那么这些defer调用的执行顺序采取类似于压栈的方式：后定义的defer调用先执行，最开始定义的defer定义最后执行。具体到这个例子，如果Convertint64toint8中抛出了异常，后面定义的defer调用使用recover捕获到了这个异常，并且转换成了返回值err，但是这个err的值随后就被前面定义的defer调用给修改掉了，所以最终我们获得的函数返回状态是：error("这是一个修改后error")。


        func main() {
            if len(os.Args) != 2 {
                fmt.Printf("usage: %v <number>\n", os.Args[0])
                os.Exit(1)
            }

            strN := os.Args[1]
            n, err := strconv.ParseInt(strN, 0, 0)
            if err != nil {
                fmt.Printf("input number invalid. %v\n", err)
                os.Exit(1)
            }

            defer func() {
                if e := recover(); e != nil {
                    fmt.Printf("Main - 捕获到异常: %v, 程序将退出.\n", e)
                    os.Exit(1)
                }
            }()

            r, err := Int8FromInt64(n)
            if err != nil {
                fmt.Printf("转换%v到类型int8失败：%v\n", n, err)
                os.Exit(1)
            }
            fmt.Printf("转换%v到类型int8成功: %v\n", n, r)
        }

        func Int8FromInt64(num int64) (r int8, err error) {
            defer func() {
                if e := recover(); e != nil {
                    fmt.Printf("Int8FromInt64 - 捕获到异常: %v.\n", e)
                    panic(fmt.Sprintf("Int8FromInt64: %v", e))
                }
            }()

            r = ConvertInt64ToInt8(num)

            return r, nil
        }


这个例子演示了捕获到异常后继续使用panic向上层调用者抛出异常。需要注意的是，在Int8fromint64中如果我们不捕获这个异常，这个异常照样会向上层调用者传递，直到到达Main函数，如果Main函数不捕获这个异常进行处理，程序就会异常终止掉。之所以在Int8fromint64中捕获异常，是基于一些清理的需求，一旦捕获，就需要使用panic继续向上传递。

