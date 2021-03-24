> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/6844903574963486728)

本文首发于[知乎](https://zhuanlan.zhihu.com/p/34324225)  
异步是继多线程、多进程之后第三种实现并发的方式，主要用于 IO 密集型任务的运行效率提升。python 中的异步基于`yield`生成器，在讲解这部分原理之前，我们先学会异步库 asyncio 的使用。

本文主要讲解`asyncio`模块的通用性问题，对一些函数细节的使用就简单略过。

本文分为如下部分

*   最简单的使用
*   另一种常见的使用方式
*   一个问题
*   一般函数下的异步
*   理解异步、协程
*   单个线程的的异步爬虫

### 最简单的使用

```
import asyncio
async def myfun(i):
    print('start {}th'.format(i))
    await asyncio.sleep(1)
    print('finish {}th'.format(i))
loop = asyncio.get_event_loop()
myfun_list = (myfun(i) for i in range(10))
loop.run_until_complete(asyncio.gather(*myfun_list))
复制代码

```

这样运行，10 次等待总共只等待了 1 秒。

上面代码一些约定俗成的用法记住就好，如

*   要想异步运行函数，需要在定义函数时前面加`async`
*   后三行都是记住就行，到时候把函数传入

### 另一种常见的使用方式

上面是第一种常见的用法，下面是另外一种

```
import asyncio
async def myfun(i):
    print('start {}th'.format(i))
    await asyncio.sleep(1)
    print('finish {}th'.format(i))
loop = asyncio.get_event_loop()
myfun_list = [asyncio.ensure_future(myfun(i)) for i in range(10)]
loop.run_until_complete(asyncio.wait(myfun_list))
复制代码

```

这种用法和上面一种的不同在于后面调用的是`asyncio.gather`还是`asyncio.wait`，当前看成完全等价即可，所以平时使用用上面哪种都可以。

上面是最常看到的两种使用方式，这里列出来保证读者在看其他文章时不会发蒙。

另外，二者其实是有细微差别的

*   `gather`更擅长于将函数聚合在一起
*   `wait`更擅长筛选运行状况

细节可以参考[这篇回答](https://stackoverflow.com/questions/42231161/asyncio-gather-vs-asyncio-wait)

### 一个问题

与之前学过的多线程、多进程相比，`asyncio`模块有一个非常大的不同：传入的函数不是随心所欲

*   比如我们把上面`myfun`函数中的`sleep`换成`time.sleep(1)`，运行时则不是异步的，而是同步，共等待了 10 秒
*   如果我换一个`myfun`，比如换成下面这个使用`request`抓取网页的函数

```
import asyncio
import requests
from bs4 import BeautifulSoup
async def get_title(a):
    url = 'https://movie.douban.com/top250?start={}&filter='.format(a*25)
    r = requests.get(url)
    soup = BeautifulSoup(r.content, 'html.parser')
    lis = soup.find('ol', class_='grid_view').find_all('li')
    for li in lis:
        title = li.find('span', class_="title").text
        print(title)
loop = asyncio.get_event_loop()
fun_list = (get_title(i) for i in range(10))
loop.run_until_complete(asyncio.gather(*fun_list))
复制代码

```

依然不会异步执行。

到这里我们就会想，是不是异步只对它自己定义的`sleep`(`await asyncio.sleep(1)`) 才能触发异步？

### 一般函数下的异步

对于上述函数，`asyncio`库只能通过添加线程的方式实现异步，下面我们实现`time.sleep`时的异步

```
import asyncio
import time
def myfun(i):
    print('start {}th'.format(i))
    time.sleep(1)
    print('finish {}th'.format(i))
async def main():
    loop = asyncio.get_event_loop()
    futures = (
        loop.run_in_executor(
            None,
            myfun, 
            i)
        for i in range(10)
        )
    for result in await asyncio.gather(*futures):
        pass
loop = asyncio.get_event_loop()
loop.run_until_complete(main())
复制代码

```

上面`run_in_executor`其实开启了新的线程，再协调各个线程。调用过程比较复杂，只要当模板一样套用即可。

上面 10 次循环仍然不是一次性打印出来的，而是像分批次一样打印出来的。这是因为开启的线程不够多，如果想要实现一次打印，可以开启 10 个线程，代码如下

```
import concurrent.futures as cf # 多加一个模块
import asyncio
import time
def myfun(i):
    print('start {}th'.format(i))
    time.sleep(1)
    print('finish {}th'.format(i))
async def main():
    with cf.ThreadPoolExecutor(max_workers = 10) as executor: # 设置10个线程
        loop = asyncio.get_event_loop()
        futures = (
            loop.run_in_executor(
                executor, # 按照10个线程来执行
                myfun, 
                i)
            for i in range(10)
            )
        for result in await asyncio.gather(*futures):
            pass
loop = asyncio.get_event_loop()
loop.run_until_complete(main())
复制代码

```

用这种方法实现`requests`异步爬虫代码如下

```
import concurrent.futures as cf
import asyncio
import requests
from bs4 import BeautifulSoup
def get_title(i):
    url = 'https://movie.douban.com/top250?start={}&filter='.format(i*25)
    r = requests.get(url)
    soup = BeautifulSoup(r.content, 'html.parser')
    lis = soup.find('ol', class_='grid_view').find_all('li')
    for li in lis:
        title = li.find('span', class_="title").text
        print(title)
async def main():
    with cf.ThreadPoolExecutor(max_workers = 10) as executor:
        loop = asyncio.get_event_loop()
        futures = (
            loop.run_in_executor(
                executor,
                get_title, 
                i)
            for i in range(10)
            )
        for result in await asyncio.gather(*futures):
            pass
loop = asyncio.get_event_loop()
loop.run_until_complete(main())
复制代码

```

这部分参考[这篇文章](http://skipperkongen.dk/2016/09/09/easy-parallel-http-requests-with-python-and-asyncio/)还有[这个回答](https://stackoverflow.com/questions/22190403/how-could-i-use-requests-in-asyncio)

这种开启多个线程的方式也算异步的一种，下面一节详细解释。

### 理解异步、协程

现在我们讲了一些异步的使用，是时候解释一些概念了

*   首先，我们要理清楚**同步、异步、阻塞、非阻塞**四个词语之间的联系
*   首先要明确，前两者后后两者并不是一一对应的，它们不是在说同一件事情，但是非常类似，容易搞混
*   一般我们说异步程序是非阻塞的，而同步既有阻塞也有非阻塞的
*   非阻塞是指一个任务没做完，没有必要停在那里等它结束就可以开始下一个任务，保证一直在干活没有等待；阻塞就相反是一件事完全结束才开始另一件事
*   在非阻塞的情况下，同步与异步都有可能，它们都可以在一个任务没结束就开启下一个任务。而二者的区别在于：（且称正在进行的程序为主程序）当第一个程序做完的时候（比如网络请求终于相应了），会自动通知主程序回来继续操作第一个任务的结果，这种是异步；而同步则是需要主程序不断去问第一个程序是否已经完成。
*   四个词的区别参考[知乎回答](https://www.zhihu.com/question/19732473)
*   （**协程与多线程的区别**）在非阻塞的情况下，多线程是同步的代表，协程是异步的代表。二者都开启了多个线程
*   多线程中，多个线程会竞争谁先运行，一个等待结束也不会去通知主程序，这样没有章法的随机运行会造成一些资源浪费
*   而协程中，多个线程（称为微线程）的调用和等待都是通过明确代码组织的。协程就像目标明确地执行一个又一个任务，而多线程则有一些彷徨迷茫的时间
*   **两种异步**
*   前面几节涉及到两种异步，一种是`await`只使用一个线程就可以实现任务切换，另一种是开启了多个线程，通过线程调度实现异步
*   一般只用一个线程将任务在多个函数之间来回切换，是使用 yield 生成器实现的，例子可以看[这篇文章最后生产消费者例子](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432090171191d05dae6e129940518d1d6cf6eeaaa969000)
*   **多进程、多线程、异步擅长方向**
*   异步和多线程都是在 IO 密集型任务上优势明显，因为它们的本质都是在尽量避免 IO 等待时间造成的资源浪费。而多进程可以利用多核优势，适合 CPU 密集型任务
*   相比于多线程，异步更适合每次等待时间较长、需要等待的任务较多的程序。因为多线程毕竟要创建新的线程，线程过多使线程竞争现象更加明显，资源浪费也就更多。如果每个任务等待时间过长，等待时间内势必开启了非常多任务，非常多线程，这时使用多线程就不是一个明智的决定。而异步则可以只开启一个线程在各个任务之间有条不紊进行，即能充分利用 CPU 资源，又不会影响程序运行效率

### 单个线程的的异步爬虫

上面我们是通过开启多个线程来实现`requests`的异步，如果我们想只用一个线程（用`await`），就要换一个网页请求函数。

事实上要想用`await`，必须是一个 awaitable 对象，这是不能使用`requests`的原因。而转化成 awaitable 对象这样的事当然也不用我们自己实现，现在有一个`aiohttp`模块可以将网页请求和`asyncio`模块完美对接。使用这个模块改写代码如下

```
import asyncio
import aiohttp
from bs4 import BeautifulSoup
async def get_title(i):
    url = 'https://movie.douban.com/top250?start={}&filter='.format(i*25)
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            print(resp.status)
            text = await resp.text()
            print('start', i)
    soup = BeautifulSoup(text, 'html.parser')
    lis = soup.find('ol', class_='grid_view').find_all('li')
    for li in lis:
        title = li.find('span', class_="title").text
        print(title)
loop = asyncio.get_event_loop()
fun_list = (get_title(i) for i in range(10))
loop.run_until_complete(asyncio.gather(*fun_list))
复制代码

```

### 欢迎关注我的知乎专栏

专栏主页：[python 编程](https://zhuanlan.zhihu.com/python-programming)

专栏目录：[目录](https://zhuanlan.zhihu.com/p/33896532?refer=python-programming)

版本说明：[软件及包版本说明](https://zhuanlan.zhihu.com/p/29663336)