> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/SL_World/article/details/86691747)

> 引言：在[上一章](https://blog.csdn.net/SL_World/article/details/86597738)中我们介绍了从 yield from 的来源到 async 的使用，并在最后以 `asyncio.wait()` 方法实现协程，下面我们通过不同控制结构来实现协程，让我们一起来看看他们的不同作用吧～

目录
--

1.  [《Python 异步 IO 之协程 (一): 从 yield from 到 async 的使用》](https://blog.csdn.net/SL_World/article/details/86597738)
2.  [《Python 异步 IO 之协程 (二): 使用 asyncio 的不同方法实现协程》](https://blog.csdn.net/SL_World/article/details/86691747)

在多个协程中的**线性控制流**很容易**通过**内置的**关键词 `await` 来管理**。使用 `asyncio` 模块中的方法可以实现更多复杂的结构，它可以**并发地**完成多个协程。

一、asyncio.wait()
----------------

你可以将一个操作分成多个部分并分开执行，而 `wait(tasks)` 可以被用于**中断**任务集合 (tasks) 中的某个**被事件循环轮询**到的**任务**，直到该协程的其他后台操作完成才**被唤醒**。

```
import time
import asyncio
async def taskIO_1():
    print(' 开始运行 IO 任务 1...')
    await asyncio.sleep(3)  # 假设该任务耗时 3s
    print('IO 任务 1 已完成，耗时 3s')
    return taskIO_1.__name__
async def taskIO_2():
    print(' 开始运行 IO 任务 2...')
    await asyncio.sleep(2)  # 假设该任务耗时 2s
    print('IO 任务 2 已完成，耗时 2s')
    return taskIO_2.__name__
async def main(): # 调用方
    tasks = [taskIO_1(), taskIO_2()]  # 把所有任务添加到 task 中
    done,pending = await asyncio.wait(tasks) # 子生成器
    for r in done: # done 和 pending 都是一个任务，所以返回结果需要逐个调用 result()
        print(' 协程无序返回值：'+r.result())

if __name__ == '__main__':
    start = time.time()
    loop = asyncio.get_event_loop() # 创建一个事件循环对象 loop
    try:
        loop.run_until_complete(main()) # 完成事件循环，直到最后一个任务结束
    finally:
        loop.close() # 结束事件循环
    print(' 所有 IO 任务总耗时%.5f 秒 ' % float(time.time()-start))

```

执行结果如下：

```
开始运行 IO 任务 1...
开始运行 IO 任务 2...
IO 任务 2 已完成，耗时 2s
IO 任务 1 已完成，耗时 3s
协程无序返回值：taskIO_2
协程无序返回值：taskIO_1
所有 IO 任务总耗时 3.00209 秒

```

【解释】：wait() [官方文档](https://docs.python.org/zh-cn/3/library/asyncio-task.html)用法如下：

```
done, pending = await asyncio.wait(aws)

```

此处并发运行传入的 `aws`(awaitable objects)，同时通过 `await` 返回一个包含 (done, pending) 的元组，**done** 表示**已完成**的任务列表，**pending** 表示**未完成**的任务列表。  
**注：**  
①只有当给 `wait()` 传入 `timeout` 参数时才有可能产生 `pending` 列表。  
②通过 `wait()` 返回的**结果集**是**按照**事件循环中的任务**完成顺序**排列的，所以其往往**和原始任务顺序不同**。

二、asyncio.gather()
------------------

如果你只关心协程并发运行后的结果集合，可以使用`gather()`，它不仅通过 `await` 返回仅一个结果集，而且这个结果集的**结果顺序**是传入任务的**原始顺序**。

```
import time
import asyncio
async def taskIO_1():
    print(' 开始运行 IO 任务 1...')
    await asyncio.sleep(3)  # 假设该任务耗时 3s
    print('IO 任务 1 已完成，耗时 3s')
    return taskIO_1.__name__
async def taskIO_2():
    print(' 开始运行 IO 任务 2...')
    await asyncio.sleep(2)  # 假设该任务耗时 2s
    print('IO 任务 2 已完成，耗时 2s')
    return taskIO_2.__name__
async def main(): # 调用方
    resualts = await asyncio.gather(taskIO_1(), taskIO_2()) # 子生成器
    print(resualts)

if __name__ == '__main__':
    start = time.time()
    loop = asyncio.get_event_loop() # 创建一个事件循环对象 loop
    try:
        loop.run_until_complete(main()) # 完成事件循环，直到最后一个任务结束
    finally:
        loop.close() # 结束事件循环
    print(' 所有 IO 任务总耗时%.5f 秒 ' % float(time.time()-start))

```

执行结果如下：

```
开始运行 IO 任务 2...
开始运行 IO 任务 1...
IO 任务 2 已完成，耗时 2s
IO 任务 1 已完成，耗时 3s
['taskIO_1', 'taskIO_2']
所有 IO 任务总耗时 3.00184 秒

```

【解释】：`gather()` 通过 `await` 直接**返回**一个结果集**列表**，我们可以清晰的从执行结果看出来，虽然任务 2 是先完成的，但最后返回的**结果集的顺序是按照初始传入的任务顺序排的**。

三、asyncio.as_completed()
------------------------

`as_completed(tasks)` 是一个生成器，它管理着一个**协程列表** (此处是传入的 tasks) 的运行。当任务集合中的某个任务率先执行完毕时，会率先通过 `await` 关键字返回该任务结果。可见其返回结果的顺序和 `wait()` 一样，均是按照**完成任务顺序**排列的。

```
import time
import asyncio
async def taskIO_1():
    print(' 开始运行 IO 任务 1...')
    await asyncio.sleep(3)  # 假设该任务耗时 3s
    print('IO 任务 1 已完成，耗时 3s')
    return taskIO_1.__name__
async def taskIO_2():
    print(' 开始运行 IO 任务 2...')
    await asyncio.sleep(2)  # 假设该任务耗时 2s
    print('IO 任务 2 已完成，耗时 2s')
    return taskIO_2.__name__
async def main(): # 调用方
    tasks = [taskIO_1(), taskIO_2()]  # 把所有任务添加到 task 中
    for completed_task in asyncio.as_completed(tasks):
        resualt = await completed_task # 子生成器
        print(' 协程无序返回值：'+resualt)

if __name__ == '__main__':
    start = time.time()
    loop = asyncio.get_event_loop() # 创建一个事件循环对象 loop
    try:
        loop.run_until_complete(main()) # 完成事件循环，直到最后一个任务结束
    finally:
        loop.close() # 结束事件循环
    print(' 所有 IO 任务总耗时%.5f 秒 ' % float(time.time()-start))

```

执行结果如下：

```
开始运行 IO 任务 2...
开始运行 IO 任务 1...
IO 任务 2 已完成，耗时 2s
协程无序返回值：taskIO_2
IO 任务 1 已完成，耗时 3s
协程无序返回值：taskIO_1
所有 IO 任务总耗时 3.00300 秒

```

【解释】：从上面的程序可以看出，使用 `as_completed(tasks)` 和 `wait(tasks)` **相同之处**是返回结果的顺序是**协程的完成顺序**，这与 gather() 恰好相反。而**不同之处**是 `as_completed(tasks)` 可以**实时返回**当前完成的结果，而 `wait(tasks)` 需要等待所有协程结束后返回的 `done` 去获得结果。

四、总结
----

以下 `aws` 指：`awaitable objects`。即**可等待对象集合**，如一个协程是一个可等待对象，一个装有多个协程的**列表**是一个`aws`。

<table><thead><tr><th>asyncio</th><th>主要传参</th><th>返回值顺序</th><th><code>await</code><h-hws hidden=""> </h-hws>返回值类型</th><th>函数返回值类型</th></tr></thead><tbody><tr><td>wait()</td><td>aws</td><td>协程完成顺序</td><td>(done,pending)<br>装有两个任务列表元组</td><td>coroutine</td></tr><tr><td>as_completed()</td><td>aws</td><td>协程完成顺序</td><td>原始返回值</td><td>迭代器</td></tr><tr><td>gather()</td><td>*aws</td><td>传参任务顺序</td><td>返回值列表</td><td>awaitable</td></tr></tbody></table>

【参考文献】：  
[[1] Composing Coroutines with Control Structures](https://pymotw.com/3/asyncio/control.html)  
[[2] Python 3.7.2 文档. 协程与任务](https://docs.python.org/zh-cn/3/library/asyncio-task.html)  
[[3] 控制组合式 Coroutines](https://mozillazg.com/2017/08/python-asyncio-note-control-coroutines)