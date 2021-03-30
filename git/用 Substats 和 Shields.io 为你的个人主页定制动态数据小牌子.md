> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [sspai.com](https://sspai.com/post/59593)

### **Matrix 精选**

[Matrix](https://sspai.com/matrix) 是少数派的写作社区，我们主张分享真实的产品体验，有实用价值的经验与思考。我们会不定期挑选 Matrix 最优质的文章，展示来自用户的最真实的体验和观点。

文章代表作者个人观点，少数派仅对标题和排版略作修改。

如果你浏览过一些 GitHub 的开源项目，你一定见过很多 README 文档中都会出现的五颜六色、各式各样的「小牌子」。

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978643.png) 经常出现在 GitHub 项目里面的小牌子

￼最初，这些「小牌子」的主要作用是为了显示「某个 GitHub 项目」的「某种状态」，比如项目的编译是否成功、文档是否更新至最新、软件的下载数量有多少…… 不过，从原理的角度来说，这些「小牌子」都是通过我们给「牌子渲染服务器」提供一些数据后，服务器返回给我们一个 SVG 格式的图片来工作的。我们将返回的 SVG 图片嵌入到 GitHub 的 README 文档或其他网页里面，就完成了一个「小牌子」的制作。

因此，我们不仅可以将这些「小牌子」用在 GitHub 里，如果你拥有自己的「个人主页」、「博客」或其他展示个人资料的地方，只要你可以控制网页的 HTML，能自己向其中插入一些自定义代码，你就可以借助于「小牌子服务器」来自制一些好看又能实时更新的「个人资料展示牌」、「订阅地址标识牌」等等。

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978664.png) 我自己博客最下方定制的「小牌子」

Shields.io 的基本用法
----------------

### 简单定制静态小牌子

[Shields.io](https://shields.io/) 就是一个我们开头提到的「牌子渲染服务」。事实上，GitHub 上面大部分「小牌子」都是用 Shields.io 来渲染的。我们可以借助于 Shields.io 服务定制一些个性化的「小牌子」。一个最简单的例子就是：[`https://img.shields.io/badge/少数派-SpencerWoo-da282a`](https://img.shields.io/badge/%E5%B0%91%E6%95%B0%E6%B4%BE-SpencerWoo-da282a)，这一请求可以渲染得到如下效果的小牌子。

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978673.png) 少数派 - SpencerWoo-da282a

￼可以发现，简单定制小牌子非常方便，最最基础的语法规则就是：

```
https://img.shields.io/badge/{左半部分标签}-{右半部分标签}-{右半部分颜色}
```

另外，在 [Shields.io 的官网](https://shields.io/)上面有非常方便的「小牌子生成器」，我们可以直接用它来构造一个「小牌子」，只需要按照下图的样子：填入左半边标签、填入右半边标签，再定义右半边的颜色，点击 Make Badge 即可生成。其中，右半边的颜色我们可以用官方提供的几种预设颜色名称（下方 Colors）或者我们自己提供十六进制颜色代码都可以。

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978682.png) Shields.io 的静态小牌子生成器

￼我们按照上面的方法构造一个链接，就制作完成我们的小牌子啦！这样得到的链接是一个 SVG 图片链接，我们可以直接**用插入图片的语法规则加到我们的 Markdown 文本文件中或 HTML 里面。**

### 动态实时更新的小牌子

事实上，我们前面生成的小牌子都是静态的小牌子：其中的文本内容是固定不变的，仅能用于做一个好看的标识。**而事实上，Shields.io 生成的小牌子完全支持动态数据显示，比如粉丝、关注者数量、RSS 订阅者数量……**

如果我们想要显示一些动态变化的数据，那么我们就需要一个受支持的数据接口，这样我们的「Shields.io 小牌子生成服务器」就会先行从这一数据接口请求相应的动态数据，并按照请求的结果将数据渲染成不同的「小牌子」。（具体的语法请继续向下阅读）

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978692.png) Shields.io 静态小牌子与动态小牌子不同的渲染原理

￼但是，虽然 Shields.io 服务 API 的功能非常完善，能够支持许多第三方 API 节点的数据服务，但是有一些服务：

*   API 接口返回复杂，无法直接用 Shields.io 简单处理
*   API 访问缓慢、不稳定，经常出现超时或无法访问的情况
*   访问一些数据需要进行认证，直接访问会返回 403 无权限
*   直接不对外公开 API 接口，没有面向开发者的开放平台，无法直接请求数据

当我们想要显示这些平台的关注者数量、粉丝数量时，往往就力不从心了。**因此，我使用 Serverless 技术实现了「Substats: Subscriber Statistics」—— 一个专注提供多个服务、平台、网站的粉丝、关注、订阅数量的 API 中转站**，用来专门处理这些单靠 Shields.io 不方便直接处理的疑难杂症。

