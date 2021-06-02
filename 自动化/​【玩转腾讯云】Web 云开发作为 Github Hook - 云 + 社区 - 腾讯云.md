> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [cloud.tencent.com](https://cloud.tencent.com/developer/article/1619759)

> Github 为我们提供了 webHooks，它类似于发布订阅模式，它订阅了 GitHub.com 上的某些事件。当这些事件之一被触发时，将向 WebHook 的配置 UR...

我们通常会有需求：将新 push 到 Github 上的代码自动触发其他事件

Github 为我们提供了 webHooks，它类似于发布订阅模式，它订阅了 GitHub.com 上的某些事件。当这些事件之一被触发时，将向 WebHook 的配置 URL 发送 _HTTP POST payload_。

例如 我们向 Github 新 push 上了代码，webHooks 就会监听到这个 push 事件，随后向配置的 URL 发送 HTTP POST payload

[webHooks](https://developer.github.com/webhooks/) 文档戳这

而[云开发](https://cloud.tencent.com/product/tcb?from=10680)中的云函数刚好匹配这一需求，当我们在云上部署一个云函数并为其创建一个 HTTP 触发路径，顾名思义通过这个路径可以触发对应的云函数。所以我们可以将 webHooks 与云函数进行结合~

> push 到 Github => webHooks 监听到 push 事件 => webHooks 通过配置的 URL 触发云函数 => 在云函数中触发事件

在对大概流程有一个了解后，来进行具体的实践操作~

### 开发前准备

我们需要用到一只 [node.js](http://nodejs.cn/)，一只 @cloudbase/cli

[@cloudbase/cli](https://docs.cloudbase.net/cli/intro.html#an-zhuang-cloudbase-cli) 是一个开源的命令行界面交互工具，用于帮助用户快速、方便的部署项目，管理云开发资源。

#### 安装 node

直接去官网 Download 就可以啦~

#### 安装 @cloudbase/cli

`npm i @cloudbase/cli -g`

### 开通云环境

既然我们要用到云函数，那么它当然需要有个云环境~

我们进入到[云开发](https://console.cloud.tencent.com/tcb/env/index), 新建一个环境~ 如果还没有开通云环境可以勾选**开启免费资源**

**注： 每个账户只能开通一个享有免费资源的环境**

![](https://ask.qcloudimg.com/http-save/4093852/hkw58ap2uf.png)

创建后会进行自动初始化环境 (大概 2-3 分钟)~

我们可以使用 cli 工具进行查看环境状态也可以在[控制台](https://console.cloud.tencent.com/tcb/env/index)进行查看

我们使用 cli 命令 `tcb env:list` 来进行查看环境状态

![](https://ask.qcloudimg.com/http-save/4093852/sx41bbmudv.png)

当环境状态变为正常就代表初始化已经完成可以正常使用啦~

### 初始化云开发项目

使用命令 `tcb init` 创建一个云开发项目

```
$ tcb init
√ 选择关联环境 · xxxx - [xxx-xxxx:空] 
√ 请输入项目名称 · cloudFunction
√ 选择开发语言 · Node 
√ 选择云开发模板 · Hello World
√ 创建项目 cloudFunction 成功！

```

创建完成的云开发目录结构如下?

```
.        
├── functions 
      └─app  
├── .editorconfig 
├── .gitignore 
├── cloudbaserc.js 
└── README.md

```

在 **functions** 中一个目录代表一个云函数，我们创建完成会自动为我们添加一个名为 app 的云函数

我们可以将 app 修改一下，当然也可以新建一个云函数~

将 app 修改为 webHooks

将云函数的入口文件也就是 index.js 添加一个日志输出?

```
exports.main = async (event) => {
    console.log('触发了')
}

```

### 部署云函数

此时的云函数还在本地，我们需要将它部署到云端。通过命令`tcb functions:deploy webHooks`

```
$ tcb functions:deploy webHooks
? 未找到函数发布配置，是否使用默认配置（仅适用于 Node.js 云函数） Yes
√ [webHooks] 云函数部署成功！

```

部署后我们可以通过命令查看云函数详情 `tcb functions:detail webHooks -e 环境ID`

注： 环境 ID 可通过命令 `tcb env:list` 查看

```
$ tcb functions:detail webHooks -e 对应的环境ID
云函数 [webHooks] 详情：

┌─────────────────────┬─────────────────────┐
│        信息         │         值          │
├─────────────────────┼─────────────────────┤
│       环境 Id       │     对应的环境ID    │
├─────────────────────┼─────────────────────┤
│      函数名称       │      webHooks       │
├─────────────────────┼─────────────────────┤
│        状态         │      部署完成       │
├─────────────────────┼─────────────────────┤
│    代码大小（B）    │         220         │
├─────────────────────┼─────────────────────┤
│ 环境变量(key=value) │         无          │
├─────────────────────┼─────────────────────┤
│      执行方法       │     index.main      │
├─────────────────────┼─────────────────────┤
│    内存配置(MB)     │         256         │
├─────────────────────┼─────────────────────┤
│      运行环境       │      Nodejs8.9      │
├─────────────────────┼─────────────────────┤
│     超时时间(S)     │          3          │
├─────────────────────┼─────────────────────┤
│      网络配置       │         无          │
├─────────────────────┼─────────────────────┤
│       触发器        │         无          │
├─────────────────────┼─────────────────────┤
│      修改时间       │ 2020-04-23 16:07:51 │
├─────────────────────┼─────────────────────┤
│    自动安装依赖     │        TRUE         │
└─────────────────────┴─────────────────────┘

函数代码（Java 函数以及入口大于 1 M 的函数不会显示）


exports.main = async (event) => {
    console.log('触发了')
}

```

### 创建 HTTP 触发路径

当云函数部署完成后我们怎样才能触发这个函数呢？

我们需要为它创建一个触发路径，每当我们进入到这个 URL 都会触发这个云函数

通过命令 `tcb service:create -f webHooks -p /webHooks` 为云函数创建一个 HTTP 触发路径

```
tcb service:create -f webHooks -p /webHooks     
√ 云函数 HTTP Service 创建成功！

```

随后在控制台会生成一个连接，这个连接就是触发这个云函数的路径?

![](https://ask.qcloudimg.com/http-save/4093852/q4lxv0hywz.png)

### 创建 webHooks

我们云函数搞定之后剩下的就是 webHooks 的创建啦~

我们进入到对应的 Github 仓库中，点击 Setting，并 ADD 一个 webhook

![](https://ask.qcloudimg.com/http-save/4093852/9xy1ss0637.png)

![](https://ask.qcloudimg.com/http-save/4093852/so912wclmw.png)

添加完成后我们可以进行测试喽~

### 测试

向你的 Github 上进行 push 操作

随后在[云开发](https://console.cloud.tencent.com/tcb/scf/index)控制台内查看对应云函数的日志

![](https://ask.qcloudimg.com/http-save/4093852/cajc9a1nx2.png)

发现打印出来了 ‘触发了’ 三个字~，说明成功实现了对 push 操作的监听，并触发云函数~

### 总结

总结一句话?

将 webhooks 的 URL 配置到**云函数**的 HTTP 触发路径即可实现监听~

原创声明，本文系作者授权云 + 社区发表，未经许可，不得转载。

如有侵权，请联系 yunjia_community@tencent.com 删除。