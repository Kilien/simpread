> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/SL_World/article/details/86597738)

> 引言：协程 (coroutine) 是 Python 中一直较为难理解的知识，但其在多任务协作中体现的效率又极为的突出。众所周知，Python 中执行多任务还可以通过`多进程`或`一个进程中的多线程`来执行，但两者之中均存在一些缺点。因此，我们引出了协程。

Tips
----

欲看完整代码请见[我的 GitHub](https://github.com/SparksFly8/Learning_Python/tree/master/coroutine).

目录
--

1.  [《Python 异步 IO 之协程 (一): 从 yield from 到 async 的使用》](https://blog.csdn.net/SL_World/article/details/86597738)
2.  [《Python 异步 IO 之协程 (二): 使用 asyncio 的不同方法实现协程》](https://blog.csdn.net/SL_World/article/details/86691747)

_为什么需要协程？_
----------

首先，我们需要知道同步和异步是什么东东，不知道的看[详解](https://www.cnblogs.com/Anker/p/5965654.html)。  
简单来说：  
**【同步】：就是发出一个 “调用” 时，在没有得到结果之前，该 “调用” 就不返回，“调用者” 需要一直等待该 “调用” 结束，才能进行下一步工作。  
【异步】：“调用” 在发出之后，就直接返回了，也就没有返回结果。“被调用者” 完成任务后，通过状态来通知 “调用者” 继续回来处理该 “调用”。**

下面我们先来看一个用普通同步代码实现多个 IO 任务的案例。

```
# 普通同步代码实现多个 IO 任务
import time
def taskIO_1():
    print(' 开始运行 IO 任务 1...')
    time.sleep(2)  # 假设该任务耗时 2s
    print('IO 任务 1 已完成，耗时 2s')
def taskIO_2():
    print(' 开始运行 IO 任务 2...')
    time.sleep(3)  # 假设该任务耗时 3s
    print('IO 任务 2 已完成，耗时 3s')

start = time.time()
taskIO_1()
taskIO_2()
print(' 所有 IO 任务总耗时%.5f 秒 ' % float(time.time()-start))

```

执行结果：

```
开始运行 IO 任务 1...
IO 任务 1 已完成，耗时 2s
开始运行 IO 任务 2...
IO 任务 2 已完成，耗时 3s
所有 IO 任务总耗时 5.00604 秒

```

上面，我们顺序实现了两个同步 IO 任务 `taskIO_1()` 和`taskIO_2()`，则最后总耗时就是 **5 秒**。我们都知道，在计算机中 CPU 的运算速率要远远大于 IO 速率，而当 CPU 运算完毕后，如果再要闲置很长时间去等待 IO 任务完成才能进行下一个任务的计算，这样的任务执行效率很低。

所以我们需要有一种异步的方式来处理类似上述任务，会极大增加效率 (当然就是协程啦～)。而我们最初很容易想到的，**是能否在上述 IO 任务执行前中断当前 IO 任务** (对应于上述代码 `time.sleep(2)`)，**进行下一个任务，当该 IO 任务完成后再唤醒该任务。**

而在 Python 中**生成器**中的关键字 `yield` 可以实现**中断功能**。所以起初，协程是基于生成器的变形进行实现的，之后虽然编码形式有变化，但基本原理还是一样的。[戳我查看生成器及迭代器和可迭代对象的讲解和区别](https://blog.csdn.net/SL_World/article/details/86507872)。

一、使用 yield from 和 @asyncio.coroutine 实现协程
-----------------------------------------

在 Python3.4 中，协程都是通过使用 yield from 和 `asyncio 模块`中的 @asyncio.coroutine 来实现的。`asyncio` 专门被用来实现异步 IO 操作。

#### （1）什么是 yield from? 和 yield 有什么区别?

【1】我们都知道，`yield` 在生成器中有中断的功能，可以传出值，也可以从函数外部接收值，而 `yield from` 的实现就是简化了 `yield` 操作。  
让我们先来看一个案例：

```
def generator_1(titles):
    yield titles
def generator_2(titles):
    yield from titles

titles = ['Python','Java','C++']
for title in generator_1(titles):
    print(' 生成器 1:',title)
for title in generator_2(titles):
    print(' 生成器 2:',title)

```

执行结果如下：

```
生成器 1: ['Python', 'Java', 'C++']
生成器 2: Python
生成器 2: Java
生成器 2: C++

```

在这个例子中 `yield titles` 返回了 `titles` 完整列表，而 `yield from titles` 实际等价于：

```
for title in titles:　# 等价于 yield from titles
    yield title　　

```

【2】而 `yield from` 功能还不止于此，它还有一个主要的功能是省去了很多异常的处理，不再需要我们手动编写，其**内部已经实现大部分异常处理**。

【举个例子】：下面通过生成器来实现一个**整数加和**的程序，通过 `send()` 函数向生成器中传入要加和的数字，然后最后以返回 `None` 结束，`total` 保存最后加和的总数。

```
def generator_1():
    total = 0
    while True:
        x = yield 
        print(' 加 ',x)
        if not x:
            break
        total += x
    return total
def generator_2(): # 委托生成器
    while True:
        total = yield from generator_1() # 子生成器
        print(' 加和总数是:',total)
def main(): # 调用方
    g1 = generator_1()
    g1.send(None)
    g1.send(2)
    g1.send(3)
    g1.send(None)
    # g2 = generator_2()
    # g2.send(None)
    # g2.send(2)
    # g2.send(3)
    # g2.send(None)
    
main()

```

执行结果如下。可见对于生成器`g1`，在最后传入 `None` 后，程序退出，报 `StopIteration` 异常并返回了最后 `total` 值是５。

```
加 2
加 3
加 None
------------------------------------------
StopIteration       
<ipython-input-37-cf298490352b> in main()
---> 19     g1.send(None)
StopIteration: 5

```

如果把 `g1.send()` 那５行注释掉，解注下面的 `g2.send()` 代码，则结果如下。可见 `yield from` **封装了处理常见异常的代码**。对于 `g2` 即便传入 `None` 也不报异常，其中 `total = yield from generator_1()` 返回给 `total` 的值是 `generator_1()` 最终的 `return total`

```
加 2
加 3
加 None
加和总数是: 5

```

【3】借用上述例子，这里有几个概念需要理一下：

*   **【子生成器】**：yield from 后的 generator_1() 生成器函数是**子生成器**
*   **【委托生成器】**：generator_2() 是程序中的**委托生成器**，它负责委托**子生成器**完成具体任务。
*   **【调用方】**：main() 是程序中的**调用方**，负责调用委托生成器。

**`yield from` 在其中还有一个关键的作用是：建立调用方和子生成器的通道**，

*   在上述代码中 `main()` 每一次在调用 `send(value)` 时，`value` 不是传递给了**委托生成器** generator_2()，而是借助 `yield from` 传递给了**子生成器** generator_1() 中的 `yield`
*   同理，**子生成器**中的数据也是通过 `yield` 直接发送到**调用方** main() 中。

_之后我们的代码都依据`调用方-子生成器-委托生成器`的**规范形式**书写。_

#### （2）如何结合 @asyncio.coroutine 实现协程

那 `yield from` 通常用在什么地方呢？在协程中，**只要是和 IO 任务类似的、耗费时间的任务都需要使用 `yield from` 来进行中断，达到异步功能！**  
我们在上面那个同步 IO 任务的代码中修改成协程的用法如下：

```
# 使用同步方式编写异步功能
import time
import asyncio
@asyncio.coroutine # 标志协程的装饰器
def taskIO_1():
    print(' 开始运行 IO 任务 1...')
    yield from asyncio.sleep(2)  # 假设该任务耗时 2s
    print('IO 任务 1 已完成，耗时 2s')
    return taskIO_1.__name__
@asyncio.coroutine # 标志协程的装饰器
def taskIO_2():
    print(' 开始运行 IO 任务 2...')
    yield from asyncio.sleep(3)  # 假设该任务耗时 3s
    print('IO 任务 2 已完成，耗时 3s')
    return taskIO_2.__name__
@asyncio.coroutine # 标志协程的装饰器
def main(): # 调用方
    tasks = [taskIO_1(), taskIO_2()]  # 把所有任务添加到 task 中
    done,pending = yield from asyncio.wait(tasks) # 子生成器
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
IO 任务 1 已完成，耗时 2s
IO 任务 2 已完成，耗时 3s
协程无序返回值：taskIO_2
协程无序返回值：taskIO_1
所有 IO 任务总耗时 3.00209 秒

```

【使用方法】： `@asyncio.coroutine` **装饰器**是协程函数的标志，我们需要在每一个任务函数前加这个装饰器，并在函数中使用`yield from`。在同步 IO 任务的代码中使用的 `time.sleep(2)` 来假设任务执行了 2 秒。但在协程中 `yield from` 后面必须是**子生成器函数**，**而 `time.sleep()` 并不是生成器**，所以这里需要使用内置模块提供的生成器函数`asyncio.sleep()`。  
【功能】：通过使用协程，极大增加了多任务执行效率，最后消耗的时间是任务队列中耗时最多的时间。上述例子中的总耗时 3 秒就是 `taskIO_2()` 的耗时时间。  
【执行过程】：

1.  上面代码先通过 `get_event_loop()` **获取**了一个**标准事件循环** loop(因为是一个，所以协程是单线程)
2.  然后，我们通过 `run_until_complete(main())` 来运行协程 (此处把调用方协程 main() 作为参数，调用方负责调用其他委托生成器)，`run_until_complete` 的特点就像该函数的名字，直到循环事件的所有事件都处理完才能完整结束。
3.  进入调用方协程，我们把多个任务 [`taskIO_1()` 和 `taskIO_2()`] 放到一个 `task` 列表中，可理解为打包任务。
4.  现在，我们使用 `asyncio.wait(tasks)` 来获取一个 **awaitable objects 即可等待对象的集合** (此处的 aws 是协程的列表)，**并发运行传入的 aws**，同时通过 `yield from` 返回一个包含 `(done, pending)` 的元组，**done 表示已完成的任务列表，pending 表示未完成的任务列表**；如果使用 `asyncio.as_completed(tasks)` 则会按完成顺序生成协程的**迭代器** (常用于 for 循环中)，因此当你用它迭代时，会尽快得到每个可用的结果。【此外，当**轮询**到某个事件时 (如 taskIO_1())，直到**遇到**该**任务中的 `yield from` 中断**，开始**处理下一个事件** (如 taskIO_2()))，当 `yield from` 后面的子生成器**完成任务**时，该事件才再次**被唤醒**】
5.  因为 `done` 里面有我们需要的返回结果，但它目前还是个任务列表，所以要取出返回的结果值，我们遍历它并逐个调用 `result()` 取出结果即可。(注：对于 `asyncio.wait()` 和 `asyncio.as_completed()` 返回的结果均是先完成的任务结果排在前面，所以此时打印出的结果不一定和原始顺序相同，但使用 `gather()` 的话可以得到原始顺序的结果集，[两者更详细的案例说明见此](https://blog.csdn.net/SL_World/article/details/86691747))
6.  最后我们通过 `loop.close()` 关闭事件循环。

综上所述：协程的完整实现是靠**①事件循环＋②协程**。

二、使用 async 和 await 实现协程
-----------------------

在 Python 3.4 中，我们发现很容易将**协程和生成器混淆** (虽然协程底层就是用生成器实现的)，所以在后期加入了其他标识来区别协程和生成器。

在 **Python 3.5** 开始引入了新的语法 `async` 和`await`，以简化并更好地**标识异步 IO**。

要使用新的语法，只需要做两步简单的替换：

*   把`@asyncio.coroutine` 替换为 `async`；
*   把 `yield from` 替换为`await`。

更改上面的代码如下，可得到同样的结果：

```
import time
import asyncio
async def taskIO_1():
    print(' 开始运行 IO 任务 1...')
    await asyncio.sleep(2)  # 假设该任务耗时 2s
    print('IO 任务 1 已完成，耗时 2s')
    return taskIO_1.__name__
async def taskIO_2():
    print(' 开始运行 IO 任务 2...')
    await asyncio.sleep(3)  # 假设该任务耗时 3s
    print('IO 任务 2 已完成，耗时 3s')
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

三、总结
----

最后我们将整个过程串一遍。  
【引出问题】：

1.  同步编程的并发性不高
2.  **多进程**编程效率受 CPU 核数限制，当任务数量远大于 CPU 核数时，执行效率会降低。
3.  **多线程**编程需要线程之间的通信，而且需要**锁机制**来防止**共享变量**被不同线程乱改，而且由于 Python 中的 **GIL(全局解释器锁)**，所以实际上也无法做到真正的并行。

【产生需求】：

1.  可不可以采用**同步**的方式来**编写异步**功能代码？
2.  能不能只用一个**单线程**就能做到不同任务间的切换？这样就没有了线程切换的时间消耗，也不用使用锁机制来削弱多任务并发效率！
3.  对于 IO 密集型任务，可否有更高的处理方式来节省 CPU 等待时间？

【结果】：所以**协程**应运而生。当然，实现协程还有其他方式和函数，以上仅展示了一种较为常见的实现方式。此外，**多进程和多线程是内核级别**的程序，而**协程是函数级别**的程序，是可以通过程序员进行调用的。以下是协程特性的总结：

<table><thead><tr><th>协程</th><th>属性</th></tr></thead><tbody><tr><td>所需线程</td><td><strong>单线程</strong><br>(因为仅定义一个 loop<h-char unicode="ff0c" class="biaodian cjk bd-end bd-cop bd-hangable"><h-inner>，</h-inner></h-char>所有 event 均在一个 loop 中)</td></tr><tr><td>编程方式</td><td>同步</td></tr><tr><td>实现效果</td><td><strong>异步</strong></td></tr><tr><td>是否需要锁机制</td><td>否</td></tr><tr><td>程序级别</td><td>函数级</td></tr><tr><td>实现机制</td><td><strong>事件循环＋协程</strong></td></tr><tr><td>总耗时</td><td>最耗时事件的时间</td></tr><tr><td>应用场景</td><td>IO 密集型任务等</td></tr></tbody></table>

【额外加餐】：使用 `tqdm` 库实现**进度条**  
这是一个免费的库：`tqdm` 是一个用来生成进度条的优秀的库。这个协程就像 `asyncio.wait` 一样工作，不过会显示一个代表完成度的进度条。详情见：[python 进度可视化](https://ptorch.com/news/170.html)

```
async def wait_with_progress(coros):
    for f in tqdm.tqdm(asyncio.as_completed(coros), total=len(coros)):
        await f

```

```
from tqdm import tqdm
for i in tqdm(range(10000)):
    pass

```

使用 `tqdm` 实现效果：  
![](https://img-blog.csdnimg.cn/20190311101757955.png)

四、结束语
-----

感谢大家能耐心读到这里，写了这么多文字，再来个真实的案例实战一下效果更佳哦~！  
以下是一个[协程在爬虫的应用实战案例](https://blog.csdn.net/SL_World/article/details/86633611)，其中对比了分布式多进程爬虫，最后将异步爬虫和多进程爬虫融合 (含案例以及耗时对比)，效果更好。我们可以先提前来通过一幅图看清**多进程**和**协程**的爬虫之间的**原理**及其**区别**。(图片来源于网络)  
![](https://img-blog.csdnimg.cn/20190124144910994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NMX1dvcmxk,size_16,color_FFFFFF,t_70)  
这里，**异步爬虫**不同于多进程爬虫，它使用**单线程** (即仅创建一个事件循环，然后把所有任务添加到事件循环中) 就能**并发处理多任务**。在**轮询**到某个任务后，当**遇到耗时操作** (如请求 URL) 时，**挂起**该任务并进行下一个任务，当之前被挂起的任务**更新了状态** (如获得了网页响应)，则**被唤醒**，程序继续**从上次挂起的地方运行**下去。极大的减少了中间不必要的等待时间。

【参考文献】：  
[[1] Python 协程：从 yield/send 到 async/await](http://python.jobbole.com/86069/)  
[[2] 廖雪峰. 协程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432090171191d05dae6e129940518d1d6cf6eeaaa969000)  
[[3] Python 3.7.2 文档. 协程与任务](https://docs.python.org/zh-cn/3/library/asyncio-task.html)  
[[4] 加速爬虫: 异步加载 Asyncio](https://morvanzhou.github.io/tutorials/data-manipulation/scraping/4-02-asyncio/)  
[[5]《python 高级编程》异步 IO 和协程（视频）](https://www.bilibili.com/video/av41014269/?p=10)  
[[6] 关于同步、异步与阻塞、非阻塞的理解](https://www.cnblogs.com/Anker/p/5965654.html)  
[[7] python: 利用 asyncio 进行快速抓取 (aiohttp)](https://www.bbsmax.com/A/Ae5RXrL3zQ/)  
[[8] 控制组合式 Coroutines](https://mozillazg.com/2017/08/python-asyncio-note-control-coroutines)

![](https://img-blog.csdnimg.cn/20190215122907151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NMX1dvcmxk,size_16,color_FFFFFF,t_70)

TensorFlow 2.0 三大项目实战