*   Substats 项目开源在：[GitHub - spencerwooo/Substats](https://github.com/spencerwooo/Substats)
*   Substats 的 API 地址位于：[Home - Substats API](https://api.spencerwoo.com/substats/)
*   另外，关于如何调用 Substats API，我还撰写了比较详细的文档：[Substats Documentation](https://substats.spencerwoo.com/)

下面我来简单介绍一下如何利用 Substats 配合 Shields.io 定制小牌子 (•̀ ω •́)✧

用 Substats 配合 Shields.io 制作动态小牌子
--------------------------------

### 通过 Substats API 获取我们期望的数据

为了更好的配合 Shields.io 服务，我特意将 Substats 的 API 设计成简单拼接 URL 即可进行数据请求。Substats API 的语法非常简单，我们只需要关注并提供如下的两个字段即可进行请求：

*   目标服务名称 `source`：你所想要请求的服务、网站和平台名（比如：`sspai`、`weibo`……）
*   请求数据标签 `queryKey`：在这一服务中查询的关注数据对应的标签或名称（比如我的少数派用户名 `spencerwoo`）

这样，我们就可以用这样的语法来拼接一个 URL（注意第一个字符是 `?`，其他用 `&` 拼接）：

```
https://api.spencerwoo.com/substats/?source={目标服务名称}&queryKey={请求数据标签}
```

利用这样的语法，我们就可以进行数据请求啦。继续上面图示中的例子，比如我想要制作一个实时显示我自己的少数派关注数量的小牌子，我拼接成的 URL 即为：

```
https://api.spencerwoo.com/substats/?source=sspai&queryKey=spencerwoo
```

非常方便！这一 URL 会给我们返回类似下面的 JSON 结果：

```
{
  "status": 200,
  "data": {
    "totalSubs": 638,
    "subsInEachSource": {
      "sspai": 638
    },
    "failedSources": {}
  }
}
```

我们可以这样理解返回的 JSON 数据：

*   `status` 是请求是否成功，成功即为 200（表示 HTTP OK）
*   `data` 就是请求返回的数据（其中 `totalSubs` 表示总关注数量，`subsInEachSource` 表示每个服务请求到的粉丝数据，最后 `failedSources` 表示请求失败的数据源。）

可以看到我们所需要的字段即为 `$.data.totalSubs`，也就是 638 —— 我的少数派总关注人数。接下来，我们只需要告诉 Shields.io：

1.  我们请求的 URL 地址
2.  返回数据中所要的字段

这两个参数，即可成功制作一个动态小牌子。

### 用 Shields.io 制作最终动态小牌子

我们继续借助 Shields.io 官网上面提供的「小牌子生成器」，这次我们稍微向下滚动，找到 Dynamic 版本「小牌子生成器」，并按照这样的规则依次操作：

1.  数据类型 `data type` 选择：JSON
2.  标签 `label` 填入：小牌子左侧的标签，比如 `少数派关注`
3.  API 地址 `data url` 填入：我们刚刚的 API URL：`https://api.spencerwoo.com/substats/?source=sspai&queryKey=spencerwoo`
4.  请求字段 `query` 填入：我们 Substats API 数据中的这一字段：`$.data.totalSubs`
5.  标签颜色 `color` 填入：一个十六进制的颜色代码，比如少数派强调色：`da282a`
6.  ……（余下的两个参数：前缀 `prefix` 和后缀 `suffix`，可以根据自己的需要自行定义）

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978702.png) 借助 Shields.io 官方提供的生成器构造动态小牌子

￼这样，我们就借助 Shields.io 构造出来一个自定义的动态 SVG 小牌子（由于我们的请求中包含有 URL 中非法的字符，因此下面这个是 URL 编码之后的 SVG 小牌子地址）：

```
https://img.shields.io/badge/dynamic/json?color=da282a&label=%E5%B0%91%E6%95%B0%E6%B4%BE%E5%85%B3%E6%B3%A8&query=%24.data.totalSubs&url=https%3A%2F%2Fapi.spencerwoo.com%2Fsubstats%2F%3Fsource%3Dsspai%26queryKey%3Dspencerwoo
```

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978712.png) 动态的少数派关注数量小牌子

￼这样，我们的动态小牌子就完整的制作完成啦，我们可以用 Markdown 的语法将这一图片链接嵌入一篇文章之中：

```
![](https://img.shields.io/badge/dynamic/json?color=da282a&label=%E5%B0%91%E6%95%B0%E6%B4%BE%E5%85%B3%E6%B3%A8&query=%24.data.totalSubs&url=https%3A%2F%2Fapi.spencerwoo.com%2Fsubstats%2F%3Fsource%3Dsspai%26queryKey%3Dspencerwoo)
```

或者用 HTML 定义图片的方法直接将这一 SVG 嵌入一个网页：

```
<img src="https://img.shields.io/badge/dynamic/json?color=da282a&label=%E5%B0%91%E6%95%B0%E6%B4%BE%E5%85%B3%E6%B3%A8&query=%24.data.totalSubs&url=https%3A%2F%2Fapi.spencerwoo.com%2Fsubstats%2F%3Fsource%3Dsspai%26queryKey%3Dspencerwoo" />
```

其他 Substats API 的功能和语法规则
------------------------

另外，Substats API 还可以串联多个不同的数据源和它们对应的请求参数。比如，我同时请求少数派、知乎、GitHub 三个平台上面的关注，即可这样构造请求（多个 `source` 和 `queryKey` 组合按照顺序进行请求即可，顺序在请求过程中不会丢失）：

```
https://api.spencerwoo.com/substats/?source=sspai&queryKey=spencerwoo&source=zhihu&queryKey=spencer-woo-64&source=github&queryKey=spencerwooo
```

可以看到，上面的 URL 里，我直接串联了多个 `source` 和 `queryKey` 的请求组合，同时请求。这样我们就可以得到这三个平台上面关注者数量的总和 `totalSubs`，以及每个平台各自的关注者数量 `subsInEachSource`：

```
{
  "status": 200,
  "data": {
    "totalSubs": 1312,
    "subsInEachSource": {
      "sspai": 638,
      "zhihu": 361,
      "github": 313
    },
    "failedSources": {}
  }
}
```

那么，我们就可以直接用 Shields.io 构造一个如下的 SVG 小牌子：

```
https://img.shields.io/badge/dynamic/json?color=0084ff&label=%E5%B0%91%E6%95%B0%E6%B4%BE%7C%E7%9F%A5%E4%B9%8E%7CGitHub&query=%24.data.totalSubs&url=https%3A%2F%2Fapi.spencerwoo.com%2Fsubstats%2F%3Fsource%3Dsspai%26queryKey%3Dspencerwoo%26source%3Dzhihu%26queryKey%3Dspencer-woo-64%26source%3Dgithub%26queryKey%3Dspencerwooo
```

这样我们就可以直接得到三个平台总关注数量的一个「小牌子」：

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978721.png) 少数派、知乎、GitHub 三个平台总关注者数量

