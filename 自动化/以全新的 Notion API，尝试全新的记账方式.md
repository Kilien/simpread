> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [sspai.com](https://sspai.com/post/66658)

### **Matrix 首页推荐**

[Matrix](https://sspai.com/matrix) 是少数派的写作社区，我们主张分享真实的产品体验，有实用价值的经验与思考。我们会不定期挑选 Matrix 最优质的文章，展示来自用户的最真实的体验和观点。

文章代表作者个人观点，少数派仅对标题和排版略作修改。

> The wait is over, Notion API is in public beta!

——在 Notion 发来的邮件中如是写道。

是的，终于，Notion API 开放了公测，现在所有用户都可以去 Notion 创建自己的应用，并充分利用 Notion 提供的 API 来构筑完全属于自己的工作流。

也是为了体验 Notion API 带来的快乐，我尝试用它制作了一个记账小工具，可以方便导入支付宝、微信的账单，并对这些账目进行管理和核对。

在 Notion 中设计数据表
---------------

在当前 Notion API 的设计中，数据表的创建是在 Notion 网页端或者客户端完成，并不能直接通过 API 来实现。

在我的记账表格中，相关的数据表字段如下：

![](https://cdn.sspai.com/2021/05/16/253796374fb7d31938a6247d48224948.png)

价格是一个 Number 字段，格式选择了 Yuan；标签是一个 Multi-select 字段，按自己的分类需求，对账目进行简单、快速而准确的标记；时间是 Date 字段，并选择了更符合中国人习惯的显示格式；来源是一个文本；创建于、更新于分别对应 Created time 和 Last edited time，由表格自动生成；星期是一个 Formula 字段，内容如下：

```
replace(replace(replace(replace(replace(replace(replace(formatDate(prop("时间"), "d"), "6", "星期六"), "5", "星期五"), "4", "星期四"), "3", "星期三"), "2", "星期二"), "1", "星期一"), "0", "星期日") + ((formatDate(prop("时间"), "HHmm") == "0000") ? "" : (" | " + ((hour(prop("时间")) <= 7) ? "凌晨" : ((hour(prop("时间")) <= 10) ? "早上" : ((hour(prop("时间")) <= 15) ? "中午" : ((hour(prop("时间")) <= 21) ? "晚上" : "夜里"))))))

```

这个公式可以根据前面的「时间」字段，从日期中取得星期，再从时间中取得时间段，便于快速查看。例如，2021/05/13 会提取成为「星期四」，而 8:52 则提取为「早上」。

账单条目的内容则填在页面标题中。

创建 Notion 机器人
-------------

我们在使用 Notion API 时，并不是以「账户」身份登录，来操作所有的数据表；而是通过创建一个个的机器人（称为 integration），每个机器人分别来完成不同的事务，并根据每个机器人所需涉及的数据表，分别对每个机器人进行访问授权。

在机器人 [管理页面](https://www.notion.so/my-integrations) 中，只需要填入机器人名字，就可以快速创建一个机器人。

![](https://cdn.sspai.com/2021/05/16/7c75c9c3265488c18820a59a4ea6e413.png)

提交（Submit）后，系统会给出一个复杂的密钥，点击 Show 记录。这个密钥可以随时在前述机器人管理页面中查看。

![](https://cdn.sspai.com/2021/05/16/db7e510fdb55402e0e12ede832ef356b.png)

创建完成后，回到之前的数据表中，点击 Share 并选择 Invite，把我们前面创建的机器人邀请进我们的数据表中。

![](https://cdn.sspai.com/2021/05/16/596564aa8f8af83f06604033ec542bd9.png)

这样，我们的机器人就可以访问并编辑这个数据表了。

Notion API 概述
-------------

在 Notion [开发者网站](https://developers.notion.com/) 中列举了 Notion 当前支持的所有 API 接口。Notion API 遵循 RESTful 设计风格，目前可以操作的资源及其对应的操作类型包括：

*   数据表 Database
    *   获取指定数据表的字段等信息
    *   过滤、排序后输出指定数据表的内容
    *   查询所有允许操作的数据表
*   页面 Page
    *   获取指定页面中各字段的值
    *   创建新页面
    *   更新指定页面中各字段的值
*   块 Block
    *   列举指定页面中的所有块
    *   在指定页面中创建新的块
*   用户 User
    *   查看指定用户的信息
    *   查看工作区中所有用户的信息（包括机器人）
*   搜索 Search
    *   在有权限的范围内执行全局搜索

所有涉及数据的内容，无论是请求体还是响应体，都以 JSON 的形式呈现，十分优雅。

借助 Python 访问 Notion API
-----------------------

由于 Notion API 基于 HTTP 提供，因此可以使用 Python 的 [requests](https://docs.python-requests.org/en/master/) 模块进行简单开发。

为了便于编写消息体，有一个小技巧是，可以先通过「[获取指定页面中各字段的值](https://developers.notion.com/reference/get-page)」这一接口来拉取页面结构。

先在之前创建的数据表中手动填写一条内容，并复制该页面的网址中的 ID 部分，也就是下图中划线的部分。

![](https://cdn.sspai.com/2021/05/16/9811651cfeb67bbff0ffdf751ea3ed59.png)

现在，就可以通过接口来获取这个页面的内容了。Python 代码如下：

```
r = requests.request(
    "GET",
    "https://api.notion.com/v1/pages/4a2c9f40c27e478fba4de58af0787a69",
    headers={"Authorization": "Bearer " + token, "Notion-Version": "2021-05-13"},
)
print(r.text)

```

得到的结果如下：

```
{
    "object": "page",
    "id": "4a2c9f40-c27e-478f-ba4d-e58af0787a69",
    "created_time": "2021-05-13T01:34:08.030Z",
    "last_edited_time": "2021-05-16T00:03:00.000Z",
    "parent": {
        "type": "database_id",
        "database_id": "7b093d33-7d89-40c0-8985-86be964a3fc4"
    },
    "archived": false,
    "properties": {
        "标签": {
            "id": "+z@-",
            "type": "multi_select",
            "multi_select": [
                {
                    "id": "0bd6b895-bf66-4a6a-ae99-13b162b02715",
                    "name": "🚦交通",
                    "color": "default"
                }
            ]
        },
        "时间": {
            "id": ":Wx1",
            "type": "date",
            "date": { "start": "2021-05-13T08:52:00.000+08:00", "end": null }
        },
        "价格": { "id": "==wp", "type": "number", "number": 7.8 },
        "来源": {
            "id": "TeSe",
            "type": "rich_text",
            "rich_text": [
                {
                    "type": "text",
                    "text": { "content": "支付宝", "link": null },
                    "annotations": {
                        "bold": false,
                        "italic": false,
                        "strikethrough": false,
                        "underline": false,
                        "code": false,
                        "color": "default"
                    },
                    "plain_text": "支付宝",
                    "href": null
                }
            ]
        },
        "星期": {
            "id": "dQj*",
            "type": "formula",
            "formula": { "type": "string", "string": "星期四 | 早上" }
        },
        "更新于": {
            "id": "qeNP",
            "type": "last_edited_time",
            "last_edited_time": "2021-05-16T00:03:00.000Z"
        },
        "创建于": {
            "id": "{pT[",
            "type": "created_time",
            "created_time": "2021-05-13T01:34:08.030Z"
        },
        "内容": {
            "id": "title",
            "type": "title",
            "title": [
                {
                    "type": "text",
                    "text": { "content": "滴滴快车", "link": null },
                    "annotations": {
                        "bold": false,
                        "italic": false,
                        "strikethrough": false,
                        "underline": false,
                        "code": false,
                        "color": "default"
                    },
                    "plain_text": "滴滴快车",
                    "href": null
                }
            ]
        }
    }
}

```

为了记录一条新的账单条目，可以调用「[创建新页面](https://developers.notion.com/reference/post-page)」的接口。事实上，「创建新页面」的请求体，和前面这个「获取指定页面中各字段的值」接口的响应体几乎完全一致，因此稍作修改就可以直接调用「创建新页面」的接口了。

消息体的内容为：

```
body = {
    "parent": {"type": "database_id", "database_id": "7b093d33-7d89-40c0-8985-86be964a3fc4"},
    "properties": {
        "标签": {"multi_select": {"name": "🚦交通"}},
        "时间": {"date": {"start": arrow.get(time).to("+08").isoformat()}},
        "价格": {"number": 7.8},
        "来源": {"rich_text": [{"text": {"content": "支付宝"}}]},
        "内容": {"title": [{"type": "text", "text": {"content": "滴滴快车"}}]},
    },
}

```

将这个请求体和前面接口的响应体对比发现，请求体中所有字段全部来源于响应体，并且可以将响应体中不需要填写的、繁复的细节去除，而只留下我们关心的内容。这些细节会由 Notion 后台自动补足。

对应的 Python 请求代码如下：

```
requests.request(
    "POST",
    "https://api.notion.com/v1/pages",
    json=body,
    headers={"Authorization": "Bearer " + token, "Notion-Version": "2021-05-13"},
)

```

请求完成后，就可以在网页端或客户端的 Notion 上看到已经新增了一条记录。

从支付宝和微信导出账单
-----------

与 Notion 交互的部分已经完成，最后就是获取账单了。因为我日常的消费基本都是从支付宝或者微信走，所以统计的时候只需要导入微信和支付宝的账单即可。如果有零星使用现金或信用卡的，可以手动在 Notion 中记录。

微信的账单导出方法为：

1.  打开微信 APP
2.  点击「我」
3.  点击「支付」
4.  点击「钱包」
5.  点击「账单」
6.  点击「常见问题」
7.  点击「下载账单」
8.  点击「用于个人对账」
9.  选择「账单时间」
10.  输入「邮箱地址」
11.  输入「支付密码」

稍候，账单就会发送到你的邮箱中。解压后是一个类似这样的逗号分隔（csv）文件：

```
微信支付账单明细,,,,,,,,
微信昵称：[...],,,,,,,,
起始时间：[2021-04-26 00:00:00] 终止时间：[2021-04-28 22:29:42],,,,,,,,
导出类型：[全部],,,,,,,,
导出时间：[2021-04-28 22:29:59],,,,,,,,
,,,,,,,,
共5笔记录,,,,,,,,
收入：0笔 0.00元,,,,,,,,
支出：5笔 15.00元,,,,,,,,
中性交易：0笔 0.00元,,,,,,,,
注：,,,,,,,,
1. 充值/提现/理财通购买/零钱通存取/信用卡还款等交易，将计入中性交易,,,,,,,,
2. 本明细仅展示当前账单中的交易，不包括已删除的记录,,,,,,,,
3. 本明细仅供个人对账使用,,,,,,,,
,,,,,,,,
----------------------微信支付账单明细列表--------------------,,,,,,,,
交易时间,交易类型,交易对方,商品,收/支,金额(元),支付方式,当前状态,交易单号,商户单号,备注
2021-04-28 08:28:47,商户消费,花小猪打车,"滴滴出行服务",支出,¥1.60,中信银行,支付成功,420000101320	,Gx	,"/"
2021-04-27 09:03:47,商户消费,花小猪打车,"滴滴出行服务",支出,¥4.80,中信银行,支付成功,420000101020	,fx	,"/"
2021-04-26 13:28:02,商户消费,花小猪打车,"滴滴出行服务",支出,¥3.50,中信银行,支付成功,420000100520	,hx	,"/"
2021-04-26 11:51:41,商户消费,花小猪打车,"滴滴出行服务",支出,¥3.50,中信银行,支付成功,420000100220	,7x	,"/"
2021-04-26 08:35:30,商户消费,花小猪打车,"滴滴出行服务",支出,¥1.60,中信银行,支付成功,420000100820	,ux	,"/"

```

在文件的前面几行都是一些统计信息，我们并不需要，因此只保留后半部分账单明细的内容。为了从中取出我们需要填入 Notion 中的内容，相应的 Python 代码可以是：

```
def wechat(filepath):
    with open(filepath, "r", encoding="utf-8-sig", newline="") as f:
        lines = f.readlines()
        striped_lines = []
        start = False
        for line in lines:
            if not start:
                if line.startswith("----------------------"):
                    start = True
                continue
            striped_lines.append(line.strip())

        csvreader = csv.DictReader(striped_lines)
        for row in csvreader:
            t = arrow.get(row["交易时间"]).replace(tzinfo="+08").datetime
            c = row["商品"] + "，" + row["交易类型"] + "，" + row["交易对方"]
            a = row["金额(元)"]
            d = row["收/支"]
            print(t, c, a, d)
            if d == "收入":
                a = "-" + a[1:]
            elif d == "支出":
                a = a[1:]
            else:
                print("[未被计入]")
                continue
            Notion.add_bill(t, c, a, "微信")

```

在这里，t、c、a 分别代码时间、内容、金额，而 Notion.add_bill 则是在前面一节所编写的与 Notion API 交互的函数。

与此相似的，支付宝也可以导出指定时间范围内的账单明细：

1.  在桌面浏览器中访问支付宝的 [账单页](https://consumeprod.alipay.com/record/standard.htm)
2.  使用手机支付宝 APP 扫码登录
3.  选择账单时间区间
4.  点击「下载查询结果」

支付宝的账单下载、解压后也是一个逗号分隔文件。将其用 Python 解析后，可以使用同样的 Notion.add_bill 函数处理并同步到 Notion 中。处理过程如下：

```
def alipay(filepath):
    with open(filepath, "r", encoding="gbk", newline="") as f:
        lines = f.readlines()
        striped_lines = []
        start = False
        for line in lines:
            if not start:
                if line.startswith("----------------------------"):
                    start = True
                continue
            if line.startswith("----------------------------"):
                break
            l = regex.sub(r"\s+,", ",", line)
            striped_lines.append(l)

        csvreader = csv.DictReader(striped_lines)
        for row in csvreader:
            t = arrow.get(row["交易创建时间"]).replace(tzinfo="+08").datetime
            c = row["商品名称"] + "，" + row["交易对方"]
            a = row["金额（元）"]
            d = row["资金状态"]
            print(t, c, a, d)
            if a == "0":
                print("[未被计入]")
                continue
            elif d == "已收入" or d == "解冻":
                a = "-" + a
            elif d == "已支出" or d == "冻结":
                pass
            else:
                print("[未被计入]")
                continue
            Notion.add_bill(t, c, a, "支付宝")

```

相关链接
----

致敬 [notion-py](https://github.com/jamalex/notion-py)，它在官方 Notion API 鸽了又鸽的时光里为我们带来了便利：

所有机器人有统一的管理页面和申请入口：[https://www.notion.so/my-integrations](https://www.notion.so/my-integrations)

Notion 的 API 文档优雅而简洁：[https://developers.notion.com/reference/intro](https://developers.notion.com/reference/intro)

Notion 官方提供了 Node.js 的 SDK，不过便利性提升有限，举个例子：

```
 await notion.pages.create({
    parent: {
        // ...
    },
    properties: {
        标签: {
            multi_select: [
                {
                    name: '🚦交通',
                },
            ],
        },
        时间: {
            // ...
        },
        // ...
    },
});

```

可见，body 部分依然需要自己组装，无非是把 request 的请求换成了一个 notion.pages.create 直接调用，如果感兴趣的也可以尝试这个 [SDK](https://github.com/makenotion/notion-sdk-js)。

如果不想自己亲自开发，也可以使用已经集成了 Notion 的低代码方案，例如 [Zapier](https://zapier.com/apps/notion/integrations)

总体而言，Notion API 还是带来了足够的惊喜。我也用过 notion-py 这样的非官方第三方库，相比之下，官方 API 提升了接口的稳定性、可靠性、易用性，也提供了更丰富的功能和更方便的调用方式。

不过，官方 API 库目前依然是 Beta 版本，许多功能尚未实现，例如文章正文中的 Block 只能添加（Append）而不能修改、数据表的字段必须预先创建而无法动态调整、机器人对数据表的权限必须「读写」而不能设为「只读」。相信 Notion API 在经过几次迭代之后就会变得足够好用，也会令 Notion 的生产力更进一级。

此外，除了可以为自己的 Notion 构建私有机器人，Notion 还允许将机器人设为公开，使所有用户都可以使用你的机器人。随着开发者们的加入，这样社区化的运营方式，必会诞生许多基于 Notion 的有趣应用。

> 下载 [少数派 2.0 客户端](https://sspai.com/page/client)、关注[少数派公众号](https://sspai.com/s/J71e)，让你的工作更有效率 ⏱

> 实用、好用的 [正版软件](https://sspai.com/mall)，少数派为你呈现🚀