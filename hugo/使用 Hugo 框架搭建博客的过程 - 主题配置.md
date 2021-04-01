> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/langyao/p/14285886.html)

前言
--

博客部署完成后，恭喜你可以发表第一篇：Hello world！但是 **LoveIt** 这么好用的主题，不配置一番可惜了。

基本功能配置
------

主题配置最好参考已有的配置，比如 LoveIt 作者写的[介绍](https://hugoloveit.com/zh-cn/theme-documentation-basics/)，还有主题目录下的配置文件`\themes\LoveIt\exampleSite\config.toml` 文件。

笔者认为一些配置项解释的不够清楚，所以将网站的[源码](https://github.com/lang-yao/LoveIt-extend)放在了 Github 上，仅供参考。

下面介绍其中一些配置。

### 双语言配置

配置后需要每篇文章存在多个语言的文件，否则会报错。

例如：`content\about\index.en.md`、`content\about\index.zh-cn.md`

### Gravatar 头像

[gravatar 头像注册](https://en.gravatar.com/)，需要使用 wordpress 帐号，注册帐号时，有些邮箱的邮件会被过滤，使用 163 邮箱等了 1 个多小时方才收到注册的邮件。

### 图片画廊功能

在配置文件 `config.toml` 中开启 lightgallery

```
# 是否使用 lightgallery
lightgallery = true


```

或者在文章的头部参数中设置 `lightgallery: true`  
最后文章中的图片引用格式为：`![weichat](/images/weichat-logo_500px.png " 公众号 ")`，注意路径后面要加 " 内容 "。

### 搜索配置

使用 algolia 作为搜索引擎，因为 lunr 的加载速度会让你等到花都谢了。虽然 algolia 需要上传 index.json，但是可以使用 [Algolia Atomic](https://github.com/chrisdmacrae/atomic-algolia) 简化操作。

### 评论系统设置

国内不能用 disqus，不过还有 [Valine 评论系统](https://valine.js.org/)。留言可以设置邮件提醒功能，但是 LeanCloud 的云引擎域名需要使用自己的域名并配置 DNS 解析。

### 社交信息设置

首页的社交信息，不同语言的界面，可分别设置。

### 社交信息拓展

以微信公众号为例。  
在 `config.toml` 的社交信息中添加

```
# 作者的社交信息设置
[social]
    ...
    Wechat = "https://img.xiaodejiyi.com/img/wechat%20logo_500px.png"
    ...


```

配置 `themes\LoveIt\assets\data\social.yml`:

```
# 064: wechat
wechat:
  # weight 值排序
  Weight: 2
  Title: 公众号
  Newtab: true
  Icon:
    Simpleicons: wechat


```

其中图标可参考其他形式，如：

```
# Src 形式
cnblog:
  Weight: 1
  Prefix: https://www.cnblogs.com/
  Title: 博客园
  Icon:
  # themes\LoveIt\assets\svg\icons\cnblog.svg
    Src: svg/icons/cnblog.svg
# fontawesome class 形式
mastodon:
  Weight: 56
  Prefix: https://mastodon.social/
  Title: Mastodon
  Icon:
    Class: fab fa-mastodon fa-fw
# Simpleicons
googlescholar:
  Weight: 54
  Template: https://scholar.google.com/citations?%v
  Title: Google Scholar
  Icon:
  # themes\LoveIt\assets\lib\simple-icons
    Simpleicons: googlescholar


```

### 使用站长工具，向搜索引擎提交网站地图

让搜索引擎收录网站内容。

*   百度搜索资源平台  
    [https://ziyuan.baidu.com/site/index#/](https://ziyuan.baidu.com/site/index#/)
    
*   Google search console  
    [https://search.google.com/search-console/about?hl=zh-CN](https://search.google.com/search-console/about?hl=zh-CN)
    

```
# 网站验证代码，用于 Google/Bing/Yandex/Pinterest/Baidu
[verification]
	google = "xxxxxxxxxxxxxxxx"
	bing = ""
	yandex = ""
	pinterest = ""
	baidu = "code-xxxxxxx"


```

网站统计与分析
-------

### 网站流量分析

分析网站点击流量，访客 IP 等数据。

1.  Google Analytics
2.  百度统计

注册后，需要先添加 DNS 解析，验证域名所有权，可能会与其他解析记录存在冲突。

解决方法，暂停其他解析，验证所有权通过后，在网站分析中配置 ID，最后删除验证的 DNS 解析，重新开启其他冲突的解析记录。

```
#  Google 网站分析配置
[analytics]
	enable = true
	# Google Analytics
	[analytics.google]
		id = "G-xxxxxxx"
		# 是否匿名化用户 IP
		anonymizeIP = true


```

百度统计需要在网站代码中加入百度的统计代码，可以在 `themes\LoveIt\layouts\partials\plugin\analytics.html` 中添加以下代码。

```
{{- /* baidu Analytics */ -}}
<script>
    var _hmt = _hmt || [];
    (function() {
        var hm = document.createElement("script");
        # 需要修改为自己的 url
        hm.src = "https://hm.baidu.com/hm.js?9c04b6d35915817e67da8ad2fdcfbfdf";
        var s = document.getElementsByTagName("script")[0]; 
        s.parentNode.insertBefore(hm, s);
    })();
</script>
# 下面网站访问数量统计中，友盟+和 51LA 也可以加在这里。
{{- /* 51la Analytics */ -}}
<script type="text/javascript" src="//js.users.51.la/21009067.js"></script>


```

### 网站访问数量统计

对比样式之后，选择了 51LA 统计。也可以用 JS 修改统计的样式。

这三个访问统计都需要在网站代码中加入统计的 JS 代码。注册后，获取 JS 统计代码，可以和**网站流量分析**中百度分析一样加到 `themes\LoveIt\layouts\partials\plugin\analytics.html` 中。

1.  不算子  
    样式：  
    本文总阅读量 929966 次  
    本站总访问量 3152598 次  
    本站总访客数 672421 人
    
2.  友盟 +  
    互联网数据服务平台缔元信和 CNZZ 合并成为友盟 +。  
    样式：  
    站长统计 | 今日 IP[43] | 今日 PV[191] | 昨日 IP[31] | 昨日 PV[133] | 当前在线 [5]
    
3.  51LA  
    样式：  
    总访问量 21,195，本月访问量 2,820，昨日访问量 93，今日访问量 103，当前在线 4
    

### 分类页文章总数

在 `themes\LoveIt\layouts\_default\section.html` 中添加以下代码：

```
<!-- articles -->
<span style="font-size:.8rem;font-weight:500;">
    {{- len ( where .Site.RegularPages "Section" "posts" ) | dict "Nums" | T "totalPageNums" -}}
</span>


```

**T** 和 **i18n** 函数是翻译函数，按照不同的语言，使用对应语言的字符串。参考 [i18n](https://gohugo.io/functions/i18n/)

i18n 配置为：

```
# themes\LoveIt\i18n\zh-CN.toml
[totalPageNums]
other = " 共 {{ .Nums }} 篇文章 "

# themes\LoveIt\i18n\en.toml
[totalPageNums]
other = " {{ .Nums }} articles"


```

### 网站总字数统计

参考 [Hugo 总文章数和总字数](https://immmmm.com/hugo-total-count/)。

底部链接设计
------

### 关于知识共享许可协议

可以看这篇 [“知识共享”（CC 协议）简单介绍](https://zhuanlan.zhihu.com/p/20641764)，笔者最终决定采用：知识共享署名 - 非商业性使用 - 相同方式共享 4.0 国际许可协议。

### 网站运行时间

在 `themes\LoveIt\layouts\partials\footer.html` 中加入以下代码。

```
{{- /* Hugo and LoveIt */ -}}
{{- if ne .Site.Params.footer.hugo false -}}
    <div class="footer-line">
        # 运行时间在这里
        <span id="timeDate">{{ T "worktime" }} | </span>
        <script>
            var now = new Date();
            function createtime() {
                var start_time= new Date("09/16/2020 00:00:00");
                now.setTime(now.getTime()+250);
                days = (now - start_time ) / 1000 / 60 / 60 / 24; dnum = Math.floor(days);
                var worktime = document.getElementById("timeDate").innerHTML.replace(/time/, Math.floor(days));
                document.getElementById("timeDate").innerHTML = worktime ;
            }
            createtime();
        </script>
        {{- $hugo := printf `<a href="https://gohugo.io/" target="_blank" rel="noopener noreffer" title="Hugo %v">Hugo</a>` hugo.Version -}}
        {{- $theme := .Scratch.Get "version" | printf `<a href="https://github.com/dillonzq/LoveIt" target="_blank" rel="noopener noreffer" title="LoveIt %v"><i class="far fa-kiss-wink-heart fa-fw"></i> LoveIt</a>` -}}
        {{- dict "Hugo" $hugo "Theme" $theme | T "poweredBySome" | safeHTML }}
    </div>
{{- end -}}


```

i18n 配置为：

```
# themes\LoveIt\i18n\zh-CN.toml
[worktime]
other = " 运行 time 天 "

# themes\LoveIt\i18n\en.toml
[worktime]
other = "Almost time days."


```

### 小徽章

![](https://img.shields.io/badge/-langyao-orange)

如果你喜欢这样的小徽章，前往 [shield](https://shields.io/) 进行 DIY 吧！参考[动态小牌子制作](https://sspai.com/post/59593)

第三方库配置
------

使用 **jsdelivr** 加速第三方库文件的加载。

LoveIt 主题对 cdn 文件的加载过程是这样的。

1.  配置文件中补充 cdn 文件名称，可以直接复制主题的 cdn 文件到 blog 的 `assets/data/cdn/`目录下。

```
[params.cdn]
    # CDN 数据文件名称, 默认不启用
    # ("jsdelivr.yml")
    # 位于 "themes/LoveIt/assets/data/cdn/" 目录
    # 可以在你的项目下相同路径存放你自己的数据文件:
    # "assets/data/cdn/"
    data = ""


```

2.  `themes\LoveIt\layouts\partials\init.html` 中读取 cdn 文件中的数据，`.Scratch.Set "cdn" $cdn` 设置全局变量，之后在其他文件中使用`.Scratch.Get "cdn"` 获取 cdn 数据。
3.  `themes\LoveIt\layouts\partials\assets.html` 将 cdn 中的第三方库渲染后，追加在页面结尾部分。

### 调用 JS 的三种方法

1.  查找 jsdelivr 已有的第三方库，加入 jsdelivr.yml 中。
2.  在 `themes\LoveIt\layouts\partials\assets.html` 中添加 jquery.min.js，需要 jquery 文件位于`assets\js\jquery.min.js`。

```
{{- /* custom jquery */ -}}
{{- $source := $cdn.jqueryJS | default ( resources.Get "js/jquery.min.js" ) -}}
{{- dict "Source" $source "Fingerprint" $fingerprint | dict "Scratch" .Scratch "Data" | partial "scratch/script.html" -}}


```

3.  配置文件中添加第三方库配置

```
#  第三方库配置
[page.library]
    [page.library.css]
    # someCSS = "some.css"
    # 位于 "assets/"
    # 或者
    # someCSS = "https://cdn.example.com/some.css"
    # css 路径：assets\css\custom.css
    customCSS = "css/custom.css"

    [page.library.js]
    # someJavascript = "some.js"
    # 位于 "assets/"
    # 或者
    # someJavascript = "https://cdn.example.com/some.js"

    customJS = "js/custom.js"


```

完成以上配置后，可满足很多功能需求。但如果要拓展主题功能，像[分类](https://www.xiaodejiyi.com/memories/)，[列表页面](https://www.xiaodejiyi.com/websites/)，则需要学习 Hugo 语法。

参考
--

*   [LoveIt 主题文档](https://hugoloveit.com/zh-cn/theme-documentation-basics/)
*   [LoveIt-extend](https://github.com/lang-yao/LoveIt-extend)
*   [Hugo 帮助文档](https://gohugo.io/getting-started/)