￼同时，如果你想同时请求多个平台，但是平台中请求的数据标签名称是一样的，比如我们同时请求 Feedly 和 NewsBlur 两个 RSS 订阅服务里我自己的 RSS 链接 `https://blog.spencerwoo.com/posts/index.xml` 的订阅数量，那么我们可以：

*   直接用 `|` 将 `feedly` 和 `newsblur` 直接连接，传递给 `source` 作为参数
*   并将 RSS 链接传递给 `queryKey` 作为参数

从而构造这样的请求：

```
https://api.spencerwoo.com/substats/?source=feedly|newsblur&queryKey=https://blog.spencerwoo.com/posts/index.xml
```

这样，我们就可以直接得到两个平台同一个 RSS 源的总订阅数量：

```
{
  "status": 200,
  "data": {
    "totalSubs": 17,
    "subsInEachSource": {
      "feedly": 14,
      "newsblur": 3
    },
    "failedSources": {}
  }
}
```

从而制作表示 RSS 链接总订阅人数的「小牌子」：

![](https://cdn.sspai.com/editor/u_spencerwoo/15846965978731.png) 显示 RSS 订阅总人数的动态小牌子

￼简单方便！实在好用！（不瞒大家说，最初开发这一项目，我就是为了显示我自己 RSS 总订阅人数。😂）

小结
--

Substats 是我借助 Serverless 技术构建的一个 Cloudflare Worker，**直接部署在 Cloudflare 全球 CDN 节点上，访问速度非常的快。**因此，如果你使用 Substats 和 Shields.io 来制作「小牌子」，生成 SVG 所消耗的时间的占比较大的部分应该是和原服务 API 沟通的时间（比如 NewsBlur 就特别慢）。

虽然 Substats 仅诞生了不到一周，但是目前在我和其他两位同学的共同努力下，已经支持了包括哔哩哔哩、少数派、知乎、微博、GitHub 等知名网站、社交平台以及 Feedly 和 NewsBlur 两大 RSS 服务提供商。未来我会尽我所能，继续维护，支持更多的平台。

![](https://cdn.sspai.com/2020/03/23/a73844d5b08cb4a9627292f6b62b7714.png) Substats API 目前支持的服务、网站和平台

￼希望这篇文章，以及 Substats API，能帮助你更方便、更轻松的**零成本自制动态实时更新、快速加载**的小牌子，用来装扮你的个人主页、博客、GitHub 项目 README 或其他网页。**如果你觉得 Substats 简直太棒了，那请一定 [前往 GitHub 项目页面](https://github.com/spencerwooo/Substats) 给我一个 🌟 Star！**本文的介绍到这里就结束啦，感谢阅读。

> 下载少数派 [客户端](https://sspai.com/page/client) 、关注 [少数派公众号](https://sspai.com/s/J71e) ，了解更精彩的数字生活 🍃