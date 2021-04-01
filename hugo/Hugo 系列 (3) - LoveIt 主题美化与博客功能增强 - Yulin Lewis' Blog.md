> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [lewky.cn](https://lewky.cn/posts/hugo-3.html/)

本博客使用的是 Hugo 的 LoveIt 主题，本文也是基于该主题而写的，不过 Hugo 的美化步骤应该大同小异，版本如下：

```
hexo: 3.8.0

hugo: v0.74.2/extended windows/amd64 BuildDate: unknown

LoveIt: v0.2.10
```

**请注意，本文的所有功能都离不开两个新增加的文件：`_custom.scss`和`custom.js`，部分功能还需要`jquery`，下文会提及如何引入。**

LoveIt 主题提供了一个自定义的`_custom.scss`，可以在该文件里添加自定义的 css 样式。该文件目录位于`\themes\LoveIt\assets\css\_custom.scss`，不建议直接在该文件里写 css 代码。

Hugo 在渲染页面时优先读取站点根目录下的同名字的目录和文件，所以可以利用这个特点来美化主题。**只需要把想修改的主题模板文件拷贝到根目录下同样的目录中并进行修改，这样就可以在不改动原本的主题文件的情况下实现主题美化。**

首先在站点根目录下创建一个自定义的文件：`\assets\css\_custom.scss`，这样 Hugo 就会最终以该文件来渲染页面的样式。

