> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.dongwm.com](https://www.dongwm.com/post/78/)

之前我们使用多线程 (threading) 和多进程 (multiprocessing) 完成常规的需求，在启动的时候 start、jon 等步骤不能省，复杂的需要还要用 1-2 个队列。随着需求越来越复杂，如果没有良好的设计和抽象这部分的功能层次，代码量越多调试的难度就越大。有没有什么好的方法把这些步骤抽象一下呢，让我们不关注这些细节，轻装上阵呢？

答案是：**有的**。

从 Python3.2 开始一个叫做 concurrent.futures 被纳入了标准库，而在 Python2 它属于第三方的 futures 库，需要手动安装：

```
❯ pip install futures


```

这个模块中有 2 个类：ThreadPoolExecutor 和 ProcessPoolExecutor，也就是对 threading 和 multiprocessing 的进行了高级别的抽象， 暴露出统一的接口，帮助开发者非常方便的实现异步调用：

```
import time
from concurrent.futures import ProcessPoolExecutor, as_completed

NUMBERS = range(25, 38)


def fib(n):
    if n<= 2:
        return 1
    return fib(n-1) + fib(n-2)


start = time.time()

with ProcessPoolExecutor(max_workers=3) as executor:
    for num, result in zip(NUMBERS, executor.map(fib, NUMBERS)):
        print 'fib({}) = {}'.format(num, result)

print 'COST: {}'.format(time.time() - start)


```

感受下是不是很轻便呢？看一下花费的时间：

```
❯ python fib_executor.py
fib(25) = 75025
fib(26) = 121393
fib(27) = 196418
fib(28) = 317811
fib(29) = 514229
fib(30) = 832040
fib(31) = 1346269
fib(32) = 2178309
fib(33) = 3524578
fib(34) = 5702887
fib(35) = 9227465
fib(36) = 14930352
fib(37) = 24157817
COST: 10.8920350075


```

除了用 map，另外一个常用的方法是 submit。如果你要提交的任务的函数是一样的，就可以简化成 map。但是假如提交的任务函数是不一样的，或者执行的过程之可能出现异常（使用 map 执行过程中发现问题会直接抛出错误）就要用到 submit：

```
from concurrent.futures import ThreadPoolExecutor, as_completed

NUMBERS = range(30, 35)


def fib(n):
    if n == 34:
        raise Exception("Don't do this")
    if n<= 2:
        return 1
    return fib(n-1) + fib(n-2)


with ThreadPoolExecutor(max_workers=3) as executor:
    future_to_num = {executor.submit(fib, num): num for num in NUMBERS}
    for future in as_completed(future_to_num):
        num = future_to_num[future]
        try:
            result = future.result()
        except Exception as e:
            print 'raise an exception: {}'.format(e)
        else:
            print 'fib({}) = {}'.format(num, result)


with ThreadPoolExecutor(max_workers=3) as executor:
    for num, result in zip(NUMBERS, executor.map(fib, NUMBERS)):
        print 'fib({}) = {}'.format(num, result)


```

执一下：

```
❯ python fib_executor_with_raise.py
fib(30) = 832040
fib(31) = 1346269
raise an exception: Don't do this
fib(32) = 2178309
fib(33) = 3524578
Traceback (most recent call last):
  File "fib_executor_with_raise.py", line 28, in <module>
    for num, result in zip(NUMBERS, executor.map(fib, NUMBERS)):
  File "/Library/Python/2.7/site-packages/concurrent/futures/_base.py", line 580, in map
     yield future.result()
  File "/Library/Python/2.7/site-packages/concurrent/futures/_base.py", line 400, in result
    return self.__get_result()
  File "/Library/Python/2.7/site-packages/concurrent/futures/_base.py", line 359, in __get_result
    reraise(self._exception, self._traceback)
  File "/Library/Python/2.7/site-packages/concurrent/futures/_compat.py", line 107, in reraise
    exec('raise exc_type, exc_value, traceback', {}, locals_)
  File "/Library/Python/2.7/site-packages/concurrent/futures/thread.py", line 61, in run
    result = self.fn(*self.args, **self.kwargs)
  File "fib_executor_with_raise.py", line 9, in fib
    raise Exception("Don't do this")
Exception: Don't do this


```

可以看到，第一次捕捉到了异常，但是第二次执行的时候错误直接抛出来了。

### 原理

我们就拿 ProcessPoolExecutor 介绍下它的原理，引用官方代码注释中的流程图：

```
|======================= In-process =====================|== Out-of-process ==|

+----------+     +----------+       +--------+     +-----------+    +---------+
|          |  => | Work Ids |    => |        |  => | Call Q    | => |         |
|          |     +----------+       |        |     +-----------+    |         |
|          |     | ...      |       |        |     | ...       |    |         |
|          |     | 6        |       |        |     | 5, call() |    |         |
|          |     | 7        |       |        |     | ...       |    |         |
| Process  |     | ...      |       | Local  |     +-----------+    | Process |
|  Pool    |     +----------+       | Worker |                      |  #1..n  |
| Executor |                        | Thread |                      |         |
|          |     +----------- +     |        |     +-----------+    |         |
|          | <=> | Work Items | <=> |        | <=  | Result Q  | <= |         |
|          |     +------------+     |        |     +-----------+    |         |
|          |     | 6: call()  |     |        |     | ...       |    |         |
|          |     |    future  |     |        |     | 4, result |    |         |
|          |     | ...        |     |        |     | 3, except |    |         |
+----------+     +------------+     +--------+     +-----------+    +---------+


```

我们结合源码和上面的数据流分析一下：

1.  executor.map 会创建多个_WorkItem 对象，每个对象都传入了新创建的一个 Future 对象。
2.  把每个_WorkItem 对象然后放进一个叫做「Work Items」的 dict 中，键是不同的「Work Ids」。
3.  创建一个管理「Work Ids」队列的线程「Local worker thread」，它能做 2 件事：
    1.  从「Work Ids」队列中获取 Work Id, 通过「Work Items」找到对应的_WorkItem。如果这个 Item 被取消了，就从「Work Items」里面把它删掉，否则重新打包成一个_CallItem 放入「Call Q」这个队列。executor 的那些进程会从队列中取_CallItem 执行，并把结果封装成_ResultItems 放入「Result Q」队列中。
    2.  从「Result Q」队列中获取_ResultItems，然后从「Work Items」更新对应的 Future 对象并删掉入口。

看起来就是一个「生产者 / 消费者」模型，不过要注意，整个过程并不是多个进程与任务 + 结果 - 2 个队列直接通信的，而是通过一个中间的「Local worker thread」。

设想，当某一段程序提交了一个请求，期望得到一个答复。但服务程序对这个请求可能很慢，在传统的单线程环境下，调用函数是同步的，也就是说它必须等到服务程序返回结果后，才能进行其他处理。而在 Future 模式下，调用方式改为异步，而原先等待返回的时间段，在主调用函数中，则可用于处理其他事物。

### Future

Future 是常见的一种并发设计模式，在多个其他语言中都可以见到这种解决方案。

一个 Future 对象代表了一些尚未就绪（完成）的结果，在「将来」的某个时间就绪了之后就可以获取到这个结果。比如上面的例子，我们期望并发的执行一些参数不同的 fib 函数，获取全部的结果。传统模式就是在等待 queue.get 返回结果，这个是同步模式，而在 Future 模式下，**调用方式改为异步，而原先等待返回的时间段，由于「Local worker thread」的存在，这个时候可以完成其他工作**

在 tornado 中也有对应的实现。2013 年的时候，我曾经写过一篇博客 [使用 tornado 让你的请求异步非阻塞](http://www.dongwm.com/archives/shi-yong-tornadorang-ni-de-qing-qiu-yi-bu-fei-zu-sai/) ，最后也提到了用 concurrent.futures 实现异步非阻塞的完成耗时任务。

### 用 multiprocessing 中的 Pool 还是 concurrent.futures 中的 PoolExecutor？

上面说到的 map，有些同学马上会说，这不是进程（线程）池的效果吗？看起来确实是的：

```
import time
from multiprocessing.pool import Pool

NUMBERS = range(25, 38)


def fib(n):
    if n<= 2:
        return 1
    return fib(n-1) + fib(n-2)


start = time.time()

pool = Pool(3)
for num, result in zip(NUMBERS, pool.map(fib, NUMBERS)):
    print 'fib({}) = {}'.format(num, result)

print 'COST: {}'.format(time.time() - start)

```

好像代码量更小。好吧，看一下花费的时间：

```
❯ python fib_pool.py
fib(25) = 75025
fib(26) = 121393
fib(27) = 196418
fib(28) = 317811
fib(29) = 514229
fib(30) = 832040
fib(31) = 1346269
fib(32) = 2178309
fib(33) = 3524578
fib(34) = 5702887
fib(35) = 9227465
fib(36) = 14930352
fib(37) = 24157817
COST: 11.8551170826


```

是的，使用 multiprocessing.pool 中的 ThreadPool/Pool 其实是更快一点的。

既然他们都是面向线程 / 进程提供相同的 API，甚至在一些方法上的参数和使用方式上都是一样的，为什么要保存 2 份用起来差不多的方案呢（甚至可以说后期实现的 concurrent.futures 慢一点）？

我翻了下源码，试着分析下：

1.  concurrent.futures 的架构明显要复杂一些，不过更利于写出高效、异步、非阻塞的并行代码，而 ThreadPeool/Pool 更像一个黑盒，你用就好了，细节不仅屏蔽定制性也差。
2.  concurrent.futures 的接口更简单一些。ThreadPool/Pool 的 API 中有 processes, initializer，initargs，maxtasksperchild，context 等参数，新人看起来容易不解，而 concurrent.futures 的参数就一个 max_workers。

其实 concurrent.futures 底层还是用着 threading 和 multiprocessing，相当于在其上又封装了一层，并且重新设计了架构，所以会慢一点。

然后我开始查找各种证据，找到了一个 [SO 的链接](https://stackoverflow.com/questions/24896193/whats-the-difference-between-pythons-multiprocessing-and-concurrent-futures) ，其中引用了 Python 核心贡献者 Jesse Noller 说的 [一段话](http://bugs.python.org/issue9205#msg132661) ：

> Brian and I need to work on the consolidation we intend(ed) to occur as people got comfortable with the APIs. My eventual goal is to remove anything but the basic multiprocessing.Process/Queue stuff out of MP and into concurrent.* and support threading backends for it.

和我的理解差不多吧。

不过我尝试让待处理的任务量比较大（上面的 NUMBERS 才包含 13 个），结果发现 multiprocessing 版本的速度差距成倍的扩大（下面会有实验代码）：concurrent.futures 不可思议的要慢很多！

这个时候我回忆了一下标准库的实现，没找到这 2 种架构性能差别能这么大的原因，正在疑惑中，以为自己使用的姿势不对。不过正好在上面的链接中作者还特别说明了如下一段：

> multiprocessing.Pool.map outperforms ProcessPoolExecutor.map. Note that the performance difference is very small per work item, so you'll probably only notice a large performance difference if you're using map on a very large iterable. The reason for the performance difference is that multiprocessing.Pool will batch the iterable passed to map into chunks, and then pass the chunks to the worker processes, which reduces the overhead of IPC between the parent and children. ProcessPoolExecutor always passes one item from the iterable at a time to the children, which can lead to much slower performance with large iterables, due to the increased IPC overhead. The good news is this issue will be fixed in Python 3.5, as as chunksize keyword argument has been added to ProcessPoolExecutor.map, which can be used to specify a larger chunk size if you know you're dealing with large iterables. See this bug([http://bugs.python.org/issue11271](http://bugs.python.org/issue11271)) for more info.

简单说明一下。multiprocessing.Pool.map 要优于 ProcessPoolExecutor.map，不过性能的差别是很小，如我上面说的，是由于架构和接口实现上的取舍。但是如果要处理的是一个很大的可迭代对象，就会有非常大的性能差别。这是因为 multiprocessing.Pool 是批量提交任务的，可以节省 IPC (进程间通信) 开销。而 ProcessPoolExecutor 每次都只提交一个任务。不过在 Python3.5 的时候已经通过给 map 方法添加 chunksize 参数解决了。

你可能比较迷惑，我们看一段代码就好了：

```
# coding=utf-8
import time
from multiprocessing.pool import Pool
from concurrent.futures import as_completed, ProcessPoolExecutor

NUMBERS = range(1, 100000)
K = 50


def f(x):
    r = 0
    for k in range(1, K+2):
        r += x ** (1 / k**1.5)
    return r


print('multiprocessing.pool.Pool:\n')
start = time.time()

l = []
pool = Pool(3)
for num, result in zip(NUMBERS, pool.map(f, NUMBERS)):
    l.append(result)
print(len(l))
print('COST: {}'.format(time.time() - start))

print('ProcessPoolExecutor without chunksize:\n')
start = time.time()

l = []
with ProcessPoolExecutor(max_workers=3) as executor:
    for num, result in zip(NUMBERS, executor.map(f, NUMBERS)):
        l.append(result)

print(len(l))

print('COST: {}'.format(time.time() - start))

print('ProcessPoolExecutor with chunksize:\n')
start = time.time()

l = []
with ProcessPoolExecutor(max_workers=3) as executor:
    # 保持和multiprocessing.pool的默认chunksize一样
    chunksize, extra = divmod(len(NUMBERS), executor._max_workers * 4)

    for num, result in zip(NUMBERS, executor.map(f, NUMBERS, chunksize=chunksize)):
        l.append(result)

print(len(l))

print('COST: {}'.format(time.time() - start))


```

先运行一下：

```
❯ python map_comparison.py
multiprocessing.pool.Pool:

99999
COST: 0.7973268032073975
ProcessPoolExecutor without chunksize:

99999
COST: 28.467172861099243
ProcessPoolExecutor with chunksize:

99999
COST: 0.8141732215881348


```

其中第一段使用 multiprocessing.pool，最快。 第二段使用没有加 chunksize， 这个速度不忍直视。大家不要再这样犯错了。 第三段加上了 chunksize，分块的原则和第一段标准库实现的一样，速度又回到同一个水平了。

迷惑这么久的原因其实是我一直在看 Python 2 的对应的代码，当我看了最新版的代码就懂了。

最后我的建议是：虽然会慢一点点，还是推荐使用接口更简单的 concurrent.futures，不过**大家使用的时候注意要用 Python3. 及以后的版本啦！**

PS：本文全部代码可以在 [微信公众号文章代码库项目](https://github.com/dongweiming/mp/tree/master/2017-06-12) 中找到。