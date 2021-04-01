> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [kuang.netlify.app](https://kuang.netlify.app/blog/hugo.html)

Hugo 是使用 go（又称 golang）语言编写的一个静态网站生成器，主要用于构建 Blog、项目文档、静态网站等。以其极快的速度、强大的内容管理和强大模板语言而著称，不仅解决了环境依赖、性能较差的问题，还有简单、易用、高效、易扩展、快速部署等诸多优点，开发过程中使用 LiveReload（即时渲染，即时预览网站效果），极大优化了文章写作体验。

“静态” 的含义反映在网站页面的生成时间。一般 web 服务器（WordPress、Ghost、Drupal 等）在收到页面请求时，需要调用数据库生成页面，再返回给用户请求。静态网站在建立网站前就生成所有页面（称为静态文件），访问时直接返回现成的静态页面，不需要数据库参与。

一、概述
----

静态网站基本不需要维护，不需要考虑复杂的运行时间、依赖、数据库和安全性等问题。静态网站最大好处是访问快速。当网站有更改时，静态网站生成器需要重新生成所有与更改相关的页面。对于小型的个人网站、项目主页等，网站规模很小，重新生成整个网站也非常快。Hugo 在速度方面做得非常好，官方宣传每篇页面的生成时间不到 1ms。

如果说速度快是 Hugo 的第一大优点，那么安装简单应该就是 Hugo 的第二大优点。

Hexo 基于 node.js 开发，需要依赖环境才能运行，要实现一些主题额外功能还需要下载和匹配各种依赖，甚是麻烦。Hugo 基于 go 开发，打包成独立文件，不需要额外环境，安装并设置系统的环境变量后，就能直接运行，可以说是非常便利和快捷。

### 1、安装

示例：Windows 平台安装 Hugo。

1.  [Hugo Releases](https://github.com/gohugoio/hugo/releases) 下载对应平台的 Hugo 二进制文件
    
    ![](https://kuang.netlify.app/blog/hugo/image-20191127114555468.png)
    
2.  本地创建一个目录存储 Hugo 的可执行文件和项目文件
    
    在本地创建一个新目录：示例为 **E:/Hugo**。
    
    *   Hugo 目录中创建一个子目录（**E:/Hugo/bin**）：存储可执行文件
    *   Hugo 目录中创建另一个子目录（**E:/Hugo/sites**）：存储 Hugo 项目文件
3.  下载的 zip 包解压到 **E:/Hugo/bin** 文件夹内，就完成了安装
    
4.  设置环境变量，将 **E:/Hugo/bin** 添加到系统变量中的环境变量 Path 中
    
5.  安装好后，运行`hugo version`检测是否成功返回版本号
    
    ```
    $ hugo version
    
    Hugo Static Site Generator v0.61.0-9B445B9D windows/amd64 BuildDate: 2019-12-11T08:29:13Z
    
    ```
    

升级时：将新版本的 zip 包解压覆盖 **E:/Hugo/bin** 文件夹内的文件。

### 2、命令

语法：

command 的参数（可使用`hugo --help`命令查看其详细帮助文档）有：

<table><thead><tr><th>command</th><th>功能</th></tr></thead><tbody><tr><td>config</td><td>输出（显示）站点配置</td></tr><tr><td>convert</td><td>转换内容（content）的格式</td></tr><tr><td>deploy</td><td>将站点部署到云提供商</td></tr><tr><td>env</td><td>输出（显示）Hugo 版本和环境信息</td></tr><tr><td>gen</td><td>几个有用的生成器的集合</td></tr><tr><td>help</td><td>关于任何命令的帮助</td></tr><tr><td>import</td><td>从其他站点导入您的站点</td></tr><tr><td>list</td><td>列出内容的各种类型</td></tr><tr><td>mod</td><td>Hugo 模块助手</td></tr><tr><td>new</td><td>为您的站点创建新内容</td></tr><tr><td>server</td><td>一个高性能的网络服务器（调用本地 web 服务器）</td></tr><tr><td>version</td><td>输出（显示）Hugo 的版本号</td></tr></tbody></table>

#### 2.1 创建内容文件

在项目根目录（又称网站根目录，示例为 **Hugo/sites/ebook**）中运行 Hugo 命令。

*   新建网站（Hugo 项目）或主题
    
    参数：
    
    <table><thead><tr><th>command</th><th>功能</th><th>示例</th><th>备注</th></tr></thead><tbody><tr><td>site</td><td>新建项目</td><td><code>hugo new site ebook</code></td><td>项目以目录形式存在，目录名任意</td></tr><tr><td>theme</td><td>新建主题</td><td></td><td></td></tr></tbody></table>
*   网站内新建内容（称为页面文件）
    
    根据提供的路径（path 参数）创建一个新的内容文件，根据原型文件自动设置日期和标题等。原型文件使用 “-k｜–kind” 参数指定或由 Hugo 智能猜测。
    
    <table><thead><tr><th>参数</th><th>描述</th><th>示例</th><th>备注</th></tr></thead><tbody><tr><td>path</td><td>内容文件在<strong> content</strong> 目录中的相对路径</td><td><code>hugo new hugo/hugo.md</code></td><td>文件路径：content/hugo/hugo.md</td></tr><tr><td>flags</td><td>使用 “-k｜–kind” 指定内容文件的类型</td><td><code>hugo new --kind chapter hugo/_index.md</code></td><td>文件类型指定为 chapter</td></tr></tbody></table>

#### 2.2 编译内容文件

*   本地预览
    
    编译生成缓存的静态文件（不会输出到 **public** 目录）并启动本地 web 服务：
    
    ```
    $ hugo server
    
    Building sites …
    
    …………                         
    
    Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
    
    Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
    
    Press Ctrl+C to stop
    
    ```
    
    一直保持运行命令。手动结束命令后，将会清除缓存的静态文件。
    
*   生成网站文件
    
    生成静态文件并输出到默认的 **public** 目录（不会启动本地 web 服务）：
    
    ```
    $ hugo
    
    Building sites …
    
                       | EN
    
    +------------------+-----+
    
      Pages            |  47
    
      Paginator pages  |   0
    
      Non-page files   |  47
    
      Static files     | 211
    
      Processed images |   0
    
      Aliases          |  11
    
      Sitemaps         |   1
    
      Cleaned          |   0
    
      
    
    Total in 897 ms
    
    ```
    

### 3、项目

#### 3.1 、新建项目

CLI（命令行工具，例如 cmd、PowerShell 或 Bash 等）进入 **E:/Hugo/sites** 目录：

```
E:\>cd E:\Hugo\sites

E:\Hugo\sites>

```

在 **E:/Hugo/sites** 目录下新建项目（假设名称为 ebook）：

```
$ hugo new site ebook

Congratulations! Your new Hugo site is created in E:\Hugo\sites\ebook.



Just a few more steps and you're ready to go:



1. Download a theme into the same-named folder.

   Choose a theme from https://themes.gohugo.io/ or

   create your own with the "hugo new theme <THEMENAME>" command.

2. Perhaps you want to add some content. You can add single files

   with "hugo new <SECTIONNAME>\<FILENAME>.<FORMAT>".

3. Start the built-in live server via "hugo server".



Visit https://gohugo.io/ for quickstart guide and full documentation.

```

*   提示信息：下载主题、添加内容、运行`hugo server`命令
*   Hugo 没有预设主题，安装 Hugo 后需要下载主题

#### 3.2 、项目结构

项目目录的树结构为：

```
.                    

├─archetypes         

| └─default.md       

├─content            

| ├─_index.md        

| ├─章节文件夹        

| | ├─_index.md      

| | ├─内容文件夹      

| |   ├─xxx.md       

| |   └─本地图片      

├─data               

├─layouts            

├─resource           

├─static             

├─themes             

| └─XXX主题          

| | └─layouts        

├─public             

└─config.toml        

```

*   content 目录：存放网站内所有页面文件的源代码
*   data 目录：存放网站的自定义配置文件
*   static 目录：存放源代码的静态资源文件（图片、css、js 等）。渲染时，会直接将 static 目录的文件直接复制到 public 目录，不做任何渲染
*   指定主题：在 **config.toml** 中使用`theme = "xxx"`设置默认主题（**xxx** 是 theme 目录内的主题名）， 或运行`hugo｜hugo server --theme=xxx`命令时设置临时渲染主题
*   优先级：若存在同名文件，Hugo 优先使用项目根目录内的文件，然后才是 themes 目录内的指定主题

### 4、Hugo 的坑

虽然只是一些小问题，但每次重复发生，也是一个麻烦。

1.  Hugo 的 Date 属性带有时区
    
    Hugo 日期格式：date: 2019-12-11T23:19:14+08:00；Hexo 日期格式：date: 2019-11-25 18:49:20。在迁移时注意此问题并进行格式转换。
    
2.  Hugo 的网址链接
    
    若直接输入网址（示例：https://gohugo.io/），Hugo 不能识别为网址；
    
    若粗体显示网址（示例：**[https://gohugo.io/](https://gohugo.io/)**），Hugo 能识别为网址。
    
3.  Hugo 默认使用 “Pretty URLs”（漂亮 URL）,Hexo 使用 “Ugly URLs”（丑陋 URL）
    
    ```
    ├─content           
    
    | ├─_index.md       
    
    | ├─posts           
    
    | | ├─_index.md     
    
    | | ├─first.md      
    
    | | └─topic         
    
    | | | ├─second.md   
    
    | | | └─123.jpg     
    
    ```
    
    “_index.md”和”index.md”文件在 Hugo 中具有特殊含义（用于组织页面）。在 “Pretty URLs” 渲染下，与实际路径一致。
    
    若文件名不为 “_index.md” 或“index.md”时，“Pretty URLs”将 md 文件渲染为目录。导致后果：md 文件引用本地图片时，渲染路径比实际路径多了一个与文件名同名的目录名，导致引用图片失败。
    
4.  Hugo_v0.60 的 Markdown 渲染引擎使用 [Goldmark](https://github.com/yuin/goldmark/)，Typora 使用 [GFM’s spec](https://github.github.com/gfm/)
    
    1.  代码（`<code>`标签）/ 代码块（`<pre>`标签）内包含 html 或 js 语法时
        
        *   Typora
            
            Typora 内的代码块会屏蔽所有语法，只显示源代码。
            
        *   Hugo（Goldmark）
            
            **config.toml** 中若设置了`unsafe = true`，将会允许 md 文件使用 html 和 js 语法，执行 html 或 js 语句的优先级高于代码块，结果执行代码块内的 html 语句，导致格式错乱。因此，必须慎重开启 unsafe。
            
    2.  在列表项中新建块 / 段落时
        
        Typora 成功（参阅[严格模式](https://support.typora.io/Strict-Mode/)，使用 Enter 键代替输入空格），Hugo 有时失败。
        
        *   原因一：Hugo 的 **config.toml** 开启了`unsafe = true`
            
        *   原因二：列表项中有代码块（`<pre>`标签）
            
            Hugo 有时渲染成功，有时渲染失败，暂未明原因。
            
            解决方法：貌似只要一次性完成列表项就能成功，即不要在现有列表项中插入新的块 / 段落。若需插入，应该是重写列表项内的所有内容。
            
        *   原因三：列表项中的代码块的扉页语法貌似不能使用 yaml 语法
            
            在 md 文件的扉页使用了 yaml 语法的前提下：
            
            *   若在列表项中的代码块的扉页语法同样使用 yaml 语法，会造成格式错乱，解决方法是改用 toml 语法
            *   若代码块不在列表项中时，扉页语法可正常使用 yaml 语法
    3.  标题锚点的语法
        
        *   Typora
            
            只能实现页面内跳转（简捷语法：`<a href="#标题路径">xx</a>`）、页面跳转（Markdown 语法：`[替换文本](文件相对路径)`）。
            
        *   Hugo
            
            Hugo 生成的每一个 html 文件和 html 文件内的每一个标题在网络上都有唯一的 URL，可实现页面内（简捷语法：`<a href="#标题网址">xx</a>`）、页面、页面间跳转，建议统一使用 Markdown 语法：`[替换文本](标题网址)`）。
            

二、主题
----

Hugo 通过主题将 md 文件渲染为 HTML 格式，主题实质是 HTML/JavaScript/CSS 的集合：

*   HTML：构造页面的标签元素
*   CSS：配置标签元素的样式
*   JavaScript：设置标签元素的动作

若想自定义（修改）主题，首先定位 html 文件了解主题的架构、使用浏览器的开发者工具（F12）了解标签元素和其样式，然后[遵循此页面](https://gohugo.io/themes/customizing/)新建一个 HTML/JavaScript/CSS 文件（直接修改主题代码是一个坏习惯）：

*   主题的预设部件（**themes/hugo-theme-learn/layouts/partials** 目录）：
    
    在 **layouts/partials** 目录内自定义一个同名文件（优先级高于主题预设的部件）。
    
*   主题的预设 CSS（**themes/hugo-theme-learn/static/css** 目录）：
    
    在 **static/css** 目录内自定义一个 css 文件（优先级高于主题预设 CSS）。
    

### 1、安装主题

Hugo 没有预设主题，可在 Hugo 的[官方网站](https://themes.gohugo.io/)（或[此 GitHub 仓库](https://github.com/kingfsen/hugoThemes)）将目标主题下载（克隆）到 Hugo 项目根目录内的 **themes** 目录中。

```
git clone 主题的git地址 themes/新名称

```

1.  进入 themes 目录（**E:/Hugo/sites/ebook/themes**）
    
    ```
    E:\Hugo\sites\ebook>cd themes
    
    ```
    
2.  安装主题
    
    推荐：[learn](https://github.com/matcornic/hugo-theme-learn)、[docDock](https://github.com/vjeantet/hugo-theme-docdock)（基于 [learn](https://github.com/matcornic/hugo-theme-learn) 的移植，最大特色是幻灯片式播放 md 文件）。
    
    ```
    $ git clone https://github.com/matcornic/hugo-theme-learn.git
    
    Cloning into 'hugo-theme-learn'...
    
    remote: Enumerating objects: 2249, done.
    
       
    
    Receiving objects: 100% (2249/2249), 13.40 MiB | 11.00 KiB/s, done.
    
    Resolving deltas: 100% (1224/1224), done.
    
    ```
    

### 2、learn 主题

**learn** 主题的官方文档： [https://learn.netlify.com/](https://learn.netlify.com/) 。

**learn** 主题结构：

*   网站配置文件：配置网站的基本参数
    
    将 **themes/hugo-theme-learn/exampleSite/config.toml** 覆盖项目根目录内的 **config.toml** 后，进行自定义修改。
    
*   网站布局文件：构造网站的 HTML 结构
    
    **themes/hugo-theme-learn/layouts/index.html**，文件内调用其它 html 文件、Hugo 的页面变量等。
    
*   网站样式文件：渲染 HTML 标签元素的样式
    
    **themes/hugo-theme-learn/static/css**，在 html 文件中引用 CSS 文件。使用浏览器的开发者工具查看 HTML 标签元素来源于哪一个 CSS 文件。
    
*   原型文件：内容文件的模板，主要是配置扉页等
    
    **themes/hugo-theme-learn/archetypes**，learn 主题预设了 2 个原型文件：
    
    *   chapter.md：构建章节文件（type：chapter）
        
        将其放入 **archetypes** 目录内，作为一个通用模板，方便其它主题调用。
        
    *   default.md：构建文件（type：所有类型）
        

#### 2.1 网站配置文件

Hugo 的网站配置文件（**config.toml**）配置网站的整体设置，其初始默认设置非常简单：

```
baseURL = "http://example.org/"       # 网站URL，运行本地web服务器时不需要此值

languageCode = "en-us"                # 网站语言

title = "My New Hugo Site"            # 网站标题

```

**config.toml** 自定义修改后的结果：

```
baseURL = "http://example.org/"

DefaultContentLanguage = "zh-cn"

title = "无边风月"

theme = "hugo-theme-learn"



[outputs]

home = [ "HTML", "RSS", "JSON"]



[[menu.shortcuts]]

  name = "<i class='fas fa-user'></i> 简介"

  url = "/about/"

  weight = 11



[[menu.shortcuts]]

  name = "<i class='fas fa-align-justify'></i> 分类"

  url = "/categories/"

  weight = 12



[[menu.shortcuts]]

  name = "<i class='fas fa-bookmark'></i> 标签"

  url = "/tags/"

  weight = 13



[markup]

  [markup.tableOfContents]

    endLevel = 4

    startLevel = 2

  [markup.goldmark.renderer]

    unsafe = true

	

[params]

  themeVariant = "mine"

  author = "komantao"

  ordersectionsby = "weight"

```

##### 2.1.1 默认语言

根据主题的语言包（**themes/hugo-theme-learn/i18n**）内的文件名称配置网站语言：

```
languageCode = "en-us"   →→→   DefaultContentLanguage = "zh-cn"

```

##### 2.1.2 params 参数

```
[params]

  editURL = ""                           

  author = ""                            

  description = ""                       

  showVisitedLinks = false               

  disableSearch = false                  

  disableAssetsBusting = false           

  disableInlineCopyToClipBoard = false   

  disableShortcutsTitle = false          

  disableLanguageSwitchingButton = false 

  disableBreadcrumb = true               

  disableNextPrev = true                 

  ordersectionsby = "weight"             

  themeVariant = ""                      

```

##### 2.1.3 markup 参数

Hugo v_0.60 的 Markdown 渲染库改用 [Goldmark](https://github.com/yuin/goldmark/)（放弃了 [Blackfriday](https://github.com/russross/blackfriday)）。

默认情况下，Goldmark 禁止原始 HTML 和潜在危险的链接。若 Markdown 文件内有内联 HTML 或 JavaScript，则需要在 **config.toml** 内开启 unsafe：

```
[markup]

  [markup.goldmark.renderer]

    unsafe = false  

```

*   慎重开启 unsafe 选项！！！
    
    因为 Hugo 会执行代码块内的 HTML 源代码，造成版面格式错乱或其它莫名其妙的问题。
    

配置 TOC 目录的层级：

```
[markup]

  [markup.tableOfContents]

    endLevel = 4

    startLevel = 1

```

*   startLevel：设置标题标签的开始层级，在 TOC 目录内显示的最小层级是 H2
    *   若取值为 1，在 HTML 页面构建 H1 的标签；但不会在 TOC 目录内显示 H1
    *   若取值大于 1，在 HTML 页面不构建指定层级之前的标签
*   endLevel：设置标题标签的结束层级，但在 TOC 目录内显示的最大层级是 H4
    *   若取值小于 4，在 HTML 页面不构建指定层级之后的标签
    *   若取值大于 4，根据实际在 HTML 页面构建标签；但不会在 TOC 目录内显示 H5

配置语法高亮（highlight）：

```
[markup]

  [markup.highlight]

    codeFences = true

    hl_Lines = ""

    lineNoStart = 1

    lineNos = false

    lineNumbersInTable = true

    noClasses = true

    style = "monokai"

    tabWidth = 4

```

由于 learn 主题在 **header.html** 和 **footer.html** 文件中自定义了语法高亮（本地引用 highlight.js）插件，覆盖了 Hugo 的语法高亮。

##### 2.1.4 搜索功能

```
[outputs]

home = [ "HTML", "RSS", "JSON"]

```

**learn** 主题将使用 hugo 20 + 版本中的最新改进来生成 json 索引文件，以供 lunr.js javascript 搜索引擎使用。

Hugo 在 **public** 文件夹的根目录下生成 lunrjs index.json，构建网站（`hugo｜hugo server`）时，在内部生成它（不会显示在文件系统中）。

##### 2.1.5 菜单项

在菜单（menu）中定义其他菜单条目（menu.main）或快捷方式（menu.shortcuts）。

在网站配置文件 **config.toml** 中添加快捷方式：

```
[[menu.shortcuts]]

name = "<i class='fab fa-github'></i> Github repo"

identifier = "ds"

url = "https://github.com/matcornic/hugo-theme-learn"   # 使用远程仓库地址

weight = 10

[[menu.shortcuts]]

  name = "<i class='fas fa-tags'></i> 标签"

  url = "/tags"                                         

  weight = 11

[[menu.shortcuts]]

  name = "<i class='fas fa-camera'></i> 关于"

  url = "/about.html"                                   

  weight = 11

```

*   url 参数值的来源：
    
    *   使用远程仓库网址
        
    *   使用扉页中的属性值，例如：tags 属性、categories 属性
        
        1.  在 Markdown 文件的扉页中设置 tag 属性，标签按其插入顺序显示在页面的顶部
            
            ```
            +++
            
            date = 2018-11-29T08:41:44+01:00
            
            title = Theme tutorial
            
            weight = 15
            
            tags = ["tutorial", "theme"] 
            
            +++
            
            ```
            
        2.  在 menu 中显示标签
            
            ```
            [[menu.shortcuts]]
            
            name = "<i class='fas fa-tags'></i> Tags"
            
            url = "/tags"
            
            weight = 30
            
            ```
            
    *   自定义本地内容文件（示例：关于 about）
        
        1.  在 **content** 根目录内新建一个 **about.md** 文件（不需要创建子目录）
            
        2.  在 **config.toml** 文件中添加快捷方式，自定义 name 和 url
            
            可以通过更改本地的 i18n 目录内的语言包配置文件来覆盖（翻译）标题（name）。
            
            示例：在 **i18n/en.toml** 文件，添加以下内容
            
            ```
            [Shortcuts-Title]
            
            other = "<Your value>"
            
            ```
            
            若设置了`disableShortcutsTitle=true`，则此标题（name）被禁用。
            
*   url 参数值的写法由 **uglyurls** 参数决定：
    
    *   若`uglyurls = true`：丑陋 URLs，url 应该写为`url = "/about.html"`
    *   若`uglyurls = false`：漂亮 URLs，url 应该写为`url = "/about"`

#### 2.2 HTML

learn 主题预设的 html 文件存放路径：**themes/hugo-theme-learn/layouts**。

内容页面的 header 标签（包含导航），不会被覆盖。

**themes/hugo-theme-learn/layouts/partials/header.html** 文件：

1.  在内容文件中添加以下信息：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20191214131536049.png)
    
    懒得新建 html 文件，直接添加：
    
    1.  在`<head>`标签内添加 busuanzi 的 js 插件
        
        ```
        <head>
        
            <script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js">         </script>
        
        ```
        
    2.  在`<div>`标签内添加一个`<p>`标签：
        
        ```
        <div>
        
          {{if and (not .IsHome) (not .Params.chapter)}}
        
          <br>
        
          <h1>{{.Title}}</h1>
        
          {{ if not .Data.Terms }}
        
            <p>
        
               {{with .Params.author}}
        
                   <i class='fa fa-user'></i>  <a href="https://github.com/username/username.github.io">{{ . }}</a>
        
               {{end}}
        
                 <span> <i class='fa fa-calendar'></i> {{.Date.Format "2006-01-02"}}</span>
        
               <span> <i aria-hidden="true"></i> {{.Lastmod.Format "2006-01-02"}}</span>
        
               <span> 字数 {{.WordCount}}</span>
        
               <span> 阅读量 <span></span>次</span>
        
              </p>
        
            <br>
        
          {{end}}  
        
          {{end}}
        
        ```
        
2.  自定义语法高亮
    
    通过 CDN（[BootCDN](http://www.bootcdn.cn/) 或 [staticfile.org](http://staticfile.org/)）在线引用 [Highlight.js](http://highlightjs.org/) 插件。
    
    备注：若 [Highlight.js](http://highlightjs.org/) 插件无法识别编程语言，将无法着色。此时，应该不选择语言或选择近似语言。
    
    在`<head>`标签内添加：
    
    ```
    <link href="https://cdn.bootcss.com/highlight.js/9.15.10/styles/zenburn.min.css" rel="stylesheet">
    
    <script src="https://cdn.bootcss.com/highlight.js/9.15.10/highlight.min.js"></script>
    
    <script>hljs.initHighlightingOnLoad();</script>
    
    ```
    
    *   第一行：在线引用[配色方案](https://highlightjs.org/static/demo/) **monokai.min.css**，learn 主题本地使用（**themes/hugo-theme-learn/static/css/atom-one-dark-reasonable.css**），调用文件 **themes/hugo-theme-learn/layouts/partials/header.html**
        
    *   第二行：在线引用 highlight.js 压缩版，learn 主题本地使用（**themes/hugo-theme-learn/static/js/highlight.pack.js**），调用文件 **themes/hugo-theme-learn/layouts/partials/footer.html**
        
    *   第三行：调用 highlight.js 进行着色（语法高亮）
        
    
    因此，若想更改 learn 主题的语法高亮，只需要修改第一行，即将
    
    ```
    <link href="{{"css/atom-one-dark-reasonable.css" | relURL}}{{ if $assetBusting }}?{{ now.Unix }}{{ end }}" rel="stylesheet">
    
    ```
    
    修改为：
    
    ```
    <link href="https://cdn.bootcss.com/highlight.js/9.15.10/styles/zenburn.min.css" rel="stylesheet">
    
    ```
    
3.  代码块添加行号
    
    highlight.js 插件本身没有显示行号的功能，百度搜索[教程](https://jingyan.baidu.com/article/fcb5aff7fc879aadaa4a71ea.html)自行添加行号：
    
    **themes/hugo-theme-learn/layouts/partials/footer.html** 的 body 标签内的末尾位置添加：
    
    ```
    <script type="text/javascript">
    
        var e = document.querySelectorAll("pre code");
    
        var i;
    
        for (i = 0; i < e.length; i++) {
    
         e[i].innerHTML = "<ul><li>" + e[i].innerHTML.replace(/\n/g, "\n</li><li>") + "\n</li></ul>";
    
         $("code ul li").last().remove();
    
        }
    
    </script>
    
    ```
    
    *   将代码块（pre）内的代码（code）的换行符`\n`替换为标签`<ul>`和`<li>`
    *   使用 `$("code ul li").last().remove();`删除多增加的一行空行
    
    在 CSS 文件（**static/scc/theme-mine.css**）内添加：
    
    ```
    .hljs ul {
    
        list-style: decimal;
    
        margin: 0 0 0 40px!important;
    
        padding: 0
    
    }
    
    .hljs li {
    
        list-style: decimal-leading-zero;
    
        border-left: 1px solid #111!important;
    
        padding: 2px 5px!important;
    
        margin: 0!important;
    
        line-height: 14px;
    
        width: 100%;
    
        box-sizing: border-box
    
    }
    
    .hljs li:nth-of-type(even) {
    
        background-color: rgba(255,255,255,.015);
    
        color: inherit
    
    }
    
    ```
    
    *   `list-style: decimal;`是根据`<li>`标签自动编号
4.  增加 “back to top” 按钮
    
    在`<div>`标签内添加一个`<p>`标签和 js 代码：
    
    ```
    <p><i aria-hidden="true"></i></p>
    
    <script>
    
          var backButton=$('#back-to-top');
    
          backButton.hide();      
    
       function backToTop() {
    
            $('html,body').animate({scrollTop: 0}, 800);
    
       return false;
    
          }
    
          backButton.on('click', backToTop);
    
          $(window).on('scroll', function () {
    
            if ($(window).scrollTop() > $(window).height())
    
                backButton.fadeIn();
    
            else
    
                backButton.fadeOut();
    
          });
    
          $(window).trigger('scroll');
    
    </script>
    
    ```
    
    然后在 CSS 文件中设置样式：
    
    ```
    p#back-to-top{
    
        position:fixed;
    
        bottom:6%;
    
        right:3%;
    
    }
    
    p#back-to-top i{
    
        color:blue;
    
        display:block;
    
        z-index: -100;
    
    }
    
    p#back-to-top i:hover{
    
        color:red;
    
    }
    
    ```
    

##### 2.2.2 favicon

网站图标（在浏览器的标题内显示）。

在 **layouts/partials** 目录内新建一个文件，命名 **favicon.html**。然后编写 HTML。使用`<link>`标签并引用在 **static** 文件夹内的图像（假设在 **static/images** 目录内）。

```
<link rel="shortcut icon" href="/images/favicon.png" type="image/x-icon" />

```

##### 2.2.3 logo

网站徽标（在页面左上角显示）。

在 **layouts/partials** 目录内新建一个文件，命名 **logo.html**。然后编写想要的任何 HTML。使用 img 标签并引用在 **static** 文件夹内的图像（假设在 **static** 目录内）。

```
<a href="{{ .Site.BaseURL }}">

  <img src="夜空.jpg">

</a>

```

左侧菜单的页脚。

```
<p>Theme：<a href="https://getgrav.org">Grav</a> and <a href="https://gohugo.io/">Hugo</a></p>

```

#### 2.3 CSS

learn 主题预设的 CSS 文件存放路径：**themes/hugo-theme-learn/static/css**。

预设了 3 种 CSS 配色方案（red、blue、green），直接在 **config.toml** 修改参数即可使用：

```
[params]

  themeVariant = "green"

```

自定义 CSS 配色方案：在 **static/css** 目录内新建一个 CSS 文件（文件名使用 theme 前缀，例如 **theme-mine.css**）。

```
:root{

    

    --MAIN-TEXT-color:#323232; 

    --MAIN-TITLES-TEXT-color: #5e5e5e; 

    --MAIN-LINK-color:#1C90F3; 

    --MAIN-LINK-HOVER-color:#DC143C; 

    --MAIN-ANCHOR-color: #1C90F3; 



    

	

	--MENU-HEADER-BG-color:#FFFFF0; 

    --MENU-HEADER-BORDER-color:#33a1ff;  

	--MENU-WIDTH:15em; 

    

	

    --MENU-SEARCH-BG-color:#D3D3D3; 

    --MENU-SEARCH-BOX-color: #33a1ff; 

    --MENU-SEARCH-BOX-ICONS-color: #000000; 



    

    --MENU-SECTIONS-BG-color:#E0FFFF; 

    --MENU-SECTIONS-LINK-color: #000000; 

    --MENU-SECTIONS-LINK-HOVER-color: #FF0000;  

	--MENU-SECTIONS-ACTIVE-BG-color:#D3D3D3; 

    --MENU-SECTION-ACTIVE-CATEGORY-color: #777; 

    --MENU-SECTION-ACTIVE-CATEGORY-BG-color: #FDF5E6; 



    --MENU-VISITED-color: #33a1ff; 

    --MENU-SECTION-HR-color: #20272b; 

    

}



body {

    color: var(--MAIN-TEXT-color) !important;

}



#body .padding {

	padding: 15px 1rem;

}



#sidebar {

    width: var(--MENU-WIDTH) !important;

}

#body {

    margin-left: var(--MENU-WIDTH) !important;

}



textarea:focus, input[type="email"]:focus, input[type="number"]:focus, input[type="password"]:focus, input[type="search"]:focus, input[type="tel"]:focus, input[type="text"]:focus, input[type="url"]:focus, input[type="color"]:focus, input[type="date"]:focus, input[type="datetime"]:focus, input[type="datetime-local"]:focus, input[type="month"]:focus, input[type="time"]:focus, input[type="week"]:focus, select[multiple=multiple]:focus {

    border-color: none;

    box-shadow: none;

}



h1 {

	background: var(--MENU-SECTIONS-BG-color);

	font-weight: 700;

}



#chapter h1 {

	border-bottom: none;

}



#chapter p {

	text-align: left;

}

h2, h3, h4, h5 {

    color: var(--MAIN-TITLES-TEXT-color) !important;

}



h2 {

	font-size: 1.8em;

	margin: 20px 0;

	padding-left: 0px;

	font-weight: 700;

	line-height: 1.4;

	background: var(--MENU-HEADER-BORDER-color);

}

h3 {

	font-weight: 700;

	font-size: 1.2em;

	line-height: 1.4;

	margin: 10px 0 5px;

	padding-top: 10px;

}

h4 {

	font-weight: 700;

	

	font-size: 1.1em;

	line-height: 1.4;

	margin: 10px 0 5px;

	padding-top: 10px

}

h5, h6 {

	font-size: 1em;

}



a {

    color: var(--MAIN-LINK-color);

}



.anchor {

    color: var(--MAIN-ANCHOR-color);

}



a:hover {

    color: var(--MAIN-LINK-HOVER-color);

}



#sidebar ul li.visited > a .read-icon {

	color: var(--MENU-VISITED-color);

}



#body a.highlight:after {

    display: block;

    content: "";

    height: 1px;

    width: 0%;

    -webkit-transition: width 0.5s ease;

    -moz-transition: width 0.5s ease;

    -ms-transition: width 0.5s ease;

    transition: width 0.5s ease;

    background-color: var(--MAIN-LINK-HOVER-color);

}

#sidebar {

	background-color: var(--MENU-SECTIONS-BG-color);

}

#sidebar #header-wrapper {

    background: var(--MENU-HEADER-BG-color);

    color: var(--MENU-SEARCH-BOX-color);

    border-color: var(--MENU-HEADER-BORDER-color);

}

#sidebar .searchbox {

	border-color: var(--MENU-SEARCH-BOX-color);

    background: var(--MENU-SEARCH-BG-color);

}



#sidebar ul.topics > li.parent, #sidebar ul.topics > li.active {

    background: var(--MENU-SECTIONS-ACTIVE-BG-color);

}

#sidebar .searchbox * {

    color: var(--MENU-SEARCH-BOX-ICONS-color);

}



#sidebar a {

    color: var(--MENU-SECTIONS-LINK-color);

	font-weight: 400;

}



#sidebar a:hover {

    color: var(--MENU-SECTIONS-LINK-HOVER-color) !important;

}



#sidebar ul li.active > a {

    background: var(--MENU-SECTION-ACTIVE-CATEGORY-BG-color);

    color: var(--MENU-SECTION-ACTIVE-CATEGORY-color) !important;

}



#sidebar hr {

    border-color: var(--MENU-SECTION-HR-color);

}



#sidebar ul.topics ul a {

	color: #1E90FF;

}



#sidebar section#shortcuts {

	background: #DDA0DD;

}



#body #navigation {

	display: none;

}





#TableOfContents > ul > li > a {

	padding: 0;

	color: #000000;

}

#TableOfContents > ul > li > ul > li > a {

	padding: 0 20px;

}

#TableOfContents > ul > li > ul > li > ul > li > a {

	padding: 0 25px;

}





#body-inner pre {

	max-height: 300px;

    overflow: auto;

	padding: 0;

    margin: 0;

	white-space: pre;	

}



#body img, #body .video-container {

	margin: 1rem auto;

}





table th {

	background: #999999;

	text-align: center;

}

table th, table td {

  border: 1px solid #000000;

  padding: 0.2rem;

}



blockquote {

	background: #F5F5F5;

	border-left: 4px solid #0066FF;

}

```

自定义配色方案后，在 **config.toml** 中配置参数：

```
[params]

  themeVariant = "mine"

```

#### 2.4 图标和徽标

**Hugo-theme-learn** 主题已加载了 [**Font Awesome**](https://fontawesome.com/) 库，可以轻松显示 Font Awesome 中的免费图标（icon）或徽标（logo）。

浏览旧版本的 [**Font Awesome**](https://fontawesome.com/v4.7.0/icons/) 库（v5.12 不能预览图标）选择好图标（例如 [heart](https://fontawesome.com/icons/heart?style=solid)）后，复制其 HTML 后粘贴到 Markdown 文件中：

```
<i></i>

```

*   docDock 主题默认内置 **font-awesome.min.css**（不支持 fas 图标）
*   learn 主题默认内置 **fontawesome-all.min.css**（支持 fas 图标）

### 3、docDock 主题

**Hugo-theme-docDock** 主题教程：

*   官网文档： [https://docdock.netlify.com/](https://docdock.netlify.com/)
*   本地文档：**themes/hugo-theme-docdock/exampleSite/content**

**Hugo-theme-docDock** 主题结构：

*   网站配置文件：配置网站的基本参数
    
    将 **themes/hugo-theme-docdock/exampleSite/config.toml** 覆盖项目根目录内的 **config.toml** 后，进行自定义修改。
    
*   网站页面布局文件：构建页面的 HTML 结构
    
    路径：**themes/hugo-theme-docdock/layouts**。
    
    *   页面文件模板：**_default/baseof.html**
    *   单体页面模板：**_default/single.html**，例如：构建 md 文件的页面
    *   列表页面模板：**_default/list.html**，例如：构建 tags 或 categories 的页面
    *   列表页面模板：**_default/li.html**，构建 docDock 主题的 tags 首页
    *   章节页面模板：**index.html**，
*   网站样式文件：渲染 HTML 标签元素的样式
    
    路径：**themes/hugo-theme-docdock/static/css**。html 文件引用 CSS 文件，使用浏览器的开发者工具可查看 HTML 标签元素来源于哪一个 CSS 文件。
    
*   原型文件：**themes/hugo-theme-learn/archetypes**
    
    learn 主题预设了 2 个原型文件（生成内容文件的模板）：
    
    *   slide.md：构建章节文件（type：slide，幻灯片）
    *   default.md：构建文件（type：所有类型）

docDock 主题移植于 learn 主题，因此，可参考 learn 主题的配置。

#### 3.1 网站配置文件

参考[示例](https://github.com/lisliehaha/myHugoBlog/blob/master/blog/config.toml)自定义修改 **config.toml** 为：

```
baseURL = "https://kuang.netlify.com/"   # 发布网站的URL

languageCode = "en"                      # 网站语言

defaultContentLanguage = "zh-cn"         # 没有语言指示符的内容将默认为这种语言

title = "风月"                           # 网站名称

theme = "hugo-theme-docdock"             # 启用主题

enableRobotsTXT = true                   # 生成robots.txt，告诉搜索引擎允许其抓取所有内容

enableEmoji = true                       # 启用表情符号

hasCJKLanguage = true                    # true时，自动检测内容中的中文/日语/韩语。这将使.Summary和.WordCount正确识别CJK语言

enableGitInfo = true                     # Lastmod项使用Git的最后提交时间

relativeURLs = true                      # URL相对于content目录

canonifyURLs = true                      # 将相对URL转换为绝对

paginate = 10                            # 分布（pagination）中每页的默认元素数量。但分页失败，建议填写一个较大值，避免渲染失败

PaginatePath = "page"

buildDrafts = false                      # 构建（build）时，true表示包含草稿（drafts）



[outputs]

home = [ "HTML", "RSS", "JSON"]



[[menu.shortcuts]]

    name = "<i class='fa fa-th'></i> 分类"

    url = "/categories"

    weight = 12



[[menu.shortcuts]]

    name = "<i class='fa fa-tags'></i> 标签"

    url = "/tags"

    weight = 13

	

[markup]

    [markup.tableOfContents]

        endLevel = 6                                  

        startLevel = 1                                

    [markup.goldmark.renderer]

        unsafe = false



[params]

    author = "komantao"                               

	authorsdescribe =  "一缕风尘空蒙 一泓月色潋滟"    

	description = "学习笔记"                          

	github = true                                     

	editURL = ""                                      

    themeStyle = "original"                           

    themeVariant = "mine"                             

    ordersectionsby = "weight"                        

    disableHomeIcon = false                           

    disableSearch = false                             

    disableNavChevron = true                          

    highlightClientSide = true                        

    menushortcutsnewtab = true                        

	disableAssetsBusting = false

	copyrighthtml = "Copyright &

	enableGitalk = true



[params.reward]                                       

    enable = true

    wechat = "/images/wechat.png"                     



[params.gitalk]                                       

    clientID = "xxx"                                  

    clientSecret = "xxx"                              

    repo = "discuss"                                              

    owner = "xxx"                                                 

    admin= "xxx"                                                  

    id= "location.pathname"                                       

    labels= "gitalk"                                              

    perPage= 15                                                   

    pagerDirection= "last"                                        

    createIssueManually= true                                     

    distractionFreeMode= false                                    

```

*   paginate
    
    Hugo_v0.6 自动分页失败，建议将 paginate 值设置为一个较大值，避免渲染失败：
    
    > ERROR 2020/02/14 11:27:41 failed to render pages: render of “taxonomyTerm” failed: “E:\Hugo\sites\book\themes\hugo-theme-docdock\layouts_default\list.html:7:42”: execute of template failed: template: _default\list.html:8:3: executing “main” at <partial “pagination.html” $paginator>: error calling partial: “E:\Hugo\sites\book\themes\hugo-theme-docdock\layouts\partials\pagination.html:7:42”: execute of template failed: template: partials/pagination.html:7:42: executing “partials/pagination.html” at <.Next.RelPermalink>: can't evaluate field RelPermalink in type *page.Pager
    
*   启用 highlight.js
    
    ```
    [params]
    
        highlightClientSide = true
    
    ```
    
    同时，禁用（注释或删除）pygments（ 从 0.30 版本开始，被 [Chroma](https://github.com/alecthomas/chroma) 替代 ）
    
    ```
    # pygmentsCodeFences = true
    
    # pygmentsStyle = "monokailight"
    
    ```
    

#### 3.2 HTML

##### 3.2.1 网站 logo

在导航栏内（`<div>`标签）显示网站 logo。

1.  新建一个 **logo.html** 存入 **layouts/partials** 目录
    
    ```
    <a href="{{ .Site.BaseURL }}">
    
        <img src="/images/风车.gif">  /* 网站logo图片存放在static/images目录内 */
    
    </a>
    
    ```
    
2.  调用 **logo.html**
    
    在 **themes/hugo-theme-docdock/layouts/partials/original/body-beforecontent.html** 文件（`<div>`标签）内调用 **logo.html**：
    
    ```
    <div>
    
        {{ partial "logo.html" . }}
    
    ```
    

导航栏内（`<div>`标签）显示作者、作者描述、网站描述和社交链接等。

1.  **config.toml** 中设置选项开关
    
    ```
    [params]
    
        author = "komantao"                               
    
        authorsdescribe =  "一缕风尘空蒙 一泓月色潋滟"    
    
        description = "学习笔记"                          
    
        github = true                                     
    
    ```
    
2.  添加 HTML 标签
    
    ```
    {{with .Site.Params.author}}                          /* 添加作者标签 */
    
        <a>{{ . }}</a>
    
    {{end}}
    
    <br/>
    
    {{with .Site.Params.authorsdescribe}}                 /* 添加作者描述标签 */
    
        <span>{{ . }}</span>
    
    {{end}}
    
    <br/>
    
    {{with .Site.Params.github}}                          /* 添加社交标签 */
    
        <a href="https://github.com/xxx/xxx.github.io"><i aria-hidden="true"></i></a>
    
    {{end}}
    
    ```
    

##### 3.2.3 导航栏标题

在导航栏的列表（`<ul>`标签）内增加一个标记，显示为 “目录”。

在 **themes/hugo-theme-docdock/layouts/partials/original/body-beforecontent.html** 中的`<ul>`标签内新增一个标签：

```
<ul>

    <a>目录：</a>

```

显示效果：

![](https://kuang.netlify.app/blog/hugo/image-20191217230519267.png)

同理，在导航栏的`<section>`标签内增加一个标题，显示为 “其它” 并修改为：

```
<section>

    <a>其它：</a>

    {{- range sort . "Weight"}}

         <li role="">

             {{- .Pre -}}

             <a href="{{.URL}}" target="_blank" rel="noopener">{{safeHTML .Name}}</a>

             {{- .Post -}}

          </li>

    {{- end}}

</section>

```

*   `<a>`标签内的`target="_blank"`，表示新建页面打开链接。若删除，在原页面打开链接

##### 3.2.4 Top Bar

##### 3.2.4 标签页

docdock 主题预设了一个 tags 首页，点击页面内的标签（区别于点击导航栏内的 tags 快捷键）弹出的首页，文件路径为：**themes/hugo-theme-docdock/layouts/_default/li.html**。

##### 3.2.5 文章信息

在文章开始位置增加以下信息：

![](https://kuang.netlify.app/blog/hugo/image-20191214131536049.png)

1.  添加 **busuanzi** 的 js 插件
    
    在 **themes/hugo-theme-docdock/layouts/partials/original/head.html** 文件中添加：
    
    ```
    <script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js">         </script>
    
    ```
    
2.  设置 **config.toml**
    
    ```
    hasCJKLanguage = true              # 若为true，则自动检测内容中的中文/日语/韩语。这将使.Summary和.WordCount正确识别CJK语言
    
    ```
    
3.  添加 html 标签
    
    在 **themes/hugo-theme-docdock/layouts/partials/original/body-beforecontent.html** 文件中的`<div>`标签内添加一个`<p>`标签：
    
    ```
    <div>
    
        {{if and (not .IsHome) (not .Params.chapter)}}
    
            <br>
    
            <br>
    
            <h1>{{.Title}}</h1>
    
            {{ if not .Data.Terms }}
    
                <p>
    
                    {{with .Params.author}}
    
                        <i class='fa fa-user'></i>  <a>{{ . }}</a>
    
                    {{end}}
    
                    <span> <i class='fa fa-calendar'></i> {{.Date.Format "2006-01-02"}}</span>
    
                    <span> <i aria-hidden="true"></i> {{.Lastmod.Format "2006-01-02"}}</span>
    
                    <span> 字数 {{.WordCount}}</span>
    
                    <span> 阅读量 <span></span>次</span>
    
                </p>
    
                <br>
    
            {{end}}
    
        {{end}}
    
    ```
    

##### 3.2.6 Highlight.js

1.  开启 Highlight.js
    
    docdock 主题已在本地安装了 Highlight.js 插件，在 **themes/hugo-theme-docdock/layouts/partials/original/scripts.html** 文件中调用 **themes/hugo-theme-docdock/static/js/highlight.pack.js**，但只使用了缺省的配色方案。
    
    在 **config.toml** 中开启 Highlight.js：
    
    ```
    [params]
    
        highlightClientSide = true
    
    ```
    
    或通过 CDN（[BootCDN](http://www.bootcdn.cn/) 或 [staticfile.org](http://staticfile.org/)）在线引用 [Highlight.js](http://highlightjs.org/) 插件。
    
    在 **themes/hugo-theme-docdock/layouts/partials/original/head.html** 文件中添加：
    
    ```
    <link href="https://cdn.bootcss.com/highlight.js/9.15.10/styles/zenburn.min.css" rel="stylesheet">
    
    <script src="https://cdn.bootcss.com/highlight.js/9.15.10/highlight.min.js"></script>
    
    <script>hljs.initHighlightingOnLoad();</script>
    
    ```
    
    *   第一行：在线引用 zenburn.min.css [配色方案](https://highlightjs.org/static/demo/)
        
    *   第二行：在线引用 highlight.js 插件
        
    *   第三行：调用 highlight.js 进行着色（语法高亮）
        
2.  着色（选择配色方案）
    
    若选择了 [Highlight.js](http://highlightjs.org/) 插件不能识别的语言，将会着色失败，从而导致后面的添加行号也失败。解决方法是清空 "选择语言" 内容，[Highlight.js](http://highlightjs.org/) 将使用默认着色。
    
    若想更改配色方案，只需要修改第一行：
    
    ```
    <link href="https://cdn.bootcss.com/highlight.js/9.15.10/styles/zenburn.min.css" rel="stylesheet">
    
    ```
    
3.  代码块添加行号
    
    highlight.js 插件本身不能显示行号，百度搜索[教程](https://jingyan.baidu.com/article/fcb5aff7fc879aadaa4a71ea.html)自行添加行号。
    
    在 **themes/hugo-theme-docdock/layouts/partials/original/body-aftercontent.html** 文件的`{{ partial "custom-content-footer.html" . }}`后面添加：
    
    ```
    <script type="text/javascript">
    
        var e = document.querySelectorAll("pre code");
    
        var i;
    
        for (i = 0; i < e.length; i++) {
    
            e[i].innerHTML = "<ul><li>" + e[i].innerHTML.replace(/\n/g, "\n</li><li>") + "\n</li></ul>";
    
            $("code ul li").last().remove();
    
        }
    
    </script>
    
    ```
    
    *   将代码块（pre）内的代码（code）的换行符`\n`替换为标签`<ul>`和`<li>`
    *   使用 `$("code ul li").last().remove();`删除多增加的一行空行
    
    **themes/hugo-theme-docdock/static/theme-original/variant-mine.css** 文件内添加：
    
    ```
    .hljs ul {
    
        list-style: decimal;
    
        margin: 0 0 0 40px !important;
    
        padding: 0;
    
    }
    
    .hljs li {
    
        list-style: decimal-leading-zero;
    
        border-left: 1px solid #111!important;
    
        padding: 2px 5px!important;
    
        margin: 0!important;
    
        line-height: 14px;
    
        width: 100%;
    
        box-sizing: border-box;
    
    }
    
    .hljs li:nth-of-type(even) {
    
        background-color: rgba(255,255,255,.015);
    
        color: inherit;
    
    }
    
    .copy-to-clipboard {
    
        display: none;
    
    }
    
    pre .copy-to-clipboard {
    
        display: inline-block!important;
    
        top: 0;
    
        right: 0;
    
    }
    
    ```
    
    *   `list-style: decimal;`是根据`<li>`标签自动编号
4.  复制按钮
    
    docDock 主题的代码块已设计了复制按钮（Copy to clipboard）。代码块（`<pre code>`标签）设置最大高度（添加滑条）和将复制按钮固定在`<pre>`标签内、`<code>`标签外（复制按钮不承滑条而滚动）：
    
    ```
    #body-inner pre {
    
     background: black;
    
     padding: 1.5rem 0 0 0!important;    
    
     margin: 0;
    
     white-space: pre;	
    
    }
    
    #body-inner pre code {
    
     max-height: 300px;         
    
        overflow: auto;            
    
    }
    
    pre .copy-to-clipboard {
    
     display: inline-block!important;
    
     top: 0;                     
    
     right: 0;                   
    
    }
    
    ```
    

##### 3.2.7 back to top

在页面增加 “back to top” 按钮。

在 **themes/hugo-theme-docdock/layouts/partials/original/body-aftercontent.html** 文件的`{{ partial "custom-content-footer.html" . }}`后面添加一个`<p>`标签和 js 代码：

```
<p><i aria-hidden="true"></i></p>

<script>

    var backButton=$('#back-to-top');

    backButton.hide();                    

    function backToTop() {

        $('html,body').animate({scrollTop: 0}, 800);

        return false;

    }

    backButton.on('click', backToTop);

    $(window).on('scroll', function () {   

        if ($(window).scrollTop() > $(window).height())

            backButton.fadeIn();

        else

            backButton.fadeOut();

    });

    $(window).trigger('scroll');            

</script>

```

在 **themes/hugo-theme-docdock/static/theme-original/variant-mine.css** 文件内添加：

```
p#back-to-top{

    position: fixed;

    bottom: 22%;

    right: 1%;

}

p#back-to-top i{

    color: #1E90FF;

    display: block;

    z-index: -100;

}

p#back-to-top i:hover{

    color: red;

}

```

##### 3.2.8 页脚

docdock 主题页脚（footer）：**themes/hugo-theme-docdock/layouts/partials/custom-content-footer.html**。

```
<footer >

	{{with .Params.LastModifierDisplayName}}

	    <i class='fa fa-user'></i> <a href="mailto:{{ $.Params.LastModifierEmail }}">{{ . }}</a> {{with $.Date}} <i class='fa fa-calendar'></i> {{ .Format "2006/01/02" }}{{end}}

	    </div>

	{{end}}

```

在页脚（footer）中增加打赏和版权信息。

1.  **config.toml** 文件增加：
    
    ```
    [params]
    
      copyrighthtml = "Copyright &
    
    [params.reward]                             
    
        enable = true
    
        wechat = "/images/wechat.png"           
    
    ```
    
    *   在每年的 1 月 1 日，修改 “2020” 为当前年份
2.  在页脚文件末尾添加：
    
    ```
    {{if and (not .IsHome) (not .Params.chapter) (not .Data.Terms)}}
    
        {{if .Site.Params.reward.enable}}
    
         <p>感谢您的赞赏支持：</p>
    
         <img src="/images/wechat.png">
    
        {{end}}
    
        {{ if .Site.Params.copyrighthtml }}
    
            <div>{{.Site.Params.CopyrightHTML | safeHTML}}</div>
    
        {{ end }}
    
    {{ end }}
    
    ```
    

##### 3.2.9 打开图片

在新标签页打开图片。

在 **themes/hugo-theme-docdock/layouts/partials/original/body-aftercontent.html** 的`<div>`标签末尾位置添加：

```
<script type="text/JavaScript" language="javascript">

    <!--给页面中所有img对象添加onclick事件 -->

    function AddImgClickEvent(){

        var objs = document.getElementsByTagName("img");

        for(var i=0;i<objs.length;i++){

            objs[i].onclick=function(){

                window.open(this.src);

            }

            objs[i].style.cursor = "pointer";

        }

    }

    AddImgClickEvent();

</script>

```

#### 3.3 CSS

**mine.css** 命名为 **variant-mine.css**，存放在 **themes/hugo-theme-docdock/static/theme-original** 目录内：

```
:root{

    

    --MAIN-TEXT-color:#323232; 

    --MAIN-TITLES-TEXT-color: #5e5e5e; 

    --MAIN-LINK-color:#1C90F3; 

    --MAIN-LINK-HOVER-color:#DC143C; 

    --MAIN-ANCHOR-color: #1C90F3; 



    

	

	--MENU-HEADER-BG-color:#E0FFFF; 

    --MENU-HEADER-BORDER-color:#33a1ff;  

	--MENU-WIDTH:16em; 

    

	

    --MENU-SEARCH-BG-color:#D3D3D3; 

    --MENU-SEARCH-BOX-color: #33a1ff; 

    --MENU-SEARCH-BOX-ICONS-color: #000000; 



    

    --MENU-SECTIONS-BG-color:#E0FFFF; 

    --MENU-SECTIONS-LINK-color: #000000; 

    --MENU-SECTIONS-LINK-HOVER-color: #FF0000;  

	--MENU-SECTIONS-ACTIVE-BG-color: #87CEFA; 

    --MENU-SECTION-ACTIVE-CATEGORY-color: #000000; 

    --MENU-SECTION-ACTIVE-CATEGORY-BG-color: #FDF5E6; 

    --MENU-SECTION-HR-color: #20272b;   

}







body {

    color: var(--MAIN-TEXT-color) !important;

	font-family: -apple-system,BlinkMacSystemFont,Helvetica Neue,Microsoft YaHei,Source Han Sans CN,Source Han Sans SC,Noto Sans CJK SC,WenQuanYi Micro Hei,sans-serif;

	font-weight: 200;

	font-size: 16px !important;

}







#sidebar {

    max-width: var(--MENU-WIDTH) !important;

	background-color: var(--MENU-SECTIONS-BG-color);

}

#sidebar #header-wrapper {

    background: var(--MENU-HEADER-BG-color);

	border-bottom: 3px solid var(--MENU-HEADER-BORDER-color);

}

#sidebar #header-wrapper #header a.baselink {

	font-weight: 800;

	font-size: 28px !important;

}

#sidebar #header-wrapper #header span {

	color: #b22222;

}

#sidebar .searchbox {

	border-color: var(--MENU-SEARCH-BOX-color);

    background: var(--MENU-SEARCH-BG-color);

}

#sidebar .searchbox * {

    color: var(--MENU-SEARCH-BOX-ICONS-color);

}

#sidebar ul.topics > a, #sidebar ul.topics section#shortcuts > a {

    font-weight: 800;

	font-size: 20px !important;

}

#sidebar ul.topics > li.parent, #sidebar ul.topics > li.active {

    background: var(--MENU-SECTIONS-ACTIVE-BG-color);

}

#sidebar ul.topics li.active > div > a {

    background: var(--MENU-SECTION-ACTIVE-CATEGORY-BG-color);

    color: var(--MENU-SECTION-ACTIVE-CATEGORY-color) !important;

	font-weight: 800 !important;

}

#sidebar a {

    color: var(--MENU-SECTIONS-LINK-color);

	font-weight: 400;

	padding: 0 !important;

	width: calc(100%) !important;

}



#sidebar a:hover {

    color: var(--MENU-SECTIONS-LINK-HOVER-color) !important;

}

#sidebar hr {

    border-color: var(--MENU-SECTION-HR-color);

}

#sidebar section#shortcuts {

	border-top: 3px solid var(--MENU-HEADER-BORDER-color);

}



#sidebar .parent li, #sidebar .active li{

border-left:1px solid red;

} 



#body .nav{

	width:1.2rem;

}







@media only screen and (min-width: 780px) {       

    #body {

	    margin-left: 15em!important;

    }

	#body .padding {

	    padding: 0.5rem 2rem 0.5rem 2rem;

    }

	#top-github-link {

	    position: fixed;

	}

}



#body a.highlight:after {

    display: block;

    content: "";

    height: 1px;

    width: 0%;

    -webkit-transition: width 0.5s ease;

    -moz-transition: width 0.5s ease;

    -ms-transition: width 0.5s ease;

    transition: width 0.5s ease;

    background-color: var(--MAIN-LINK-HOVER-color);

}





#top-bar {

	width: 100%;

    position: fixed; top: 0;

	z-index: 99;

	background: #FFEBCD;

	margin: 0rem -1rem 1rem;

}

#breadcrumbs {

	display: inline-block;

}

#top-github-link {

	top: 1.6rem;right: 3rem;

	z-index: 199;

	display: inline-block;

}



#tags {

	margin-top: 1.8rem;

}

#tags a.label {

	background-color: #EE82EE;

}



#TableOfContents {

	padding: 2px !important;

}



#TableOfContents > ul > li > ul > li > a {

	padding: 0;

	color: #000000;

}



#TableOfContents > ul > li > ul > li > ul > li > a {

	padding: 0 20px;

}



#TableOfContents > ul > li > ul > li > ul > li > ul > li > a {

	padding: 0 30px;

	color: #FFA500;

}

textarea:focus, input[type="email"]:focus, input[type="number"]:focus, input[type="password"]:focus, input[type="search"]:focus, input[type="tel"]:focus, input[type="text"]:focus, input[type="url"]:focus, input[type="color"]:focus, input[type="date"]:focus, input[type="datetime"]:focus, input[type="datetime-local"]:focus, input[type="month"]:focus, input[type="time"]:focus, input[type="week"]:focus, select[multiple=multiple]:focus {

    border-color: none;

    box-shadow: none;

}

div#body-inner > p.authorline {

    text-align: center;

	background-color: #E0FFFF;

    border-bottom: 1px dashed #183f81!important;

}

h1, h2, h3, h4, h5, h6 {

    color: var(--MAIN-TITLES-TEXT-color) !important;

	font-weight: 700 !important;

}

h1 {

	color: red!important;

}

h2 {

	font-size: 1.8em;

	margin: 20px 0;

	padding-left: 0px;

	line-height: 1.4;

}

h3 {

	font-size: 1.2em;

	line-height: 1.4;

	margin: 10px 0 5px;

	padding-top: 10px;

}

h4 {

	

	font-size: 1.1em;

	line-height: 1.4;

	margin: 10px 0 5px;

	padding-top: 10px

}

h5, h6 {

	font-size: 1.2em;

}

a {

    color: var(--MAIN-LINK-color);

}

.anchor {

    color: var(--MAIN-ANCHOR-color);

}

a:hover {

    color: var(--MAIN-LINK-HOVER-color) !important;

}





table th {

	background: #999999;

	text-align: center;

}

table th, table td {

    border: 1px solid #000000;

    padding: 0.2rem;

}



blockquote {

	background: #E0FFFF;

	border-left: 4px solid #0066FF;

}

blockquote p,blockquote ul {

	margin: 0.5rem !important;

}



#body-inner pre {

	background: black;

	padding: 1.5rem 0 0 0!important;

    margin: 0;

	white-space: pre;	

}

code {

	white-space:pre-wrap;

}

#body-inner pre code {

	max-height: 300px;

    overflow: auto;

}

#body img, #body .video-container {

	margin: 1rem auto;

}



.hljs ul {

    list-style: decimal;

    margin: 0 0 0 40px !important;

    padding: 0;

}

.hljs li {

    list-style: decimal-leading-zero;

    border-left: 1px solid #111!important;

    padding: 2px 5px!important;

    margin: 0!important;

    line-height: 14px;

    width: 100%;

    box-sizing: border-box;

}

.hljs li:nth-of-type(even) {

    background-color: rgba(255,255,255,.015);

    color: inherit;

}

.copy-to-clipboard {

	display: none;

}

pre .copy-to-clipboard {

	display: inline-block!important;

	top: 0;

	right: 0;

}



p#back-to-top{

    position: fixed;

    bottom: 22%;

    right: 1%;

}

p#back-to-top i{

    color: #1E90FF;

    display: block;

    z-index: -100;

}

p#back-to-top i:hover{

    color: red;

}

```

Top Bar，文章页面顶部的导航栏，包含 4 部分：

![](https://kuang.netlify.app/blog/hugo/image-20200122121955654.png)

*   1：sidebar 开关，在小屏幕时显示或隐藏 sidebar
*   2：TOC 目录
*   3：面包屑（breadcrumb）导航，显示路径
*   4：GitHub 源代码仓库内此文章的代码（code）页面，使用 GItHub 的 "Edit-this-page" 插件

页面滚动时固定顶栏：

```
#top-bar {

	width: 100%;

    position: fixed; top: 0;

	z-index: 99;

	background: #FFEBCD;

	margin: 0rem -1rem 1rem;

}

#breadcrumbs {

	display: inline-block;

}

#top-github-link {

	top: 1.6rem;right: 3rem;

	z-index: 199;

	display: inline-block;

}

```

#### 3.4 i18n

复制 learn 主题中的简体中文语言包 **zh-cn.toml**（或新建）到 docDock 主题的 **i18n** 目录内：

```
[Search-placeholder]

    other = "搜索..."

[Clear-History]

    other = "清理历史记录"

[Attachments-label]

    other = "附件"

[title-404]

    other = "错误"

[message-404]

    other = "哎哟。 看起来这个页面不存在 ¯\\_(ツ)_/¯。"

[Go-to-homepage]

    other = "转到主页"

[Edit-this-page]

    other = "编辑当前页"

[Shortcuts-Title]

    other = "更多"

[Expand-title]

    other = "展开"

```

三、页面内容
------

### 1、原型

原型是创建新页面内容（运行`hugo new`命令）时使用的模板，预先配置格式：例如 md 文件的扉页（ front matter）、其它格式等。原型文件应该存放在 **archetypes** 目录内。

原型（**archetypes/default.md**）内的扉页貌似不能进行日期格式转换：

*   date 属性只能是`date: {{ .Date }}`，因为之后的日期格式转换[基于此 date 属性](https://gohugo.io/functions/format/#hugo-date-and-time-templating-reference)。若`date: {{.Date.Format "2006-01-02"}}`，将会触发错误：Error: Failed to process archetype file “default.md”:: template: default:3:19: executing “default” at <.Date.format>: can't evaluate field format in type string

#### 1.1 default.md

default.md：将 md 文件构建为 HTML 的页面文件（type：缺省）。

```
---

title: "{{ replace .Name "-" " " | title }}"

date: {{ .Date }}

author: "komantao"

LastModifierDisplayName: "komantao"

LastModifierEmail: komantao@hotmail.com

weight: 20

url: {{ replace .Dir "\\" "/" }}{{ replace .Name "-" " " | title }}.html

draft: false

description: "文章描述"

keywords: [keyword1, keyword2, keyword3]

tags: [标签1, 标签2]

categories: [分类1, 分类2]

---

首页描述。



<!--more-->

## 一、概述





## 参考资料



> - []()

> - []()

> - []()



## 操作环境



> - 操作系统：Win 10

> - 

```

#### 1.2 chapter.md

chapter.md：将 md 文件构建为 HTML 的章节文件（type：chapter）。

```
+++

title = "{{ replace .Name "-" " " | title }}"

date = {{ .Date }}

weight = 5

chapter = true

+++











章节介绍。

```

一定要设置`chapter = true`或`type="chapter"`才能生成章节文件。

#### 1.3 slide.md

slide.md：将 md 文件构建为 HTML 的幻灯片文件（type：slide）。

```
+++

title = "Slide title"

type="slide"



theme = "league"

[revealOptions]

transition= 'concave'

controls= true

progress= true

history= true

center= true

+++







___







- Turn off alarm

- Get out of bed



___







- Eat eggs

- Drink coffee



---







___







- Eat spaghetti

- Drink wine



___







- Get in bed

- 

```

docDock 主题提供了 slide 插件，必须配合插件才能生成 HTML 的幻灯片。

### 2、页面绑定

内容组织（Content Organization）指存放在 **content** 目录内的页面（pages），结构假设为：

```
├─content           

| ├─_index.md       

| ├─posts           

| | ├─_index.md     

| | └─topic         

| | | ├─topic.md    

| | | └─123.jpg     

```

备注：内容文件若引用图片，Hugo 建议将其与内容文件放在同一个目录内。

Hugo 中的内容组织的管理依赖于 Page Bundles（页面绑定）。Page Bundles 包括：

*   Leaf Bundle（没有子节点）
    
*   Branch Bundle（home page、section、taxonomy terms、taxonomy list）
    
    *   home page：网站主页
        
    *   section：章节，在 **content** 目录内定义的子目录（多个子页面的集合）
        
        **content** 目录下的第一级子目录都是一个 section。如果想让任意一个子目录成为 section，需要在目录下面定义_index.md 文件。 所有的 section 构成一个 section tree。
        
        ```
        content
        
        └── blog                       <-- Section, 因为是content的第一级子目录
        
            ├── funny-cats
        
            │   ├── mypost.md
        
            │   └── kittens            <-- Section, 因为包含_index.md
        
            │       └── _index.md
        
            └── tech                   <-- Section, 因为包含 _index.md
        
                └── _index.md
        
        ```
        
        如果 section tree 需要可导航，至少最底层的部分需要定义一个_index.md 文件。
        

### 3、扉页

扉页（ front matter）用来配置文章的标题、时间、链接、分类等元信息，提供给模板调用。可使用的格式有：yaml 格式（默认格式，使用 3 个减号 -）、toml 格式（使用 3 个加号 +）、json 格式（使用大括号 {}）。除了网站主页外，其它内容文件都需要扉页来识别文件类型和编译文件。

```
---

title: "xxx"                  

menuTitle: "xxx"              

description: "xxx"            

keywords: ["Hugo","keyword"]  

date: "2018-08-20"            

tags: [ "tag1", "tag2"]       

categories: ["cat1","cat2"]   

weight: 20                    

disableToc: "false"           

pre: ""                       

post: ""                      

chapter: false                

hidden: false                 

LastModifierDisplayName: ""   

LastModifierEmail: ""         

draft: false                  

url:                          

type:                         

layout: 

---

```

*   weight 属性
    
    *   缺省时按照 date 属性的倒序排序（新日期排在前面）
    *   设置时，自定义此页面在章节中的排序（按照数字值的正序排序，数字小的排在前面，若数字值相同，则按照 date 属性的倒序排序）
*   pre 属性
    
    在菜单栏中的标题前添加前缀：可为数字、文字、图标（[**Font Awesome**](https://fontawesome.com/v4.7.0/icons/) 库）等。
    
    ```
    +++
    
    title = "Github repo"
    
    pre = "<i class='fab fa-github'></i> "
    
    +++
    
    ```
    
*   menuTitle 属性
    
    *   缺省时调用 title 属性作为此页面在 menu 中显示的名称
    *   设置时，自定义此页面在 menu 中显示的名称

### 4、主页页面

1.  新建网站主页文件
    
    若未创建网站主页，运行`hugo server`时会提示创建一个网站主页：
    
    ```
    Customize your own home page
    
    The site is working. Don't forget to customize this homepage with your own. You typically have 3 choices : 
    
    1. Create an _index.md document in content folder and fill it with Markdown content
    
    2. Create an index.html file in the static folder and fill the file with HTML content
    
    3. Configure your server to automatically redirect home page to one your documentation page
    
    ```
    
    使用`hugo new XX.md`命令在 **content** 目录内新建一个_index.md 文件作为网站主页：
    
    ```
    $ hugo new _index.md
    
    E:\Hugo\sites\ebook\content\_index.md created
    
    ```
    
2.  编写网站主页文件
    
    *   打开网站主页文件（**content/_index.md**）文件，编写内容
    *   通常不需要扉页，即使设置了，Hugo 会忽略

### 5、章节页面

章节是包含其他子页面的页面，具有特殊的布局样式：通常仅包含章节序号、章节名称和本章节的简短摘要。

**Hugo-theme-learn** 主题调用 **chapter.md** 原型文件来构建章节文件的扉页：

1.  新建章节页面文件（同时新建了章节目录）
    
    ```
    $ hugo new --kind chapter 个人网站/_index.md
    
    E:\Hugo\sites\ebook\content\个人网站\_index.md created
    
    ```
    
2.  编写章节主页文件
    
    打开章节主页文件（**_index.md**），编写内容（关键是设置 chapter 属性值为 true）：
    
    ```
    +++
    
    title = "blog"                     # 修改标题
    
    date = 2019-11-28T13:48:56+08:00   # 修改日期
    
    weight = 5                         # 修改排序优先级别
    
    chapter = true                     # true，表示此页面是章节页面
    
    pre = "<b>X. </b>"                 # 修改在侧边栏显示的前缀
    
    +++
    
       
    
    ### 第二章                         # 修改在章节页面显示的章节序号
    
    # 个人网站                         # 修改在章节页面显示的标题
    
    介绍制作个人静态网站的各种工具。      # 修改在章节页面显示的简短摘要
    
    ```
    
    网页的效果示例：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20191128145802960.png)
    

docDock 主题没有提供 **chapter.md**，但可直接使用 learn 主题的 **chapter.md**。

### 6、内容页面

在项目根目录中执行`hugo new XX.md`命令，会在 content 目录中自动创建 XX.md 文件：

```
$ hugo new 个人网站/hugo/"hugo.md"

E:\Hugo\sites\ebook\content\个人网站\hugo\hugo.md created

```

*   文件名若有空格，则必需使用双引号括起；否则，可省略双引号
    
    不建议文件名含有空格！！！因为 Hugo 编译时，会将 URL 中文件的空格解析为`-`，从而导致本地图片链接解析失败。
    
*   创建内容文件时应该使用子目录来实现文档分类
    
*   若不指定`--kind type`，Hugo 默认调用 **archetypes/default.md** 原型文件来构建内容页面文件的扉页
    

### 7、图片

#### 7.1 引用图片

假设 **content** 目录的结构为：

```
├─content           

| ├─_index.md       

| ├─posts           

| | ├─_index.md     

| | └─topic         

| | | ├─topic.md    

| | | └─123.jpg     

```

假设在 md 文件引用图片时使用 Markdown 语法为：

```
![](123.jpg)

```

Hugo 渲染时默认使用 “Pretty URLs”，将 md 文件渲染为目录，在浏览器的地址栏中显示渲染后的路径（baseURL = “[http://example.com/"](http://example.com/%22)，渲染为本地 web 服务器 http://localhost:1313/）。

开发者工具查看本地图片的渲染地址：

```
<a href="http://localhost:1313/posts/topic/topic/123.jpg" data-featherlight="image"><img src="topic/123.jpg" alt="123"></a>

```

发现图片的渲染路径比实际多了一个与文件名同名的目录。所以，本地图片渲染失败。

解决方法：基本思路是将 “Pretty URLs” 转换为“Ugly URLs”。

*   使用 “Pretty URLs” 时
    
    *   方法 1：使用 “Pretty URLs” 的文件命名方法
        
        md 文件分目录存放，文件名统一命名为 index.md 或_index.md。
        
        ```
        ├─content           
        
        | ├─_index.md       
        
        | ├─posts           
        
        | | ├─_index.md     
        
        | | └─topic         
        
        | | | ├─_index.md   
        
        | | | └─123.jpg     
        
        ```
        
        *   md 文件的扉页的 title 属性自动将目录名（示例为 topic）识别为文件名
        *   本地图片和 markdown 文件位于同一目录内（目的是将路径简单化）
        *   缺点：一个目录只有一个 md 文件，文件名统一为 index.md 或_index.md
    *   不使用 “Pretty URLs” 的文件命名方法（md 文件名使用个性化命名）时
        
        ```
        ├─content           
        
        | ├─_index.md       
        
        | ├─posts           
        
        | | ├─_index.md     
        
        | | └─topic         
        
        | | | ├─topic.md    
        
        | | | └─123.jpg     
        
        ```
        
        *   方法 2：在 md 文件的扉页内手动设置 url 参数
            
            在 md 文件的扉页中手动设置 url 为实际路径或 “Ugly URLs”
            
            ```
            url: posts/topic         
            
            url: posts/topic.html    
            
            ```
            
            缺点：每一个有图片的 md 文件都需要手动设置 url 参数。
            
        *   方法 3：在原型文件（**archetypes/default.md**）设置变量自动获取 url 参数
            
            自动获取 url 的实际路径：
            
            *   .Dir：一个页面变量，获取内容文件的路径（相对于 content 目录）
            
            在 Windows 平台下，路径的显示格式为：blog\。本地预览时没问题，发布到网站时，此格式就造成 URL 乱码（blog%5Chugo.html），导致读取图片失败。
            
            解决方法：
            
            *   手动改变格式为：blog/
                
            *   使用 replace 替换函数：
                
                ```
                url: {{ replace .Dir "\\" "/" }}
                
                ```
                
                *   `\`是一个转义符，因此使用`\\`将其转义为普通字符。转义后，将`blog\`替换为`blog/`
            *   缺点：一个目录内只能有一个 md 文件（否则，多个 md 文件将会渲染为同一个目录，Hugo 只能选择一个）
                
            
            自动获取 “Ugly URLs”：
            
            ```
            url: {{ replace .Dir "\\" "/" }}{{ replace .Name "-" " " | title }}.html
            
            ```
            
            *   此方法最完美：
                *   既不破坏 Hugo 默认的 “Pretty URLs” 设置
                *   指定的 md 文件又能实现 “Ugly URLs”：一个目录内可以有多个 md 文件，md 文件与引用图片可以在不同目录内
                *   解决了 Windows 平台下的路径转换
    *   方法 4：在网站配置文件 **config.toml** 中设置
        
        ```
        [permalinks]
        
          blog = "/:sections/:slug/"  
        
        ```
        
        *   此方法不可行（只是演示 Permalink（固定链接）的用法），因为不能转换为 “Ugly URLs”，渲染路径依然是 “Pretty URLs”
    *   方法 5：愚蠢的方法，图片复制为 2 份，分别为 Markdown 语法和 Hugo 渲染所用
        
        *   若不设置 relativeURLs（默认），静态资源的路径相对于 **static** 目录
            
            1.  md 文件和本地图片在同一级目录内，目的是将路径简单化
                
            2.  md 文件中引用图片时使用 Markdown 语法
                
                ```
                ![](123.jpg)
                
                ```
                
            3.  将本地图片复制一份到 **static** 目录，按照 “Pretty URLs” 格式构建路径
                
                例如 md 文件在 **content** 目录的路径为 **content/posts/topic.md**，则其引用图片在 **static** 目录的路径为 **static/posts/topic/123.jpg**。
                
        *   若设置 relativeURLs，静态资源的路径相对于 **content** 目录
            
            1.  md 文件和本地图片在同一级目录内，目的是将路径简单化
                
            2.  md 文件中引用图片时使用 Markdown 语法
                
                ```
                ![](123.jpg)
                
                ```
                
            3.  将本地图片复制一份到 **content** 目录，按照 “Pretty URLs” 格式构建路径
                
                例如 md 文件在 **content** 目录的路径为 **content/posts/topic.md**，则其引用图片在 **content** 目录的路径为 **content/posts/topic/123.jpg**。
                
            4.  在网站配置文件 **config.toml** 中设置
                
                ```
                relativeURLs = false   =>   relativeURLs = true
                
                ```
                
*   使用 “Ugly URLs” 时
    
    静态资源的路径相对于 **content** 目录，且与 Markdown 语法使用的路径完全一致。
    
    1.  md 文件和本地图片在同一级目录内，目的是将路径简单化
        
    2.  md 文件中引用图片时使用 Markdown 语法
        
        ```
        ![](123.jpg)
        
        ```
        
    3.  在网站配置文件 **config.toml** 中设置
        
        使用 “Ugly URLs” 渲染 **content** 目录后，将 md 文件渲染为 html 文件：
        
        ```
        content/posts/_index.md   =>   example.com/posts/index.html
        
        content/posts/topic/topic.md   =>   example.com/posts/topic/topic.html
        
        ```
        
    4.  此方法破坏了 url 默认设置（“Pretty URLs”），因此需要修改 url 渲染失败的功能
        
        *   搜索 config.toml
            
            ```
            [[menu.shortcuts]]
            
              name = "<i class='fas fa-align-justify'></i> 分类"
            
              url = "/categories.html"  
            
              weight = 12
            
                   
            
            [[menu.shortcuts]]
            
              name = "<i class='fas fa-bookmark'></i> 标签"
            
              url = "/tags.html"        
            
              weight = 13
            
            ```
            
        *   搜索 partials
            
            **hugo-theme-learn** 主题在页面上显示的标签导航使用 “Pretty URLs”：
            
            ![](https://kuang.netlify.app/blog/hugo/image-20191204125335380.png)
            
            在 **themes/hugo-theme-learn/layouts/partials/header.html** 文件中禁用此功能：
            
            ```
                    
                                          # 新增
            
            ```
            
            或将其转换为. html（“Ugly URLs”）。（不懂修改模板）
            

#### 7.2 调整图片

1.  调整图片大小
    
    添加图像的 HTTP 参数 (（width、height），值为 CSS 值（默认为 auto）：
    
    ```
    ![Minion](https://octodex.github.com/images/minion.png?height=50px&width=300px)
    
    ```
    
2.  调整图片显示效果
    
    添加一个 CSS 类（classes），值为 shadow、border 等：
    
    ```
    ![Minion](https://octodex.github.com/images/minion.png?classes=border,shadow)
    
    ```
    

四、模板
----

Hugo 以 go 语言的 html/template 库作为模版引擎，将内容通过 template 渲染成 html，模版作为内容和显示视图之间的桥梁。

### 1、模板类型

Hugo 有一套模版查找机制，如果找不到与内容完全匹配的模板，它将搜索上一级。直到找到匹配的模板。如果找不到模板，将使用默认模板。

hugo 有三种类型的模版：

1.  Single Template：用于渲染页面内容
    
2.  List Template：用于渲染一组相关内容
    
    一个站点下所有内容或一个目录下的内容（section listings、home page、taxonomy lists、taxonomy terms）。homepage（_index.md），是一个特殊类型的 list template，homepage 是一个站点所有内容的入口。
    
3.  partial Template：可理解为模版级别的组件，可以被其他模版引用
    
    通常存放在项目根目录（或主题目录）内的 **layouts/partial** 目录。
    

页面模版查询规则：判断页面是 single page 还是 list page：

*   single page：选择模版 **themes / 主题名 / layouts/_default/single.html**
*   list page，选择模版 **themes / 主题名 / layouts/_default//list.html**

Output Format：

*   根据输出格式的名称和后缀，选择匹配的模版。例如输出格式是 rss，后缀是. html, 首先看有没有匹配的 index.rss.html 格式的模版

Language：

*   根据站点设置的语言选择匹配的模版，比如，站点的语言为 fr，模版匹配的优先级是：index.fr.amp.html > index.amp.html > index.fr.html

Layout：

*   可以在页面头部设置 front matter：layout，设置页面用指定的模版进行渲染，例如，页面在目录 posts（默认 section）下面，layout=custom，查找规则如下：
    
    ```
    layouts/posts/custom.html
    
    layouts/posts/single.html
    
    layouts/_default/custom.html
    
    layouts/_default/single.html
    
    ```
    

type：

*   如果在页面的头部设置了属性 type 属性，例如 type=event，查找规则如下：
    
    ```
    layouts/event/custom.html
    
    layouts/event/single.html
    
    layouts/events/single.html
    
    layouts/_default/single.html
    
    ```
    

### 2、模板语法

block：

*   在父模板中通过`{{block “xxx”}}`定义一个块
    
*   在子模块中通过`{{define “xxx”}}`定义的内容进行替换
    

模板引用：

*   方法一：partial（推荐使用）
    
    引入定义的局部模板（位置在 **themes/layouts/partials** 或 **layouts/partials** 目录内）：
    
    ```
    {{ partial "chrome/header.html" . }}
    
    ```
    
*   方法二：template
    
    在一些比较老的版本中用于引入定义的局部模版，现在在内部模版中仍然使用。
    
    *   `-`用于去除空格
        
        {{- xxx}用于去除 {{- 前边的空格、{{ xxx -} 用于去除 -}}后边的空格、{{- xxx -}}用于去除两边的空格。
        

Paginator：

*   **.Paginator** 主要用来构建菜单，只能在 homePage、listPage 中使用
    
    ```
    {{ range .Paginator.Pages }}
    
       {{ .Title }}
    
    {{ end }}
    
    ```
    

简码：

*   简码（shortcodes）相当于一些自定义模版，通过在 md 文档中引用，生成代码片段，类似于一些 js 框架中的组件
    
*   简码必须在 **themes/layouts/partials** 或者 **layouts/partials** 目录内定义
    
*   简码在模版文件中引用是无效的，只能在 content 目录下的 md 文件中引用
    
*   引用方式
    
    ```
    {{% msshortcodes params1="xxx" %}}**this** is a text{{% /msshortcodes %}}
    
    {{< msshortcodes params1="xxx"  >}} **Yeahhh !** is a text {{< /msshortcodes >}}
    
    ```
    
    *   %：代表短代码中的内容使用 Markdown 语法，需要进一步渲染
    *   <：代表短代码中间的内容使用 HTML 语法，不需要渲染

taxonomy：

参阅：[Hugo 中定制 Taxonomy 页面](https://note.qidong.name/2017/10/hugo-taxonomy/)。

*   Hugo 中通过 taxonomy 来实现对内容的分组。taxonomy 需要在站点配置文件中定义：
    
    ```
    [taxonomies]
    
      category = "categories"
    
      tag = "tags"
    
      series = "series"
    
    ```
    
    *   默认情况下，Hugo 提供了 category、tag 两个分类，不需要用户在配置文件中定义，但如果还有其他的 taxonomy 定义，则默认的 tag、category 也需要定义
    *   使用方式
        1.  在站点配置文件中添加 taxonomy，例如：series
        2.  定义 taxonomy list template，例如：layouts/taxonomy/series.html
        3.  在内容文件的 front matter 中设置 term；例如：series = [“s1”,“s2”]
        4.  访问 taxonomy 列表，例如：localhost:1313/series/s1

变量：

*   Hugo 提供了很多不同类型变量用于创建模版，构建站点

Site：

*   大部分站点变量定义在站点配置文件中（config.[yaml|toml|json] ）
    
    ```
    .Site.Menus:站点下的所有菜单
    
    .Site.Pages:站点下的所有页面
    
    .Site.Params: 站点下的参数
    
    ```
    

Page：

*   页面级参数都定义在页面头部的 front matter 中，例如：
    
    ```
    title = "Hugo"
    
    date = 2018-08-13T18:29:20+08:00
    
    description = ""
    
    draft = false
    
    weight = 10
    
    ```
    
*   使用方式
    
    ```
    {{ .Params.xxx }} 或者是 {{ .Site.Params.xxx }}
    
    ```
    

五、简码
----

简码（shortcode）是内容（content）文件中的简单代码段，调用内置或自定义模板，在 Markdown 文件中使用 HTML 语法实现某些功能。

Hugo 使用 Markdown 是因为其内容格式简单。但是，Markdown 有其不足，有时被迫在 Markdown 文件中使用纯 HTML 来扩展用法。但这与 Markdown 语法的简单性相矛盾。为了避免这种限制，Hugo 创建了[简码](https://gohugo.io/extras/shortcodes/)。

简码不能用于模板文件。如果需要在模板中插入简码，则很可能需要 [partial template](https://gohugo.io/templates/partials/) 。

除了 Markdown 更简洁外，简码可以随时更新以反映新的类、技术或标准。在网站生成时，Hugo 简码将轻松合并到您的更改中。您避免了可能很复杂的搜索和替换操作。

Hugo 通过简码扩展了 Markdown 用法（在不破坏 Markdown 语法简单性的基础上使用 HTML 语法）。Typora 编辑器不能识别简码，即只能通过 Hugo 渲染后在 HTML 网页上才能浏览效果。

### 1、预设简码

简码在 Markdown 文件中的调用语法：

```
# 分隔符使用% %，内含的源代码使用Markdown语法（可被渲染，**World**显示为粗体）

# 从Hugo_V_0.55开始，%作为最外层的分隔符，某些简码不能使用%分隔符，触发错误raw HTML omitted

{{% shortcodename parameters %}}Hello **World**{{% /shortcodename %}}

# 分隔符使用< >，内含的源代码使用HTML语法（不可被渲染，**World**显示为**World**）

{{< shortcodename parameters >}}Hello **World**{{< /shortcodename >}}

```

*   shortcodename：简码文件（shortcodename.html）的文件名，需要事先定义
    
*   parameters：参数
    
*   简码的优先级高于代码块
    
    即使将简码放入代码内，Hugo 依然识别出简码。若简码文件没有事先定义，Hugo 渲染时会触发错误：failed to extract shortcode: template for shortcode “shortcodename” not found。
    
*   若想运行简码，简码的源代码不应该放入代码块内
    
    有的简码可在代码块内运行，有的不行。
    
*   若想在代码块内放入简码的源代码，需要破坏简码的语法
    
    例如：在`{{`之间添加空格或添加注释符号`/* */`；使用时，删除增加的部分。
    
*   简码的分隔符有`% %`（Hugo_V_0.55 后，作为最外层的分隔符）和`< >`
    
    分隔符和大括号之间不能有空格分隔。
    
*   简码参数（shortcodename、parameters）之间、分隔符和简码参数之间由空格分隔
    
    若 parameters 包含空格，则应该使用引号括起，格式为：。
    

示例：引用 Hugo 的预设简码 figure，构建 HTML5 的 figure 标签显示图片

```
{ {< figure src="/images/风车.gif" title="网站logo" >}} 

```

显示效果（经过 Hugo 渲染后，在 HTML 网页才能看到效果）：

![](https://kuang.netlify.app/images/%e9%a3%8e%e8%bd%a6.gif)

#### 网站 logo

简码在网页 HTML 结构中显示的标签为：

```
<figure>

    <img src="../images/风车.gif"> <figcaption>

            <h4>网站logo</h4>

        </figcaption>

</figure>

```

**Hugo-theme-learn** 主题的简码：https://learn.netlify.com/en/shortcodes/。

**Hugo-theme-docDock** 主题的简码：https://docdock.netlify.com/shortcodes。

*   [alert](https://docdock.netlify.com/shortcodes/alert/)：突出显示页面中的信息
*   [attachments](https://docdock.netlify.com/shortcodes/attachments/)：显示页面附件文件的列表
*   [button](https://docdock.netlify.com/shortcodes/button/)：在页面中显示可操作的按钮
*   [children](https://docdock.netlify.com/shortcodes/children/)：列出页面的子页面
*   [excerpt](https://docdock.netlify.com/shortcodes/excerpt/)：标记页面内容的一部分以供重复使用
*   [excerpt-include](https://docdock.netlify.com/shortcodes/excerpt-include/)：显示另一页面中 “摘录”（即一部分）内容
*   [expand](https://docdock.netlify.com/shortcodes/expand/)：在页面上显示文本的可打开 / 折叠部分
*   [icon](https://docdock.netlify.com/shortcodes/icon/)：显示图标
*   [mermaid](https://docdock.netlify.com/shortcodes/mermaid/)：生成图表（diagram）和流程图（flowchart），其方式与 Markdown 语法类似
*   [notice](https://docdock.netlify.com/shortcodes/notice/)：构建页面的免责声明
*   [panel](https://docdock.netlify.com/shortcodes/panel/)：突出显示信息或将其放在框中，在文本周围创建一个彩色框
*   [revealjs](https://docdock.netlify.com/shortcodes/revealjs/)：将内容显示为 Reveal.js 幻灯片
*   [siteparam](https://learn.netlify.com/en/shortcodes/siteparam/)：获取页面中站点参数变量的值（用于 **config.toml**）

### 2、新建简码

新建简码时，将模板（**shortcodename.html** 文件）放入项目根目录或主题的 **layouts/shortcodes** 目录中，Hugo 渲染时，会以此目录作为简码的根目录读取简码的 HTML 文件。

示例：创建一个折叠代码行的简码

1.  新建简码的 HTML 文件，假设命名为 **collapsible.html**
    
    ```
    <details>
    
        <summary>
    
            {{ with .Get 0}}{{.}}{{else}}click to expand{{ end }}
    
        </summary>
    
        {{.Inner}}
    
    </details>
    
    ```
    
    *   设置的功能非常简单，只是简单的折叠行，尚未实现折叠代码块的功能
2.  在 Markdown 文件中使用简码 collapsible
    
    ```
    { {< collapsible "折叠" >}}
    
    折叠行的代码内容1<br/.>
    
    折叠行的代码内容2
    
    { {< /collapsible >}}
    
    ```
    
    显示的效果（经过 Hugo 渲染后，在 HTML 网页才能看到效果）：
    
    折叠 折叠行的代码内容 1  
    折叠行的代码内容 2

六、编译
----

在项目根目录中执行`hugo server` 命令调用 Hugo 内置的本地 web 服务器调试预览博客：

*   `--theme`：指定主题
*   `--watch`：修改文件后自动刷新浏览器（v0.15 版本后，缺省此参数也可自动刷新）
*   `--buildDrafts`：编译草稿文件（扉页中的 draft 值为 true）

```
$ hugo server --theme=hugo-theme-learn --buildDrafts --watch

Building sites … WARN 2019/11/27 16:09:27 .File.UniqueID on zero object. Wrap it in if or with: {{ with .File }}{{ .UniqueID }}{{ end }}



                   | EN

+------------------+----+

  Pages            | 10

  Paginator pages  |  0

  Non-page files   |  1

  Static files     | 77

  Processed images |  0

  Aliases          |  0

  Sitemaps         |  1

  Cleaned          |  0



Total in 75 ms

Watching for changes in E:\Hugo\sites\ebook\{archetypes,content,data,layouts,static,themes}

Watching for config changes in E:\Hugo\sites\ebook\config.toml

Environment: "development"

Serving pages from memory

Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender

Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)

Press Ctrl+C to stop

```

七、部署
----

实现：源代码推送到源代码仓库后，自动部署到多个不同的发布网站。

将博客部署到：

*   Pages 网站
    
    Git 托管平台免费提供的 Pages 服务，缺点是必须公开仓库（别人可轻易获取代码）。
    
    *   GitHub Pages
        
        公开 GitHub 的发布仓库后，就能免费使用 GitHub Pages 服务。
        
        服务器在国外，国内访问速度不稳定。而且，GitHub Pages 屏蔽了百度爬虫，百度无法爬取 GitHub Pages 网页。
        
    *   Gitee Pages
        
        Gitee（国内的 Git 免费托管平台）提供的免费 Pages 服务（发布仓库可私有，但需要手机验证），服务器在国内，国内访问速度快，但受各种政策影响体验。
        
*   Netlify
    
    [Netlify](https://www.netlify.com/) 是 GitHub Pages + Travis CI 的完美结合。Netlify 在线克隆 GitHub 源代码仓库（可私有），持续集成和自动部署（Travis CI 功能）到其提供的免费网站（Pages 服务），然后还额外提供了众多的免费服务。
    
    缺点是服务器在国外，国内访问速度不稳定，而且不能查看发布仓库。
    
*   个人网站
    
    个人在域名提供商（例如 [freenom](https://www.freenom.com/)）购买域名。在国内 CDN 提供商注册域名需要公安局备案。
    

### 1、代码安全

一次提交推送到多个仓库（目的纯粹是为了源代码安全）。

为了源代码安全，应该将源代码推送到不同的 git 代码仓库（例如 GitHub 和 Gitee）：

*   方法一：推送到 GitHub 后，在 Gitee 导入 GitHub 仓库
    
    手动操作：新建仓库 — 导入已有仓库 — 输入已有仓库的 git 地址。
    
*   方法二：手动多次推送到不同仓库
    
    每一个仓库都手动推送一次：
    
    ```
    git remote add origin <仓库的git地址>     # 关联仓库
    
    git push -u origin master                # 推送到仓库
    
    ```
    
*   方法三：将一个提交一次性推送到不同仓库
    
    将多个地址添加到 origin 库后，使用`git push origin master` 一次性 push 到多个仓库：
    
    ```
    git remote add origin <仓库1的git地址>               # 关联仓库1，别名为origin
    
    git remote set-url --add origin <仓库2的git地址>     # origin仓库添加仓库2
    
    git remote set-url --add origin <仓库3的git地址>     # origin仓库添加仓库3，origin仓库共指向3个不同仓库
    
    git push -u origin master                           # 推送到3个不同的仓库
    
    ```
    

### 2、手动部署

以 GitHub 为例。

1.  在 GitHub 创建一个源码仓库
    
    仓库名随意，权限可为私有，用来存储博客的源代码（**pbulic** 目录外的所有文件）。若不需要对源代码进行版本管理，可不需要创建源码仓库。
    
2.  在 GitHub 创建一个发布仓库
    
    权限公开，才能开启 GitHub Pages 服务，用来存储博客的发布内容（**pbulic** 目录）。
    
    *   使用用户仓库
        
        命名为`<username>.github.io`，GitHub Pages 的网址为 http://username.github.io/。一个帐户只能有一个用户仓库，优点是网址相对简单。
        
    *   使用项目仓库
        
        命名随意，GitHub Pages 的网址为 http://username.github.io/xxx。一个帐户可以有多个项目仓库。
        
3.  Hugo 生成发布内容（**public** 目录）
    
    在项目根目录内运行`hugo --theme=<themename>`命令（若在 **config.toml** 已配置了`theme`选项，可直接运行`hugo`命令），将发布内容生成到 **public** 目录。
    
    ```
    $ hugo
    
    Building sites … WARN 2020/01/07 21:30:32 .File.BaseFileName on zero object. Wrap it in if or with: {{ with .File }}{{ .BaseFileName }}{{ end }}
    
       
    
                       | EN
    
    +------------------+-----+
    
      Pages            |  60
    
      Paginator pages  |   0
    
      Non-page files   |  89
    
      Static files     | 211
    
      Processed images |   0
    
      Aliases          |  17
    
      Sitemaps         |   1
    
      Cleaned          |   0
    
       
    
    Total in 833 ms
    
    ```
    
4.  在 **public** 目录内创建本地仓库
    
    ```
    $ cd public                         
    
    $ git init                          
    
    $ git add .                         
    
    $ git commit -m "first commit"      
    
    ```
    
5.  将 **pubilc** 目录推送到 GitHub 的发布仓库
    
    ```
    $ git remote add origin git@github.com:username/username.github.io.git
    
    $ git push -u origin master
    
    ```
    
6.  GitHub 根据发布仓库自动创建 GitHub Pages 网站，浏览器输入网址（https://username.github.io/）就能访问博客
    

### 3、Travis CI

手动部署（Manual Deployment）是一个重复性劳动，既繁琐又耗时且容易出错。因为部署流程基本固定，所以应该交由 CI/CD 工具自动完成。

编写代码只是软件开发的一小部分，更多时间往往花在构建（build）和测试（test）上。为了提高软件开发效率，持续集成、持续交付、持续部署工具层出不穷。在 [GitHub 市场](https://github.com/marketplace)上提供了众多的持续集成服务提供商（工具）。

*   持续集成（Continuous Integration）
    
    是一个开发行为，强调开发人员提交新代码后，立刻进行构建、（单元）测试。根据测试结果，即时发现问题，即时修复。
    
*   持续交付（Continuous Delivery）
    
    在持续集成的基础上，将集成后的代码部署到更贴近真实运行环境的 “类生产环境”（production-like environments）中。例如，完成单元测试后，可以把代码部署到连接数据库的 Staging 环境中进行更多的测试。若没有问题，可以继续手动部署到生产环境中。
    
*   持续部署（Continuous Deployment）
    
    在持续交付的基础上，将部署到生产环境的过程自动化。
    

Travis CI 是一个使用 Ruby 语言开发、在线托管、开源的分布式持续集成服务提供商，是 CI 工具中市场份额最大的一个。Travis CI 目前有两个官网：

*   [Travis CI](https://www.travis-ci.org/)：旧平台（已逐步放弃），只对开源项目（公开仓库）免费
*   [Travis Pro](https://www.travis-ci.com/)：新平台，对开源项目和私有项目（私有仓库）免费

通过 Travis CI 部署 Hugo 或者 Hexo 博客在配置时可能麻烦一点，但配置好后非常方便好用，特别是对于 Hugo 这种没有部署插件的静态网站生成器。

#### 3.1 部署到 GitHub Pages

GitHub 使用 Travis CI 非常简单，因为 GitHub 的 token 具有推送代码的权限，一切都能在线操作。

##### 3.1.1 两个远程仓库

缺点：公开了发布代码（源代码可私有）。

参阅：[使用 Travis CI 自动部署 Hugo 博客](https://mogeko.me/2018/028/)。

1.  GitHub 创建 2 个远程仓库
    
    *   仓库 A：存放 Hugo 的输入文件（**public** 目录外的所有文件）
        
        命名随意，权限可为私有。
        
    *   仓库 B：存放博客发布文件（**public** 目录）
        
        使用用户仓库（`<username>.github.io`），权限公开，开启 GitHub Pages 服务。
        
2.  GitHub 生成个人访问令牌（Personal Access Token）
    
    Travis CI 自动部署时，需要 push 内容到仓库的某个分支，而访问 GitHub 仓库需要用户授权，授权方式就是用户提供访问令牌给 Travis CI。
    
    在 GitHub 生成 token（路径：帐户 Settings — Developer settings — Personal access tokens — Generate new token），至少需要勾选 `public_repo`权限。建议：勾选 repo 上的所有项目，别的项目一个都不要选。
    
3.  配置 Travis CI Pro
    
    1.  登录（注册）Travis CI Pro
        
        进入 [Travis CI 官网](https://travis-ci.com/)，登录（注册）Travis CI：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114143054489.png)
        
        首次登录（注册）时，Travis CI 请求关联 GitHub：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114143219508.png)
        
    2.  激活帐户，选择源代码仓库
        
        登录后，进入 [getting_started](https://travis-ci.com/getting_started) 页面，点击帐户（页面上方的导航栏最右侧登录头像）内的 “Settings”：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114144017361.png)
        
        进入个人帐户（MY ACCOUNT）页面：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114144415406.png)
        
        首次使用时，激活 “GitHub Apps Integration”。
        
        “GitHub Apps Integration” 支持私有和公开仓库，与 GitHub 交互时提供增强的安全性。
        
        点击 “Activate” 后，弹出页面：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114145037486.png)
        
        *   选择 “All repositories”，表示选择 GitHub 帐户中的所有仓库（包含未来新建）
        *   选择 “Only select repositories”，表示只选择 GitHub 帐户中的指定仓库
        *   点击 “Approve & Install” 后，可在 GitHub（帐户 Settings — Applications — Install GitHub Apps）中查看安装结果或重新配置
    3.  同步帐户
        
        “GitHub Apps Integration”激活完毕后，Travis 返回 “MY ACCOUNT” 页面，并自动（或点击“Sync account”）显示帐户内的仓库。
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114150416516.png)
        
    4.  激活源代码仓库
        
        指定帐户内的某个仓库为源代码仓库。Travis 将根据此激活仓库的提交自动部署到博客网站。点击上图中的仓库列表中某一个，即可进入仓库界面：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200114160144369.png)
        
        仓库的功能菜单项有：
        
        *   Current：仓库的当前状态
            
            可查看最新构建的日志（log）：
            
            *   hugo 命令成功运行时显示：**The command “hugo” exited with 0.**
            *   build 命令成功运行时显示：**Done. Your build exited with 0.**
        *   Build History：仓库的构建历史
            
        *   Settings：仓库设置，部署触发条件和添加环境变量（Personal Access Token）
            
            *   勾选触发条件
                
                General、Auto Cancellation、Config Import 等选项，使用默认值即可。
                
            *   设置环境变量（Environment Variables）
                
                在 “Environment Variables” 中输入 token 信息后，点击 Add 按钮： ![](https://kuang.netlify.app/blog/hugo/image-20200107164339086.png)
                
                *   NAME：名称任意，例如 GITHUB_TOKEN
                *   VALUE：在 GitHub 申请到的 Token 的值
                *   BRANCH：默认值（all branches）
        *   设置好的变量在配置文件中以 `${变量名}`来引用
            
    
    *   Trigger build：手动触发构建
        
        当源代码仓库已有提交时，可手动触发构建。日常使用时，可忽略此步骤。
        
        弹出界面使用默认值即可：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200113151506405.png)
        
4.  新建 Travis 配置文件
    
    配置文件（**.travis.yml**）告诉 Travis CI 如何部署博客, 一个完整的构建生命周期为：
    
    ```
    Install apt addons                              
    
    Install cache components                        
    
    before_install                                  
    
    install                                         
    
    before_script                                   
    
    script                                          
    
    before_cache(for cleaning up cache)             
    
    after_success or after_failure                  
    
    before_deploy                                   
    
    deploy                                          
    
    after_deploy                                    
    
    after_script                                    
    
    ```
    
    Travis CI 的一次构建分为两个阶段：
    
    *   install（安装）阶段：安装所需的依赖项，例如 hugo 框架
    *   script（脚本）阶段：运行构建脚本所需的命令，例如 hugo 命令、git 命令
    
    在本地项目根目录中新建 Travis 配置文件（**.travis.yml**），此文件应该上传到仓库 A。如果配置文件不在仓库 A 中，或者不是有效的 YAML，Travis CI 将无法构建项目。
    
    使用 Python 语言（[Travis 指定语言示例](http://docs.travis-ci.com/user/language-specific/) ，Python 耗时比 Golang 少）配置 Travis 环境。
    
    ```
    language: python                      
    
    python:
    
      - "3.7"                             
    
    branches:                             
    
      only:
    
        - master                          
    
    env:                                  
    
     global:
    
       - GH_REF: github.com/username/username.github.io.git    
    
    install:                              
    
      - wget https://github.com/gohugoio/hugo/releases/download/v0.62.2/hugo_0.62.2_Linux-64bit.deb   
    
      - sudo dpkg -i hugo*.deb            
    
    script:
    
      - hugo                                    
    
    
    
    after_script:
    
      - cd ./public
    
      - git init
    
      - git config user.name "xxx"
    
      - git config user.email "xxx@xxx.com"
    
      - git add .
    
      - git commit -m "Update Blog By TravisCI With Build $TRAVIS_BUILD_NUMBER"
    
      - git push --force --quiet "https://$GITHUB_TOKEN@${GH_REF}" master:master
    
       
    
    
    
    
    
      
    
      
    
      
    
      
    
        
    
      
    
      
    
      
    
      
    
      
    
    ```
    
    *   Travis 是在线构建，不占用本地空间
    *   若仓库 B 的 Git 地址（**GH_REF** 变量）填写错误时，不提示错误且不会上传到仓库 B
    *   部署（deploy）环节的内容：是将发布内容上传到源代码仓库（仓库 A）的另一个分支（target-branch 名称不能与源代码分支名相同，否则覆盖源代码）
5.  本地仓库
    
    在本地项目根目录中新建一个本地仓库存放 Hugo 的输入文件（**public** 目录外的所有文件）。
    
    1.  创建忽略文件
        
        本地仓库不应该包含 **public** 目录（Travis 会在线生成，可忽略）、**themes** 目录（若自定义了主题，则不能忽略）和 **resources** 目录（可忽略）。
        
        忽略文件（**.gitignore**）内容假设为：
        
        ```
        public/
        
        resources/
        
        ```
        
    2.  当前项目（假设为 book）创建本地仓库并推送到 GitHub 的仓库 A
        
        ```
        $ git init
        
        Initialized empty Git repository in E:/Hugo/sites/book/.git/
        
        $ git add .
        
        $ git commit -m "generator code"
        
        $ git remote add github git@github.com:username/xxx.git
        
        $ git push -u github master
        
        ```
        
6.  持续集成和自动部署
    
    若 Travis 设置了 “提交触发 build”，本地仓库的每次推送到仓库 A，Travis CI 都会得到提示，然后根据仓库 A 中的配置文件（**.travis.yml**）自动进行部署，实现了博客的自动持续集成（仓库 B 根据仓库 A 的内容自动更新，GitHub Pages 网站根据仓库 B 的内容自动更新）。
    
    简单一句话：只需要将写好的文章推送到 GitHub 的仓库 A，不需要编译、不需要推送到仓库 B、 甚至连 Hugo 都可以不安装（假设使用主题没有自定义）。
    

##### 3.1.2 一个远程仓库

缺点：源代码和发布代码都公开了。

方法一：参阅[利用 Travis CI 和 Hugo 將 Blog 自動部署到 Github Pages](https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/)。

1.  在 GitHub 创建一个远程仓库
    
    存放 Hugo 的输入文件和博客发布文件。命名为`<username>.github.io`，权限为公开，开启 GitHub Pages 服务。
    
2.  GitHub 生成个人访问令牌（TOKEN）和配置 Travis CI
    
    步骤同上。
    
3.  新建 Travis CI 的配置文件（**.travis.yml**）
    
    ```
    
    
       
    
    dist: xenial
    
    language: python
    
    python:
    
      - "3.7"
    
       
    
    
    
    install:
    
        
    
        
    
        
    
        - wget https://github.com/gohugoio/hugo/releases/download/v0.62.2/hugo_0.62.2_Linux-64bit.deb
    
        - sudo dpkg -i hugo*.deb
    
       
    
    
    
    script:
    
        - hugo
    
       
    
    deploy:
    
      provider: pages
    
      skip-cleanup: true
    
      github-token: $token
    
      email:
    
      name:
    
      verbose: true
    
      keep-history: true
    
      local-dir: public
    
      target_branch: master  
    
      on:
    
        branch: code  
    
    ```
    
4.  本地仓库
    
    1.  创建忽略文件
        
        本地仓库不应该包含 **public** 目录（Hugo 本地编译时产生）、**themes** 目录（存放主题）和 **resources** 目录（由主题产生）。
        
        忽略文件（**.gitignore**）内容假设为：
        
        ```
        public/
        
        resources/
        
        ```
        
    2.  当前项目（假设为 book）创建本地仓库
        
        ```
        $ git init
        
        Initialized empty Git repository in E:/Hugo/sites/book/.git/
        
        ```
        
    3.  分支 A（code 分支）
        
        命名为 code，存放 Hugo 的输入文件（博客工程文件，即 **public** 目录外的文件）。
        
        ```
        $ git checkout -b code
        
        
        
        $ git add .
        
        $ git commit -m "generator code"
        
        $ git remote add github git@github.com:username/username.github.io.git
        
        $ git push -u github code
        
        ```
        
    4.  分支 B（master 分支）
        
        命名为 master，博客发布文件（即 **public** 目录）存放在 master 分支。
        
        ```
        $ hugo                                   
        
        $ HUGO_TEMP_DIR=$(mktemp -d)             
        
        $ cp -R public/* "$HUGO_TEMP_DIR"        
        
        $ git checkout --orphan master           
        
        
        
        $ rm .git/index                          
        
        $ git clean -fdx                         
        
        $ cp -R "$HUGO_TEMP_DIR"/. .             
        
        $ git add .
        
        $ git commit -m 'initial blog content'
        
        $ git push -u github master
        
        $ [[ -d "$HUGO_TEMP_DIR" ]] && rm -rf "$HUGO_TEMP_DIR"   
        
        
        
        $ git checkout code
        
        ```
        
5.  持续集成
    
    如果上述步骤中设置了 “提交触发 build”，本地仓库的每次推送到 code 分支，Travis CI 会自动进行部署，将上传的博客内容（code 分支）更新到仓库的 master 分支中，实现了博客的持续集成（GitHub Pages 网站根据 master 分支内容自动更新），不再需要关心 travis 状态。
    

方法二：

*   方法一略显复杂，简捷方法是在[两个 GitHub 仓库](http://localhost:1313/blog/hugo.html#21-%E4%B8%A4%E4%B8%AA%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93)方法中的 Travis 配置文件（**.travis.yml**）中启用 “deploy” 配置，就能实现在一个仓库内实现源代码分支和发布分支。

#### 3.2 部署到 Gitee Pages

GitHub Pages 和 Netlify 的服务器在国外，国内访问速度慢且稳定性差。解决这一问题的最优解是购买个人域名使用 CDN。对于不想购买个人域名的人来说，使用 Gitee Pages（[码云](https://gitee.com/)的 Pages 服务）是当前的最优解决方案。

[码云](https://gitee.com/)仓库（可为私有，GitHub 的必需公开）开启 Pages 服务需要绑定手机号（不需要在公安局备案）。[码云](https://gitee.com/)是国内的 Git 托管平台，所以在国内访问速度超快（对比 GitHub）且稳定。仓库有更新时，Gitee Pages 貌似不会自动更新，需要手动更新（GitHub Pages 是自动更新）。

Gitee 使用 Travis CI 比较麻烦，因为 Gitee 的 token 没有推送代码的权限，所以需要下载 Travis CI 到本地，然后在本地加密私钥文件或密码，最后使用加密后的密码 push。

参阅：[travis 官网文档](https://docs.travis-ci.com/user/encrypting-files/)、[手摸手教你搭建 Travis CI 持续集成和自动化部署](https://segmentfault.com/a/1190000018687703?utm_source=tag-newest)、[使用 travis-ci 自动部署 hexo 博客](https://zzde.me/2018/auto-deploy-hexo-blog-with-traivs-ci/)、[Hexo 集成 Travis CI](https://www.dazhuanlan.com/2019/09/28/5d8e8289c53c2/)。

使用 Travis CI 部署到 Gitee 的过程：

1.  源代码仓库是 GitHub 仓库
    
    因为 Travis CI 只能使用 GitHub 帐户注册，因此只能绑定 GitHub 仓库为源代码仓库。
    
2.  发布仓库是 Gitee 仓库
    
    修改 Travis 配置文件（**.travis.yml**）中的 **GH_REF** 变量（发布仓库，为了区分不同的仓库，名称可自定义命名）的值为 Gitee 仓库的 git 地址：
    
    ```
    env:                                  
    
     global:
    
       - GH_REF: github.com/username/username.github.io.git    
    
       
    
    ```
    
3.  自动验证密码
    
    Gitee 的 token 没有推送代码的权限，所以此命令失效：
    
    ```
    git push --force --quiet "https://$Gitee_TOKEN@${GE_REF}" master:master    
    
    ```
    
    改用其它方式验证密码：
    
    1.  使用 https 协议，显式添加用户名、密码进行推送
        
        ```
        git push --force --quiet "https://用户名:密码@${GE_REF}" master:master
        
        ```
        
        缺点：彻底暴露 Gitee 帐户的用户名和密码（即使 GitHub 仓库是私有），风险极大。
        
    2.  使用 ssh 协议，使用私钥文件免密推送
        
        ```
        git push --force --quiet "git@gitee.com:用户名/用户名.git" master:master
        
        ```
        
        缺点：需要将本地的私钥文件推送到 GitHub 仓库，风险极大。
        
    3.  通过加密私钥文件或密码
        
4.  具体的 Travis 配置文件（**.travis.yml**）与 GitHub 的基本一致
    

#### 3.3 Travis 加密

无论是使用 token（GitHub，token 在 Travis CI 中显式显示），还是显式使用密码或上传或 ssh 私钥（Gitee），即使源代码仓库是私有的，也是存在极大风险。

Travis 提供了加密服务，可以对密码或者私钥文件进行加密。

1.  本地安装 Ruby
    
    Travis 客户端使用 Ruby 语言编写，所以先安装 Ruby。（参阅：[Ruby 安装 - Windows](https://www.runoob.com/ruby/ruby-installation-windows.html)）
    
    不建议安装 Ruby for Windows。因为 Windows 不支持 openssl 验证，导致 Travis 对密钥文件解密时触发错误：The command “openssl xxx -in id_rsa.enc -out ~/.ssh/id_rsa -d” failed and exited with 1 during。
    
    验证 Ruby 和 gem（安装 Ruby 时自动安装）是否安装成功：
    
    ```
    $ ruby -v
    
    ruby 2.7.0p0 (2019-12-25 revision 647ee6f091) [x64-mingw32]
    
    $ gem -v
    
    3.1.2
    
    ```
    
2.  本地安装 Travis 客户端
    
    ```
    $ gem install travis
    
    ```
    
    验证是否安装成功：
    
    ```
    $ travis -v
    
    D:/Program/Ruby/lib/ruby/gems/2.7.0/gems/highline-1.7.10/lib/highline.rb:624: warning: Using the last argument as keyword parameters is deprecated
    
    1.8.10
    
    ```
    
3.  关联 GitHub 源代码仓库
    
    （克隆 GitHub 源代码仓库）进入本地工程目录，输入命令：
    
    ```
    $ travis login --auto --pro
    
    Username:xxx                          # 输入GitHub帐户名
    
    Password for xxx: xxx                 # 输入GitHub帐户密码
    
    Successfully logged in as xxx!
    
    ```
    
    *   –pro：绑定 travis-ci.com，缺省时绑定 travis-ci.org
4.  加密
    
    1.  加密本地系统的私钥文件（**~/.ssh/id_rsa** 文件），使用 SSH 协议 push
        
        ```
        $ travis encrypt-file ~/.ssh/id_rsa --add
        
        # 下面是命令行打印的日志
        
        Detected repository as xxx/xxx, is this correct? |yes| yes
        
        encrypting ~/.ssh/id_rsa for xxx/xxx
        
        storing result as id_rsa.enc
        
        storing secure env variables for decryption
        
        Make sure to add id_rsa.enc to the git repository.
        
        Make sure not to add ~/.ssh/id_rsa to the git repository.
        
        Commit all changes to your .travis.yml.
        
        ```
        
        *   第一行：运行命令，加密**~/.ssh/id_rsa** 文件
            
        *   第二行：检测 Travis 绑定的 GitHub 源代码仓库，若正确，输入 yes
            
        *   第三行：为 GitHub 源代码仓库加密本地系统的私钥文件
            
        *   第四行：在本地工程根目录内生成 **id_rsa.enc** 文件（已加密），**.travis.yml** 内自动添加（清空所有备注）以下配置信息
            
            ```
            before_install:
            
            - openssl aes-256-cbc -K $encrypted_xxx_key -iv $encrypted_xxx_iv
            
              -in id_rsa.enc -out ~\/.ssh/id_rsa -d
            
            ```
            
            实际使用时，要将`\`去掉，否则会编译失败。
            
        *   第五行：存储用于解密的安全环境变量
            
        *   第六行：将 **id_rsa.enc** 文件推送到 GitHub 源代码仓库
            
        *   第七行：不能将本地系统的私钥文件推送到 GitHub 源代码仓库
            
        *   第八行：**.travis.yml** 的修改推送到 GitHub 源代码仓库
            
        
        **.travis.yml** 的最终结果：
        
        ```
        language: python                                     
        
        python:
        
          - "3.7"                                            
        
        branches:                                            
        
          only:
        
            - master                                         
        
        addons:
        
          ssh_known_hosts:
        
          - gitee.com
        
        env:                                                 
        
         global:
        
           
        
           - GE_REF: gitee.com:koman/koman.git               
        
        before_install:                                      
        
          - openssl aes-256-cbc -K $encrypted_abf3a382006f_key -iv $encrypted_abf3a382006f_iv -in id_rsa.enc -out ~/.ssh/id_rsa -d
        
          - eval "$(ssh-agent -s)"                           
        
          - chmod 600 ~/.ssh/id_rsa                          
        
          - ssh-add ~/.ssh/id_rsa                            
        
        install:                                             
        
          - wget https://github.com/gohugoio/hugo/releases/download/v0.62.2/hugo_0.62.2_Linux-64bit.deb   
        
          - sudo dpkg -i hugo*.deb                           
        
        script:
        
          - hugo                                             
        
        after_script:                                        
        
          - cd ./public
        
          - git init
        
          - git config user.name "koman"
        
          - git config user.email "komantao@hotmail.com"
        
          - git add .
        
          - git commit -m "Update Blog By TravisCI With Build $DATE"
        
          
        
          - git push --force --quiet "git@${GE_REF}" master:master
        
              
        
        
        
        
        
          
        
          
        
          
        
          
        
            
        
          
        
          
        
          
        
          
        
          
        
        ```
        
        *   使用了 Ruby for Windows，因为 Windows 不支持 openssl 验证，导致 Travis 对密钥文件解密时触发错误，建议改用 Ruby for Linux
    2.  加密密码，使用 HTTPS 协议 push
        
        如果密码中有特殊符号，需要转义：例如 @需要转成 %40。
        
        ```
        $ travis encrypt gitee_password=xxx 
        
        ```
        
        **.travis.yml** 文件的 global 变量增加一个 secure 字段
        
        ```
        env:
        
          global:
        
          - secure: xxxxxx   
        
        ```
        
        **.travis.yml** 的最终结果：
        
        ```
        language: python
        
        python:
        
        - '3.7'
        
        branches:
        
          only:
        
          - master
        
        env:
        
          global:
        
          - GE_REF: gitee.com/xxx/xxx.git
        
          - secure: xxxxxx
        
        install:
        
        - wget https://github.com/gohugoio/hugo/releases/download/v0.62.2/hugo_0.62.2_Linux-64bit.deb
        
        - sudo dpkg -i hugo*.deb
        
        script:
        
        - hugo
        
        after_script:
        
        - cd ./public
        
        - git init
        
        - git config user.name "xxx"
        
        - git config user.email "xxx@xxx.com"
        
        - git add .
        
        - git commit -m "Update Blog By TravisCI With Build $DATE"
        
        - git push --force --quiet "https://xxx:${gitee_password}@${GE_REF}" master:master
        
        ```
        
        *   Ruby for Windows 使用加密密码方式成功

### 4、Netlify

[Netlify](https://www.netlify.com/) 是国外一家提供静态网络托管服务的综合平台，专注于静态网站托管的 web 服务，完美且免费支持 ssl、域名绑定、http/2 和 TLS，官网宣传其是一个工作流（workflow），包含构建一个网站所需的一切。

Netlify 的功能：

*   托管静态资源
    
    与 GitHub Pages 一样，但比 GitHub Pages 强多了，而且速度也快。两者的对比在 [netlify 官网](https://www.netlify.com/github-pages-vs-netlify/)有介绍。
    
*   将静态网站部署到 CDN，加速国内访问
    
    GitHub Pages 无法进行 CDN 加速。Netlify 能够使用自带 CDN 加速服务。
    
*   持续部署（Continuous Deployment）
    
    当博客源码提交推送到托管平台后，Netlify 就会自动运行 build command（自动克隆博客源码仓库，自动判断博客框架，自动运行 build 命令生成博客发布代码），自动部署到绑定域名。
    
    Netlify 更关注于前端（网站或 APP）的持续集成与持续部署，这是它与其他持续集成工具最大的区别。而且，Netlify 的使用非常简单。
    
*   可以添加自定义域名（个人购买的域名）
    
*   可以启用免费的 TLS 证书，启用 HTTPS
    
*   自带 CI、支持重定向、插入代码、打包和压缩 JS 和 CSS、压缩图片、处理图片
    
    [重定向](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Redirections)（也称 URL 转发）。GitHub Pages 对 redirects、[重定向](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Redirections)（也称 URL 转发）支持并不友好，如果放弃 GitHub Pages 提供的 **username.github.io** 子域名时很麻烦。对比之下，从 **username.netlify.com** 重定向（重定向编码是 301 或 302）至新发布网站时很简单。重定向后，搜索引擎可以清楚识别你的页面已被转移，从而对你的新页面进行重新排名。所以即使哪天 Netlify 车毁人亡，只要设定好了重定向，你的网站也不会受到任何影响。
    

#### 4.1 部署流程

1.  登录（注册）Netlify，关联 GitHub 帐户
    
    [Netlify](https://app.netlify.com/signup) 提供邮箱注册和包括 GitHub 在内的第三方验证登陆（注册）。
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113165140198.png)
    
    1.  选择第三方（GitHub）验证登陆（注册）
        
    2.  GitHub 授权验证登陆 Netlify
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200113165555863.png)
        
    3.  关联 GitHub 账户
        
        登录后，页面自动（或点击导航栏上方的 Netlify 图标）跳转到 “New site from Git”，点击 “New site from Git”：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200113170641881.png)
        
        页面跳转到 “Create a new site”，点击 GitHub：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200113170934010.png)
        
        Netlify 首次关联 GitHub 仓库时，弹出窗口：
        
        ![](https://kuang.netlify.app/blog/hugo/image-20200113171237033.png)
        
        点击 “Authorize Netlify by Netlify” 同意授权后，Netlify 就有权访问 GitHub 仓库内容。
        
2.  选择仓库
    
    授权完毕后，页面跳转到 “Install Netlify”，选择 GitHub 的源代码仓库（可为私有仓库）：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113171953598.png)
    
    *   备注：可在 GitHub 中修改 Netlify 的配置：帐户 Settings — Applications
    
    点击 Install 后，等待一段时间，页面跳转回 “Create a new site”，显示关联后的仓库：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113173158915.png)
    
3.  构建选项和部署
    
    点击上图中的已关联仓库，进入第 3 步骤：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113173949722.png)
    
    *   Owner：Netlify 自动识别，已绑定了托管平台的仓库
        
    *   Branch to deploy：被部署分支，存储源代码的分支
        
    *   Build command：构建命令
        
        Netlify 会自动判断博客框架，自动显示命令，可自定义参数。
        
    *   Publish directory：发布目录，默认为 public
        
    *   show advanced：提示将 [netlify.toml](https://www.netlify.com/docs/continuous-deployment/#deploy-contexts) 添加到存储库中，可获得更大的灵活性
        
        在本地项目根目录内新建 **netlify.toml** 文件，保存后上传到 GitHub 的源代码仓库：
        
        ```
        [build]
        
        publish = "public"
        
        command = "hugo"
        
        [context.production.environment]
        
        HUGO_VERSION = "0.62.2"
        
        HUGO_ENV = "production"
        
        HUGO_ENABLEGITINFO = "true"
        
        [context.split1]
        
        command = "hugo --enableGitInfo"
        
        [context.split1.environment]
        
        HUGO_VERSION = "0.62.2"
        
        HUGO_ENV = "production"
        
        [context.deploy-preview]
        
        command = "hugo -b $DEPLOY_PRIME_URL"
        
        [context.deploy-preview.environment]
        
        HUGO_VERSION = "0.62.2"
        
        [context.branch-deploy]
        
        command = "hugo -b $DEPLOY_PRIME_URL"
        
        [context.branch-deploy.environment]
        
        HUGO_VERSION = "0.62.2"
        
        [context.next.environment]
        
        HUGO_ENABLEGITINFO = "true"
        
        ```
        
        Netlify 的构建环境使用 Unix。
        
4.  构建并发布网站
    
    前面 3 个步骤完成后，点击上图中的 “Deploy site” 按钮（Netlify 自动构建并发布博客内容）后，开始构建并发布网站：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113175351176.png)
    
    *   点击上方导航栏中的 “Deploys”，可实时显示部署过程（log 日志）：
        *   仅用 30 秒，Netlify 就完成了整个 CD 过程：Finished processing build request in 30.640996398s
    
    网站发布成功后，网站地址：“https://” + 随机生成的子域名 + “.netlify.com”。
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113180150738.png)
    
5.  美化 Netlify 子域
    
    默认情况下，基于 Netlify 子域访问站点。若不喜欢 Netlify 随机子域且没有购买自定义域名，可以将 Netlify 子域美化（自定义名称）：
    
    *   方法一：点击上图中的 “Domain Settings”
    *   方法二：点击上方导航栏中的 “Settings” — “General” — “Site details” — “Change site name”
    *   方法三：“Settings” — “Domain management” — “Domains” — “Custom domains”
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113184702770.png)
    
6.  自定义域名
    
    点击上上图中的 “Set up a custom domain”，进入添加域名页面：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117124617111.png)
    
    输入已购买的域名（不包含 www），点击 “Verify” 后设置 DNS（以 GitHub Pages 示例）：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117125825508.png)
    
    *   Check DNS configuration：因为尚未将域名解析到 Netlify 服务器。此时，需要到域名提供商绑定（购买）的域名下添加一条 CNAME 解析，解析的主机记录对应 Netlify 的子域名值（**xxx.netlify.com**）
    
    要求必须拥有域名所有权才能完成所有步骤，所以自定义域名不能使用 Pages 网站。
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117130503668.png)
    
    由于没有个人域名，此步骤暂停。
    
7.  网站（Netlify 子域）设置
    
    路径：选择网站（Netlify 子域）— 导航栏点击 “Settings” — Build & deploy — 编辑设置。
    
    设置内容有：持续部署命令、webhooks、构建映像、环境变量、注入代码、资产优化（压缩 CSS/JS / 图片文件）、预渲染等。
    

#### 4.2 Netlify CMS

内容管理系统（content management system，简称 CMS）是指在一个合作模式下，用于管理工作流程的一套制度。该系统可应用于手工操作中，也可以应用到电脑或网络。作为一种中央储存器（central repository），内容管理系统可将相关内容集中储存并具有群组管理、版本控制等功能。版本控制是内容管理系统的一个主要优势。

[Netlify CMS](https://www.netlifycms.org/docs/configuration-options/) 是一款团队的编辑工具，但可以作为个人博客的在线编辑工具，可以随时随地修改、编辑博客内容，而不用考虑多设备同步的问题。

#### 4.3 页面重定向

Netlify 提供了自定义页面重定向的功能。如果你的域名或者文章结构发生了变化，可以借助重定向功能，将原来的文章 URL 重定向到现在的地址。

新建一个 **netlify.toml** 文件，存放在博客的根目录下，在里面添加以下内容：

```
[[redirects]]

  from = "https://原始域名/*"

  to = "https://自定义域名/:splat"

  force = true

```

#### 4.4 Netlify 的坑

1.  Netlify 通过 API ID 绑定 GitHub 仓库
    
    若删除了 GitHub 的源代码仓库，即使之后重建（名称一样），Netlify 也自动部署失败。因为绑定仓库的 API ID 已改变，需要重新绑定。
    
2.  对 TOC 的分层理解
    
    在 **config.toml** 内设置`startLevel = 2`的前提下：
    
    *   Netlify 的 TOC 从 H1 标题（第一层）开始构建`<ul>`标签
    *   Hugo 可以从 H2 标题（第二层）开始构建`<ul>`标签
    
    解决方法：在 **config.toml** 内设置`startLevel = 1`
    
3.  对嵌套列表的理解
    
    无序列表和有序列表混用时，Hugo 正常显示，Netlify 有时会格式错乱。添加了 **netlify.toml** 文件后，格式错乱问题自动解决。
    

### 5、webhook

在 web 上，越来越多的操作被描述为事件。webhook（网络钩子）是一种 web 回调或者 http 的 push API，是向 APP 或者其他应用提供实时信息的一种方式。Webhook 在数据产生时立即发送数据，也就是你能实时收到数据。这一种不同于典型的 API，需要用了实时性需要足够快的轮询。这无论是对生产还是对消费者都是高效的，唯一的缺点是初始建立困难。webhook 是一种实现事件响应的方式，用于在项目发生事件时通知外部服务器。

以 Gitee 示例演示 webhook 的用法。

原理：

![](https://kuang.netlify.app/blog/hugo/20190724181523548.png)

1.  本地执行`git push`命令，push 代码到 Gitee 仓库
2.  Gitee 接收到 push 请求后，调用自己服务器上的一个接口
3.  接口是一个脚本文件，可使用 php、Python 等脚本语言编写，实现命令的自动执行：例如`git pull`和重启服务等命令

八、CDN
-----

GitHub Pages 和 Netlify 的服务器都部署在海外，在国内访问本博客的速度会比较慢 (Ping 下来 100 到 200 多毫秒)。解决这一问题的最优解就是使用 CDN。

CDN（Content delivery network 或 Content distribution network，内容分发网络）是指一种通过互联网互相连接的计算机网络系统，利用最靠近每位用户的服务器，更快、更可靠地将音乐、图片、影片、应用程序及其他文件发送给用户，来提供高性能、可扩展性及低成本的网络内容传递给用户。

简单来说，CDN 就是部署在世界各地的缓存服务器，它们会提前缓存网站上的资源，然后当用户想要访问相关资源时，直接从 CDN 服务器上取就可以了。这样不仅可以增加访问速度、减少访问延迟，还可以减缓网站服务器上的压力。

要使用 CDN，需要在域名提供商更改 DNS 服务器，变为它提供的域名服务器地址。CDN 服务提供商有很多，国内的有七牛云、阿里云、腾讯云等，国内的 CDN 都要求域名在公安局备案。国外的不需要备案。

[Cloudflare](https://dash.cloudflare.com/) 是全球最大的 DNS 服务提供商之一 （号称全球最快的 DNS`1.1.1.1`就是它搞的）。除此之外，还提供 CDN、SSL 证书、DDos 保护等服务，Cloudflare 还与百度有合作，在国内部署有大量的节点，还能顺便解决百度爬虫无法抓取 GitHub Pages 的问题。

在 [Cloudflare](https://dash.cloudflare.com/) 注册一个帐号，注册好后点击 “Add site” 添加你的网站：

![](https://kuang.netlify.app/blog/hugo/image-20200114010426839.png)

没有个人网站，此步骤暂停。

九、评论系统
------

若使用国内的评论插件，前提是网站需要在公安局备案，因此转向使用国外的评论插件。

### 1、Gitalk

官方的 GitHub 仓库： [https://github.com/gitalk/gitalk/](https://github.com/gitalk/gitalk/)。

官方网站：https://gitalk.github.io/

Gitalk 是一个基于 GitHub Issue 和 Preact 开发的评论插件，支持多种语言 (包括 en、zh-CN、zh-TW、es-ES、fr) 并自动判断当前语言。最重要的是 Gitalk 使用的是 GitHub Issue 的 api，不依赖任何第三方平台。也就是说，只要 Github 不倒闭，你的评论系统就不会被关闭。

原理：Hugo 是将 Markdown 文档按照主题 (包括 HTML 模板、CSS、JavaScript 等) 编译成静态网页。所以只需要将 Gitalk 作为一个`<div>`插入到 HTML 模板中，然后在 **config.toml** 中添加相关配置，就可以实现 “评论区” 了。

步骤为：

1.  新建一个评论仓库
    
    新建一个仓库（或博客发布仓库）作为存放评论的仓库（The repo to store comments），点击菜单栏中的 “Settings”，勾选 “Issues”，Gitalk 会将评论放在这个仓库的 issues 里面。
    
2.  注册 Github Application
    
    在 GitHub 注册一个 Oauth Application，让 Gitalk 有权限操作 GitHub 上的仓库。
    
    *   方法一：帐户 “Settings” — “Developer settings” — “OAuth Apps”— “New OAuth App”
    *   方法二：点击链接 [Github Oauth Application](https://github.com/settings/applications/new)
    
    OAuth application 的新建页面：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200120112205356.png)
    
    *   点击 “Register application” 注册后，记下 **Client ID** 和 **Client Secret** 备用
    *   注册后，可在 “Developer settings” — “OAuth Apps" 中查看或修改
3.  添加模板 gitalk.html
    
    在主题的 **layouts/partials** 目录中新建 **gitalk.html** 文件：
    
    ```
    {{ if .Site.Params.enableGitalk }}
    
        <div></div>
    
        <link rel="stylesheet" href="https://unpkg.com/gitalk/dist/gitalk.css">
    
        <script src="https://unpkg.com/gitalk/dist/gitalk.min.js"></script>
    
        <script>
    
            const gitalk = new Gitalk({
    
                clientID: '{{ .Site.Params.Gitalk.clientID }}',
    
                clientSecret: '{{ .Site.Params.Gitalk.clientSecret }}',
    
                repo: '{{ .Site.Params.Gitalk.repo }}',
    
                owner: '{{ .Site.Params.Gitalk.owner }}',
    
                admin: ['{{ .Site.Params.Gitalk.owner }}'],
    
                id: location.pathname, 
    
                distractionFreeMode: false 
    
            });
    
            (function() {
    
                if (["localhost", "127.0.0.1"].indexOf(window.location.hostname) != -1) {
    
                    document.getElementById('gitalk-container').innerHTML = 'Gitalk comments not available by default when the website is previewed locally.';
    
                    return;
    
                }
    
                gitalk.render('gitalk-container');
    
            })();
    
        </script>
    
    {{ end }}
    
    ```
    
    *   建议直接将 gitalk 的资源文件下载放到项目目录中（本地引用 gitalk.min.js 插件）
    *   使用国内的 CDN（https://www.bootcdn.cn / 搜索 gitalk）在线引用
    
    主题（**themes/hugo-theme-docdock/layouts/partials/custom-content-footer.html**）调用 **gitalk.html** 文件，放置位置在版权信息前面：
    
    ```
    {{ partial "gitalk.html" . }}
    
    ```
    
4.  配置 config.toml
    
    在 **config.toml** 中添加以下配置（参数名称对应 **gitalk.html** 文件中的命名）：
    
    ```
    [params]
    
        enableGitalk = true            
    
    [params.gitalk] 
    
        clientID = "xxx"               
    
        clientSecret = "xxx"           
    
        repo = "discuss"               
    
        owner = "xxx"                  
    
        admin= "xxx"                   
    
        id= "location.pathname"        
    
        labels= "gitalk"               
    
        perPage= 15                    
    
        pagerDirection= "last"         
    
        createIssueManually= false     
    
        distractionFreeMode= false     
    
    ```
    
    *   配置请参考：https://github.com/gitalk/gitalk
5.  初始化评论区
    
    设置好后，将博客内容推送到发布网站，首次启用 Gitalk 需要初始化（运行`hugo server`时，**gitalk.html** 文件设置不启用 Gitalk）。未初始化时，弹出警告提示如下：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113225215637.png)
    
    使用注册 Github Application 时的账号登陆并授权：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200113225528620.png)
    
    授权后进入每一篇博客刷新页面，才能激活每一篇博客的评论区。
    

十、SEO
-----

SEO（Search Engine Optimization，搜索引擎优化），利用搜索引擎的规则提高网站在有关搜索引擎内的自然排名。目的是让其在行业内占据领先地位，获得品牌收益。

检查网站是否被收录：Google / 百度搜索框输入`site:yoursite.github.io`。

验证网站：[Google 搜索引擎](https://www.google.com/webmasters/tools/home?hl=zh-CN)、[百度搜索引擎](http://zhanzhang.baidu.com/site/siteadd)。

验证方法：

*   文件验证 (简单方便, 推荐使用)
*   html 标签验证
*   CNAME 验证

网站上线后，做 SEO 时需要检查如下内容：

*   集成 Google Analytics 或百度统计
    
*   为页面增加 header 信息，如 title 和 description
    
*   sitemap.xml
    
    将网站生成工具自动生成的站点地图的 URL 提交给 Google Webmaster Tools。
    
*   robots.txt
    
    阻止搜索引擎爬取网站上的敏感网页。
    
*   结构化数据：可以帮助爬虫理解页面内容，参考 [HTML5 的结构化数据](https://schema.org/docs/full.html)
    

### 1、网站内容优化

1.  描述（description）
    
    在 **config.toml** 中设置网站描述信息：
    
    ```
    [params]
    
        description = "xxxxxx"
    
    ```
    
    在每篇文章的扉页中设置文章描述信息：
    
    ```
    description = "Where you should come to find my homepage updates and stuff"
    
    
    
    ```
    
    在 **themes/hugo-theme-docdock/layouts/partials/original/head.html** 或 **themes/hugo-theme-docdock/layouts/partials/original/custom-head.html** 文件（简称 **head.html**）中添加`<meta>`标签：
    
    ```
    <meta {{ with .Description }}{{ . }}{{ else }}{{ with .Site.Params.description }}{{ . }}{{ end }}{{ end }}">
    
    ```
    
2.  文章关键词（keywords）
    
    在每篇文章的扉页中设置 keywords：
    
    ```
    +++
    
    keywords = [mysite,mysite keyword,Another useful keyword]
    
    +++
    
    ```
    
    然后，在`<head>`标签（**head.html**）内添加`<meta>`标签：
    
    ```
    <meta content="{{ delimit .Keywords ", " }}" >
    
    ```
    
3.  文章标题（title）
    
    每篇文章都应有一个标题，方便搜索引擎收录。Hugo 生成的文章会在`<head>`标签内自动添加`<title>`标签。若无，在文章模板的`<head>`标签内添加`<title>`标签。
    
    ```
    <title>{{ $isHomePage := eq .Title .Site.Title }}{{ .Title }}{{ if eq $isHomePage false }} - {{ .Site.Title }}{{ end }}</title>
    
    ```
    
    也可以在`<meta>`标签中添加标题标签。
    
    ```
    <meta content="{{ $isHomePage := eq .Title .Site.Title }}{{ .Title }}{{ if eq $isHomePage false }} - {{ .Site.Title }}{{ end }}" property="og:title">
    
    ```
    

### 2、Google 搜索优化

1.  进入 Google Search Console
    
    打开 [Google 网站站长](https://www.google.com/webmasters/)，点击 “[SEARCH CONSOLE](https://search.google.com/search-console/welcome?hl=zh-CN) ” 进入：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117164644387.png)
    
    *   网域：需要验证 DNS，因此只适用于个人域名
    *   网址前缀：Pages 网站只能选择此方法，输入完整网址后，点击 “继续” 按钮
2.  验证所有权
    
    进入验证页面：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117170542353.png)
    
    要求下载一个 html 文件（`google××××.html`），将这个 html 文件保存到 hugo 站点根目录内的 **static** 子目录，提交推送到被验证网站后，回到验证页面点击 “验证”。
    
3.  资源
    
    验证成功后，点击 “前往资源页面”：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117170730185.png)
    
    点” 索引” 下的” 站点地图”，
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117171415848.png)
    
    在” 添加新的站点地图” 处输入被验证网站的 sitemap（自动生成，存放在网站根路径）：
    
    ![](https://kuang.netlify.app/blog/hugo/image-20200117172801027.png)
    
    等待一天左右就可以搜到了！
    

### 3、baidu 搜索优化

打开[百度搜索资源平台的链接提交](https://ziyuan.baidu.com/linksubmit/url?sitename)，输入网址：

![](https://kuang.netlify.app/blog/hugo/image-20200122001242571.png)

提交后，百度不保证一定能够收录您提交的链接。

点击 “用户中心” — “站点管理” — “添加网站” 时，或使用站长工具需要实名认证。

和 Google 不一样，百度不容许以子目录的方式提交子站点，https://xxx.github.io/xxx/` 的形式就不能直接提交了，只能在提交 sitemap 文件时，提交多个 sitemap 文件。这样也能勉强让百度收录。

十一、脑图
-----

Hugo 除了能够制作个人博客网站外，还能制作脑图（mindmap，又称思维导图）和 HTML 格式的幻灯片（slide）。

### 1、Hugo 制作脑图

参阅：[Hugo 中使用思维导图](https://wocai.de/post/2019/03/hugo-%E4%B8%AD%E4%BD%BF%E7%94%A8%E6%80%9D%E7%BB%B4%E5%AF%BC%E5%9B%BE/)。此方法尚未实现。

想要在 blog 中插入脑图，之前一直都在使用插入图片的方法，这样非常不优雅。

基于百度的 [kityminder](https://github.com/fex-team/kityminder) 修改为适用于 Hugo：

1.  在[这里](https://github.com/HunterXuan/unordered-list-to-mind-map)下载插件
    
    *   **kity.min.js**：svg（可缩放矢量图）渲染库，放入 hugo 目录的 **static/js/** 中
    *   **kityminder.core.min.js**：脑图渲染库，放入 hugo 目录的 **static/js/** 中
    *   **mindmap.js** 和 **mindmap.min.js**：将 **li** 标签（Markdown 的列表）转换成脑图需要的 **json** 文件，放入 hugo 目录的 **static/js/** 中
    *   **mindmap.css**：脑图渲染库，放入 hugo 目录的 **static/css/** 中
2.  在线引用 **jquery.min.js** 插件
    
    ```
    <script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script>
    
    ```
    
    通常情况下，主题已具备，不需要另外准备。
    
3.  在[这里](https://github.com/fex-team/kityminder-core/blob/dev/src/kityminder.css)下载 **kityminder.css**，改名为 **kityminder.core.css**，放入 hugo 目录的 **static/css/** 中
    
4.  引用文件
    
    在模板的 **head** 标签（**themes/hugo-theme-docdock/layouts/partials/original/head.html**）内引用文件：
    
    ```
    <link href="{{.Site.BaseURL}}css/kityminder.core.css" rel="stylesheet">
    
    <link href="{{.Site.BaseURL}}css/mindmap.css" rel="stylesheet">
    
    <script src="{{.Site.BaseURL}}js/kity.min.js"></script>
    
    <script src="{{.Site.BaseURL}}js/kityminder.core.min.js"></script>
    
    <script src="https://cdn.bootcss.com/jquery/3.4.1/jquery.min.js"></script></script>
    
    <script src="{{.Site.BaseURL}}js/mindmap.js"></script>
    
    ```
    
    *   jquery.slim.min.js 导致 TOC 失败，删除
5.  使用 shortcodes（简码）将 Markdown 渲染为 html
    
    在 hugo 目录 **layouts/shortcodes** 下新建一个 html（例如为 **mind.html**）：
    
    ```
    <div id="{{ .Get 0 }}">
    
        {{- .Inner -}}
    
    </div>
    
    ```
    
    这个 shortcodes 非常简单，就是将其中的内容渲染出来套个 div 标签，加上 mindmap 的类名。
    
6.  然后在 Markdwon 中添加文本
    
    ```
    { {< mind mindid >}}
    
    - 根目录
    
        - 一级目录1
    
            - 二级目录1
    
            - 二级目录2
    
        - 一级目录2
    
    { {< /mind >}}
    
    ```
    
    凡事都不会一帆风顺。Hugo 渲染后无显示结果
    
    *   首先，在模板的 **head** 标签内引用文件后，导致页面的 TOC 失效，删除引用 jquery.slim.min.js，TOC 恢复正常
    *   然后，查看控制台（Console）发现是 mindmap.js 触发错误：Uncaught TypeError: Cannot read property ‘childNodes’ of undefined

*   问题尚未解决（放弃）

### 2、Hugo 引用脑图

通过 Hugo 制作的脑图，实现的功能和样式单一。现改变思路：Hugo 引用脑图的 HTML 文件。此方法的脑图的功能和样式由制作工具决定，缺点是转换的 HTML 文件容量比较大。

*   首先，通过专用工具（例如 mindmanager）制作脑图，脑图导出为 HTML 文件
    
    下载 [mindmanager](https://www.mindmanager.cn/) 版本，搜索注册码（目前可用：MP20-345-DP56-7778-919A）破解。制作脑图后，“另存为” 时选择保存类型为 HTML5 交互式导图（*.html）。
    
*   然后，Hugo 引用脑图导出的 HTML 文件
    
    假设脑图的 HTML 文件为 Git.html，必须存放在 **static** 目录内。避免被 Hugo 渲染。否则，Hugo 渲染脑图文件时会触发错误（命令中存在 “-” 字符）：
    
    > Rebuild failed: “E:\xxx\xxx.html:16:1”: template: __E:\xxx\xxx.htmlHTML:16: unexpected bad character U+002D ‘-’ in command
    > 
    > 16 <script id="mmap-config” type="text/plain">{{mmap-config}}< /script >
    
    *   方法一：使用 [button](https://docdock.netlify.com/shortcodes/button/) 简码跳转到指定页面显示脑图
        
        优点是布局简洁且不影响当前页面的加载速度（需要时，才点击按钮，在本页面显示脑图的 HTML 文件内容。缺点是覆盖了本页面（来回切换页面）。
        
        button 简码默认在本页跳转到指定页面（`onclick="location.href='{{.}}'"`），修改后（`onclick="window.open('{{.}}')"`），可在新页跳转到指定页面。
        
        docDock 主题的 button 简码（button.html）的源代码为：
        
        ```
        {{ with .Get "align" }}<center>{{end}}
        
        <button class="btn  {{with .Get "theme"}}btn-{{.}}{{else}}btn-primary{{end}}" type="button" {{ with .Get "href" }} onclick="location.href='{{.}}'"{{end}} >{{.Inner}}</button>
        
        {{ with .Get "align" }}</center>{{end}}
        
        
        
        ```
        
        *   href="url”：url 可为任意文件类型。若为本地文件时，url 路径相对于 **static** 目录。
        
        在 Markdown 文件中使用 button 简码：
        
        ```
        { {< button href="/images/Git.html" >}} Git的思维导图 { {< /button >}}
        
        ```
        
    *   方法二：使用`<iframe>`框架跳转到子页面显示脑图
        
        优点是在当前页面显示另一个 HTML 页面（又称外部页面、子页面）的内容且能自定义 CSS；缺点是脑图文件过大，严重影响当前页面的加载速度。
        
        1.  定义一个`<iframe>`框架
            
            在当前页面嵌套子页面：
            
            ```
            <iframe ></iframe>
            
            ```
            
            *   id 或 name=“xxx”：区分不同的子页面
                
            *   frameborder="0|1”：0 表示无边框；1 表示有边框
                
            *   style="CSS”：设置子页面的 CSS
                
                width 为子页面的宽，height 为子页面的高；可以使用百分比，会自适应父容器的百分比，也可以使用固定的行高列宽：100px,60px。
                
            *   src="url”：设置子页面的关联 url（链接地址），url 可为任意文件类型
                
            *   scrolling="auto|yes|no”：是否显示子页面滚动条，缺省时值为 auto
                
        2.  若只打算在 Markdown 文件中显示脑图
            
            需要在 **config.toml** 文件中开启`unsafe = true`，url 输入脑图的相对路径（相对于当前的 Markdown 文件）。
            
        3.  若打算在 Hugo（个人博客）中显示脑图
            
            1.  新建（`<iframe>`框架修改为）一个简码文件（存入 **layouts/shortcodes** 目录，假设为 **mind.html**）：
                
                ```
                <iframe id="{{ .Get 0 }}" name="{{ .Get 0 }}" frameborder="0" src="{{ .Get 1 }}">
                
                </iframe>
                
                ```
                
                *   `{{ .Get 0 }}`：0 表示使用第一个传入参数；1 表示使用第二个传入参数
            2.  Markdown 文件使用简码：
                
                ```
                { {< mind mindid "/images/Git.html" >}}
                
                ```
                
                *   第一个参数是简码名称、第二个参数传递给 id 和 name、第三个参数传递给 src（脑图的 HTML 文件，存入 **static** 目录，路径相对于 **static** 目录）

十二、幻灯片
------

幻灯片，是使用全屏来显示 Markdown 文件内容的页面。

Markdown 文件内容可以呈现为全屏的 manifest.js 演示文稿。[manifest.js](https://revealjs.com/#/) 使您可以使用 HTML 创建漂亮的交互式幻灯片。参阅：[PRESENT A SLIDE](https://docdock.netlify.com/create-page/page-slide/)。

1.  在 Markdown 文件的扉页中设置`type="slide"`和`[revealOptions]`
    
    ```
    +++
    
    title = "Test slide"     # Markdown文件的标题
    
    type="slide"             # Markdown文件的类型：slide（幻灯片）
    
    theme = "league"         # revel.js框架（幻灯片）的主题
    
    [revealOptions]
    
    transition= 'concave'    # 幻灯片过渡类型值：none|fade|slide|convex|concave|zoom
    
    controls= true           # 显示幻灯片的控制箭头
    
    progress= true           # 显示幻灯片的演示进度条
    
    history= true            # 将每个幻灯片的更改记入浏览器历史记录
    
    center= true             # 滑块垂直对中
    
    +++
    
    ```
    
    *   参数参阅：https://github.com/hakimel/reveal.js/#configuration
2.  幻灯片定界符
    
    在 Markdown 文件中为幻灯片演示文稿创建内容时，使用定界符区分每一张幻灯片：
    
    *   水平幻灯片的定界符：3 个减号`---`
        
    *   垂直幻灯片的定界符：3 个下划线`___`
        
        通常，垂直幻灯片用于在顶层水平幻灯片下方显示信息。
        
3.  创建幻灯片内容
    
    使用`#`标记标题的层级
    

* * *

Getting up
----------

*   Turn off alarm
*   Get out of bed

* * *

Breakfast
---------

*   Eat eggs
*   Drink coffee

* * *

* * *

Dinner
------

*   Eat spaghetti
*   Drink wine

* * *

Going to sleep
--------------

*   Get in bed
*   Count sheep

```
4. 使用HTML语法实现各种功能



reveal.js控制幻灯片的行为和样式，Markdown控制幻灯片的内容，HTML语法控制幻灯片的多媒体功能。Hugo的设置（例如：**config.toml**内的设置、简码等）在幻灯片中无效，可直接使用HTML语法扩展幻灯片的多样化功能。







## 参考资料



> 官网：

>

> - [Hugo官网](https://gohugo.io/)

> - [Hugo官网中文镜像](https://s0gohugo0io.icopy.site/ )

> - [Hugo官方教程（英文）Hugo Docs](https://gohugo.io/documentation/)

> - [HUGO LEARN THEME](https://learn.netlify.com/en/)

> - [Travis CI官方文档](https://docs.travis-ci.com/)

> - [Netlify官方文档](https://docs.netlify.com/)

>

> 个人博客：

>

> - [Tutorials for the Hugo static site generator](https://kodify.net/hugo-static-site-tutorials/)

> - [Blog养成记](https://orianna-zzo.github.io/series/blog养成记/)

> - [本站引用图片的“顺滑”流程](https://rayhy.com/blog/20190301-本站引用图片的顺滑流程/)

> - [Hugo静态网站生成器中文教程](http://nanshu.wang/post/2015-01-31/)

> - [Hugo 搭建博客实践](https://creaink.github.io/post/Devtools/Hugo/Hugo-intro.html)

> - [使用Hugo建个网站](https://lyzhang.me/post/make_a_blog_with_hugo/)

> - [hugo中文帮助文档](https://hugo.aiaide.com/)

> - [Hugo学习笔记](https://skyao.io/learning-hugo/)

> - [苏洋博客](https://soulteary.com/)

> - [静态网站构建手册-使用Hugo构建个人博客](https://jimmysong.io/hugo-handbook/)



## 操作环境



> - 操作系统：Win 10

> - Hugo工具：hugo_0.61.0_Windows-64bit

```

Copyright © 2020 komantao. All Rights Reserved.