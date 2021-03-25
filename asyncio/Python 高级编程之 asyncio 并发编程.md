> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/137698989)

### **目录**

[1. asyncio 简介](https://zhuanlan.zhihu.com/p/137698989/h1asyncio)

[1.1 协程与 asyncio](https://zhuanlan.zhihu.com/p/137698989/h11asyncio)

[2 asyncio 的使用](https://zhuanlan.zhihu.com/p/137698989/h2asyncio)

[2.1 demo](https://zhuanlan.zhihu.com/p/137698989/h21demo)

[2.2 获取 asyncio 的返回值](https://zhuanlan.zhihu.com/p/137698989/h22asyncio)

[2.3 call back](https://zhuanlan.zhihu.com/p/137698989/h23callback)

[2.4 wait 和 gather](https://zhuanlan.zhihu.com/p/137698989/h24waitgather)

[2.4.1 wait](https://zhuanlan.zhihu.com/p/137698989/h241wait)

[2.4.2 gather](https://zhuanlan.zhihu.com/p/137698989/h242gather)

[2.4.3 wait 与 gather 中的区别](https://zhuanlan.zhihu.com/p/137698989/h243waitgather)

[2.5 run_until_complete 实现的原理](https://zhuanlan.zhihu.com/p/137698989/h25run_until_complete)

[2.6 取消任务](https://zhuanlan.zhihu.com/p/137698989/h26)

[3 协程中嵌套协程，子协程](https://zhuanlan.zhihu.com/p/137698989/h3)

[4. asyncio 中的几个函数](https://zhuanlan.zhihu.com/p/137698989/h4asyncio)

[4.1 call soon](https://zhuanlan.zhihu.com/p/137698989/h41callsoon)

[4.2 call later](https://zhuanlan.zhihu.com/p/137698989/h42calllater)

[4.3 call at](https://zhuanlan.zhihu.com/p/137698989/h43callat)

[4.4 call_soon_threadsafe](https://zhuanlan.zhihu.com/p/137698989/h44call_soon_threadsafe)

[5.1 使用 asyncio 模拟 HTTP 请求](https://zhuanlan.zhihu.com/p/137698989/h51asynciohttp)

[6. asuncio 中的 future 和 task](https://zhuanlan.zhihu.com/p/137698989/h6asunciofuturetask)

[7. asyncio 同步和通信](https://zhuanlan.zhihu.com/p/137698989/h7asyncio)

[7.1 asyncio 模块中的 Lock 源码分析](https://zhuanlan.zhihu.com/p/137698989/h71asynciolock)

[7.2 asyncio 中的 Queue](https://zhuanlan.zhihu.com/p/137698989/h72asyncioqueue)

[8. 使用 aiohttp 实现高并发爬虫](https://zhuanlan.zhihu.com/p/137698989/h8aiohttp)

### **1. asyncio 简介**

![](https://pic1.zhimg.com/v2-2db160adfe318e82870380ba8873b288_r.jpg)

### **1.1 协程与 asyncio**

> 协程编写的三个组成部分：1. 事件循环， 2. 回调 (驱动生成器)， 3. epoll（IO 多路复用）  
> **asyncio 是 python 用于解决异步 IO 编程的一整套解决方案。**基于 asyncio 的框架有: tornado、gevent、twisted（scrapy， django channels）。  
> django channels 用于 HTTP 2.0 开发；torando (实现 web 服务器)， 如果使用 django ，通常使用 django + flask (uwsgi, gunicorn+nginx) 的搭配方式；tornado 可以直接部署， 通常使用 nginx + tornado 的搭配方式。  
> asyncio 不能和 requests 库结合使用  
> [http://www.imooc.com/article/24759](https://link.zhihu.com/?target=http%3A//www.imooc.com/article/24759)  

### **2 asyncio 的使用**

### **2.1 demo**

```
import asyncio
import time


async def get_html(url):
    print("start get url")
    # 这里不能使用 time.sleep(2) 模拟 HTTP 请求，因为这是一个同步阻塞的方式
    # 这个地方必须加 await
    await asyncio.sleep(2)
    print("end get url")

if __name__ == "__main__":
    start_time = time.time()
    # 相当于之前例子中的 loop 函数
    loop = asyncio.get_event_loop()
    tasks = [get_html("http://www.imooc.com") for i in range(10)]
    # loop.run_until_complete(get_html("http://www.imooc.com"))
    loop.run_until_complete(asyncio.wait(tasks))
    print(time.time()-start_time)


```

### **2.2 获取 asyncio 的返回值**

```
# 获取协程的返回值
import asyncio
import time
from functools import partial
async def get_html(url):
    print("start get url")
    await asyncio.sleep(2)
    return "bobby"


if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    # 方式一：
    # get_future = asyncio.ensure_future(get_html("http://www.imooc.com"))
    # loop.run_until_complete(get_future)
    # print(get_future.result())
    # 方式一中会产生一个疑惑？即：
    # 使用 asyncio 模块，传入的参数中没有传入 Event loop，
    # 那么上面的 get_html("http://www.imooc.com") 事件是如何被注入到 loop 中的呢？
    # 答案不是在 loop.run_until_complete(get_future) 中完成的，而是在 ensure_future 中完成的，
    # 在这个方法中获取 Event loop，这个 loop 和我们自己创建的 loop 是同一个 loop。具体可以参考 ensure_future 源码


    # 方式二：
    # task 是 Future 的一个子类
    task = loop.create_task(get_html("http://www.imooc.com"))
    loop.run_until_complete(task)
    print(task.result())


```

### **2.3 call back**

> 使用 call back 可以完成某些回调需求，比如完成某个任务，发送一封邮件；或者某个抓取线程耗时过长，通知你一下。  

```
import asyncio
import time
from functools import partial
async def get_html(url):
    print("start get url")
    await asyncio.sleep(2)
    return "bobby"

# 注意，这里必须有一个 future 参数，这个 Future 就是下面的 task
def callback(future):
    print("send email to bobby")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    task = loop.create_task(get_html("http://www.imooc.com"))
    task.add_done_callback(callback)
    loop.run_until_complete(task)
    print(task.result())


```

> 当 call back 有参数的时候，使用 from functools import partial 包装 callback。  

```
import asyncio
import time
from functools import partial
async def get_html(url):
    print("start get url")
    await asyncio.sleep(2)
    return "bobby"

# 注意，这里必须有一个 Future 参数
def callback(url, future):
    print(url)
    print("send email to bobby")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    task = loop.create_task(get_html("http://www.imooc.com"))
    task.add_done_callback(partial(callback, "http://www.imooc.com"))
    loop.run_until_complete(task)
    print(task.result())


```

### **2.4 wait 和 gather**

### **2.4.1 wait**

```
from functools import partial
import asyncio
import time


async def get_html(url):
    print("start get url")
    # 这里不能使用 time.sleep(2) 模拟 HTTP 请求，因为这是一个同步阻塞的方式
    # 这个地方必须加 await
    await asyncio.sleep(2)
    print("end get url")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    tasks = [get_html("http://www.imooc.com") for i in range(10)]
    # wait 一次性完成多个任务的时候使用
    loop.run_until_complete(asyncio.wait(tasks))
    print(time.time()-start_time)


```

### **2.4.2 gather**

> gather 是比 wait 更加高一层的功能抽象。  

```
from functools import partial
import asyncio
import time


async def get_html(url):
    print("start get url")
    # 这里不能使用 time.sleep(2) 模拟 HTTP 请求，因为这是一个同步阻塞的方式
    # 这个地方必须加 await
    await asyncio.sleep(2)
    print("end get url")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    tasks = [get_html("http://www.imooc.com") for i in range(10)]
    # 这儿使用 gather 的时候，需要加一个星号，会将列表中的元素传进去
    loop.run_until_complete(asyncio.gather(*tasks))
    print(time.time()-start_time)


```

### **2.4.3 wait 与 gather 中的区别**

> gather 比 wait 更加高层。gather 可以将任务分组，一般优先使用 gather。在某些定制化任务需求的时候，会使用 wait。  

```
# 例子一
from functools import partial
import asyncio
import time


async def get_html(url):
    print("start get url")
    await asyncio.sleep(2)
    print("end get url")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    tasks = [get_html("http://www.imooc.com") for i in range(10)]

    group1 = [get_html("http://projectsedu.com") for i in range(2)]
    group2 = [get_html("http://www.imooc.com") for i in range(2)]

    loop.run_until_complete(asyncio.gather(*group1, *group2))
    print(time.time() - start_time)

# 例子二
from functools import partial
import asyncio
import time


async def get_html(url):
    print("start get url")
    await asyncio.sleep(2)
    print("end get url")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    tasks = [get_html("http://www.imooc.com") for i in range(10)]

    group1 = [get_html("http://projectsedu.com") for i in range(2)]
    group2 = [get_html("http://www.imooc.com") for i in range(2)]
    group1 = asyncio.gather(*group1)
    group2 = asyncio.gather(*group2)

    loop.run_until_complete(asyncio.gather(group1, group2))
    print(time.time() - start_time)

# 例子三：成批的取消任务
from functools import partial
import asyncio
import time


async def get_html(url):
    print("start get url")
    await asyncio.sleep(2)
    print("end get url")

if __name__ == "__main__":
    start_time = time.time()
    loop = asyncio.get_event_loop()
    tasks = [get_html("http://www.imooc.com") for i in range(10)]

    group1 = [get_html("http://projectsedu.com") for i in range(2)]
    group2 = [get_html("http://www.imooc.com") for i in range(2)]
    group1 = asyncio.gather(*group1)
    group2 = asyncio.gather(*group2)
    group2.cancel()
    loop.run_until_complete(asyncio.gather(group1, group2))
    print(time.time() - start_time)


```

### **2.5 run_until_complete 实现的原理**

> loop.run_forever() 会让线程一直运行。loop.run_until_complete() 借助了 run_forever 方法。  
> 在 run_until_complete 的实现中，调用了 future.add_done_callback(_run_until_complete_cb)。  

```
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

> 在 _run_until_complete_cb 方法中，在运行指定的任务后，停止掉。  

```
def _run_until_complete_cb(fut):
    exc = fut._exception
    if (isinstance(exc, BaseException)
    and not isinstance(exc, Exception)):
        # Issue #22429: run_forever() already finished, no need to
        # stop it.
        return
    fut._loop.stop()


```

### **2.6 取消任务**

> 这个需求非常常用。  

```
import asyncio
import time

async def get_html(sleep_times):
    print("waiting")
    await asyncio.sleep(sleep_times)
    print("done after {}s".format(sleep_times))


if __name__ == "__main__":
    task1 = get_html(2)
    task2 = get_html(3)
    task3 = get_html(3)

    tasks = [task1, task2, task3]

    loop = asyncio.get_event_loop()

    try:
        loop.run_until_complete(asyncio.wait(tasks))
    except KeyboardInterrupt as e:
        all_tasks = asyncio.Task.all_tasks()
        for task in all_tasks:
            print("cancel task")
            print(task.cancel())
        loop.stop()
        # stop 调用之后，需要调用 run_forever，不然会报错
        loop.run_forever()
    finally:
        loop.close()


```

### **3 协程中嵌套协程，子协程**

> [https://docs.python.org/3.6/library/asyncio-task.html](https://link.zhihu.com/?target=https%3A//docs.python.org/3.6/library/asyncio-task.html)  

```
# 这个例子，参考上面链接的序列图理解。
import asyncio

async def compute(x, y):
    print("Compute %s + %s ..." % (x, y))
    await asyncio.sleep(1.0)
    return x + y

async def print_sum(x, y):
    result = await compute(x, y)
    print("%s + %s = %s" % (x, y, result))

loop = asyncio.get_event_loop()
loop.run_until_complete(print_sum(1, 2))
loop.close()


```

### **4. asyncio 中的几个函数**

### **4.1 call soon**

> call soon 函数 并不是表示下一行代码就执行，而是在队列中等到下一个循环的时候就执行。  
> 这儿定义的是函数，不是协程。因为在很多时候，我们希望在 loop 当中，也就是循环体系当中，插入一个函数，可以让函数立即执行  

```
import asyncio

# 这儿定义的是函数，不是协程
# 因为在很多时候，我们希望在 loop 当中，也就是循环体系当中，插入一个函数，可以让函数立即执行
def callback(sleep_times):
    print("success time {}".format(sleep_times))

def stoploop(loop):
    loop.stop()


#call_soon
if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    loop.call_soon(callback, 2)
    # 通过定义 stoploop 函数，停止 run_forever 循环
    # 注意这里的参数，是传入的 loop
    loop.call_soon(stoploop, loop)
    # 启动，这儿不是使用 run_until_complete，因为 callback 不是协程。
    loop.run_forever()


```

### **4.2 call later**

> call_later 中执行顺序并不是添加顺序，会根据延迟调用时间确定一个先后顺序。  

```
import asyncio

def callback(sleep_times):
    print("success time {}".format(sleep_times))

def stoploop(loop):
    loop.stop()


#call_later, call_at
if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    # 执行顺序并不是添加顺序，会根据延迟调用时间确定一个先后顺序
    loop.call_later(2, callback, 2)
    loop.call_later(1, callback, 1)
    loop.call_later(3, callback, 3)

    # 启动，这儿不是使用 run_until_complete，因为 callback 不是协程。
    loop.run_forever()


```

> 当 call_soon 和 call_later 同时出现的时候，会先执行 call_soon。  

```
import asyncio

def callback(sleep_times):
    print("success time {}".format(sleep_times))

def stoploop(loop):
    loop.stop()


#call_later
if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    # 执行顺序并不是添加顺序，会根据延迟调用时间确定一个先后顺序
    loop.call_later(2, callback, 2)
    loop.call_later(1, callback, 1)
    loop.call_later(3, callback, 3)
    loop.call_soon(callback, 4)
    # 这儿不能加入call_soon stoploop 函数，call soon 会立即执行，call_later 就不会再执行了。
    # loop.call_soon(stoploop, loop)
    # 启动，这儿不是使用 run_until_complete，因为 callback 不是协程。
    loop.run_forever()


```

### **4.3 call at**

> call at 可以在指定的时间运行函数。但是，这里的时间是 loop 里面的时间，不是 time 模块里面的时间。  

```
import asyncio

def callback(sleep_times, loop):
    print("success time {}".format(loop.time()))
def stoploop(loop):
    loop.stop()


#call_at
if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    now = loop.time()
    loop.call_at(now+2, callback, 2, loop)
    loop.call_at(now+1, callback, 1, loop)
    loop.call_at(now+3, callback, 3, loop)
    # call_soon 会被先执行
    loop.call_soon(callback, 4, loop)
    # 通过定义 stoploop 函数，停止函数
    loop.call_soon(stoploop, loop)
    # 启动，这儿不是使用 run_until_complete，因为 callback 不是协程。
    loop.run_forever()


```

### **4.4 call_soon_threadsafe**

> 这个方法用于解决对互斥资源访问的问题。如果有对互斥资源访问，需要使用这个方法。使用方法和上面的 call_soon 相同。  
> **5. 线程池与 asyncio 结合起来**  
> asyncio 是异步 IO 的解决方案。异步 IO 包括了多线程、协程、进程。  
> 协程里面不能加入阻塞 IO，但是某些库只能提供阻塞 IO 接口，那么这个时候就需要将协程放到线程中。  

```
# 使用多线程：在协程中集成阻塞io
import asyncio
from concurrent.futures import ThreadPoolExecutor
import socket
from urllib.parse import urlparse


def get_url(url):
    # 通过socket请求html
    url = urlparse(url)
    host = url.netloc
    path = url.path
    if path == "":
        path = "/"

    # 建立socket连接
    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # client.setblocking(False)
    client.connect((host, 80))  # 阻塞不会消耗cpu

    # 不停的询问连接是否建立好， 需要while循环不停的去检查状态
    # 做计算任务或者再次发起其他的连接请求

    client.send(
        "GET {} HTTP/1.1\r\nHost:{}\r\nConnection:close\r\n\r\n".format(path, host).encode("utf8"))

    data = b""
    while True:
        d = client.recv(1024)
        if d:
            data += d
        else:
            break

    data = data.decode("utf8")
    html_data = data.split("\r\n\r\n")[1]
    print(html_data)
    client.close()


if __name__ == "__main__":
    import time
    start_time = time.time()
    loop = asyncio.get_event_loop()
    executor = ThreadPoolExecutor(3)
    tasks = []
    for url in range(20):
        url = "http://shop.projectsedu.com/goods/{}/".format(url)
        # 返回 task
        task = loop.run_in_executor(executor, get_url, url)
        tasks.append(task)
    loop.run_until_complete(asyncio.wait(tasks))
    print("last time:{}".format(time.time()-start_time))


```

### **5.1 使用 asyncio 模拟 HTTP 请求**

> asyncio 目前为止没有提供 HTTP 协议接口，只提供了 UDP 和 TCP 接口，也许以后会提供。可以使用 aiohttp 完成这个功能。现在，我们使用原生的底层接口实现 HTTP 请求。  

```
# asyncio 没有提供http协议的接口
import asyncio
import socket
from urllib.parse import urlparse


# 改成使用协程
async def get_url(url):
    #通过socket请求html
    url = urlparse(url)
    host = url.netloc
    path = url.path
    if path == "":
        path = "/"

    #建立socket连接
    # open_connection 源码中调用了 yield from loop.create_connection 。这里实现的功能完成了 register 和 unregister
    reader, writer = await asyncio.open_connection(host,80)
    # write 方法完成 register 和 unregister
    writer.write("GET {} HTTP/1.1\r\nHost:{}\r\nConnection:close\r\n\r\n".format(path, host).encode("utf8"))
    all_lines = []
    # async for 语法，可以将读数据的方式异步化。
    async for raw_line in reader:
        data = raw_line.decode("utf8")
        all_lines.append(data)
    html = "\n".join(all_lines)
    return html

async def main():
    # 使用 tasks 放置 future 对象
    tasks = []
    for url in range(20):
        url = "http://shop.projectsedu.com/goods/{}/".format(url)
        # 使用 asyncio.ensure_future 包装协程，变成 Future 对象，从而获得协程的结果
        tasks.append(asyncio.ensure_future(get_url(url)))
    for task in asyncio.as_completed(tasks):
        # 这儿需要使用 await，因为返回的是一个协程，需要使用 await 关键字修饰
        result = await task
        print(result)

if __name__ == "__main__":
    import time
    start_time = time.time()
    loop = asyncio.get_event_loop()
    # 这儿重新定义了一个函数 main，从而实现 as_completed，即完成一个 task，打印一个 task
    # 在 main 中使用了 asyncio.as_completed，这和线程池中的 as_completed 效果一样
    loop.run_until_complete(main())
    print('last time:{}'.format(time.time()-start_time))


```

### **6. asuncio 中的 future 和 task**

> future 是一个结果容器，将运行结果放到 Future 中。task 是协程和 Future 的桥梁。  
> 我们之前的章节说过，线程是由操作系统调用的，协程是由程序员自己调用的，在定义一个协程之后， 在驱动这个协程之前，需要调用 next 或者 send(None)，是协程生效。task 的 init 方法中有 self._loop.call_soon(self._step)，在 step 方法中有 result = coro.send(None) 激活协程，通过这行语句解决协程启动的问题。并且，在 step 方法中，如果抛出 StopIteration，会执行 self.set_result(exc.value)。exc.value 表示异常中的值，正如之前章节说的，这个值也是协程中的 return 值。通过这个我们可以看出，task 不管启动了协程，还将最后 StopIteration 中的值做了处理。  

```
def _step(self, exc=None):
    assert not self.done(), \
        '_step(): already done: {!r}, {!r}'.format(self, exc)
    if self._must_cancel:
        if not isinstance(exc, futures.CancelledError):
            exc = futures.CancelledError()
        self._must_cancel = False
    coro = self._coro
    self._fut_waiter = None

    self.__class__._current_tasks[self._loop] = self
    # Call either coro.throw(exc) or coro.send(None).
    try:
        if exc is None:
            # We use the `send` method directly, because coroutines
            # don't have `__iter__` and `__next__` methods.
            result = coro.send(None)
        else:
            result = coro.throw(exc)
    except StopIteration as exc:
        if self._must_cancel:
            # Task is cancelled right before coro stops.
            self._must_cancel = False
            self.set_exception(futures.CancelledError())
        else:
            self.set_result(exc.value)
    except futures.CancelledError:
        super().cancel()  # I.e., Future.cancel(self).
    except Exception as exc:
        self.set_exception(exc)
    except BaseException as exc:
        self.set_exception(exc)
        raise
    else:
        blocking = getattr(result, '_asyncio_future_blocking', None)
        if blocking is not None:
            # Yielded Future must come from Future.__iter__().
            if result._loop is not self._loop:
                self._loop.call_soon(
                    self._step,
                    RuntimeError(
                        'Task {!r} got Future {!r} attached to a '
                        'different loop'.format(self, result)))
            elif blocking:
                if result is self:
                    self._loop.call_soon(
                        self._step,
                        RuntimeError(
                            'Task cannot await on itself: {!r}'.format(
                                self)))
                else:
                    result._asyncio_future_blocking = False
                    result.add_done_callback(self._wakeup)
                    self._fut_waiter = result
                    if self._must_cancel:
                        if self._fut_waiter.cancel():
                            self._must_cancel = False
            else:
                self._loop.call_soon(
                    self._step,
                    RuntimeError(
                        'yield was used instead of yield from '
                        'in task {!r} with {!r}'.format(self, result)))
        elif result is None:
            # Bare yield relinquishes control for one event loop iteration.
            self._loop.call_soon(self._step)
        elif inspect.isgenerator(result):
            # Yielding a generator is just wrong.
            self._loop.call_soon(
                self._step,
                RuntimeError(
                    'yield was used instead of yield from for '
                    'generator in task {!r} with {}'.format(
                        self, result)))
        else:
            # Yielding something else is an error.
            self._loop.call_soon(
                self._step,
                RuntimeError(
                    'Task got bad yield: {!r}'.format(result)))
    finally:
        self.__class__._current_tasks.pop(self._loop)
        self = None  # Needed to break cycles when an exception occurs.


```

> 从系统设计的角度看，asyncio 模块尽量保持与线程池中的接口一致，为了达到这个目的，就设计了 task，用于解决线程和协程之间不一样的地方。  

### **7. asyncio 同步和通信**

> asyncio 实际上是基于单线程做的，而且，asyncio 是不需要锁的。那我们为什么还会谈到 asyncio 的同步呢？  
> 首先，我们来看一下，使用 asyncio 做单线程是不需要锁的。  
> 下面的例子，不管执行多少次都是 0，说明：凡是不涉及到 IO，或者不涉及到 await，都会执行完了，再执行另一段代码，这是为什么结果始终为 0 的本质。例子中，add 中的 for i in range(1000000) 运行完了，才会运行 desc 中的 for 循环。  

```
total = 0


async def add():
    # 1. dosomething1
    # 2. io操作
    # 1. dosomething3
    global total
    for i in range(1000000):
        total += 1


async def desc():
    global total
    for i in range(1000000):
        total -= 1


if __name__ == '__main__':
    import asyncio
    tasks = [add(), desc()]
    loop = asyncio.get_event_loop()
    loop.run_until_complete(asyncio.wait(tasks))
    print(total)


```

> 上面的例子已经说明了不需要设置锁。但是，在某种情况下，还是需要设置锁的，具体看下面的例子：  
> 在 parse_stuff 和 use_stuff 中都会调用 get_stuff。有可能出现：缓存中没有某个 URL，两个协程 parse_stuff 和 use_stuff 运行的时候，可能都会发起 get_stuff 中的 await aiohttp.request。这个时候，就会发起两次请求，并且，这两个请求都是**非常耗时的**。并且，某些网站后台会进行反爬虫处理。如果有锁的话，就可以避免重复请求的情况。  

```
import asyncio
from asyncio import Lock, Queue

lock = Lock()
cache = {}

# 获取 URL 的返回值
async def get_stuff(url):
    if url in cache:
        return cache[url]
    stuff = await aiohttp.request('GET',url)
    cache[url] = stuff
    return stuff

async def parse_stuff():
    stuff = await get_stuff()
    # do some parsing

async def use_stuff():
    stuff = await get_stuff()
    # use stuff to do something interesting

tasks = [parse_stuff(), use_stuff()]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))


```

> 使用锁之后，代码如下：  

```
import asyncio
# 这里使用的是 asyncio 中的 Lock
# 这个 Lock 调用系统的锁，是程序员级别的锁，因为协程本身不涉及，正如我们之前说过的，协程是一个单线程。
# 通过自己定义的 self._locked 完成互斥执行一段代码
# 我们之前说过调用 acquire 这些方法的时候，一定不能是阻塞的。在 Lock 的 acquire 方法中，首先创建了 Future，然后 yield from fut
from asyncio import Lock, Queue

lock = Lock()
cache = {}

# 获取 URL 的返回值
async def get_stuff(url):
    # 第一个知识点：
    # 不使用 with 语句的时候，可以使用 await lock.acquire() 和 lock.release()。
    # 除了使用 with，还可以使用 async with 方式。
    # with await lock:
    # 说到 Lock，Condition 的 asyncio 的用法也是一样的，用途和之前也是一样。

    # 第二个知识点：
    # async with 调用的不是 __enter__ 和 __exit__，而是 __await__ 和 __aenter__。
    async with lock:
        if url in cache:
            return cache[url]
        stuff = await aiohttp.request('GET',url)
        cache[url] = stuff
        return stuff

async def parse_stuff():
    stuff = await get_stuff()
    # do some parsing

async def use_stuff():
    stuff = await get_stuff()
    # use stuff to do something interesting

tasks = [parse_stuff(), use_stuff()]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))


```

### **7.1 asyncio 模块中的 Lock 源码分析**

> 这个 Lock 调用系统的锁，是程序员级别的锁，因为协程本身不涉及，正如我们之前说过的，协程是一个单线程。  

```
# asyncio 模块中的 Lock 通过自己定义的 self._locked 完成互斥执行一段代码
# 我们之前说过调用 acquire 这些方法的时候，一定不能是阻塞的。
# 在 Lock 的 acquire 方法中，首先创建了 Future，然后将 future 对象放到一个双端队列中，然后执行 yield from fut，之后这个协程会暂停。不理解暂停的需要补充 yield from 的知识。
# 那么问题来了，暂停之后，谁来完成任务的驱动呢？答案是在 release 中。

@coroutine
def acquire(self):
    """Acquire a lock.

    This method blocks until the lock is unlocked, then sets it to
    locked and returns True.
    """
    if not self._locked and all(w.cancelled() for w in self._waiters):
        self._locked = True
        return True

    fut = self._loop.create_future()
    self._waiters.append(fut)
    try:
        yield from fut
        self._locked = True
        return True
    except futures.CancelledError:
        if not self._locked:
            self._wake_up_first()
        raise
    finally:
        self._waiters.remove(fut)

# 执行 acquire 之后，另一个协程在执行 release 方法的时候，会执行 _wake_up_first()。
# 在 _wake_up_first() 会从队列中取出一个 future，也就是从 self._waiters 中取出一个 future，判断这个 future 有没有 done，没有 done 的话，就会直接 set_result。我们之前说过 set_result 会驱动向下执行。

def release(self):
    """Release a lock.

    When the lock is locked, reset it to unlocked, and return.
    If any other coroutines are blocked waiting for the lock to become
    unlocked, allow exactly one of them to proceed.

    When invoked on an unlocked lock, a RuntimeError is raised.

    There is no return value.
    """
    if self._locked:
        self._locked = False
        self._wake_up_first()
    else:
        raise RuntimeError('Lock is not acquired.')

def _wake_up_first(self):
    """Wake up the first waiter who isn't cancelled."""
    for fut in self._waiters:
        if not fut.done():
            fut.set_result(True)
            break


```

### **7.2 asyncio 中的 Queue**

> 这个 Queue 和多线程中的 Queue 接口一样，**使用 get 和 put 函数的时候，需要在前面加 await**。  

```
@coroutine
def put(self, item):
    """Put an item into the queue.

    Put an item into the queue. If the queue is full, wait until a free
    slot is available before adding item.

    This method is a coroutine.
    """
    # 判断消息队列是否已满
    # 如果已满，则进入循环
    while self.full():
        putter = self._loop.create_future()
        # 将 future 放到队列中
        self._putters.append(putter)
        try:
            # 队列已满，执行 yield from
            # 那么暂停了，谁来驱动后面的代码？答案是：有数据取了不为空的 future，看 get 函数。
            yield from putter
        except:
            putter.cancel()  # Just in case putter is not done yet.
            if not self.full() and not putter.cancelled():
                # We were woken up by get_nowait(), but can't take
                # the call.  Wake up the next in line.
                self._wakeup_next(self._putters)
            raise
    return self.put_nowait(item)

@coroutine
def get(self):
    """Remove and return an item from the queue.

    If queue is empty, wait until an item is available.

    This method is a coroutine.
    """
    while self.empty():
        getter = self._loop.create_future()
        self._getters.append(getter)
        try:
            yield from getter
        except:
            getter.cancel()  # Just in case getter is not done yet.

            try:
                self._getters.remove(getter)
            except ValueError:
                pass

            if not self.empty() and not getter.cancelled():
                # We were woken up by put_nowait(), but can't take
                # the call.  Wake up the next in line.
                self._wakeup_next(self._getters)
            raise
    # 调用 get_nowait
    return self.get_nowait()

def get_nowait(self):
    """Remove and return an item from the queue.

    Return an item if one is immediately available, else raise QueueEmpty.
    """
    if self.empty():
        raise QueueEmpty
    item = self._get()
    # 这里的 _wakeup_next 和 Lock 中的类似
    # _putters 是一个队列
    self._wakeup_next(self._putters)
    return item

# 这里的 waiters 就是 _putters，里面放的是 future 对象
def _wakeup_next(self, waiters):
    # Wake up the next waiter (if any) that isn't cancelled.
    while waiters:
        waiter = waiters.popleft()
        if not waiter.done():
            # set_result 会驱动协程的运行，也就是 waiter，而 waiter 就是 putters，putters 也就是前面 def put(self, item) 中的 putter，也就驱动 yield from putter，之后代码会跑到 return self.put_nowait(item)。在 put_nowait 中，将 item 放到 _put 中，之后再驱动 self._wakeup_next(self._getters)。
            waiter.set_result(None)
            break


```

> 我们自己生成一个全局变量 queue 也能达到消息通信的目的，因为协程是一个单线程。但是 asyncio 中的 Queue 实现了 maxsize，当我们想限制流量的时候，这个时候就发挥了作用。如果不需要限流的功能，可以不需要使用 asyncio 中的 Queue。  

### **8. 使用 aiohttp 实现高并发爬虫**

```
# asyncio爬虫.去重.入库
import asyncio
import re

import aiohttp
import aiomysql
from pyquery import PyQuery

start_url = "http://www.jobbole.com/"
# 可是使用 list 做通信，也可以使用 asyncio 中的 Queue 做通信都可以
waitting_urls = []
# 这里使用的 set 做去重。真实的场景不可能使用 set 做去重过滤器的，比如上亿条数据，需要使用布隆过滤器
seen_urls = set()
stopping = False
#限制并发数量
sem = asyncio.Semaphore(3)

async def fetch(url, session):
    async with sem:
        try:
            async with session.get(url) as resp:
                print("url status: {}".format(resp.status))
                if resp.status in [200,201]:
                    data = await resp.text()
                    return data
        except Exception as e:
            print(e)

def extract_urls(html):
    urls = []
    pq = PyQuery(html)
    for link in pq.items("a"):
        url = link.attr("href")
        if url and url.startswith("http") and url not in seen_urls:
            urls.append(url)
            waitting_urls.append(url)
    return urls

async def init_urls(url, session):
    html = await fetch(url, session)
    seen_urls.add(url)
    extract_urls(html)

async def article_handler(url, session, pool):
    # 获取文章详情并解析入库
    html = await fetch(url, session)
    seen_urls.add(url)
    extract_urls(html)
    pq = PyQuery(html)
    title = pq("title").text()
    async with pool.acquire() as conn:
        async with conn.cursor() as cur:
            await cur.execute("SELECT 42;")
            insert_sql = "insert into article_test(title) values('{}')".format(title)
            await cur.execute(insert_sql)

async def consumer(pool):
    async with aiohttp.ClientSession() as session:
        while not stopping:
            if len(waitting_urls) == 0:
                await asyncio.sleep(0.5)
                continue
            url = waitting_urls.pop()
            print("start get url: {}".format(url))
            if re.match("http://.*?jobbole.com/\d+/", url):
                if url not in seen_urls:
                    asyncio.ensure_future(article_handler(url, session, pool))
                    await asyncio.sleep(30)
            else:
                if url not in seen_urls:
                    asyncio.ensure_future(init_urls(url, session))


async def main(loop):
    #等待连接mysql
    # charset='utf8' 如果不设置，在插入中文的时候，不会报错，但是库中没有数据
    # autocommit=True 需要设置，否则库中没有数据
    pool = await aiomysql.create_pool(host="127.0.0.1", port=3306,
                                      user='root',password='jinhua2018',
                                      db='mysql',loop=loop,
                                      charset='utf8',autocommit=True)

    async with aiohttp.ClientSession() as session:
        html = await fetch(start_url, session)
        seen_urls.add(start_url)
        extract_urls(html)
    asyncio.ensure_future(consumer(pool))


if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    asyncio.ensure_future(main(loop))
    loop.run_forever()

```