这是我个人站点的 [css 文件](https://lewky.cn/css/style.min.css)，有兴趣的可以看看。

这里有个很关键的点，只有使用的是扩展版本的 Hugo，才能令`_custom.scss`文件生效！！！因为原生的 Hugo 并不支持编译 sass 文件，必须使用扩展版本的 Hugo 才行。

所以请查看你所使用的 Hugo 版本，如果不是`hugo_extended`版本，请前往 [Hugo Release 页面](https://github.com/gohugoio/hugo/releases)下载你当前版本 Hugo 所对应的`hugo_extended`版本。

比如我原本使用的是`hugo_0.74.0_Windows-64bit.zip`，就需要改为使用`hugo_extended_0.74.0_Windows-64bit.zip`。

此外，本文会涉及多个文件的修改，包括 hmtl、js、scss 等文件类型，且由于**引入了中文字符**，可能导致页面**显示乱码**，这是因为文件的编码使用的是`ANSI`，**需要改为`UTF-8`的编码**。

LoveIt 主题并没有提供一个文件来让我们自定义 JavaScript，所以需要自己创建一个 js 文件来自定义 JavaScript。

首先在站点根目录下创建一个自定义的 JavaScript 文件：`\static\js\custom.js`。这个文件需要在 body 的闭合标签之前引入，并且要在`theme.min.js`的引入顺序之后。这样可以防止样式被其他文件覆盖，并且不会因为 JavaScript 文件假装太久导致页面长时间的空白。

对于 LoveIt 主题，`custom.js`添加在`\themes\LoveIt\layouts\partials\assets.html`里。

首先把该文件拷贝到根目录下的`\layouts\partials\assets.html`，然后打开拷贝后的文件，把自定义的 JavaScript 文件添加到最末尾的`{{- partial "plugin/analytics.html" . -}}`的上一行：

```
{{- /* 自定义的js文件 */ -}}
<script type="text/javascript" src="/js/custom.js"></script>
```

由于本文提及的部分功能会用到 jQuery，建议一起引入，最终如下：

```
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/jquery@2.1.3/dist/jquery.min.js"></script>

{{- /* 自定义的js文件 */ -}}
<script type="text/javascript" src="/js/custom.js"></script>
```

如果有其他的 JavaScript 文件要引入，加在一样的地方就行，但是要放在自定义的`custom.js`之前。这是我的 [custom.js 文件](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/js/custom.js)，有兴趣的可以看看。

对于一些静态资源，比如图片、音乐文件、第三方库等，如果有自己的 cdn 或者图床等，可以在站点配置文件自定义一个 cdn 变量，如下：

```
[params]
  # cdn变量，末尾不需要加/
  cdnPrefix = "http://xxxx"
```

接下来就可以在你需要的地方使用该变量，大概有如下 3 种用法。

在 md 文件中可以用内置的 shortcodes 来使用该变量：

```
{{< param cdnPrefix >}}

![avatar.jpg]({{< param cdnPrefix >}}/images/avatar.jpg)
```

在`layouts`目录下有很多 html 文件，这些是用来渲染站点的模板文件，可以用 Hugo 的语法来引入该变量：

```
{{ .Site.Params.cdnPrefix }}
```

如果在一个模板文件里有多个地方使用到该变量，可以定义一个局部变量来使用：

```
{{- $cdn := .Site.Params.cdnPrefix -}}

/* 使用局部变量 */
{{ $cdn }}
```

由于 JavaScript 文件中不能使用上述的 shortcodes 或 Hugo 语法来引用变量，只能通过在`\layouts\partials\assets.html`中用 JavaScript 语法来引入该变量，如下：

```
/* 这是可以应用于JavaScript文件的全局变量 */
<script>
	/* cdn for some static resources */
	var $cdnPrefix = {{ .Site.Params.cdnPrefix }};
</script>
```

这样就可以在其他被引入的 JavaScript 文件中使用这个`$cdnPrefix`变量：

```
$(function () {
	$.backstretch([
		  $cdnPrefix + "/images/background/saber1.jpg"
	], { duration: 60000, fade: 1500 });
});
```

如果是想在模板文件里引入某个自定义的 JavaScript 文件，如下：

```
{{- /* 自定义的js文件 */ -}}
<script type="text/javascript" src="{{ .Site.Params.cdnPrefix }}/js/custom.js"></script>
```

LoveIt 提供了`admonition` shortcode，支持 **12** 种样式，可以在页面中插入提示的横幅。代码如下：

```
{{< admonition >}}
一个 **注意** 横幅
{{< /admonition >}}

{{< admonition abstract >}}
一个 **摘要** 横幅
{{< /admonition >}}

{{< admonition info >}}
一个 **信息** 横幅
{{< /admonition >}}

{{< admonition tip >}}
一个 **技巧** 横幅
{{< /admonition >}}

{{< admonition success >}}
一个 **成功** 横幅
{{< /admonition >}}

{{< admonition question >}}
一个 **问题** 横幅
{{< /admonition >}}

{{< admonition warning >}}
一个 **警告** 横幅
{{< /admonition >}}

{{< admonition failure >}}
一个 **失败** 横幅
{{< /admonition >}}

{{< admonition danger >}}
一个 **危险** 横幅
{{< /admonition >}}

{{< admonition bug >}}
一个 **Bug** 横幅
{{< /admonition >}}

{{< admonition example >}}
一个 **示例** 横幅
{{< /admonition >}}

{{< admonition quote >}}
一个 **引用** 横幅
{{< /admonition >}}
```

效果如下：

主题默认的 404 页面太普通，可以通过新增`\layouts\404.html`来自定义自己想要的 404 页面。这是本站的 [404 页面](https://lewky.cn/404.html)，有兴趣的可以看看[源码页面](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/404.html)。

这个功能需要引入图片轮播插件`jquery-backstretch`的 cdn，并且该插件依赖于 jQuery，需要在引入该插件之前引入 jQuery。

打开`\layouts\partials\assets.html`，在你引入的`custom.js`的上面一行添加如下代码（必须要在 custom.js 之前引入这两个文件才有效果）：

```
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/jquery@2.1.3/dist/jquery.min.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/jquery-backstretch@2.1.18/jquery.backstretch.min.js"></script>
```

然后在`custom.js`里添加如下代码，具体想要轮播哪些图片可以自行修改，如下：

```
/* 轮播背景图片 */
$(function () {
	$.backstretch([
		  "/images/background/saber1.jpg",
		  "/images/background/saber2.jpg",
		  "/images/background/wlop.jpg"
	], { duration: 60000, fade: 1500 });
});
```

`LoveIt`主题自带的搜索插件是`lunr`和`algolia`，这两个的使用都比较麻烦，后者甚至还想要去注册账号，虽然可以免费使用搜索服务，但是抓取收录时间好像是一小时一次，并且还有每月使用量的限制，太不便利了。

之前在 Hexo 那边用的是自带的搜索插件，是每次部署时自动为所有文章生成索引到一个文件里，然后直接搜索该文件来实现本地搜索功能。这个还是比较方便个人站点使用的，于是在网上找到了类似的一个 Hugo 专用的搜索插件 [hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js)。

`hugo-search-fuse-js`并不是一个单独的主题，而是 Hugo 主题的一个组件：

1.  下载 [hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js) 到站点的主题目录`/themes/hugo-search-fuse-js`下，**注意，目录名必须是`hugo-search-fuse-js`**
2.  把该主题组件名字添加到站点配置文件里，**注意，搜索组件名字要在最前面，后面跟着的是你的主题的文件夹名字**：

```
theme = ["hugo-search-fuse-js", "my-theme"]
```

3.  新建一个`content/search.md`文件，内容如下：

```
+++
title = "Search"
layout = "search"
outputs = ["html", "json"]
[sitemap]
  priority = 0.1
+++
```

1.  把主题目录下的`\themes\LoveIt\layouts\_default\baseof.html`拷贝到站点根目录下的`\layouts\_default\baseof.html`
2.  在拷贝后的`baseof.html`的适当位置插入两段代码：`{{ block "main" . }}{{ end }}`和`{{ block "footer" . }}{{ end }}`，下面是一个修改后的样例：

```
<div>
    {{- partial "header.html" . -}}
    <main>
        <div>
			{{ block "main" . }}{{ end }}
            {{- block "content" . }}{{ end -}}
        </div>
    </main>
    {{- partial "footer.html" . -}}
	{{ block "footer" . }}{{ end }}
</div>
```

在站点配置文件里添加一个新的按钮给搜索功能使用，如下：

```
[menu]
  [[menu.main]]
    pre = "<i class='fas fa-fw fa-search'></i>"
    name = "搜索"
    weight = 7
    identifier = "search"
    url = "/search/"
```

如果你的配置文件里的菜单属性是多语言的，样例如下：

```
[languages]
  [languages.en]
    [languages.en.menu]
      [[languages.en.menu.main]]
	    pre = "<i class='fas fa-fw fa-search'></i>"
	    name = "Search"
	    weight = 7
	    identifier = "search"
	    url = "/search/"
```

在站点配置文件里把默认的搜索插件关闭，如下：

```
[params]
  [params.app]
    [params.search]
      enable = false
```

如果你使用的是多语言的配置，则应该把每个语言的搜索插件都关闭，如下：

```
[languages]
  [languages.en]
    [languages.en.params]
      [languages.en.params.search]
        enable = false

  [languages.zh-cn]
    [languages.zh-cn.params]
      [languages.zh-cn.params.search]
        enable = false
```

如果对该插件生成的搜索页面样式不满意，可以自行修改，下面是我的样式代码：

```
/* 搜索页面 */
.search {
    position: relative;
    padding-top: 3.5rem;
    padding-bottom: 1rem;
    width: 57.5%;
    margin: 0 auto;
    background: white;
    opacity: .95;
}
[theme=dark] .search {
    background: #3a3535;
}

[theme=dark] .search header,
.search header {
    background-color: #f8f8f8;
}

[theme=dark] .search header:hover,
.search header:hover {
    -webkit-box-shadow: none;
    box-shadow: none;
}

.search header h1 {
    padding-left: 1rem;
    background: white;
}
[theme=dark] .search header h1 {
    background: #3a3535;
}

[theme=dark] .search input,
.search input {
	height: initial;
    width: initial;
    color: initial;
	background-color: white;
	margin: 0 0 0 1rem;
	border-width: 2px;
    border-style: inset;
    border-color: initial;
    border-image: initial;
	-webkit-border-radius: 0;
    -moz-border-radius: 0;
    border-radius: 0;
}

.search #search-results {
    padding-left: 1rem;
    padding-right: 1rem;
}

[theme=dark] a:active, [theme=dark] a:hover {
    color: #2d96bd;
}

.search hr {
    margin-left: 1rem;
    margin-right: 1rem;
}
```

这个搜索功能借助了 Fuse.js 模糊搜索引擎，为了更好的适配中文搜索结果，需要修改模糊搜索的相关参数，相对的会导致英文搜索结果变多，不过这个可以接受。因为搜索结果变多了，总好过搜索不出来想要的中文结果。而且可以通过设置搜索结果的权重来改变结果的排序，这样越前面的搜索结果就越是我们想要的。

打开`\themes\hugo-search-fuse-js\static\js\search.js`，这里面配置了 fuse.js 的搜索配置选项，可以参考下我的配置，我已经添加了部分中文注释：

```
// Options for fuse.js
let fuseOptions = {
  shouldSort: true, // 是否按分数对结果列表排序
  includeMatches: true, //  是否应将分数包含在结果集中。0分表示完全匹配，1分表示完全不匹配。
  tokenize: true,
  matchAllTokens: true,
  threshold: 0.2, // 匹配算法阈值。阈值为0.0需要完全匹配（字母和位置），阈值为1.0将匹配任何内容。
  location: 0, // 确定文本中预期找到的模式的大致位置。
  /**
   * 确定匹配与模糊位置（由位置指定）的距离。一个精确的字母匹配，即距离模糊位置很远的字符将被视为完全不匹配。
   *  距离为0要求匹配位于指定的准确位置，距离为100则要求完全匹配位于使用阈值0.2找到的位置的20个字符以内。
   */
  distance: 100,
  maxPatternLength: 64, // 模式的最大长度
  minMatchCharLength: 2, // 模式的最小字符长度
  keys: [
    {name:"title",weight:0.8},
    {name:"tags",weight:0.5},
    {name:"categories",weight:0.5},
    {name:"contents",weight:0.4}
  ]
};
```

这里和中文搜索有关的主要就 3 个选项：`threshold`，`location`，`distance`。

`threshold`是阈值，这个参数搭配`distance`使用。如果阈值填了`0.0`，相当于`distance`没有意义。`location`填 0 就行，`distance`填 100 就足够了，太大了会导致搜索到过多的结果。上面根据我个人的中文搜索测试结果，选择了这样的配置：

```
threshold: 0.2,
  location: 0,
  distance: 100
```

可以根据个人情况来修改这几个参数的值，另外我还将`minMatchCharLength`的值改成了 2，不过经过测试，和之前默认的`3`并没有什么差别。

除了发布草稿和正文，我们还可以添加自定义的页面 page。page 不会像文章那样被渲染，而是被渲染成一个单独的页面，类似于你的文档、标签页面。

方法很简单：

1.  在站点根目录的`/content/`目录下，新建一个文件夹，比如`about`文件夹。然后在该文件夹里新建一个`index.md`文件，该文件将作为站点访问该目录的页面，你可以将其当成一篇特殊的文章。
2.  在`index.md`文件里加上下面的内容，实际上这里只需要`title`就够了，`date`这个日期属性可要可不要，因为 page 页面是看不到这个日期的：
    
    ```
    ---
    title: "关于"
    date: 2018-04-24T22:01:44+08:00
    ---
    ```
    
    ``
    
3.  接下来你就可以像写普通文章一样，在这个`index.md`文件里随便写你想要展示的内容就行了。

首先在站点根目录的`/content/`目录下，新建一个`friends`文件夹。在该文件夹里新建一个`index.md`文件，内容如下：

```
---
title: "友链墙"
---
```

由于博主想要将友链分类，并能使用上目录，所以不使用这种 page 形式的友链页面，而是直接创建一篇文章作为友链使用，文件头如下：

```
title: "友链墙"
url: friends
hiddenFromHomePage: true
```

我们通过自定义一个`shortcode`来实现该功能，以后就可以方便地通过这个`shortcode`快速新增友链到页面上。

在站点根目录下新增一个文件：`/layouts/shortcodes/friend.html`，其内容如下：

```
{{ if .IsNamedParams }}
{{ $defaultImg := "https://sdn.geekzu.org/avatar/d41d8cd98f00b204e9800998ecf8427e?d=retro" }}
	<a target="_blank" href={{ .Get "url" }} title={{ .Get "name" }}---{{ .Get "word" }}>
	  <div class="friend block whole {{ .Get "primary-color" | default "default"}} {{ .Get "border-animation" | default "shadow"}}">
		<div>
		  <img class="friend logo {{ .Get "img-animation" | default "rotate"}}" src={{ .Get "logo" }} onerror="this.src='{{ $defaultImg }}'" />
		</div>
		<div>
		  <div>{{ .Get "name" }}</div>
		  <div{{ .Get "word" }}"</div>
		</div>
	  </div>
	</a>
{{ end }}
```

在站点根目录下新增一个文件：`/assets/css/_partial/_single/_friend.scss`，内容如下：

```
.friend.url {
    text-decoration: none !important;
    color: black;
}

.friend.logo {
    width: 56px !important;
    height: 56px !important;
    border-radius: 50%;
    border: 1px solid #ddd;
    padding: 2px;
    margin-top: 14px !important;
    margin-left: 14px !important;
    background-color: #fff;
}

.friend.block.whole {
    height: 92px;
    margin-top: 8px;
    margin-left: 4px;
    width: 31%;
    display: inline-flex !important;
    border-radius: 5px;
    background: rgba(14, 220, 220, 0.15);

    &.shadow {
        margin-right: 4px;
        box-shadow: 4px 4px 2px 1px rgba(0, 0, 255, 0.2);
    }
    &.borderFlash {
        border-width: 3.5px;
        border-style: solid;
        animation: borderFlash 2s infinite alternate;
    }
    &.led {
        animation: led 3s infinite alternate;
    }
    &.bln {
        animation: bln 3s infinite alternate;
    }
}

.friend.block.whole {
    &:hover {
        color: white;
        & .friend.info {
            color: white;
        }
    }

    &.default {
        --primary-color: #215bb3bf;
        &:hover {
            background: rgba(33, 91, 179, 0.75);
        }
    }
    &.red {
        --primary-color: #e72638;
        &:hover {
            background: rgba(231, 38, 56, 0.75);
        }
    }
    &.green {
        --primary-color: #2ec58d;
        &:hover {
            background: rgba(21, 167, 33, 0.75);
        }
    }
    &.blue {
        --primary-color: #2575fc;
        &:hover {
            background: rgba(37, 117, 252, 0.75);
        }
    }
    &.linear-red {
        --primary-color: #e72638;
        &:hover {
            background: linear-gradient(to right, #f9cdcd 0, #e72638 35%);
        }
    }
    &.linear-green {
        --primary-color: #2ec58d;
        &:hover {
            background: linear-gradient(to right, #1d7544 0, #2ec58d 35%);
        }
    }
    &.linear-blue {
        --primary-color: #2575fc;
        &:hover {
            background: linear-gradient(to right, #6a11cb 0, #2575fc 35%);
        }
    }
}

.friend.block.whole .friend.block.left img {
    &.auto_rotate_left {
        animation: auto_rotate_left 3s linear infinite;
    }
    &.auto_rotate_right {
        animation: auto_rotate_right 3s linear infinite;
    }
}
.friend.block.whole:hover .friend.block.left img {
    &.rotate {
        transition: 0.9s !important;
        -webkit-transition: 0.9s !important;
        -moz-transition: 0.9s !important;
        -o-transition: 0.9s !important;
        -ms-transition: 0.9s !important;
        transform: rotate(360deg) !important;
        -webkit-transform: rotate(360deg) !important;
        -moz-transform: rotate(360deg) !important;
        -o-transform: rotate(360deg) !important;
        -ms-transform: rotate(360deg) !important;
    }
}

.friend.block.left {
    width: 92px;
    min-width: 92px;
    float: left;
}

.friend.block.left {
    margin-right: 2px;
}

.friend.block.right {
    margin-top: 18px;
    margin-right: 18px;
}

.friend.name {
    overflow: hidden;
    font-weight: bolder;
    word-wrap:break-word;
    word-break: break-all;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
}

.friend.info {
    margin-top: 3px;
    overflow: hidden;
    word-wrap:break-word;
    word-break: break-all;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    line-height: normal;
    font-size: 0.8rem;
    color: #7a7a7a;
}

@media screen and (max-width: 900px) {
    .friend.info {
        display: none;
    }
    .friend.block.whole {
        width: 45%;
    }
    .friend.block.left {
        width: 84px;
        margin-left: 15px;
    }
    .friend.block.right {
        height: 100%;
        margin: auto;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .friend.name {
        font-size: 14px;
    }
}

@keyframes bln {
	0% {
		box-shadow: 0 0 5px grey,inset 0 0 5px grey,0 1px 0 grey;
		box-shadow: 0 0 5px var(--primary-color,grey),inset 0 0 5px var(--primary-color,grey),0 1px 0 var(--primary-color,grey)
	}

	to {
		box-shadow: 0 0 16px grey,inset 0 0 8px grey,0 1px 0 grey;
		box-shadow: 0 0 16px var(--primary-color,grey),inset 0 0 8px var(--primary-color,grey),0 1px 0 var(--primary-color,grey)
	}
}

@keyframes led {
	0% {
		box-shadow: 0 0 4px #ca00ff
	}

	25% {
		box-shadow: 0 0 16px #00b5e5
	}

	50% {
		box-shadow: 0 0 4px #00f
	}

	75% {
		box-shadow: 0 0 16px #b1da21
	}

	to {
		box-shadow: 0 0 4px red
	}
}

@keyframes borderFlash {
	0% {
		border-color: white;
	}

	to {
		border-color: grey;
		border-color: var(--primary-color,grey)
	}
}

@keyframes auto_rotate_left {
	0% {
		transform: rotate(0)
	}

	to {
		transform: rotate(-1turn)
	}
}

@keyframes auto_rotate_right {
	0% {
		transform: rotate(0)
	}

	to {
		transform: rotate(1turn)
	}
}
```

请注意，该文件是`.scss`文件，只有你使用的是扩展版本的 Hugo 时才能生效。

然后把`\themes\LoveIt\assets\css\_page\_single.scss`这个文件拷贝到`\assets\css\_page\_single.scss`，然后修改我们新增的`_single.scss`，在第一行加一行新代码：

```
@import "../_partial/_single/friend";
```

这样 Hugo 就会用我们新增的这个文件来渲染友链页面了。

打开站点配置文件`config.toml`，添加友链按钮：

```
# 菜单配置
[menu]
  [[menu.main]]
    pre = "<i class='fas fa-fw fa-fan fa-spin'></i>"
    name = "友链"
    identifier = "friends"
    url = "/friends/"
    weight = 6
```

在你想要引入友链的文章里使用下面的代码即可自动渲染成对应的友链：

```
{{< friend

url="lewky.cn"
logo="https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/avatar.jpg"
word="不想当写手的码农不是好咸鱼_(xз」∠)_"
>}}
```

上面代码里的四个属性为必填项，还可以额外指定三个不同的属性来选择友链内置的多种样式，如下：

```
//边框及鼠标悬停的背景颜色，允许设置渐变色
//支持7种：default、red、green、blue、linear-red、linear-green、linear-blue
primary-color="default"

//头像动画：rotate(鼠标悬停时旋转，此为默认效果)、auto_rotate_left(左旋转)、auto_rotate_right(右旋转)
img-animation="rotate"

//边框动画：shadow(阴影，此为默认效果)、borderFlash(边框闪现)、led(跑马灯)、bln(主颜色呼吸灯)
border-animation="shadow"
```

[

![](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/avatar.jpg)

雨临 Lewis 的博客

"不想当写手的码农不是好咸鱼_(xз」∠)_"

](https://lewky.cn/posts/hugo-3.html/lewky.cn "雨临Lewis的博客---不想当写手的码农不是好咸鱼_(xз」∠)_")[

![](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/avatar.jpg)

雨临 Lewis 的博客

"不想当写手的码农不是好咸鱼_(xз」∠)_"

](https://lewky.cn/posts/hugo-3.html/lewky.cn "雨临Lewis的博客---不想当写手的码农不是好咸鱼_(xз」∠)_")[

![](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/avatar.jpg)

雨临 Lewis 的博客

"不想当写手的码农不是好咸鱼_(xз」∠)_"

](https://lewky.cn/posts/hugo-3.html/lewky.cn "雨临Lewis的博客---不想当写手的码农不是好咸鱼_(xз」∠)_")

如果友链的头像无法正常显示，会以一个默认的 Gravatar 头像显示。此外，在移动端会隐藏站点描述，只显示头像和站点名称，你可以通过将当前窗口缩小到宽度最小即可看到效果。

LoveIt 主题自带的 Valine 没有邮件通知和 QQ 提醒功能，所以需要额外使用 Valine 的增强版`Valine-Admin`来进行功能增强。网上有好几个版本，我这里选择了目前由 @W4J1e 维护的`Hexo-Valine-ASPush`项目，由于我需要自定义部分改动，所以自己 fork 了一份：[valine-admin-custom](https://github.com/lewky/valine-admin-custom)。如果需要进行自定义改动的，可以继续 fork 我的这个项目。

下面开始教程。这里默认你已经使用了 Valine 作为评论系统，如果还没有对应的 LeanCloud 应用请自行移步 Valine 的官方文档： [Valine 快速开始](https://valine.js.org/quickstart.html)。

登陆你的 LeanCloud 账号，进入你的应用，选择：`云引擎 -> 部署 -> Git部署`，然后输入下面的仓库地址：

```
https://github.com/lewky/valine-admin-custom.git
```

然后点击部署按钮，等待云引擎将上面的仓库代码克隆下来。接下来需要设置环境变量。

点击`云引擎 -> 设置`，选择添加新变量，将下列变量一一加入。

<table><thead><tr><th>变量</th><th>示例</th><th>说明</th></tr></thead><tbody><tr><td>SITE_NAME</td><td>xxx</td><td>[必填] 网站名称</td></tr><tr><td>SITE_URL</td><td><a href="https://xxx.com/">https://xxx.com</a></td><td>[必填] 网站首页地址，地址末尾不要加 /</td></tr><tr><td>SMTP_USER</td><td><a href="mailto:xxxxxx@qq.com">xxxxxx@qq.com</a></td><td>[必填] SMTP 登录用户，一般为邮箱地址</td></tr><tr><td>SMTP_PASS</td><td>ccxxxxxxxxch</td><td>[必填] SMTP 授权码，不是邮箱的登陆密码，请自行查询对应邮件服务商授权码的获取方式</td></tr><tr><td>SMTP_SERVICE</td><td>QQ</td><td>[必填] 邮件服务提供商，支持 QQ、163、126、Gmail 以及 <a href="https://nodemailer.com/smtp/well-known/#supported-services" target="_blank" rel="noopener noreffer">更多</a></td></tr><tr><td>SENDER_NAME</td><td>xxx</td><td>[必填] 发件人</td></tr><tr><td>SENDER_EMAIL</td><td><a href="mailto:xxxxxx@qq.com">xxxxxx@qq.com</a></td><td>[必填] 发件邮箱</td></tr><tr><td>ADMIN_URL</td><td><a href="https://xxx.leanapp.cn/">https://xxx.leanapp.cn/</a></td><td>[建议] Web 主机二级域名（云引擎域名），用于自动唤醒</td></tr><tr><td>BLOGGER_EMAIL</td><td><a href="mailto:xxxxx@gmail.com">xxxxx@gmail.com</a></td><td>[可选] 博主通知收件地址，默认使用 SENDER_EMAIL</td></tr><tr><td>AKISMET_KEY</td><td>xxxxxxxx</td><td>[可选] Akismet Key 用于垃圾评论检测，设为 MANUAL_REVIEW 开启人工审核，留空不使用反垃圾</td></tr><tr><td>TEMPLATE_NAME</td><td>default</td><td>[可选] 设置提醒邮件主题，目前内置了多款主题：default、rainbow、custom1、custom2</td></tr><tr><td>COMMENT</td><td>#post-comment</td><td>[可选] 评论 div 的 ID 名，直接跳转到评论位置</td></tr></tbody></table>

设置好变量后，需要重启实例。在部署页面点击重启按钮即可，每次更改变量后都需要重启才能生效。如果对应的 Git 仓库代码发生变更，需要清除部署，重新克隆代码才能生效。

首先需要设置管理员信息。在部署云引擎成功后，访问管理员注册页面 https:// 云引擎域名 / sign-up，注册管理员登录信息，如：https://cloud.lewky.cn/sign-up

注册成功后可以在`存储 -> 结构化数据 -> _User`里看到多出来一条数据，正是刚刚我们注册成功的用户。

> 注：使用原版 Valine 如果遇到注册页面不显示直接跳转至登录页的情况，请手动删除_User 表中的全部数据。

注册成功后，就可以通过这个注册的用户访问后台评论管理页面：https:// 云引擎域名

1.  前往 [Qmsg 酱官网](https://qmsg.zendee.cn/)，点击管理台登陆账号，选择其中任意一个你想使用的 Qmsg 酱 (QQ 机器人)，并加其为 QQ 好友
2.  点击菜单栏里`Qmsg酱`旁边的`QQ号码`，添加你想要接收信息推送的 QQ 号码
3.  点击菜单栏里的`KEY`，这里有一串字符串等下要添加到 LeanCloud 的环境变量里

官网里提供了所有的接口文档，你可以先行通过一个简单的测试来验证你的 QQ 能不能接收到推送，如下：

1.  在浏览器新打开一个页面，在地址栏里输入`https://qmsg.zendee.cn/send/`，然后把你管理台里的`KEY`添加到地址栏末尾，然后在末尾继续加上`?msg=test`，接着按下回车键，这里表示让 Qmsg 酱发送`test`给你的 QQ 号码
2.  如果发送成功，你会发现浏览器页面内容变成：

```
{"success":true,"reason":"操作成功","code":0,"info":{}}
```

3.  与此同时，你的 QQ 号码会收到你加的那个`Qmsg酱`QQ 好友的消息。这表明推送正常，可以往下走了。

前面我们已经在 LeanCloud 云引擎里配置了一部分必须的环境变量，为了使用 QQ 提醒功能，还想要额外配置两个变量：

<table><thead><tr><th>变量</th><th>示例</th><th>说明</th></tr></thead><tbody><tr><td>QMSG_KEY</td><td>xxxxxxx</td><td>[必填] Qmsg 酱的 KEY</td></tr><tr><td>QQ</td><td>xxxxxxx</td><td>[必填] Qmsg 酱发送的 qq，支持多个，用英文逗号分隔即可</td></tr></tbody></table>

LeanCloud 云引擎有自动休眠机制，这是官方的说法：[点击查看](https://leancloud.cn/docs/leanengine_plan.html#hash633315134)

目前实现了两种云函数定时任务来解决云引擎休眠的问题：

*   自动唤醒，定时访问 Web APP 二级域名防止云引擎休眠；
*   每天定时检查过去 24 小时内漏发的邮件通知。

进入`云引擎 -> 定时任务`，创建两个定时任务：

1.  选择 self-wake 云函数，Cron 表达式为`0 */25 7-23 * * ?`，表示每天早 7 点到晚 12 点每隔 25 分钟访问云引擎。
2.  选择 resend-mails 云函数，Cron 表达式为`0 10 7 * * ?`，表示每天 7 点 10 分检查过去 24 小时内漏发的通知邮件并补发。

云引擎还有个国际版的，要注意表达式的时区问题，不过表达式填写后会显示每天几点操作，应该不会有人填错吧。

免费版云引擎会在达到最大启动时长限制（好像是持续运行 18 个小时），进入强制休眠状态。在休眠状态时无法通过我们定义的自动唤醒函数来自动重启，可以在日志里看到报错如下：

```
{"error":"因流控原因，通过定时任务唤醒体验版实例失败，建议升级至标准版云引擎实例避免休眠 https://url.leanapp.cn/dwAEksv"}
```

但是如果在休眠期间，云引擎又受到外界的一次访问，就可以再次激活进入启动状态，这时候就可以通过自动唤醒函数来每隔半小时自我唤醒一次了。也就是说，当云引擎进入强制休眠状态后，我们通过外部的定时任务，来每天定时访问云引擎绑定好的域名（**也就是你的后台评论管理页面地址**），就可以继续白嫖了。

那么去那里弄免费的外部定时任务呢？实现方案有不少，比如：

借助`GitHub + Actions`，自动部署也是用这种方案，不过这种方案有个缺点，就是每次执行 action 都会生成一次对应的 commit，对我来说不是理想方案，这里就不介绍了。有兴趣的可以参考[这篇文章](https://www.antmoe.com/posts/ff6aef7b#%e5%bc%80%e5%a7%8b%e5%b0%9d%e8%af%95)

利用国内的云函数，自己写一个脚本。然后定时监控即可。或者宝塔、自己服务器的定时任务都是可以的。说穿了，就是用其他的类似于 LeanCloud 云引擎类似的免费容器的云函数或者定时任务来唤醒 LeanCloud 的云引擎，那如果其他的免费容器也有类似的强制休眠机制怎么办呢？

很简单，互相套娃即可。让一个免费的容器 A 通过定时任务在非休眠期间去唤醒另一个强制休眠中的容器 B，如果容器 A 强制休眠了就让另一个非休眠的容器 B 去唤醒。只要两个免费容器的强制休眠时间错开即可完成这一白嫖循环`O(∩_∩)O~`

`cloudflare`的`Workers`可以在线定义脚本，通过链接即可触发脚本，具体做法请参考[这篇文章](https://www.antmoe.com/posts/ff6aef7b#%e6%96%b9%e6%a1%88%e4%b8%89)

通过`cron-job`平台进行监控，这是[注册地址](https://cron-job.org/en/signup/)

本人使用的方案四，该方案其实和方案二是一回事。这里简单介绍下怎么用，注册该平台可能需要`tī zi`，请自行解决。

*   注册时，`Time zone`（也就是时区）请选择`Asia/Shanghai`，否则后续在定义 cron 表达式时需要自己换算时区时间。如果注册页面最下面的谷歌人机验证出不来，你懂的，请自行解决。
*   注册后需要登录邮箱通过邮件激活账号，没收到邮件的请检查你的垃圾箱，邮件可能在垃圾箱里。
*   激活账号后登录你的账号，访问这个地址：https://cron-job.org/en/members/jobs/
*   点击页面上的`Create cronjob`按钮，创建你的定时任务，各个必填配置项如下表：

<table><thead><tr><th>变量</th><th>示例</th><th>说明</th></tr></thead><tbody><tr><td>Title</td><td>xxx</td><td>定时任务名称</td></tr><tr><td>Address</td><td><a href="https://xxx.com/">https://xxx.com</a></td><td>访问的地址，请填写 LeanCloud 云引擎绑定的域名地址，也就是那个后台评论管理页面地址<code>https://云引擎域名</code></td></tr><tr><td>Schedule</td><td>按需选择</td><td>定时任务的执行时间和频率，这里建议使用第二种：<code>Every day at 6 : 50 </code>。具体每天几点执行请自行决定。</td></tr><tr><td>Notifications</td><td>按需选择</td><td>执行失败时的通知提醒</td></tr><tr><td>Save responses</td><td>按需选择</td><td>保存执行定时任务的日志</td></tr></tbody></table>

鉴于 Valine 的安全问题，以及 LeanCloud 云引擎的限流问题，改用 Waline + Vercel 来作为评论系统，Waline 是基于 Valine 进行开发的，所以迁移成本较低。这是 Waline 的[官方文档](https://waline.js.org/)，有很详细的配置、迁移等教程。

由于 LoveIt 主题没有引入 Waline，所以这里记录下如何引入 Waline，以及遇到的相关问题的解决方法。

打开站点配置文件，找到 Valine 相关变量`[params.page.comment.valine]`，在该节点下面添加 Waline 相关的变量 `[params.page.comment.waline]`：

```
# Waline comment config (https://waline.js.org/)
      # Waline 评论系统设置 (https://waline.js.org/)
      [params.page.comment.waline]
        enable = true
        #js = "https://cdn.jsdelivr.net/npm/@waline/client@latest"
        js = "https://cdn.jsdelivr.net/npm/@waline/client/dist/Waline.min.js"
        meta = ['nick','mail','link']                           # 评论者相关属性
        requiredFields = ['nick','mail']                        # 设置必填项，默认匿名
        placeholder = "提交评论较慢，请等待几秒~"              	# 评论框占位提示符
        serverURL = ""                 							# Waline的服务端地址（必填） 
        #imageHosting =                                         # 图床api，如果允许评论框上传图片
        avatar = "retro"                                        # Gravatar头像
        avatarCDN = "https://sdn.geekzu.org/avatar/"            # Gravatar头像CDN地址，不建议使用loli源
        pageSize = 10                                           # 评论列表分页，每页条数
        lang = "zh-CN"                                          # 多语言支持
        visitor = true                                          # 文章访问量统计
        highlight = true                                        # 代码高亮
```

记得把原本的评论系统的`enable`设置为 false，改用新加的 Waline。

将`\themes\LoveIt\layouts\partials\comment.html`拷贝到`\layouts\partials\comment.html`，打开拷贝后的文件，找到 Valine 相关的代码部分，然后在其下方添加 Waline 的代码，如下：

```
{{- /* Waline Comment System */ -}}
        {{- $waline := $comment.waline | default dict -}}
        {{- if $waline.enable -}}
            <div></div>
			<script src='{{ $waline.js }}'></script>

			<script>
		    	new Waline({
		    	  el: '#waline',
				  meta: {{ $waline.meta }},
				  placeholder: {{ $waline.placeholder }},
		    	  serverURL: {{ $waline.serverURL }},
		    	  avatarCDN: {{ $waline.avatarCDN }},
		    	  requiredFields: {{ $waline.requiredFields }},
		    	  pageSize: {{ $waline.pageSize }},
		    	  avatar: {{ $waline.avatar }},
		    	  lang: {{ $waline.lang }},
				  visitor: {{ $waline.visitor }},
				  highlight: {{ $waline.highlight }}
		    	});
		    </script>
        {{- end -}}
```

Waline 内置微博表情，如果想自定义表情包的，可以继续添加两个属性`emojiCDN`和`emojiMaps`到上面的代码里，具体做法可以参考[官方文档 - 自定义表情](https://waline.js.org/client/emoji.html)。

这里顺便介绍下 @小康大佬整理的一个很方便的表情包站点：[表情速查](https://emotion.xiaokang.me/)，里面有很多类别的表情包，还有对应的快速引入语法链接，以及用于配置 Valine、Waline 等评论系统表情包映射的 JSON！

将`/themes/LoveIt/layouts/posts/single.html`拷贝到`/layouts/posts/single.html`，打开拷贝后的文件，找到如下：

```
<div>
                {{- with .Site.Params.dateformat | default "2006-01-02" | .PublishDate.Format -}}
                    <i></i> <time datetime="{{ . }}">{{ . }}</time> 
                {{- end -}}
                <i></i> {{ T "wordCount" .WordCount }} 
                <i></i> {{ T "readingTime" .ReadingTime }} 
                {{- $comment := .Scratch.Get "comment" | default dict -}}
                {{- if $comment.enable | and $comment.valine.enable | and $comment.valine.visitor -}}
                    <span id="{{ .RelPermalink }}" data-flag-title="{{ .Title }}">
                        <i></i> <span class=leancloud-visitors-count></span> {{ T "views" }}
                    </span> 
                {{- end -}}
            </div>
```

将上面的代码改为如下代码：

```
<div>
                {{- with .Site.Params.dateformat | default "2006-01-02" | .PublishDate.Format -}}
                    <i></i> <time datetime="{{ . }}">{{ . }}</time> 
                {{- end -}}
                <i></i> {{ T "wordCount" .WordCount }}
                <i></i> {{ T "readingTime" .ReadingTime }} 
                {{- $comment := .Scratch.Get "comment" | default dict -}}
                {{- if $comment.enable | and $comment.valine.enable | and $comment.valine.visitor -}}
                    <span id="{{ .RelPermalink }}" data-flag-title="{{ .Title }}">
                        <i></i> <span class=leancloud-visitors-count></span> {{ T "views" }}
                    </span> 
                {{- end -}}
                {{- if $comment.enable | and $comment.waline.enable | and $comment.waline.visitor -}}
                    <span id="{{ .RelPermalink }}" data-flag-title="{{ .Title }}">
                        <i></i> <span class=leancloud-visitors-count></span> {{ T "views" }}
                    </span> 
					<a href="#comments" title="{{ T `viewComments` }}">
						<i></i> <span id="{{ .RelPermalink }}"></span> 条评论
					</a>
                {{- end -}}
            </div>
```

在`_custom.scss`里添加如下样式：

```
/* 文章元数据meta */
.post-meta .post-meta-line:nth-child(2) i:nth-child(1) {
    margin-left: 0;
}
.post-meta .post-meta-line:nth-child(2) i {
    margin-left: 0.3rem;
}
.post-meta .post-meta-line:nth-child(2) span i {
    margin-left: 0.3rem !important;
}
.post-meta a#post-meta-vcount {
    color: #a9a9b3;
    &:hover {
        color: #2d96bd;
    }
}
```

这个部分直接参考[官方文档 - Vercel 部署](https://waline.js.org/quick-start.html#vercel-%E9%83%A8%E7%BD%B2)。实际上就是在 GitHub 上帮你创建了一个仓库，仓库里只有简单的几个文件，用于 Vercel 的部署。Vercel 那边会和刚刚创建的 GitHub 仓库关联，然后部署到 Vercel 自己的服务器。

这里有个坑，之前用 Valine 的时候只需要用到 LeanCloud 的两个变量`APP ID`和`APP KEY`。但是对于 Waline，必须要再用到第三个变量`Master Key`。也就是说，**对于 Waline + Vercel，必须配置三个变量`LEAN_ID`、`LEAN_KEY`和`LEAN_MASTER_KEY`才能算部署成功。**

否则你会发现就算 Vercel 显示部署成功，一旦访问部署页面却会发现页面一片空白，具体可参考 GitHub 上的一个 issue：[Vercel 初始化后打开网址页面内容为空 #82](https://github.com/lizheming/waline/issues/82)

Waline 还带有简单的后台，可以实现对评论的管理。部署完成后访问`<serverURL>/ui/register`进行注册，第一个注册的你会被设定成管理员。登录成功后就可以看到评论管理的界面了，大家可以收藏该地址方便后续使用。`serverURL`就是 Vercel 部署成功后提供给你的那几个访问域名。

如果原本使用的是 Valine + LeanCloud 云引擎，在改用 Waline + Vercel 后记得把 LeanCloud 云引擎的部署清除掉。

Waline 支持邮件、微信、QQ 通知，想要使用通知功能，需要在 Vercel 那边配置环境变量。具体可参考[官方文档 - 评论通知](https://waline.js.org/server/notification.html#%E8%AF%84%E8%AE%BA%E9%80%9A%E7%9F%A5)。这些环境变量名字和 Valine 的配置是一样的，貌似只有 QQ 通知相关的一个变量名字不一样而已。

Vercel 配置环境变量步骤：

1.  打开你在 Vercel 上创建的项目
2.  点击`Settings` -> `Environment Variables` -> 选择添加`Plaintext`类型的环境变量 -> 输入环境变量的`name`和`value` -> 点击`Save`

所有被添加的环境变量可以在下方看到，可以删除或修改已定义的环境变量。

*   由于使用了 LeanCloud 作为存储，外加使用了反垃圾评论服务 Akismet，所以在提交评论时会比较慢，大概需要等待个两三秒。这个耗时见仁见智，一方面确实慢，一方面可以有效避免被人恶意评论攻击。
*   据说部署在 CloudBase 的速度还行。
*   Waline 的机制好像是 QQ 提醒了邮件就不提醒，所以对于新评论，如果设置了 QQ 提醒就不会再收到邮件通知。对于回复的评论则是可以同时收到。
*   Waline 提供了一个很棒的后台管理，还支持其他人的注册和登陆。

默认的统计功能只有`Google Analytics`和`Fathom Analytics`两种，想要使用百度统计需要自行修改配置文件和模板文件。

在站点配置文件里找到统计相关的配置，如下：

```
# Analytics config
  # 网站分析配置
  [params.analytics]
    enable = flase
    # Google Analytics
    [params.analytics.google]
      id = ""
      # whether to anonymize IP
      # 是否匿名化用户 IP
      anonymizeIP = true
    # Fathom Analytics
    [params.analytics.fathom]
      id = ""
      # server url for your tracker if you're self hosting
      # 自行托管追踪器时的主机路径
      server = ""
```

在这里的`[params.analytics.fathom]`后面添加一个新的变量给百度统计使用：

```
# Baidu Analytics
    # 百度统计
    [params.analytics.baidu]
      id = ""
```

首先拷贝`\themes\LoveIt\layouts\partials\plugin\analytics.html`到`\layouts\partials\plugin\analytics.html`。

打开拷贝后的 analytics.html 文件，在`Fathom Analytics`的代码下面加上如下内容：

```
{{- /* Baidu Analytics */ -}}
    {{- with $analytics.baidu.id -}}
		<script>
			var _hmt = _hmt || [];
			(function() {
			  var hm = document.createElement("script");
			  hm.src = "https://hm.baidu.com/hm.js?{{ . }}";
			  var s = document.getElementsByTagName("script")[0]; 
			  s.parentNode.insertBefore(hm, s);
			})();
		</script>
	{{- end -}}
```

将统计功能的`enable = flase`改为`enable = true`。在新增的百度统计变量的`id`那里填上你的百度统计 id 值，也就是百度统计的脚本代码里`https://hm.baidu.com/hm.js?`后面跟着的那串很长的东东。如果不知道怎么查看这个百度统计 id，请自行百度。

打开站点配置文件，添加如下变量，可以自行定制菜单里的按钮，包括数量、名称、图片和地址：

```
# Right click menu
  # 右键菜单
  [params.rightmenu]
    enable = true  # true or false  是否开启右键
    audio = false  # true or false 是否开启点击音乐
    [[params.rightmenu.layout]]
      # 按钮名称
      name = "首页"
      # 背景图片
      image = "/images/rightmenu/rightmenu1.jpg"
      # 跳转地址
      url = "/"
    [[params.rightmenu.layout]]
      name = "音乐游戏"
      image = "/images/rightmenu/rightmenu2.jpg"
      url = "/funny/mikutap/"
    [[params.rightmenu.layout]]
      name = "前方高能"
      image = "/images/rightmenu/rightmenu3.jpg"
      url = "/funny/high/"
    [[params.rightmenu.layout]]
      name = "建站日志"
      image = "/images/rightmenu/rightmenu4.jpg"
      url = "/posts/e62c38c4.html"
    [[params.rightmenu.layout]]
      name = "随笔"
      image = "/images/rightmenu/rightmenu5.jpg"
      url = "/posts/d65a1577.html"
    [[params.rightmenu.layout]]
      name = "友链"
      image = "/images/rightmenu/rightmenu6.jpg"
      url = "/friends/"
```

如果你有图床的话，还可以额外增加一个图床变量，这样可以去图床加载你的图片，可以参考前文的[添加全局 cdn 变量](http://localhost:1313/posts/hugo-3.html/#%E6%B7%BB%E5%8A%A0%E5%85%A8%E5%B1%80cdn%E5%8F%98%E9%87%8F)。

新建一个`layouts/partials/plugin/rightmenu.html`文件，内容如下：

```
{{- $rightmenu := .Site.Params.rightmenu -}}
{{- $cdn := .Site.Params.cdnPrefix -}}
{{- if $rightmenu.enable -}}
   	<div>
	      <div>
	        <div>
			{{- range $item := $rightmenu.layout -}}
			{{- $defaultURL := "/" -}}
			{{- $defaultName := "Home" -}}
			{{- $defaultImage := "https://gravatar.loli.net/avatar/c02f8b813aa4b7f72e32de5a48dc17a7?d=retro&v=1.4.14" -}}
	          <a href="{{- $item.url | default $defaultURL -}}" 
			  target="_blank" 
			{{- $itemImage := $item.image | default $defaultImage -}}
			{{- if strings.HasPrefix $item.image "http" -}}
			  style="background-image:url({{- $itemImage -}});" 
			{{- else if strings.HasPrefix $item.image "/" -}}
			  style="background-image:url({{- $cdn -}}{{- $itemImage -}});" 
			{{- else -}}
			  style="background-image:url({{- $itemImage -}});" 
			{{- end -}}
			 >{{- $item.name | default $defaultName -}}</a>
			{{- end -}}
			</div>
			{{- if $rightmenu.audio -}}
				<audio src="https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/audio/niconiconi.mp3"></audio>
	        {{- end -}}
		  </div>
	</div>
	<script type="text/javascript">
	var items = document.querySelectorAll('.menuItem'); 
	for (var i = 0, l = items.length; i < l; i++) {
        items[i].style.left = (50 - 35 * Math.cos( - 0.5 * Math.PI - 2 * (1 / l) * i * Math.PI)).toFixed(4) + "%";
        items[i].style.top = (50 + 35 * Math.sin( - 0.5 * Math.PI - 2 * (1 / l) * i * Math.PI)).toFixed(4) + "%"
      }
	</script>
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/css/GalMenu.css">
	<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/js/GalMenu.js"></script>
	<script type="text/javascript">
    	$(document).ready(function() {
        $('body').GalMenu({
          'menu': 'GalDropDown'
        })
      });
	</script>
{{- end -}}
```

这个模板代码里使用到了我项目里的`niconiconi.mp3`、`GalMenu.css`、`GalMenu.js`这三个文件，有兴趣的可以自己把文件保存到自己网站里，mp3 文件可以自己修改为其他的音频文件。

将主题的`\themes\LoveIt\layouts\partials\assets.html`拷贝一份到`\layouts\partials\assets.html`，在`{{- partial "plugin/analytics.html" . -}}`下添加如下内容：

```
{{- /* 右键菜单 */ -}}
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/jquery@2.1.3/dist/jquery.min.js"></script>
{{- partial "plugin/rightmenu.html" . -}}
```

搞定，这个功能就完成了。

将`\themes\LoveIt\layouts\posts\single.html`拷贝到`\layouts\posts\single.html`，打开拷贝后的文件，在`{{- $params := .Scratch.Get "params" -}}`的下一行添加如下代码：

```
{{- $password := $params.password | default "" -}}
    {{- if ne $password "" -}}
		<script>
			(function(){
				if({{ $password }}){
					if (prompt('请输入文章密码') != {{ $password }}){
						alert('密码错误！');
						if (history.length === 1) {
							window.opener = null;
							window.open('', '_self');
							window.close();
						} else {
							history.back();
						}
					}
				}
			})();
		</script>
    {{- end -}}
```

之后只要在文章的头部加上`password`属性即可进行加密，只有输入了正确密码才能打开文章，否则会回退到之前的页面。用法如下：

```
---
title: 随笔
password: test
---
```

将`\themes\LoveIt\layouts\partials\header.html`拷贝到`\layouts\partials\header.html`，打开拷贝后的文件，在`<div>`的下一行添加一个超链代码：

```
<a href="https://github.com/lewky" target="_blank" title="Follow me on GitHub" aria-label="Follow me on GitHub"><svg width="3.5rem" height="3.5rem" viewBox="0 0 250 250" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor"></path></svg></a>
```

将上边的超链的 href 改为自己的 GitHub 地址，如果想调整图片大小，可以修改代码里的`svg`标签的`width`和`height`属性。

然后是添加样式代码到`_custom.scss`里：

```
/* Github Corner */
.github-corner:hover .octo-arm {
	animation: octocat-wave 560ms ease-in-out
}

@keyframes octocat-wave {
	0%,100% {
		transform: rotate(0)
	}

	20%,60% {
		transform: rotate(-25deg)
	}

	40%,80% {
		transform: rotate(10deg)
	}
}

@media (max-width:500px) {
	.github-corner:hover .octo-arm {
		animation: none
	}

	.github-corner .octo-arm {
		animation: octocat-wave 560ms ease-in-out
	}
}
```

下面是 GitHub Corner 的项目地址，一共有 10 种颜色样式，随便挑！

*   [GitHub Corners 项目地址](https://tholman.com/github-corners/)

将`\themes\LoveIt\layouts\_default\baseof.html`拷贝到`\layouts\_default\baseof.html`，打开拷贝后的`baseof.html`，在`{{- /* Load JavaScript scripts and CSS */ -}}`的上面一行添加如下代码：

```
<div>
  <div>
	<img src="https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/b2t/leimuA.png" alt="雷姆" 
	onmouseover="this.src='https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/b2t/leimuB.png'" 
	onmouseout="this.src='https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/b2t/leimuA.png'" title="回到顶部">
  </div>
  <div>
	<img src="https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/b2t/lamuA.png" alt="雷姆" 
	onmouseover="this.src='https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/b2t/lamuB.png'" 
	onmouseout="this.src='https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/b2t/lamuA.png'" title="回到底部">
  </div>
</div>
```

在`_custom.scss`里添加对应的 css 代码：

```
/* 拉姆蕾姆回到顶部或底部按钮 */
.sidebar_wo {
    position:fixed;
    line-height:0;
    bottom:0;
    z-index:1000
}
#leimu {
    left:0;
    -webkit-transition:all .3s ease-in-out;
    transition:all .3s ease-in-out;
    -webkit-transform:translate(-7px,7px);
    -ms-transform:translate(-7px,7px);
    transform:translate(-7px,7px)
}
#lamu {
    -webkit-transition:all .3s ease-in-out;transition:all .3s ease-in-out;
    -webkit-transform:translate(7px,7px);
    -ms-transform:translate(7px,7px);
    transform:translate(7px,7px);
    right:0
}
#leimu:hover {
    -webkit-transform:translate(0,0);
    -ms-transform:translate(0,0);
    transform:translate(0,0)
}
#lamu:hover {
    -webkit-transform:translate(0,0);
    -ms-transform:translate(0,0);
    transform:translate(0,0)
}
.sidebar_wo img {
    cursor:pointer;
}
@media only screen and (max-width:1024px) {
    .sidebar_wo{display:none}
}
```

最后在`custom.js`里添加如下代码，注意，要先引入`jquery`才有效果，具体细节请看前文：

```
/* 拉姆蕾姆回到顶部或底部按钮 */
$(function() {
	$("#lamu img").eq(0).click(function() {
		$("html,body").animate({scrollTop:$(document).height()},800);
		return false;
	});
	$("#leimu img").eq(0).click(function() {
		$("html,body").animate({scrollTop:0},800);
		return false;
	});
});
```

这个功能分为四个部分：

*   首页头像的动画特效从浮动改为旋转，为了适配挂件还稍微缩小了头像大小
*   添加头像挂件（都是 b 站的挂件）
*   点击头像一点次数后随机刷新头像
*   加载首页时随机刷新头像（该功能可禁用）

在站点配置文件里找到你配置首页头像的变量`avatarURL`，在其下方添加两个新的变量，内容如下：

```
[params.home.profile]
        enable = true
        # 主页显示头像的 URL
        avatarURL = "/images/avatar.jpg"
        # 是否启用头像挂件
        avatarPluginURL = "/images/avatar-plug/bilibili_27.png"
        # 是否启用头像挂件自动刷新
        avatarPluginFlush = true
        # 点击频率，点击几次就换挂件
        avatarPluginFrequency = 1
        # 头像挂件总数
        avatarPluginCount = 128
```

如果你有自己的图床，还可以配置一个给头像挂件使用的图床地址，如下：

```
# 参数
[params]
  # 图床变量，末尾不需要加/
  cdnPrefix = "https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master"
```

这个变量不设置也没关系，不会影响挂件的功能。

将`\themes\LoveIt\layouts\partials\home\profile.html`拷贝到`\layouts\partials\home\profile.html`，打开拷贝后的文件，找到下面的代码：

```
<a href="{{ $url }}"{{ with .Title | default .Name }} title="{{ . }}"{{ end }}{{ if (urls.Parse $url).Host }} rel="noopener noreffer" target="_blank"{{ end }}>
    {{- dict "Src" $avatar | partial "plugin/image.html" -}}
</a>
```

这是渲染首页头像的代码，将这段代码改成如下内容：

```
{{- if $profile.avatarPluginURL -}}
	<img />
	<a href="javascript:void(0);"{{ with .Title | default .Name }} title="Please click me~~"{{ end }}{{ if (urls.Parse $url).Host }} rel="noopener noreffer" target="_blank"{{ end }}>
		{{- dict "Src" $avatar "Title" "Please click me~~" | partial "plugin/image.html" -}}
	</a>
{{- else -}}
	<a href="{{ $url }}"{{ with .Title | default .Name }} title="{{ . }}"{{ end }}{{ if (urls.Parse $url).Host }} rel="noopener noreffer" target="_blank"{{ end }}>
		{{- dict "Src" $avatar | partial "plugin/image.html" -}}
	</a>
{{- end -}}
```

打开`\layouts\partials\assets.html`，在你引入的`jquery`的下面添加如下代码，不知道怎么引入`jquery`的请看前文：

```
<!-- 头像挂件 -->
<script>
{{- $profile := .Site.Params.home.profile -}}
{{- $avatarPlugin := $profile.avatarPluginURL -}}
{{- $avatarPluginFrequency := $profile.avatarPluginFrequency -}}
{{- $avatarPluginCount := $profile.avatarPluginCount -}}
{{- $cdnPrefix := .Site.Params.cdnPrefix -}}
{{- if $avatarPlugin -}}
	/* 头像挂件自动刷新 */
	{{- if $profile.avatarPluginFlush -}}
		$(function () {
			$(".site-avatar-plug-bilibili").attr("src", "{{ $cdnPrefix }}/images/avatar-plug/bilibili_" + (~~({{ $avatarPluginCount }}*Math.random())+1) + ".png");
		});
	{{- else -}}
		$(function () {
			$(".site-avatar-plug-bilibili").attr("src", "{{ $cdnPrefix }}{{ $avatarPlugin }}");
		});
	{{- end -}}
	
	/* 点击头像更换b站挂件 */
	var avatar_plug = 0;
	var avatar_click = 1;
	jQuery(document).ready(function($) {
		/* 点击频率，点击几次就换挂件 */
		var frequency = {{ $avatarPluginFrequency }};
		/* 头像挂件总数 */
		var plug_count = {{ $avatarPluginCount }};
		$("div.home-avatar a").click(function(e) {
			if (avatar_click % frequency === 0) {
				avatar_plug ++;
				$(".site-avatar-plug-bilibili").attr("src", "{{ $cdnPrefix }}/images/avatar-plug/bilibili_" + avatar_plug + ".png");
			}		
			if (avatar_plug === plug_count) {
				avatar_plug = 0;
			}
			$("div.home-avatar a").attr("alt","再点击" + (frequency - avatar_click % frequency) + "次头像试试看~~");
			avatar_click ++;
		});
	});
{{- end -}}
</script>
```

在自定义的`_custom.scss`里添加如下代码：

```
/* 首页头像 */
/* bilibili头像挂件 */
img.site-avatar-plug-bilibili {
    position: absolute;
    display: block;
    margin: -2rem !important;
    padding: 0;
    width: 9rem !important;
    max-width: 168px;
    height: auto;
    box-shadow: none !important;
    z-index: 1;
    pointer-events: none;
}

/* 头像旋转 */
.home .home-profile .home-avatar img {
    width: 5rem;

  /* 设置循环动画
  [animation: 
	(play)动画名称
	(2s)动画播放时长单位秒或微秒
	(ease-out)动画播放的速度曲线为以低速结束 
	(1s)等待1秒然后开始动画
	(1)动画播放次数(infinite为循环播放) ]*/
 
  /* 鼠标经过头像旋转360度 */
  -webkit-transition: -webkit-transform 1.0s ease-out;
  -moz-transition: -moz-transform 1.0s ease-out;
  transition: transform 1.0s ease-out;
    &:hover {
      /* 鼠标经过停止头像旋转 
      -webkit-animation-play-state:paused;
      animation-play-state:paused;*/

      /* 鼠标经过头像旋转360度 */
      -webkit-transform: rotateZ(360deg);
      -moz-transform: rotateZ(360deg);
      transform: rotateZ(360deg);
    }
}
/* Z 轴旋转动画 */
@-webkit-keyframes play {
  0% {
    -webkit-transform: rotateZ(0deg);
  }
  100% {
    -webkit-transform: rotateZ(-360deg);
  }
}
@-moz-keyframes play {
  0% {
    -moz-transform: rotateZ(0deg);
  }
  100% {
    -moz-transform: rotateZ(-360deg);
  }
}
@keyframes play {
  0% {
    transform: rotateZ(0deg);
  }
  100% {
    transform: rotateZ(-360deg);
  }
}
```

头像和挂件的样式代码可能根据个人的定制化而需要微调下位置之类的。至于头像挂件这些图片请去我的站点里下载下来，下面是具体地址： [https://github.com/lewky/lewky.github.io/tree/master/images/avatar-plug](https://github.com/lewky/lewky.github.io/tree/master/images/avatar-plug)

文章数量统计实际上就是添加 html 里的`sup`标签，借助 hugo 提供的变量来获取对应的文章数量，即可实现该功能。

拷贝`\themes\LoveIt\layouts\taxonomy\list.html`到`\layouts\taxonomy\list.html`，打开拷贝后的文件，找到如下内容：

```
{{- if eq $taxonomy "category" -}}
    <i></i> {{ .Title }}
{{- else if eq $taxonomy "tag" -}}
    <i></i> {{ .Title }}
{{- else -}}
```

改成如下：

```
{{- if eq $taxonomy "category" -}}
    <i></i> {{ .Title }}<sup>{{ len .Pages }}</sup>
{{- else if eq $taxonomy "tag" -}}
    <i></i> {{ .Title }}<sup>{{ len .Pages }}</sup>
{{- else -}}
```

继续找到如下内容：

```
{{- range $pages.PageGroups -}}
    <h3>{{ .Key }}</h3>
```

改成如下：

```
{{- range $pages.PageGroups -}}
    <h3>{{ .Key }} <sup>{{ len .Pages }}</sup></h3>
```

原本主题的文章是按照年份来分组的，如果想按照月份来分组，找到下面的内容：

```
{{- /* Paginate */ -}}
{{- if .Pages -}}
    {{- $pages := .Pages.GroupByDate "2006" -}}
```

改成如下：

```
{{- /* Paginate */ -}}
{{- if .Pages -}}
    {{- $pages := .Pages.GroupByDate "2006-01" -}}
```

拷贝`\themes\LoveIt\layouts\taxonomy\terms.html`到`\layouts\taxonomy\terms.html`，打开拷贝后的文件，找到如下内容：

```
<div>
    {{- /* Title */ -}}
    <h2>
        {{- .Params.Title | default (T $taxonomies) | default $taxonomies | dict "Some" | T "allSome" -}}
    </h2>
```

改成如下：

```
<div>
    {{- /* Title */ -}}
    <h2>
        {{- .Params.Title | default (T $taxonomies) | default $taxonomies | dict "Some" | T "allSome" -}}<sup>{{ len .Pages }}</sup>
    </h2>
```

找到如下内容：

```
<h3>
    <a href="{{ .RelPermalink }}">
        <i></i> {{ .Page.Title }}
    </a>
</h3>
```

改成如下：

```
<h3>
    <a href="{{ .RelPermalink }}">
        <i></i> {{ .Page.Title }} <sup>{{ len .Pages }}</sup>
    </a>
</h3>
```

拷贝`\themes\LoveIt\layouts\_default\section.html`到`\layouts\_default\section.html`，打开拷贝后的文件，找到如下内容：

```
<div>
    {{- /* Title */ -}}
    <h2>
        {{- .Params.Title | default (T .Section) | default .Section | dict "Some" | T "allSome" -}}
    </h2>
```

改成如下：

```
<div>
    {{- /* Title */ -}}
    <h2>
        {{- .Params.Title | default (T .Section) | default .Section | dict "Some" | T "allSome" -}}<sup>{{ len .Pages }}</sup>
    </h2>
```

找到如下内容：

```
{{- range $pages.PageGroups -}}
            <h3>{{ .Key }}</h3>
```

改成如下：

```
{{- range $pages.PageGroups -}}
    <h3>{{ .Key }} <sup>{{ len .Pages }}</sup></h3>
```

这里同样是按照年份来分组的，如果想按照月份来分组，找到下面的内容：

```
{{- /* Paginate */ -}}
{{- if .Pages -}}
    {{- $pages := .Pages.GroupByDate "2006" -}}
```

改成如下：

```
{{- /* Paginate */ -}}
{{- if .Pages -}}
    {{- $pages := .Pages.GroupByDate "2006-01" -}}
```

默认侧边栏的目录是全展开的，如果文章太长，小标题太多，就会导致目录非常长，看起来不方便。可以改成只展开当前正在查看的小标题对应的目录，在`_custom.scss`里添加如下样式：

```
/* 目录 */
nav#TableOfContents ol {
    padding-inline-start: 30px;

    & ol {
        padding-inline-start: 25px;
        display: none;
    }

    & li.has-active ol {
        display: block;
    }
}
```

在`config.toml`添加如下变量：

```
# Display a message at the beginning of an article to warn the readers that it's content may be outdated.
  # 在文章末尾显示提示信息，提醒读者文章内容可能过时。
  [params.outdatedInfoWarning]
    enable = true
    hint = 90     # Display hint if the last modified time is more than these days ago.    # 如果文章最后更新于这天数之前，显示提醒
    warn = 180    # Display warning if the last modified time is more than these days ago.    # 如果文章最后更新于这天数之前，显示警告
```

这里是对全局文章生效，也可以在每篇文章的文件头里添加如下变量来控制是否启用该功能：

```
outdatedInfoWarning: false
```

将`\themes\LoveIt\i18n\zh-CN.toml`拷贝到`\i18n\zh-CN.toml`，然后将该文件重命名为`zh-cn.toml`。因为站点配置文件里的中文语言代码应该是全小写的`zh-cn`，如下：

```
defaultContentLanguage = "zh-cn"
```

打开拷贝后的文件，添加如下内容：

```
[outdatedInfoWarningBefore]
  other = "本文最后更新于 "

[outdatedInfoWarningAfter]
  other = "，文中内容可能已过时，请谨慎使用。"
```

如果你的国际化文件是用的`yaml`格式，则如下：

```
outdatedInfoWarningBefore:
  other: "本文最后更新于 "

outdatedInfoWarningAfter:
  other: "，文中内容可能已过时，请谨慎使用。"
```

如果有配置了其他语言，可以添加上面两个变量到对应的国际化文件里，自行修改`other`所对应的值即可。

新建模板文件`/layouts/partials/single/outdated-info-warning.html`，内容如下：

```
{{- if or .Params.outdatedInfoWarning (and .Site.Params.outdatedInfoWarning.enable (ne .Params.outdatedInfoWarning false)) }}
  {{- $daysAgo := div (sub now.Unix .Lastmod.Unix) 86400 }}
  {{- $hintThreshold := .Site.Params.outdatedInfoWarning.hint | default 30 }}
  {{- $warnThreshold := .Site.Params.outdatedInfoWarning.warn | default 180 }}

  {{- $updateTime := .Lastmod }}
  {{- if .GitInfo }}
    {{- if lt .GitInfo.AuthorDate.Unix .Lastmod.Unix }}
      {{- $updateTime := .GitInfo.AuthorDate }}
    {{- end }}
  {{- end -}}

  {{- if gt $daysAgo $hintThreshold }}
	{{- $iconDetails := "fas fa-angle-right fa-fw" -}}
    {{- if gt $daysAgo $warnThreshold }}
		{{- $type := "warning" -}}
		{{- $icon := "fas fa-exclamation-triangle fa-fw" -}}
		<div class="details admonition {{ $type }} open">
			<div>
				<i class="icon {{ $icon }}{{ $type }}"></i>{{ T $type }}<i class="details-icon {{ $iconDetails }}"></i>
	{{- else }}
		{{- $type := "note" -}}
		{{- $icon := "fas fa-pencil-alt fa-fw" -}}
		<div class="details admonition {{ $type }} open">
			<div>
				<i class="icon {{ $icon }}{{ $type }}"></i>{{ T $type }}<i class="details-icon {{ $iconDetails }}"></i>
    {{- end }}
		    </div>
			<div>
				<div>
					{{ T "outdatedInfoWarningBefore" -}}
					<span datetime="{{ dateFormat "2006-01-02T15:04:05" $updateTime }}" title="{{ dateFormat "January 2, 2006" $updateTime }}">
					{{- dateFormat "January 2, 2006" $updateTime -}}
					</span>{{ T "outdatedInfoWarningAfter" -}}
				</div>
			</div>
		 </div>
  {{- end -}}
{{- end -}}
```

将`/themes/LoveIt/layouts/posts/single.html`拷贝到`/layouts/posts/single.html`，打开拷贝后的文件，找到如下内容：

```
{{- /* Content */ -}}
<div>
    {{- dict "Content" .Content "Ruby" $params.ruby "Fraction" $params.fraction "Fontawesome" $params.fontawesome | partial "function/content.html" | safeHTML -}}
</div>
```

修改成如下：

```
<div>
    {{- dict "Content" .Content "Ruby" $params.ruby "Fraction" $params.fraction "Fontawesome" $params.fontawesome | partial "function/content.html" | safeHTML -}}
	
	{{- /* Outdated Info Warning */ -}}
	{{- partial "single/outdated-info-warning.html" . -}}
</div>
```

在`config.toml`添加如下变量：

```
[params.reward]                                  # 文章打赏
    enable = true
    wechat = "/images/wechat.png"           # 微信二维码
    alipay = "/images/alipay.png"           # 支付宝二维码
```

这里是对全局文章生效，也可以在每篇文章的文件头里添加如下变量来控制是否启用该功能：

至于微信和支付宝的收款码，如果不想用官方提供的样式，可以参考这篇文章：[微信支付宝收款码制作和美化如何做？](http://blog.sina.com.cn/s/blog_76f1a1430102yxff.html)

将`\themes\LoveIt\i18n\zh-CN.toml`拷贝到`\i18n\zh-CN.toml`，然后将该文件重命名为`zh-cn.toml`。因为站点配置文件里的中文语言代码应该是全小写的`zh-cn`，如下：

```
defaultContentLanguage = "zh-cn"
```

打开拷贝后的文件，添加如下内容：

```
[reward]
  other = "赞赏支持"

[rewardAlipay]
  other = "支付宝打赏"

[rewardWechat]
  other = "微信打赏"
```

如果你的国际化文件是用的`yaml`格式，则如下：

```
reward:
  other: "赞赏支持"

rewardAlipay:
  other: "支付宝打赏"

rewardWechat:
  other: "微信打赏"
```

如果有配置了其他语言，可以添加上面两个变量到对应的国际化文件里，自行修改`other`所对应的值即可。

新建模板文件`/layouts/partials/single/reward.html`，内容如下：

```
{{ if or .Params.reward (and .Site.Params.reward.enable (ne .Params.reward false)) -}}
<div>
  <input type="checkbox"  hidden />
  <label for="reward">{{ T "reward" }}</label>
  <div>
    {{ $qrCode := .Site.Params.reward }}
	{{- $cdnPrefix := .Site.Params.cdnPrefix -}}
    {{ with $qrCode.wechat -}}
      <label for="reward">
        <img src="{{ $cdnPrefix }}{{ . }}">
        <span>{{ T "rewardWechat" }}</span>
      </label>
    {{- end }}
    {{ with $qrCode.alipay -}}
      <label for="reward">
        <img src="{{ $cdnPrefix }}{{ . }}">
        <span>{{ T "rewardAlipay" }}</span>
      </label>
    {{- end }}
  </div>
</div>
{{- end }}
```

将`/themes/LoveIt/layouts/posts/single.html`拷贝到`/layouts/posts/single.html`，打开拷贝后的文件，找到如下内容：

```
{{- /* Content */ -}}
<div>
    {{- dict "Content" .Content "Ruby" $params.ruby "Fraction" $params.fraction "Fontawesome" $params.fontawesome | partial "function/content.html" | safeHTML -}}
</div>
```

修改成如下：

```
{{- /* Content */ -}}
<div>
    {{- dict "Content" .Content "Ruby" $params.ruby "Fraction" $params.fraction "Fontawesome" $params.fontawesome | partial "function/content.html" | safeHTML -}}

	{{- /* Reward */ -}}
	{{- partial "single/reward.html" . -}}
</div>
```

在自定义的`_custom.scss`里添加如下样式：

```
/* 打赏 */
article .post-reward {
    margin-top: 20px;
    padding-top: 10px;
    text-align: center;
    border-top: 1px dashed #e6e6e6
}

article .post-reward .reward-button {
    margin: 15px 0;
    padding: 3px 7px;
    display: inline-block;
    color: #c05b4d;
    border: 1px solid #c05b4d;
    border-radius: 5px;
    cursor: pointer
}

article .post-reward .reward-button:hover {
    color: #fefefe;
    background-color: #c05b4d;
    transition: .5s
}

article .post-reward #reward:checked~.qr-code {
    display: block
}

article .post-reward #reward:checked~.reward-button {
    display: none
}

article .post-reward .qr-code {
    display: none
}

article .post-reward .qr-code .qr-code-image {
    display: inline-block;
    min-width: 200px;
    width: 40%;
    margin-top: 15px
}

article .post-reward .qr-code .qr-code-image span {
    display: inline-block;
    width: 100%;
    margin: 8px 0
}

article .post-reward .qr-code .image {
    width: 200px;
    height: 200px
}
```

*   [自定义 Hugo 主题样式](https://sr-c.github.io/2020/07/21/Hugo-custom/)
*   [kaushalmodi / hugo-search-fuse-js](https://github.com/kaushalmodi/hugo-search-fuse-js)
*   [Hugo 篇四：添加友链卡片 shortcodes](https://blog.233so.com/2020/04/friend-link-shortcodes-for-hugo-loveit/)
*   [img 标签设置默认图片](https://www.cnblogs.com/wxcbg/p/7459022.html)
*   [DesertsP/Valine-Admin](https://github.com/DesertsP/Valine-Admin)
*   [Hexo 主题使用 Valine-Admin 管理评论和评论提醒](https://segmentfault.com/a/1190000021474516?utm_source=tag-newest)
*   [最新版基于 Leancloud 或 javascript 推送 Valine 评论到 QQ](https://w4j1e.xyz/posts/lpush.html)
*   [优雅解决 LeanCloud 流控问题](https://www.antmoe.com/posts/ff6aef7b/)
*   [cron-job.org](https://cron-job.org/en/members/jobs/)
*   [Qmsg 酱](https://qmsg.zendee.cn/)
*   [hexo 中添加鼠标右键功能](https://www.zyoushuo.cn/post/4445.html)
*   [Fuse.js 模糊搜索引擎](https://blog.csdn.net/weixin_46382477/article/details/108144964)
*   [使用 fuse.js 进行搜索](https://www.jianshu.com/p/d0c8c3de8233)
*   [在搭配 Volantis 主题的 hexo 博客上使用 waline](https://www.hin.cool/posts/waline.html)

赞赏支持

 ![](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/common/wechat.png) 微信打赏 ![](https://cdn.jsdelivr.net/gh/lewky/lewky.github.io@master/images/common/alipay.png) 支付宝打赏