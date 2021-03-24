> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [python.freelycode.com](https://python.freelycode.com/contribution/detail/1931)

Python 部落 (python.freelycode.com) 组织翻译，禁止转载，欢迎转发。

这是一篇对 Python 的 asyncio 模块的快速指南，基于 Python 3.8 版本，文章目录如下：

    引言

    为什么关注 asyncio？

    asyncio 的快速总结

    concurrent.futures 的快速总结

    绿色线程？

    事件循环

    可等待对象

        协程

        任务

        Future 类型

    运行一个 asyncio 程序

        在 REPL 中运行异步代码

        使用另一个事件循环

    并发函数

    弃用的函数

    例子

        gather 

        wait

        wait_for

        as_completed

        create_task

    回调

    池

        执行器

        asyncio.future 与 concurrent.futures.Future 对比

        asyncio.wrap_future

以下为正文：

**引言**

     那么，首先让我们从明明存在而大家不愿讨论的问题开始：Python 标准库提供了许多模块来处理异步 / 并发 / 多进程代码。

*   [`_thread`](https://docs.python.org/3.8/library/_thread.html)
    
*   [`threading`](https://docs.python.org/3.8/library/threading.html)
    
*   [`multiprocessing`](https://docs.python.org/3.8/library/multiprocessing.html)
    
*   [`asyncio`](https://docs.python.org/3.8/library/asyncio.html)
    
*   `[concurrent.futures](https://docs.python.org/3.8/library/concurrent.futures.html)`
    

     在这篇文章中，我们将关注最后两个。首先，我们将重点介绍 asyncio，然后介绍 concurrent.futures 的一些有用特性。

     这篇文章的目的是理解为什么你喜欢使用 asyncio 而不是其他可用的模块 (例如_thread 和 threading) 的原因，以及什么时候使用 multiprocessing 或 concurrent.futures 更合适。

**为什么关注 asyncio？**

     编写并发代码 (使用_thread 或 threading 模块) 的一个问题是要承受 “CPU 上下文切换” 的成本(因为 CPU 核一次只能运行一个线程)，这虽然很快，但不是免费的。

     多线程代码还必须处理 “竞争条件”、“死锁 / 活锁” 和“资源饥饿”(一些线程资源利用过多，而另一些线程资源利用过低)等问题。

     异步避免了这些问题，所以让我们看看它如何实现。

**asyncio 快速总结**  

> asyncio 是一个使用 async/await 语法编写并发代码的库。– [docs.python.org/3.8/library/asyncio.html](https://docs.python.org/3.8/library/asyncio.html)

     asyncio 模块同时提供了高层和底层 API。库和框架开发人员可以使用底层 API，而所有其他用户可以使用高层 API。

     它在概念上与更传统的 threading 或 multiprocess 异步代码执行方法不同，因为它利用一种称为[事件循环](https://www.integralist.co.uk/posts/python-asyncio/#event-loop)的东西来处理异步 “任务” 的调度，而不是使用更传统的线程或子进程。

     重要的是，asyncio 主要用于解决 I/O 网络性能问题，而不是 CPU 繁忙的操作 (这种情况应该使用多进程)。因此，asyncio 并不能替代所有类型的异步执行操作。

     Asyncio 是围绕 “协作多任务” 的概念设计的，因此你可以完全控制 CPU 何时发生“上下文切换”(即上下文切换发生在应用程序级别，而不是硬件级别)。

     当使用线程时，Python 调度器负责这项工作，因此应用程序可能在任何时刻切换上下文 (即它变得不确定)。

     这意味着在使用线程时，需要使用某种形式的 “锁定” 机制来防止多个线程访问 / 修改共享内存(否则会导致程序变成非线程安全)。

**concurrent.futures 快速的总结**  

> concurrent.futures 模块为异步执行的可调用对象提供了一个高层接口。 – [docs.python.org/3.8/library/concurrent.futures.html](https://docs.python.org/3.8/library/concurrent.futures.html)

     concurrent.futures 提供了 threading 和 multiprocessing 模块的高层抽象，这就是为什么我们不在本文中详细讨论这些模块的原因。实际上，_thread 模块是一个非常底层的 API，threading 模块本身就构建在它之上 (同样，这也是我们不打算讨论它的原因)。

     现在我们已经提到，asyncio 可以帮助我们避免使用线程，那么如果 concurrent.futures 只是多线程 (和多进程) 之上的抽象，我们为什么要使用它呢？因为并不是所有的库 / 模块 / API 都支持异步模型。

     例如，如果您使用 boto3 并与 AWS S3 交互，那么你会发现这些都是同步操作。可以用多线程代码包装这些调用，但最好使用 concurrent.futures，因为这意味着你不仅可以从传统的线程中获益，还可以从异步友好的包中获益。

     concurrent.futures 模块还可以和 asyncio 事件循环进行互操作，这使得在异步驱动的应用程序中使用线程 / 进程池更加容易。

     此外，当你需要一个线程池或进程池时，你还会想要利用 concurrent.futures 模块，这时你也在使用一个纯粹的、现代的 Python API(适用于更灵活但底层的 threading 或 multiprocessing 模块)。

**绿色线程？**

     实现异步编程的方法有很多。有事件循环方法 (asyncio 就是通过这种方式实现的)，有“回调” 方法，在历史上被单线程语言如 JavaScript 所青睐，还有更传统的方法是使用“绿色线程”。

     本质上，绿色线程的外观和感觉与普通线程完全一样，只不过这些线程是由应用程序代码而不是硬件来调度的 (因此可以像事件循环一样有效地解决确定性上下文切换问题)。但是处理共享内存的问题仍然存在。

     所以现在让我们快速看一下什么是 “事件循环”，因为它是异步工作的基础，以及为什么我们可以避免“回调地狱” 和“绿色线程”固有的问题。

**事件循环**

     所有异步应用程序的核心元素是 “事件循环”。事件循环是调度和运行异步任务的机制 (它还负责处理网络 IO 操作和子进程的运行)。

![](https://qiniumedia.freelycode.com/13945/4d3c911807f841d89e1a504de74a3117.png)

[图片来源](https://eng.paxos.com/python-3s-killer-feature-asyncio)

     使 asyncio 事件循环如此有效的原因是 Python 通过生成器实现了它。生成器允许函数部分执行，然后在某个特定点停止执行，在再次恢复运行之前维护对象和异常的堆栈。

     我最近写了关于[迭代器、生成器和协程](https://www.integralist.co.uk/posts/python-generators/)的文章，所以如果你对这些概念感兴趣，那么我将推荐你阅读这篇文章。

> 注意: 有关事件循环的更多 API 信息，请参考官方 Python 文档。

**可等待对象**

     异步通信背后的驱动力是调度异步 “任务” 的能力。Python 中有许多不同类型的对象可以帮助实现这一点，它们通常被分组到术语叫 “可等待对象” 类型中。

     最后，如果某个对象可以用在一个 await 表达式中，那么它就是一个可等待对象。

     主要有三种可等待对象类型：

1.  协程
    
2.  任务
    
3.  Future 对象
    

> 注意：Future 对象是一种底层的类型，所以如果你不是库 / 框架开发人员，就不必关心它 (因为你应该使用更高层抽象的 API)。

**协程**

     这里使用了两个密切相关的术语：

*   协程函数： 一个 async def 函数。
    
*   协程对象：通过调用协程函数返回的对象。
    

> 基于生成器的协程函数 (例如，那些用 @asyncio.coroutine 修饰函数定义的函数) 被 async/await 语法所取代，但是直到 Python 3.10 才开始支持 协程 - [docs.python.org/3.8/library/asyncio-task.html](https://docs.python.org/3.8/library/asyncio-task.html#asyncio-generator-based-coro)。
> 
> 有关基于生成器的协程及其与 asyncio 的历史相关的详细信息，请参阅我的文章 “[迭代器、生成器、协程](https://www.integralist.co.uk/posts/python-generators/)”。

**任务**

    任务用于并发地调度协程。

     所有异步应用程序通常都有 (至少) 一个 “主” 入口点任务，该任务将在事件循环中立即运行。这是使用 asyncio.run 函数完成的(参见“[运行一个 asyncio 程序](https://www.integralist.co.uk/posts/python-asyncio/#running-an-asyncio-program)”)。

     一个协程函数被期望传递给 asyncio.run，而在内部，asyncio 会使用辅助函数 coroutines.iscoroutine(参见：[源代码](https://github.com/python/cpython/blob/master/Lib/asyncio/runners.py#L8)) 来检查是否是协程。如果不是协程，则会引发一个错误，否则协程将被传递到 loop.run_until_complete(参见：[源代码](https://github.com/python/cpython/blob/master/Lib/asyncio/base_events.py#L599))。

     run_until_complete 函数期待一个 [Future](https://www.integralist.co.uk/posts/python-asyncio/#futures)(关于 Future 是什么，请参阅下面的部分)，并使用另一个帮助函数 futures.isfuture 检查所提供的对象类型。如果不是 Future，则使用底层 API ensure_future 将协程转换为 Future(参见[源代码](https://github.com/python/cpython/blob/master/Lib/asyncio/tasks.py#L653))。

> 注意：[这里](https://gist.github.com/1efc8dcfc0b1e9e8e8b89a4b2019f3af)是验证一个函数是否是协程的各种方法的比较。结果未必如你所料。

     在较早的 Python 版本中，如果你打算手动创建自己的 Future 并将其安排到事件循环中，那么你应该使用 asyncio.ensure_future(现在被认为是一个底层 API)，但是在 Python 3.7 + 中，它已经被 asyncio.create_task 所取代。

     另外在 Python 3.7 中，与事件循环直接交互的概念 (例如事件循环，用 create_task 创建任务然后传递到事件循环) 已经被 asyncio.run 所取代，它抽象了一个过程(参见“[运行一个 asyncio 程序](https://www.integralist.co.uk/posts/python-asyncio/#running-an-asyncio-program)” 来了解详细信息)。

     下面的 API 可以让你看到在事件循环中运行任务的状态:

*   `asyncio.current_task`
    
*   `asyncio.all_tasks`
    

> 注意：有关任务对象的其他可用方法，请参阅[文档](https://docs.python.org/3.8/library/asyncio-task.html#asyncio.Task)。  

### Future 对象

     Future 是一个底层的可等待对象，它表示异步操作的最终结果。

     打个比方：它就像一个空信箱。在将来的某个时候，邮递员会来把信投进邮筒。

     这个 API 允许基于回调的代码与 async/await 一起使用，而 [loop.run_in_executor](https://docs.python.org/3.8/library/asyncio-eventloop.html#asyncio.loop.run_in_executor) 是一个返回 Future 对象的 asyncio 底层 API 函数的示例 (请参阅[并发函数](https://www.integralist.co.uk/posts/python-asyncio/#concurrent-functions)中列出的一些 API)。

> 注意：关于 Future 可用的其他方法，请参阅[文档](https://docs.python.org/3.8/library/asyncio-future.html#asyncio.Future)。

**运行一个 asyncio 程序**

    使用高层 API(参照 Python 3.7+) 例子如下：

![](https://qiniumedia.freelycode.com/13945/7ff69ff1ab7d4264827331bf4e3b6028.png)

     .run 函数总是创建一个新的事件循环，并在结束时关闭它。如果您正在使用较底层的 API，那么这将是你必须手动处理的事情 (如下所示)。

![](https://qiniumedia.freelycode.com/13945/689c52ee976b4781b4c774d8b654e82f.png)

**在 REPL 中运行异步代码**

     在 Python 3.8 之前，你不能在标准 Python REPL 中执行异步代码 (这需要使用 IPython REPL)。

     要在最新版本的 Python 中做到这一点，需要运行 Python -m asyncio。一旦 REPL 启动，你就不需要使用 asyncio.run()，而是直接使用 await 语句。

![](https://qiniumedia.freelycode.com/13945/db4dfe8981c14cb193608f2ad0abfe06.png)

> 注意：REPL 在启动时自动执行 import asyncio，这样我们就可以使用任何异步函数 (例如. sleep 函数)，而不需要自己手动输入 import 语句。

**使用另一个事件循环**

     如果由于某些原因你不希望使用 asyncio 提供的事件循环 (这是一个纯 Python 实现)，你可以将其替换为另一个事件循环，比如 [uvloop](https://github.com/MagicStack/uvloop/)。  

> uvloop 是一个快速的、完全替代内置 asyncio 事件循环的方法。uvloop 是在 [Cython](https://cython.org/) 中实现的，并在底层使用 [libuv](https://libuv.org/)。

     据 uvloop 的作者说，它的速度可以和 [Go](https://golang.org/) 程序相比！我建议阅读他们的[博客文章](https://magic.io/blog/uvloop-blazing-fast-python-networking/)，了解它的最初版本。

     如果你想使用 uvloop，那么首先用 pip install uvloop 来安装它，然后像这样添加一个对 uvloop.install() 的调用：  

![](https://qiniumedia.freelycode.com/13945/247f88814adb4dc88bfaa291545eb17c.png)

**并发函数**

     以下函数有助于协调函数的并发运行，并根据应用程序的需要提供不同程度的控制。

*   asyncio.gather：获取一个可等待对象序列，返回一个已成功等待对象的聚合列表。
    
*   asyncio.shield：防止可等待对象被取消。
    
*   asyncio.wait：等待一系列可等待对象，直到满足给定的 “条件”。
    
*   asyncio.wait_for：等待单个可等待对象，直到到达给定的 “超时”。
    
*   asyncio.as_completed：类似于 gather，但在结果准备好时填充返回 Future。
    

> 注意：gather 有处理错误和取消的特定选项。例如，如果 return_exceptions: False，则由一个等待对象引发的第一个异常将返回给 gather 的调用者，如果设置为 True，则异常将与成功的结果一起聚合在列表中。如果取消了 gather()，所有提交的等待对象 (尚未完成的) 也将被取消。

**弃用的函数**

*   @asyncio.coroutine：在 Python 3.10 中，为了支持 async def 而被删除
    
*   asyncio.sleep：loop 参数将在 python3.10 中删除  
    

> 注意：你会发现在大多数这些 API 中都可以提供一个 loop 参数，以使你能够指定要使用的事件循环)。Python 似乎已经在 3.8 中弃用了这个参数，并将在 3.10 中完全删除它。

**例子**

### `gather`

     下面的示例演示如何等待多个异步任务完成。

![](https://qiniumedia.freelycode.com/13945/006aa6a4904c44f1ae7395354a9e7cdc.png)

### `wait`

     下面的示例使用 FIRST_COMPLETED 选项，这意味着无论哪个任务先完成都将返回。  

![](https://qiniumedia.freelycode.com/13945/9c43a2d3a559418e9bff99f597cb3da4.png)

     该程序的一个示例输出如下：  

![](https://qiniumedia.freelycode.com/13945/31202b90e1be4028b4acc4a36e51f358.png)

### `wait_for`

     下面的示例演示如何利用超时来防止无休止地等待异步任务完成。

![](https://qiniumedia.freelycode.com/13945/007f319061214c6eafb024c3d15b85fd.png)

> 注意：asyncio.TimeoutError 不提供任何额外的信息，所以在输出中使用它是没有意义的 (例如，except asyncio.TimeoutError as err: print(err))。  

### `as_completed`

     下面的示例演示 as_complete 如何生成第一个要完成的任务，然后是第二快的任务，然后是下一个任务，直到所有任务都完成。

![](https://qiniumedia.freelycode.com/13945/fb0c2ff0195e43548b41835a8899631e.png)

     该程序的一个示例输出如下:

![](https://qiniumedia.freelycode.com/13945/7709cd9051144a9a952178c036b13262.png)

### `create_task`

     下面的示例演示如何将协程转换为任务并将其调度到事件循环中。

![](https://qiniumedia.freelycode.com/13945/97cd64c9a6a24734b4ddb02279773f6f.png)

     我们可以从上面的程序中看到，我们使用 create_task 将协程函数转换为任务。这将自动调度要在事件循环上运行的任务到下一个可用的时钟周期。

     这与底层 API ensure_future(创建新任务的首选方法) 函数形成了对比。ensure_future 函数有特定的逻辑分支，这使得它比 create_task 适用于更多的输入类型，create_task 只支持将协同程序调度到事件循环并将其包装在任务中 (请参阅：[ensure_future 源代码](https://github.com/python/cpython/blob/master/Lib/asyncio/tasks.py#L653))。

     这个程序的输出如下：  

![](https://qiniumedia.freelycode.com/13945/b7f6729bae574912baebce43a49491f7.png)

     让我们回顾一下代码，并与上面的输出进行比较。

     我们将 foo() 转换为任务，然后在创建任务后立即打印返回的任务。因此，当我们打印任务时，我们可以看到它的状态显示为 “pending”(因为它还没有执行)。

     接下来我们将休眠 5 秒钟，因为这将导致 foo 任务现在运行 (因为当前任务 hello_world 将被认为是繁忙的)。

     在 foo 任务中，我们也会休眠，但时间比 hello_world 长，因此事件循环现在将上下文切换回 hello_world 任务，在休眠结束后，我们将打印输出字符串 Hello World。

     最后，我们再休眠 10 秒钟。这样我们就可以给 foo 任务足够的时间来完成并打印它自己的输出。如果我们不这样做，那么 hello_world 任务将完成并关闭事件循环。hello_world 的最后一行正在打印 foo 任务，此时我们将看到 foo 任务的状态现在显示为'finished'。

**回调**

     当处理一个任务时，这实际上是一个 Future 对象，然后你可以执行一个 “回调” 函数，只要 Future 对象设置了回调函数。

     下面的示例通过修改前面的 [create_task](https://www.integralist.co.uk/posts/python-asyncio/#create_task) 示例代码来演示这一点：

![](https://qiniumedia.freelycode.com/13945/284eba5de1d1475b86714d66e149d052.png)

     注意，在上面的程序中，我们添加了一个新的 got_result 函数，该函数期望接收 Future 对象类型参数，因此在 Future 对象调用. result()。

     还要注意，要调用这个函数，我们将它传递给. add_done_callback()，它在 create_task 返回的任务上被调用。

     这个程序的输出为：

![](https://qiniumedia.freelycode.com/13945/b8f8df4afb0a45b08e8d6bcd0f39cd7e.png)

**池**

     在处理大量并发操作时，利用线程池 (和 / 或子进程) 来防止耗尽应用程序的主机资源可能是明智的。

     这就是 concurrent.futures 模块的作用。它提供了一个称为 Executor 的概念来帮助实现此目的，并且可以独立运行，也可以将其集成到现有的 asyncio 事件循环中 (请参阅：[Executor 文档](https://docs.python.org/3.8/library/concurrent.futures.html#concurrent.futures.Executor))。

**执行器**

     有两种类型的 “执行器”：

*   [`ThreadPoolExecutor`](https://docs.python.org/3.8/library/concurrent.futures.html#threadpoolexecutor)
    
*   `[ProcessPoolExecutor](https://docs.python.org/3.8/library/concurrent.futures.html#processpoolexecutor)`
    

     让我们看看在这些执行器中执行代码的第一种方式，通过使用一个 asyncio 事件循环来调度执行器的运行。

     要实现这一点，你需要调用事件循环的. run_in_executor() 函数，并将 executor 类型作为第一个参数传入。如果不提供，则使用默认执行器 (即 ThreadPoolExecutor)。

     下面的例子是从 [Python 文档](https://docs.python.org/3.8/library/asyncio-eventloop.html#executing-code-in-thread-or-process-pools)中原封不动地复制过来的：

![](https://qiniumedia.freelycode.com/13945/50ee88046f4f4f9a9defb7453edae166.png)

     在其中一个执行器中执行代码的第二种方法是将要执行的代码直接发送到池。这意味着我们不必获取当前事件循环来将池传递给它（如前面的示例所示），但它附带了一个警告，即父程序不会等待任务完成，除非你明确告诉它（我将在下面演示）。

     考虑到这一点，让我们看看另一种方法。它涉及调用执行器的 submit() 方法：

![](https://qiniumedia.freelycode.com/13945/507fbf4af68d47ff9f8715c32a9ab8fe.png)

> 注意：使用全局流程执行器时要小心（例如，设置类似 process_POOL=concurrent.futures.ProcessPoolExecutor()（在全局范围内，并在 do_something() 函数中使用该引用），因为这意味着当程序复制到新进程中时，你将从 Python 解释器得到一个关于泄漏的信号量的错误。这就是我在函数中创建进程池执行器的原因。

     一件事值得注意的是，如果我们没有使用声明 (就像我们在上面的例子中) 这将意味着当完成任务时不会关闭连接池，所以 (由于你的程序继续运行) 你可能会发现资源没有被释放。

     要解决这个问题，可以调用. shutdown() 方法，该方法通过它的父类 concurrent.futures.Executor 向这两种类型的执行器提供。

     下面是一个这样做的例子，但现在使用线程池执行器：

![](https://qiniumedia.freelycode.com/13945/0f3ad75774d64b3faf9907cf6380ee42.png)

     现在我没有在本例中使用 time.sleep()，因为我们使用的是线程池和 time.sleep() 是一个 CPU 绑定操作，否则会阻止线程完成。

     这意味着在检查 future.done() 之前，我们的示例可能总是会导致 slow_op() 函数完成。因此这不是最好的例子。你可以通过合并一个真正缓慢的、不会阻塞的操作来更实际地测试这个功能。

     但是，假设代码中存在一个真正缓慢的操作，这意味着在我们检查 future.done() 时任务还没有完成。

     在这种情况下，我们应该注意到，调用. shutdown() 的位置是在我们明确等待计划任务完成之前，但是当我们断言返回的 future 是否是. done() 时，我们会发现无论试图关闭线程池，任务都被标记为 “done”。

     这是因为 shutdown 方法的默认行为是 wait=True，这意味着在关闭执行器池之前，它将等待所有计划任务的完成。

     因此. shutdown() 方法是一个同步调用 (也就是说，它确保在关闭之前所有的任务都已经完成，因此我们可以保证所有的结果都是可用的)。

     如果我们通过. shutdown (wait = False)，然后调用 future.done() 会抛出一个异常 (此时计划任务仍将运行尽管线程池正在被关闭)，所以在这种情况下我们需要使用另一种机制确保获得调度任务的结果 (比如 concurrent.futures.as_completed 或者 concurrent.futures.wait)。

### `` `asyncio.Future` 与 `concurrent.futures.Future的对比` ``

     最后要讲的是 concurrent.futures.Future 对象与 asyncio.Future 对象不同。

     asyncio.Future 用于 asyncio 的事件循环，并且是可等待的对象。concurrent.futures.Future 不是可等待的对象。

     使用事件循环的. run_in_executor() 方法，将通过在调用 [asyncio.wrap_future](https://www.integralist.co.uk/posts/python-asyncio/#asyncio-wrap-future)(详细信息参见下一节) 时包装 concurrent.futures.Future 类型，来提供两种 future 类型之间必要的互操作性。

### `asyncio.wrap_future`

     自从 Python 3.5 我们可以使用 asyncio.wrap_future，将一个 concurrent.futures.Future 转换为 an asyncio.Future。下面是一个例子。

![](https://qiniumedia.freelycode.com/13945/efada94d09254ed6a3f65af3036981e6.png)

     这个程序的输出如下：

![](https://qiniumedia.freelycode.com/13945/7efc3c837643424c8777433a06e9a36a.png)

> 英文原文：https://www.integralist.co.uk/posts/python-asyncio/  
> 译者：穆胜亮