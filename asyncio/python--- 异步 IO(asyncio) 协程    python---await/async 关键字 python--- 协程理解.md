> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/ssyfj/p/9219360.html)

> **目录**
> 
> [简单了解](#简单了解)  
> [定义一个协程（不同于上面的实例）](#定义一个协程不同于上面的实例)  
> [创建一个任务（对协程进一步封装，可以查看状态等）](#创建一个任务对协程进一步封装，可以查看状态等)  
> [绑定回调 add_done_callback](#绑定回调add_done_callback)  
> [我也可以不使用回调函数，单纯获取返回值](#我也可以不使用回调函数，单纯获取返回值)  
> [阻塞和 await](#阻塞和await)  
> [并发：使用 gather 或者 wait 可以同时注册多个任务，实现并发](#并发：使用gather或者wait可以同时注册多个任务，实现并发)  
> [协程嵌套，将多个协程封装到一个主协程中](#协程嵌套，将多个协程封装到一个主协程中)  
> [协程停止](#协程停止)  
> [上面讨论的都是在同一线程下的事件循环，下面来谈谈不同线程的事件循环](#上面讨论的都是在同一线程下的事件循环，下面来谈谈不同线程的事件循环)  
>  [同一线程：](#同一线程：)  
>  [不同线程事件循环（不涉及协程）：](#不同线程事件循环不涉及协程：)  
>  [新线程协程：](#新线程协程：)  

简单了解
====

### 在 py3 中内置了 asyncio 模块。其编程模型就是一个消息循环。

### 模块查看：

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
from .base_events import *
from .coroutines import *　　#协程模块，可以将函数装饰为协程
from .events import *　　#事件模块，事件循环和任务调度都将使用到他
from .futures import *　　　#异步并发模块，该模块对task封装了许多方法，代表将来执行或没有执行的任务的结果。它和task上没有本质上的区别
from .locks import *　　#异步保证资源同步
from .protocols import *
from .queues import *
from .streams import *
from .subprocess import *
from .tasks import *　　#创建任务，是对协程的封装，可以查看协程的状态。可以将任务集合
from .transports import *
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 调用步骤：

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1.当我们给一个函数添加了async关键字，或者使用asyncio.coroutine装饰器装饰，就会把它变成一个异步函数。
```

```
2.每个线程有一个事件循环，主线程调用asyncio.get_event_loop时会创建事件循环，

3.将任务封装为集合asyncio.gather(*args),之后一起传入事件循环中

4.要把异步的任务丢给这个循环的run_until_complete方法，事件循环会安排协同程序的执行。和方法名字一样，该方法会等待异步的任务完全执行才会结束。
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 简单使用：

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

@asyncio.coroutine　　#设为异步函数
def func1(num):
    print(num,'before---func1----')
    yield from asyncio.sleep(5)
    print(num,'after---func1----')

task = [func1(1),func1(2)]

if __name__ == "__main__":
    begin = time.time()
    loop = asyncio.get_event_loop()　　#进入事件循环
    loop.run_until_complete(asyncio.gather(*task))　　#将协同程序注册到事件循环中
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

```
1 before---func1----
2 before---func1----
1 after---func1----
2 after---func1----
5.00528621673584
```

输出结果

定义一个协程（不同于上面的实例）
================

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):　　#使用async关键字定义一个协程，协程也是一种对象，不能直接运行，需要加入事件循环中，才能被调用。
    print(num,'before---func1----')

if __name__ == "__main__":
    begin = time.time()

    coroutine = func1(2)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(coroutine)
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
func1(2)　　#由于使用async异步关键字，所以不能直接运行
```

　　  D:/MyPython/day25/mq/multhread.py:15: RuntimeWarning: coroutine 'func1' was never awaited  
　　  func1(2)

```
print(type(func1),type(coroutine))　　#<class 'function'> <class 'coroutine'>
```

同：[python---await/async 关键字](https://www.cnblogs.com/ssyfj/p/9219257.html)

### 我们可以使用 send(None) 调用协程（这里不这么使用），这里是将协程放入事件循环中进行处理

```
coroutine = func1(2)
    try:
        coroutine.send(None)
    except StopIteration:
        pass
```

创建一个任务（对协程进一步封装，可以查看状态等）
========================

协程对象不能直接运行，在注册事件循环的时候，**其实是 run_until_complete 方法将协程包装成为了一个任务（task）对象**.

task 对象是 Future 类的子类，保存了协程运行后的状态，用于未来获取协程的结果

### run_until_complete 方法查看：

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
class BaseEventLoop(events.AbstractEventLoop):

   def run_until_complete(self, future):
        """Run until the Future is done.

        If the argument is a coroutine, it is wrapped in a Task.

        WARNING: It would be disastrous to call run_until_complete()
        with the same coroutine twice -- it would wrap it in two
        different Tasks and that can't be good.

        Return the Future's result, or raise its exception.
        """
        self._check_closed()

        new_task = not futures.isfuture(future)
        future = tasks.ensure_future(future, loop=self)
if new_task:
            # An exception is raised if the future didn't complete, so there
            # is no need to log the "destroy pending task" message
            future._log_destroy_pending = False

        future.add_done_callback(_run_until_complete_cb)
        try:
            self.run_forever()
        except:
            if new_task and future.done() and not future.cancelled():
                # The coroutine raised a BaseException. Consume the exception
                # to not log a warning, the caller doesn't have access to the
                # local task.
                future.exception()
            raise
        finally:
            future.remove_done_callback(_run_until_complete_cb)
        if not future.done():
            raise RuntimeError('Event loop stopped before Future completed.')

        return future.result()
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 由源码可以知道，在协程注册后会被自动封装为 task 任务。所以我们不是必须传入 task。但是去创建一个 task 对象，有利于我们理解协程的状态。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')

if __name__ == "__main__":
    begin = time.time()

    coroutine = func1(2)

    loop = asyncio.get_event_loop()

    task = loop.create_task(coroutine)　　#创建了任务
    print(task) #pending

    loop.run_until_complete(task)
    loop.close()
    print(task) #finished
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

**对于协程的 4 种状态：[python--- 协程理解](https://www.cnblogs.com/ssyfj/p/9218730.html)**

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
print(task) #pending
    print(getcoroutinestate(coroutine))

    loop.run_until_complete(task)
    loop.close()
    print(task) #finished
    print(getcoroutinestate(coroutine))
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
CORO_CREATED
2 before---func1----
<Task finished coro=<func1() done, defined at D:/MyPython/day25/mq/multhread.py:4> result=None>
CORO_CLOSED
```

 深入了解: [关于 Task,create_task(),ensure_future 都可以用来创建任务，那么应该使用哪个？](http://blog.sina.com.cn/s/blog_6262a50e0102wngq.html)

条件使用 ensure_future, 他是最外层函数，其中调用了 create_task() 方法，功能全面，而 Task 官方不推荐直接使用

asyncio.ensure_future(coroutine) 和 loop.create_task(coroutine) 都可以创建一个 task，run_until_complete 的参数是一个 futrue 对象。当传入一个协程，其内部会自动封装成 task，task 是 Future 的子类。`isinstance(task, asyncio.Future)`将会输出 True。

绑定回调 add_done_callback
======================

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
async def func1(num):
    print(num,'before---func1----')
    return "recv num %s"%num

def callback(future):
    print(future.result())

if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(1)
    loop = asyncio.get_event_loop()
    task1=asyncio.ensure_future(coroutine1)
    task1.add_done_callback(callback)
    loop.run_until_complete(task1)
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

1 before---func1----  
recv num 1  
0.004000186920166016

可以看到，coroutine 执行结束时候会调用回调函数。并通过参数 future 获取协程执行的结果。我们创建的 task 和回调里的 future 对象，实际上是同一个对象。

我也可以不使用回调函数，单纯获取返回值
===================

### 当 task 状态为 finished 时候，我们可以直接使用 result 方法（在 future 模块）获取返回值

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
async def func1(num):
    print(num,'before---func1----')
    return "recv num %s"%num


if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(1)
    loop = asyncio.get_event_loop()
    task1=asyncio.ensure_future(coroutine1)
    loop.run_until_complete(task1)
    print(task1)
    print(task1.result())
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
1 before---func1----
<Task finished coro=<func1() done, defined at D:/MyPython/day25/mq/multhread.py:6> result='recv num 1'>
recv num 1
0.0030002593994140625
```

阻塞和 await
=========

使用 async 关键字定义的协程对象，使用 await 可以针对耗时的操作进行挂起（是生成器中的 yield 的替代，但是本地协程函数不允许使用），让出当前控制权。协程遇到 await，事件循环将会挂起该协程，执行别的协程，直到**其他协程也挂起，或者执行完毕，**在进行下一个协程的执行

使用 asyncio.sleep 模拟阻塞操作。

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time


async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num


if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(5)
    coroutine2 = func1(3)
    loop = asyncio.get_event_loop()
    task1=asyncio.ensure_future(coroutine1)
    task2=asyncio.ensure_future(coroutine2)
    tasks = asyncio.gather(*[task1,task2])　　　　#gather可以实现同时注册多个任务，实现并发操作。wait方法使用一致
    loop.run_until_complete(tasks)
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

![](https://images2018.cnblogs.com/blog/1309518/201806/1309518-20180624102403769-467800523.png)

并发：使用 gather 或者 wait 可以同时注册多个任务，实现并发
====================================

### gather：Return a future aggregating results from the given coroutines or futures.　　返回结果

```
task1=asyncio.ensure_future(coroutine1)
    task2=asyncio.ensure_future(coroutine2)
    tasks = asyncio.gather(*[task1,task2])
    loop.run_until_complete(tasks)
```

### wait：Returns two sets of Future: (done, pending).　　　# 返回 dones 是已经完成的任务，pending 是未完成的任务，都是集合类型

```
task1=asyncio.ensure_future(coroutine1)
    task2=asyncio.ensure_future(coroutine2)
    tasks = asyncio.wait([task1,task2])
    loop.run_until_complete(tasks)
```

```
Usage:

    done, pending = yield from asyncio.wait(fs)
```

### **wait 是接收一个列表，而后 gather 是接收一堆任务数据。**

**两者的返回值也是不同的**

协程嵌套，将多个协程封装到一个主协程中
===================

![](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif)![](https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)

```
import asyncio,aiohttp

async def fetch_async(url):
    print(url)
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            print(resp.status)
            print(await resp.text())

tasks = [fetch_async('http://www.baidu.com/'), fetch_async('http://www.cnblogs.com/ssyfj/')]

event_loop = asyncio.get_event_loop()
results = event_loop.run_until_complete(asyncio.gather(*tasks))
event_loop.close()
```

关于 aiohttp 模块的协程嵌套，嵌套更加明显 [![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

async def main():
    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]

    dones, pendings = await asyncio.wait(tasks)

    for task in dones:　　#对已完成的任务集合进行操作
        print("Task ret: ",task.result())

if __name__ == "__main__":
    begin = time.time()

    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码") [![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
5 before---func1----
3 before---func1----
4 before---func1----
Task ret:  recv num 4
Task ret:  recv num 5
Task ret:  recv num 3
5.000285863876343
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 也可以直接使用 gather 直接获取值

```
results = await asyncio.gather(*tasks)
    for result in results:
        print("Task ret: ",result)
```

### 我们也可以不在 main 中处理结果，而是返回到主调用方进行处理

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
async def main():
    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]

    return await asyncio.gather(*tasks)


if __name__ == "__main__":
    begin = time.time()

    loop = asyncio.get_event_loop()
    results = loop.run_until_complete(main())
    for result in results:
        print("Task ret: ",result)
    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 或者使用 wait 挂起

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
return await asyncio.wait(tasks)

----------------------------------------------------
    dones,pendings = loop.run_until_complete(main())
    for task in dones:
        print("Task ret: ",task.result())
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 或者使用 asyncio 中的 as_completed 方法

```
Return an iterator whose values are coroutines.　　#返回一个可迭代的协程函数值

    When waiting for the yielded coroutines you'll get the results (or
    exceptions!) of the original Futures (or coroutines), in the order
    in which and as soon as they complete.
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

async def main():
    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]

    for task in asyncio.as_completed(tasks):
        result = await task
        print("Task ret: ",result)

if __name__ == "__main__":
    begin = time.time()

    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())

    loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

协程停止
====

future 对象有几个状态：

*   Pending
*   Running
*   Done
*   Cacelled

 创建 future 的时候，task 为 pending，

事件循环调用执行的时候当然就是 running，

调用完毕自然就是 done，

如果需要停止事件循环，就需要先把 task 取消。

可以使用 asyncio.Task 获取事件循环的 task

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]


    loop = asyncio.get_event_loop()
    try:
        loop.run_until_complete(asyncio.wait(tasks))
    except KeyboardInterrupt as e:
        print(asyncio.Task.all_tasks())
        for task in asyncio.Task.all_tasks():　　#获取所有任务
            print(task.cancel())　　#单个任务取消
        loop.stop()　　　　#需要先stop循环
        loop.run_forever()　　#需要在开启事件循环
    finally:
        loop.close()　　#统一关闭
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码") [![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
5 before---func1----
3 before---func1----
4 before---func1----
{<Task pending coro=<func1() running at multhread.py:5> wait_for=<Future pending cb=[Task._wakeup()]> cb=[_wait.<loc
als>._on_completion() at C:\Users\Administrator\AppData\Local\Programs\Python\Python35\lib\asyncio\tasks.py:428]>, <
Task pending coro=<wait() running at C:\Users\Administrator\AppData\Local\Programs\Python\Python35\lib\asyncio\tasks
.py:361> wait_for=<Future pending cb=[Task._wakeup()]>>, <Task pending coro=<func1() running at multhread.py:5> wait
_for=<Future pending cb=[Task._wakeup()]> cb=[_wait.<locals>._on_completion() at C:\Users\Administrator\AppData\Loca
l\Programs\Python\Python35\lib\asyncio\tasks.py:428]>, <Task pending coro=<func1() running at multhread.py:5> wait_f
or=<Future pending cb=[Task._wakeup()]> cb=[_wait.<locals>._on_completion() at C:\Users\Administrator\AppData\Local\
Programs\Python\Python35\lib\asyncio\tasks.py:428]>}　　#未处理，刚刚挂起为pending状态
True　　#返回True，表示cancel取消成功
True
True
True
3.014172315597534
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

True 表示 cannel 成功，loop stop 之后还需要再次开启事件循环，最后在 close，不然还会抛出异常：

```
Task was destroyed but it is pending!
```

因为 cancel 后 task 的状态依旧是 pending

```
for task in asyncio.Task.all_tasks():
            print(task)
            print(task.cancel())
            print(task)
```

```
<Task pending coro=<func1() running at multhread.py:5> wait_for=<Future pending cb=[Task._wakeup()]> cb=[_wait.<loca
ls>._on_completion() at C:\Users\Administrator\AppData\Local\Programs\Python\Python35\lib\asyncio\tasks.py:428]>
True
<Task pending coro=<func1() running at multhread.py:5> wait_for=<Future cancelled> cb=[_wait.<locals>._on_completion
() at C:\Users\Administrator\AppData\Local\Programs\Python\Python35\lib\asyncio\tasks.py:428]>
```

### 或者使用协程嵌套，main 协程相当于最外层 task，处理 main 函数即可

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

async def main():
    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]

    dones,pendings=await asyncio.wait(tasks)

    for task in dones:
        print("Task ret: ",task.result())


if __name__ == "__main__":
    begin = time.time()

    loop = asyncio.get_event_loop()
    task = asyncio.ensure_future(main())
    try:
        loop.run_until_complete(task)
    except KeyboardInterrupt as e:
        print(asyncio.gather(*asyncio.Task.all_tasks()).cancel())　　#我们只是把上面的单个写成了所有任务集合取消，和协程嵌套关系不大。上面也可以这样写。不过协程嵌套可以简化代码
        loop.stop()
        loop.run_forever()
    finally:
        loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码") [![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
5 before---func1----
3 before---func1----
4 before---func1----
<class 'asyncio.tasks._GatheringFuture'>
True
3.008172035217285
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

### 感觉有点多余...

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]


    loop = asyncio.get_event_loop()
    try:
        loop.run_until_complete(asyncio.wait(tasks))
    except KeyboardInterrupt as e:
        print(asyncio.gather(*tasks).cancel())　　
        loop.stop()
        loop.run_forever()
    finally:
        loop.close()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
5 before---func1----
3 before---func1----
4 before---func1----
True
3.008171796798706
```

上面讨论的都是在同一线程下的事件循环，下面来谈谈不同线程的事件循环
=================================

在当前线程中创建一个事件循环（不启用，单纯获取标识），开启一个新的线程，在新的线程中启动事件循环。在当前线程依据事件循环标识，可以向事件中添加协程对象。当前线程不会由于事件循环而阻塞了。

上面在一个线程中执行的事件循环，只有我们主动关闭事件 close，事件循环才会结束，会阻塞。

同一线程：
-----

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    tasks = [
        asyncio.ensure_future(coroutine1),
        asyncio.ensure_future(coroutine2),
        asyncio.ensure_future(coroutine3),
    ]


    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait(tasks))
    loop.run_forever()
    end = time.time()
    print(end-begin)
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

![](https://images2018.cnblogs.com/blog/1309518/201806/1309518-20180624160545351-549432134.png)

不同线程事件循环（不涉及协程）：
----------------

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time,threading

def func1(num):
    print(num,'before---func1----')
    time.sleep(num)
    return "recv num %s"%num

def start_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()

if __name__ == "__main__":
    begin = time.time()

    new_loop = asyncio.new_event_loop() #在当前线程下创建时间循环，（未启用）
    t = threading.Thread(target=start_loop,args=(new_loop,))    #开启新的线程去启动事件循环
    t.start()

    new_loop.call_soon_threadsafe(func1,3)
    new_loop.call_soon_threadsafe(func1,2)
    new_loop.call_soon_threadsafe(func1,6)

    end = time.time()
    print(end-begin)    #当前线程未阻塞，耗时0.02800154685974121
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
3 before---func1----
0.02800154685974121
2 before---func1----
6 before---func1----
```

新线程协程：
------

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
import asyncio,time,threading

async def func1(num):
    print(num,'before---func1----')
    await asyncio.sleep(num)
    return "recv num %s"%num

def start_loop(loop):
    asyncio.set_event_loop(loop)
    loop.run_forever()

if __name__ == "__main__":
    begin = time.time()

    coroutine1 = func1(5)
    coroutine2 = func1(3)
    coroutine3 = func1(4)

    new_loop = asyncio.new_event_loop() #在当前线程下创建时间循环，（未启用）
    t = threading.Thread(target=start_loop,args=(new_loop,))    #开启新的线程去启动事件循环
    t.start()

    asyncio.run_coroutine_threadsafe(coroutine1,new_loop)　　#传参必须是协程对象
    asyncio.run_coroutine_threadsafe(coroutine2,new_loop)
    asyncio.run_coroutine_threadsafe(coroutine3,new_loop)

    end = time.time()
    print(end-begin)    #当前线程未阻塞，耗时0.010000467300415039
```

[![](http://common.cnblogs.com/images/copycode.gif)](javascript:void(0); "复制代码")

```
5 before---func1----
3 before---func1----
4 before---func1----
0.010000467300415039
```

主线程通过 run_coroutine_threadsafe 新注册协程对象。这样就能在子线程中进行事件循环的并发操作，同时主线程又不会被 block。

推文：[_Python 黑魔法 --- 异步 IO（ asyncio） 协程_](https://www.jianshu.com/p/b5e347b3a17c)

作者：[山上有风景](https://www.cnblogs.com/ssyfj/)  
欢迎任何形式的转载，但请务必注明出处。  
限于本人水平，如果文章和代码有表述不当之处，还请不吝赐教。