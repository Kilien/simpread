> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [sspai.com](https://sspai.com/post/39258)

欢迎大家阅读**王掌柜带你玩转 Zapier 系列**，这个系列将持续更新，本篇是这个系列第三篇 Zapier 和 IFTTT 的对比。

*   第一篇：《[王掌柜带你玩转 Zapier - 影评自动化](https://sspai.com/post/38989 "王掌柜带你玩转 Zapier - 影评自动化")》
*   第二篇：《[王掌柜带你玩转 Zapier - 阅读自动化](https://sspai.com/post/39254 "王掌柜带你玩转 Zapier - 阅读自动化")》
*   第三篇：《[王掌柜带你玩转 Zapier - Zapier vs IFTTT](https://sspai.com/post/39258 "王掌柜带你玩转 Zapier - Zapier vs IFTTT")》
*   第四篇：《[王掌柜带你玩转 Zapier - 读书笔记自动化](https://sspai.com/post/41838 "王掌柜带你玩转 Zapier - 读书笔记自动化")》

IFTTT 和 Zapier 是什么
------------------

IFTTT 的理念就像它的名字 **If This Then That** ，可以将两个不同的服务关联到一起，最典型的例子就是「如果明天下雨，那么就提醒我带雨伞」，在这个设定下，IFTTT 会定时查询第二天的天气预报，如果天气预报返回的结果是第二天下雨，那么 IFTTT 就会给我设置的手机号发送短信，通知我第二天出门别忘了带伞。

关联阅读：[《触发你的智能生活：IFTTT 入门》](https://sspai.com/post/25270 "《触发你的智能生活：IFTTT 入门》")

Zapier 的基本功能与 IFTTT 类似，但它是 IFTTT 的增强版。不同于 IFTTT 只支持串联两个服务，Zapier 支持串联多个服务，让你可以建立一套有更复杂需求的工作流。Zapier 支持大量的高级服务，具备极高的专业性和企业级应用场景。价格上，IFTTT 大部分功能都是免费的，而 Zapier 基础功能相对简单并且有运行次数限制，很多高级功能都需要付费。目前 Zapier 在国外科技圈收到大力追捧和推荐，今天王掌柜就带着大家玩转 Zapier，做一次 IFTTT vs. Zapier。

Service 数量对比
------------

Service 的数量决定了这个服务的扩展性。截止到目前 IFTTT 大约支持 44 个分类里的 1120 个 Service，而 Zapier 目前支持约 903 个 Service。

IFTTT 和 Zapier 都针对互联网上比较常见的 Service 提供了支持，数量上，IFTTT 领先，但是 Zapier 是付费服务，这就决定了他们对付费用户提供了更定制化的支持，比如 Premium 分类下的的 Service ，相比免费的 Service 更专业。

![](http://7xij6f.com1.z0.glb.clouddn.com/IMG_0379.JPG)Premium Service 示例

我们看到上图中，支持的服务包括 Amazon EC2、Amazon S3、AWS Lambda、Amazon SNS、Evernote Business 等，其实还有很多比如 Paypal、MySQL、Airtable、Facebook Pages、Flickr、LinkedIn、MailChimp、MeisterTask、Moxtra 等众多优质服务，这些服务之所以称为 **Premium** 是因为官方维护这些应用所需成本较高，或者开发这些服务成本高，所以在 Zapier 只有付费用户才能使用。

举一个简单列子说明一下，成本问题，比如我们要使用 Paypal 的 Service ，Zapier 实现他的方式不是传统的轮询调用 API 方式，他是采用 Webhook 方式，当 Paypal 完成一项任务后，会主动推送信息到 Zapier 的 Webhook，这种情况下，我们的 Zapier 才会进行相应的动作。

> Webhook 不同于我们已知的 API ，API 是 Zapier 通过类似轮询的方式主动抓去数据来启动触发器， 而 Webhook 是被动服务，被动服务是指当 Service 在完成一件事情后，主动请求 Zapier，将结果告知 Zapier，这种 webhook 在粘合剂服务中，算是代价比较高的服务且，支持起来有一定难度，一般只有付费用户才能享用。

#### 小结

![](https://cdn.sspai.com/2017/05/16/947763e5a7948cc3030269bd16ce0d08.png)

调试灵活性对比
-------

### 变量：Zapier 可以直接显示变量值，IFTTT 只能显示名称

Zapier 设置任务流中使用「变量」的时候，除了通过变量名称猜测其含义，它还可以在变量列表里清晰的展显示出变量对应的值，而 IFTTT 只能通过变量名称来猜测其对应的内容。

在使用 IFTTT 服务的时候，通常我们先有一个想法，比如「将 [Pocket](https://itunes.apple.com/cn/app/pocket/id309601447?mt=8&uo=4&at=10l6nh&ct=ms_inline "Pocket") 中的一篇文章，打上标记后，自动保存到 [Evernote](https://itunes.apple.com/cn/app/%E5%8D%B0%E8%B1%A1%E7%AC%94%E8%AE%B0/id281796108?mt=8&uo=4&at=10l6nh&ct=ms_inline "Evernote") 中] ，我们最优先的做法是去 IFTTT 找一下是否有人共享了类似的 Applet（我们将 IFTTT 每个任务流称作一个 Applet），如果有的话，我们直接激活该 Applet 就能使用了。但是，难免我们需要一些定制化的修改，这时候就必须进入到 IFTTT 的 Applet 编辑界面进行**调试**（所谓调试就是修改输入和输出，让服务按照我们的想法去执行），那么这个调试过程在 IFTTT 中是否友好呢，我们来看下图。

![](https://cdn.sspai.com/2017/05/19/a51d2e1b8330a2dc934edcba8582df7e.jpeg)

我们看一下图中箭头标注内容，代表的是存入 Evernote 时使用 Pocket 那篇要保存的文章的部分字段（对应的内容，比如标题比如 URL 链接），我们看到在图中，IFTTT 是用类似**变量**（编程中的名字，用一个变量名来抽象化我们实际用到的内容）的方式来实现，对于没有编程经验的菜鸟来说，这几乎很难能够理解。我们再来看一下 Zapier 是如何调试的。

![](https://cdn.sspai.com/2017/05/19/f51d31f03b40ee0a491ad1da64627d34.jpeg)

上图中，我们同样是实现将 Pocket 中内容保存到 Evernote 中，我们看箭头所指的标注内容，Zapier 也是用变量方式来代表我们要使用的内容，但是变量的后边还给出了实际的内容，这样看起来即使你不懂变量是什么内容，也能够知道哪个字段才是你想要的。这样调试的话是不是感觉更简单和直接呢？应该是显而易见了。

#### 小结

![](https://cdn.sspai.com/2017/05/16/982e8e224f16504a196edc7b16f9a78c.png)

### 运行结果：Zapier 可以直接调试任务流、IFTTT 只能等待系统自动调试

Zapier 可以在后台很方便的执行一个任务流从而观察执行结果，而在 IFTTT 中每次只能等待系统的轮询时间，并且这个时间也是不固定的。

调试任务流，除了变量要够清晰之外，查看运行结果也是非常必要的一环，能够很方便的查看运行结果对调试来说是**事半功倍**的事，我们分别看看 IFTTT 和 Zapier 是如何表现的吧。

在 IFTTT 中创建好任务流，如果想查看执行结果，必须要等待自动**轮询**（附录中可以查看轮询的相关说明）完成才可以，而这个时间可能是 1-3 分钟，也可能是 10-20 分钟，所以说时间是不确定的，这为我们调试任务流带来了很大的麻烦。

我们再来看看 Zapier 是如何做的呢，在 Zapier 中每一个创建好的任务流，我们都可以去 Zapier 后台，找到要执行的 Zap，选择 Run，他就会立即执行，怎么样，非常便利吧，简直是不能再好用了。

> Zap，在 Zapier 中创建的每个任务流称为一个 Zap。

![](https://cdn.sspai.com/2017/05/18/3ebdde90a6c15efd18c3b19f3bf5426d.jpg)

#### 小结

![](https://cdn.sspai.com/2017/05/16/3eb3abd12a502db66094eb5e18b21e92.png)

### 多 Service 协作：Zapier 支持串联多个服务，IFTTT 只能串联两个服务

Zapier 实现「如果 A 发生了事情 1，那么让 B 发生事情 2，再让 C 发生事情 3，最后让 D 发生事情 4」这样的任务流只需要创建一个任务流；而 IFTTT 若实现类似服务，至少要创建三个任务流。

在 IFTTT 中，我们只能设置「如果 A 发生了事情 1，我们就让 B 发生事情 2」，这里只能涉及两个服务，但是在实际使用环境中，有些任务流可能需要多个 Service 协作才能完成，恰好它的小伙伴 Zapier 支持一个任务流多个服务协作。我们还是举「影评自动化」这个例子说明一下。

在本系列的第一篇文章《[王掌柜带你玩转 Zapier - 影评自动化](https://sspai.com/post/38989 "王掌柜带你玩转 Zapier - 影评自动化")》中，我们的任务流需要完成以下三件事：

1.  当有一篇新文章存入 [Evernote](https://itunes.apple.com/cn/app/%E5%8D%B0%E8%B1%A1%E7%AC%94%E8%AE%B0/id281796108?mt=8&uo=4&at=10l6nh&ct=ms_inline "Evernote") 的 「013@电影笔记」 文件夹下的时候（代表我们完成了影评的编写）。
2.  在 [Todoist](https://itunes.apple.com/cn/app/todoist-%E5%BE%85%E5%8A%9E%E4%BA%8B%E9%A1%B9%E5%88%97%E8%A1%A8-%E4%BB%BB%E5%8A%A1%E7%AE%A1%E7%90%86%E5%99%A8/id572688855?mt=8&uo=4&at=10l6nh&ct=ms_inline "Todoist") 中的找到和 Evernote 笔记同名的任务，并且设置任务为已完成（代表「影评提醒」这个任务已经完成了）。
3.  在 [Twitter](https://itunes.apple.com/cn/app/twitter/id333903271?mt=8&uo=4&at=10l6nh&ct=ms_inline "Twitter") 中创建一条消息，将 Evernote 中的影评笔记发布出去。

我们看到这个例子中就涉及到了 3 个 Service ，分别是 Evernote、Todoist、Twitter ，在 Zapier 中我们很容易在同一个任务流中创建多个 Service 协作（实现方式可以翻看[上一期文章](https://sspai.com/post/38989 "上一期文章")），而在 IFTTT 中要想实现多服务协作，我们需要至少创建两个任务流才可以。

#### 小结

![](https://cdn.sspai.com/2017/05/16/f976947b29df54f7373079dc89615bba.png)

### 单 Service 多账号：Zapier 支持多账号，IFTTT 只支持单账号

Zapier 可以为每个 Service 设置多个不同账户，从而提升任务流的灵活性，而 IFTTT 每个 Service 只支持一个账户。

如果一个团队使用或者针对同一个 Service 有多个帐户在使用的情况，「每个 Service 支持多套账户」的功能就非常有必要了，这样可以做出更复杂的任务流，我们还是举例说明。

**任务流 1**

1.  我要求部门员工每天在 [Trello](http://itunes.apple.com/cn/app/trello/id461504587?mt=8&uo=4&at=1016nh&ct=ms_inline "Trello") 里在当天完成（或未完成）的任务卡片里写评论，算作当天的工作日志。
2.  然后我为了不用一个一个的翻看他们的评论，我在 Zapier 中设置一个任务流，触发器就是每当有人在指定的 Board 中评论了一个 Card，Action 是将评论内容写入以「当天日期」命名的文章里保存到 Evernote 中。
3.  这样我就可以每天下班前打开 Evernote 中的这篇文章查看所有人的工作日志了。

> 触发器：设置一个触发器，Zapier 会轮询检查触发器条件，当条件满足，代表触发器被成功触发。
> 
> Action：在 Zapier 中，任务流中的每一步我们称之为一个 Action。

**任务流 2**

1.  我在「少数派的选题箱」中看到了一篇题目或者我自己创建了一篇题目（ Trello 里的 Board），我觉得自己可以写，于是我把任务卡片移动到「在写」 LIst 里，我就开始去写。
2.  为了激励自己，并且提醒我有未完成的稿件，于是在 Zapier 里创建了一个任务流，触发器就是「我将一个任务卡片移动到在写 List 里」，Action 是「Zapier 在 Todoist 里自动创建一个任务」，提醒我要完成这篇稿件。

以上两个任务流，都用到了 Trello ，但是问题是我公司用的 Trello 和 少数派选题用的 Trello 是两个账户，此时「每个 Service 可以配置多套账户」就起到关键作用了，我可以设置两个不同的账户，在创建这两个任务流的时候，选择不同的账户即可，非常方便。

![](http://7xij6f.com1.z0.glb.clouddn.com/2017-05-16-FullSizeRender-3.jpg)

那么他的对手 IFTTT 表现如何呢？

IFTTT 目前并不支持一个 Service 对应多个账户，但是在它的高级版中提到了「可以与团队深度合作」，也许能支持多账户，但是这个高级版的的费用是 **$499/year**，估计一般情况也不会有人使用，我们也就无从比较了。

#### 小结

![](https://cdn.sspai.com/2017/05/16/b6501f8c5dd557a0ad3f89cdc01c7441.png)

其它易用性对比
-------

从创建任务流这个基础需求角度看，IFTTT 和 Zapier 都没有什么问题，对于有一定英文阅读基础的人来说，照着提示就可以完成配置，但是作为后期之秀的 Zapier，当然是有一些差异化的表现，它提供了几个重要特性，提高了易用性，特别是对一些大型的企业级任务流比较有帮助，下边针对几个非常有特点的特性，我们都拿出来和 IFTTT 对比一下。

### Zapier 支持全局存储变量，并且可以在不同工作流之间共享

Zapier 最强大的功能之一就是将数据存储在全局变量中，这种变量可以在不同的 zap 之间共享。这被称为 Storage by Zapier ，并且本质上它允许您存储小值（例如，文本字符串）以便在其它 zap 中重用 。

> Zap：在 Zapier 中创建的每个任务流称为一个 Zap。

它有多种存储类型：基本值（可以设置和读取，就像工作流中的变量一样），还有子值和列表。对于每种类型的存储，都有删除值和修改它们的操作：可以增加一个数值，一个值可以被删除，或者，如果你正在处理一个列表，一个值可以从开始或结束时弹出的列表。这为 Web 自动化提供了令人难以置信的可能性，我们用一个打卡事件来举例说明。

我们天都会跑步，我想记录我每次跑步的时间，于是我设置以下任务流：

1.  当我开始跑步的时候，执行 Workflow 通过 Zapier 的 Webhook，我们记录一个当前的时间戳存储在变量中，变量的 Key 为 playStartTime （运动开始时间）。
2.  当我跑步结束的时候，执行 Workflow 通过 Zapier 的 Webhook，获取变量 playStartTime ，计算一个时间差，获取到本次跑步时长，我将时间存入到 Evernote 中以作记录。

这样我只需要每次跑步开始的时候打开手机执行一下 Workflow，跑步完成的时候在执行一下 Workflow ，这条记录时间就被存储下来了。

#### 小结

![](https://cdn.sspai.com/2017/05/16/e83441f58a8b7212189166fd1c6231af.png)

### Zapier 支持自动重新执行失败的任务流

任务流执行失败了没关系，Zapier 提供了失败重新执行的特性，保证我们每一个任务流的准确无误。

此特性对于任务执行**成功率**有很大的保证作用，无论是 Zapier 还是 IFTTT ，他们都是通过 API 链接一个或者多个 Service，这里有一些不确定性，比如当时服务网络抖动，比如当时某一个 Service 出现 Bug ，这些事情是无法避免的。

那么这个特性就会在我们遇到一个任务流执行失败了的时候，自动重新执行它，一直到他能够正确运行为止，这个特性就是保证了我们任务的正确返回，保证我们不会有因为其它原因漏掉的执行结果。但是， Zapier 中享受此服务需要至少 **$50／月** ，这个绝对是硬伤，如果你不是对任务流要求百分百必须准确执行的话，可以不考虑使用此特性。

#### 小结

![](https://cdn.sspai.com/2017/05/16/d41c48c77e2f39c06f75d0fa3340fd78.png)

### IFTTT 有 iOS 原生客户端，Zapier 没有

IFTTT 提供了 iOS 原生客户端，从而打通了系统应用和 Service 之间的壁垒，Zapier 没有 iOS 客户端，但是通过 Workflow + Webhook 也可以曲线救国。

IFTTT 提供了 iOS 客户端，通过客户端我们可以处理很多 iOS 原生服务，比如「 iPhone 有新的照片了」，比如「新建了提醒事项」，有了这个原生的客户端，IFTTT 就将原生服务和其它 Service 打通了，我们再举个例子。

我的主力手机是 iPhone ，我用手机拍摄的照片都会默认存在 iCloud 中，但是我不是很放心 iCloud 服务，所以我想同时把照片同步到 [Dropbox](https://itunes.apple.com/cn/app/dropbox/id327630330?mt=8&uo=4&at=10l6nh&ct=ms_inline "Dropbox") 中，于是我在 IFTTT 中设置任务流，每当 iPhone 有新的照片时，自动同步到 Dropbox ，但是有一个前提，iPhone 照片不是一个 Service，我们就需要安装 IFTTT 的客户端，每天晚上运行一次 IFTTT 客户端，客户端会自动发现新照片，并且同步到 Dropbox ，当然这个动作你也可以不定期的打开一次 IFTTT 即可。

那么 IFTTT 的竞争对手 Zapier 表现如何呢？

Zapier 并没有提供 iOS 客户端，当然也就无法像 IFTTT 那样实现「影评自动化」例子中的功能了，但是 Zapier 支持 Webhook ，通过 Webhook 我们利用 iOS 上的应用 [Workflow](https://itunes.apple.com/cn/app/workflow-powerful-automation-made-simple/id915249334?mt=8&uo=4&at=10l6nh&ct=ms_inline "Workflow") ，每天执行一下 Wrokflow 脚本，Workflow 会选择出当天的照片，通过调用 Zapier 的 Webhook ，将照片上传到 Dropbox ，这样一样实现了上面提到这个例子的功能。

#### 小结

![](https://cdn.sspai.com/2017/05/16/fe19c0f5845cd44cab76193452f5194a.png)

费用情况对比
------

IFTTT 大部分功能都是免费的，而 Zapier 基础功能相对简单，如若使用上边提到的很多特性都需要付费才能享用。

#### IFTTT

![](https://cdn.sspai.com/2017/05/22/9f81035ec5181625f317ac3c1235e20b.png)Applet：在 IFTTT 中创建的每个任务流称为一个 Applet。

#### Zapier

![](https://cdn.sspai.com/2017/05/16/e6c897920fcdf2e7516bd25ad09d8100.png)Tasks：每个 Zap 里的一个 Action 称为一个 Tasks。

总结
--

通过以上 IFTTT vs. Zapier 我们可以看到，对于这两个粘合剂服务，各自都有自己的特点：

*   IFTTT ，老牌便宜例子多，基本上常用的任务流都会被涵盖，网友可以随意上传自己制作的 Applet 供大家修改和使用。
*   Zapier，IFTTT 优秀的模仿者，在实现了 IFTTT 的大多数功能基础上，对任务流的创建和调试做了很多优化，使用起来更加人性化也更简单；同时，Zapier 还对企业级任务流有着先天的优势，比如他的**多 Service 协作支持** 和 **单 Service 多账户支持**，都为企业级应用和专业用户提供了很好的支持。

以上两个服务都是互联网上最优的粘合剂服务，那么作为一个用户我们该如何选择他们，以及如何看待他们的费用问题，我想大家还是从各自的实际角度出发，这里王掌柜给出几个建议供大家参考：

*   如果你是一个新手入门，没有那么多任务流需求，而且够用就行，建议考虑 IFTTT，简单够用，上手入门容易。
*   如果你是一个老江湖，对与任务流有着复杂的需求，并且随时有着各种各样的想法，想调试和实现，建议考虑 Zapier，他的调试和易用性更高，随时满足你的想法。
*   如果你想在企业或团队中应用，让团队的小伙伴们都能够享受到任务流带来的便利，建议考虑 Zapier ，毕竟他针对企业级任务流做了很多优化，这些是 IFTTT 没有也做不到的。

### One More Thing

**王掌柜带你玩转 Zapier 系列**是一个长期更新的系列，如果你是对 Zapier 感兴趣的同学推荐长期关注，按照惯例，预告一下下一期内容。

#### 一句话描述

每当我开始看一本书的时候，我会通过 [Workflow](https://itunes.apple.com/cn/app/workflow/id915249334?mt=8&uo=4&at=10l6nh&ct=ms_inline "Workflow") 调用豆瓣 API 获取到书籍信息，然后通过 Zapier 设置好的任务流，自动在 [Airtable](https://itunes.apple.com/cn/app/airtable/id914172636?mt=8&uo=4&at=10l6nh&ct=ms_inline "Airtable") 中创建好读书笔记，最后在 Todoist 中创建每日写读书笔记的提醒任务。

**敬请期待......**

### 附录

轮询，IFTTT 和 Zapier 这样的粘合剂服务，通常采用轮询的方式去执行用户设置好的任务流，这种方式也叫作**主动执行**，这样做的话，服务器负载高，运行成本高，但是简单易用维护成本也低。

与之对应的还有一种方式叫**被动执行**，被动执行是指当用户设置好了任务流，每个 Service 在完成某事的时候会主动通知服务器（服务器属于被动执行）来让服务器知道 A 完成了事情 1，准备去让 B 完成事情 2 吧。这样做的话服务器成本低，负载也低，但是开发和维护成本较高。