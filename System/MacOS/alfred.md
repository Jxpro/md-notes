# Alfred配置详解和workflow推荐

`Alfred`是`macOS`平台强大的效率软件，本篇文章基于`Alfred 5.0.6 [2110]`进行介绍，主要讲解其`preferences`配置项，以及常用`workflow`推荐，暂不讲解如何制作。

## 一、配置概述

下文概述图片引自`Alfred4`，与`Alfred5`的`preferences`并无不同。

![Alfred-4.png](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-191040.png)

## 二、各项详解

下面对于每个部分的配置进行详解。

### 2.1 General

一些常用的基本设置

![image-20230325191931921](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-191932.png)

-   `Startup`
    
    勾选表示系统重启时，会自动启动 Alfred。

-   `Alfred Hotkey`
    
    一般设置为 `command + Space`，也就是`⌘`加空格，需要在设置里取消聚焦（`spotlight`）的快捷键。

    ![image-20230326042115952](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-042427.png)

-   `Where are you`
    
    设置你所有的国家或地区，目的是更加人性化的内容，比如谷歌搜索或易呗，中国就淘宝什么的，目前这个针对中国区还没有什么作用，默认即可。

-   `Permissions`
    
    主要是授权辅助功能和磁盘完全访问，其他权限将在第一次需要时自动请求。点击 `Request Permissions...` 查看详情。

### 2.2 Features

#### 2.2.1 Default Results

主要是设置搜索结果的范围。

![image-20230325200201702](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-200202.png)

-   `Applecations`

    ![image-20230325195001895](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-195002.png)
    
    -   `Fuzzy capital letters`
        
        根据大写字母可以进行简单的匹配，例如输入`gc`会显示`Google Chrome`。

    -   `Full fuzzy match from word boundary`
        
        完全模糊匹配搜索从单词边界开始的任何字符，例如输入`ps`会显示`Photoshop`。

    -   `Match Application's keywords in default results`
        
        设置是否搜索应用关键词。以苹果的音乐App为例，简介可以看到有一栏是关键词，App添加这些关键词信息，就是为了使`Spotlight`、`Alfred` 等应用搜索这些词语时，匹配并显示出来。
    
        ![image-20230325194614622](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-194615.png)

        ![image-20230325194429798](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-194430.png)

-   `Essentials`
    
    是否搜索系统偏好设置和联系人。建议都取消勾选，不影响搜索应用程序和workflow。

-   `Extras`
    
    是否搜索文件夹、文档（word，ppt文稿等）、文本文件、图片、压缩文件、苹果脚本。在右侧 `Advanced…` 按钮，可以添加自定格式，比如`markdown`文件的 `.md` ，只需要将 Alfred 无法检索到的文件类型拖至弹出的框内即可添加。建议都取消勾选，使用后面介绍的`file search`功能可以实现更强大的文件搜索功能，还不会干扰默认结果。

-   `Unintelligent`
    
    搜索全部类型的文件。不推荐开启，使用后面介绍的`file search`功能可以实现更强大的文件搜索功能，还不会干扰默认结果。

-   `Search Scope`
    
    可以设置搜索的文件空间或目录。

-   `Fallbacks`
    
    用于设置搜索没有找到结果时，显示的可选的进一步的操作。比如Google搜索或翻译等。

#### 2.2.2 File Search

##### 2.2.2.1 Search

通过关键字快速和突出搜索的文件的结果。

![image-20230325201619426](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-201619.png)

-   `Quick Search`
    
    通过输入`'`或空格可以可以快速查找文件。

-   `Opening Files`
    
    直接打开文件，默认关键字为`open`，一般设置为`op`，上述`Quick Search`默认就是`open`，即找到文件后回车，就是直接打开该文件。

-   `Revealing Files`
    
    打开文件所在的文件夹，默认关键字为`find`，一般设置为`fd`。

-   `Inside Files`
    
    按文件内容搜索，默认关键词为`in`，找到文件后可以快速打开文件。

-   `File Tags`
    
    搜索文件标签，访达里设置标签，如下所示。

    ![image-20230325203653661](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-203654.png)

-   `Don't Show`
    
    设置不展示的内容，比如邮件，书签等。

-   `Result Limit`
    
    显示的搜索结果条数，可以设置为`20`、`30`、`40`条。

##### 2.2.2.2 Navigatiton

可以在`Alfred`的输入栏中浏览`macOS`的文件系统，而无需用鼠标操作访达。

-   `/`：在 Alfred 输入栏中首先输入“/”，会带你进入 macOS根目录。
-   `~`：在 Alfred 输入栏中首先输入“~”，会带你进入当前的用户目录。

![image-20230325211121713](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-211426.png)

-   `Filtering`
    
    启动模糊搜索。

-   `Use ← and → for folder navigation`
    
    勾选这个复选框之后，可以利用 `←/→`来进入上一级目录或者下一级目录。但是`Alfred`默认`→`为打开`Universal Actions`（将在后面介绍），因此如果你不想与此功能冲突的话，可以利默认的 `Command + Up` 和 `Command + Down` 来进行导览，或者更改 `Universal Actions`使用的快捷键，后面有说明。

-   `Use ↩︎ to open folders in Finder`
    
    勾选这个复选框后，按下回车键后会直接在访达中打开选中的文件夹。

-   `Previous Path`
    
    可以在这里设置热键和关键字，来进入上一个打开过的目录。

>   在展示结果中，左侧是文件列表，右侧是文件预览。新版本在右下角添加了一个配置菜单，点击⚙️️图标可以看到有这些选项：
>
>   -   排序方式：名称、创建日期、修改日期
>   -   文件夹置顶
>   -   逆序排列，快捷键：`⇧Shift + ⌘Command + S`
>   -   隐藏预览面板，快捷键：`⇧Shift + ⌘Command + I`
>
>   ![Alfred-Features-File-Search-Navigation.png](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-020823.png)

##### 2.2.2.3 Buffer

文件多选缓存操作，可以从不同的文件夹中选择多个文件，或者同时对多个文件进行相同的操作等，可以进行批量操作，打开、共享、删除等。

![image-20230325214907777](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-214908.png)

-   Option + ↑ ：把该文件加入列表。
-   Option + ↓ ：把该文件加入列表，光标跳向下一个文件。
-   Option + ← ：删除列表最后一个文件。
-   Option + → ：对列表文件进行统一操作。
-   Option + delelte：删除列表所有文件

利用以上的快捷键将一系列的文件加入到文件缓存区中（文件缓存区位于`Alfred`输入框上方），然后利用 `fn` 键或 `ctrl` 键、`⇥`键（`Tab`键）等打开 `Universal Actions`对这些文件进行同一操作。

-   `Buffer Clearing`
    
    设置操作缓存文件后从缓存区清除文件，或5分钟不使用时清除。

-   `Compatibility`（兼容性）
    
    如果上面的 `Option`键 + 方向键与其它的操作冲突了，那么可以勾选这个复选框，那么将使用 `Shift + Alt + 方向键` 来操作文件缓存。

##### 2.2.2.4 Advanced

高级设置，可以先看看，有需要再设置。

![image-20230325221348511](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-221349.png)

-   `Escape path on 'Copy path to Clipboard' action`
    
    对于复制到剪切板的路径使用转义，方便粘贴到终端中使用。

-   `Run AppleScripts instead of opening`
    
    直接运行AppleScript而不是打开文件。

-   `Use file type icons for files on external drives`
    
    不使用文件本身的图标，可以防止不必要地加载外部驱动器，从而提高Alfred的性能。

-   `Touch folders after opening them`
    
    将文件夹的kMDltemFSContentChangeDate标志更新为当前时间，以便在`Spotlight`和`Alfred`中进行优先级排序。

-   `Touch aliases after opening them`
    
    将别名的kMDltemFSContentChangeDate标志更新为当前时间，以便在`Spotlight`和`Alfred`中进行优先级排序。

-   `Home Folder`
    
    自定义Home目录的符号。

#### 2.2.3 Universal Actions

可以对单个或多个文件执行一些操作

![image-20230325225333092](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-25-225333.png)

-   `Show Actions`
    
    在选中的文件或者目录上按下勾选的`fn` 键或 `ctrl` 键、`⇥`键（`tab`键）可以触发动作，具体动作的见 `Actions` 标签栏。

-   `Selection Hotkey`
    
    自定义快捷键来显示`Actions`。

-   `Action Ordering`
    
    排序选项，如果勾选，表示按最近使用的动作优先显示。

#### 2.2.4 Web Search

`Alfred`内置了不少网页搜索，比如`google`、`wiki`等，但对于国内用户来说，可能还需要添加百度、知乎等，对于程序员来说，可能还有github仓库搜索等，点击右下角的 `Add Custom Search`，可按照提示的格式自己增加。

![image-20230326021828711](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-021829.png)

以github仓库搜索为例，点击`Add Custom Search`，填入如下内容即可。

-   `Search URL`
    
    `{query}`表示`Alfred`输入框你所搜索的关键词，具体链接可以在相应的网页中搜索任意关键词，然后将`URL`复制，并将关键字替换为 `{query}`即可。另外，配置搜索引擎时，借助网站方面的 `OpenSearch` 与 `SearchAction` 协议，可实现自动化填写 `Search URL`，不需要自己寻找关键词参数位置，然后替换为 `{query}` 。具体操作为，添加搜索引擎时，在`Search URL`文本框中填入网站的域名，然后点击右侧的 `Lookup` 就会自动分析并显示可用的搜索链接格式，接着点击 `Use` 就可以了。但遗憾的是，并非所有网站都支持这两项协议。

-   `Title` 
    
    结果列表中显示的提示语，右边还能上传一个icon图标，用于标识此搜索。

-   `Keyword` 
    
    是这个网页搜索的快捷词，例如：`repo vue`。

-   `Validation`
    
    用于测试搜索内容是否正确，点击右边的`Test`按钮，查看搜索效果。

-   `Copy URL for sharing`
    
    点击生成一个带有`alfred://` 前缀的字符串到剪切板，在`Alfred`中粘贴回车后可自动导入该`web search`项。例如：`alfred://customsearch/Search%20Github%20Repo%20for%20'%7Bquery%7D'/repo/utf8/nospace/https://github.com/search?q=%7Bquery%7D`

![image-20230326021809326](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-021809.png)

这里分享一些网络搜集的自定义搜索项：

-   掘金搜索：`alfred://customsearch/Search%20Juejin%20for%20%27%7Bquery%7D%27/jj/utf8/nospace/https%3A%2F%2Fjuejin.im%2Fsearch%3Ftype%3Dall%26query%3D%7Bquery%7D`
-   简书搜索：`alfred://customsearch/Search%20Jianshu%20for%20%27%7Bquery%7D%27/js/utf8/nospace/https%3A%2F%2Fwww.jianshu.com%2Fsearch%3Futf8%3D%25E2%259C%2593%26q%3D%7Bquery%7D`
-   微信文章：`alfred://customsearch/Search%20Weixin%20for%20%27%7Bquery%7D%27/gzh/utf8/nospace/https%3A%2F%2Fweixin.sogou.com%2Fweixin%3Ftype%3D2%26query%3D%7Bquery%7D`
-   知乎内容：`alfred://customsearch/Search%20Zhihu%20for%20%27%7Bquery%7D%27/zh/utf8/nospace/https%3A%2F%2Fwww.zhihu.com%2Fsearch%3Ftype%3Dcontent%26q%3D%7Bquery%7D`
-   苹果文档：`alfred://customsearch/Search%20%EF%A3%BFDeveloper%20for%20%27%7Bquery%7D%27/ad/utf8/nospace/https%3A%2F%2Fdeveloper.apple.com%2Fsearch%2F%3Fq%3D%7Bquery%7D`
-   淘宝搜索：`alfred://customsearch/Search%20Taobao%20for%20%27%7Bquery%7D%27/tb/utf8/nospace/https%3A%2F%2Fs.taobao.com%2Fsearch%3Foe%3Dutf-8%26f%3D8%26q%3D%7Bquery%7D`
-   京东搜索：`alfred://customsearch/Search%20JD%20for%20%27%7Bquery%7D%27/jd/utf8/nospace/https%3A%2F%2Fsearch.jd.com%2FSearch%3Fenc%3Dutf-8%26keyword%3D%7Bquery%7D`
-   GitHub：`alfred://customsearch/Search%20Github%20for%20%27%7Bquery%7D%27/sh/utf8/nospace/https%3A%2F%2Fgithub.com%2Fsearch%3Futf8%3D%25E2%259C%2593%26q%3D%7Bquery%7D`
-   StackOverflow：`alfred://customsearch/Search%20StackOverflow%20for%20%27%7Bquery%7D%27/so/utf8/nospace/https%3A%2F%2Fwww.stackoverflow.com%2Fsearch%3Fq%3D%7Bquery%7D`

上面这个自定义搜索，只需在`Alfred`输入框中粘贴这个字符串（`alfred://`前缀的字符串），按下回车键就可以导入这个自定义搜索到自己的`Alfred`中。

另外`URLs / History`标签栏主要是记录和识别你在`Alfred`中输入的`URL`，一般不用用到。

#### 2.2.5 Web Bookmarks

可以自动索引并搜索浏览器书签，目前仅支持 `Safari` 和 `Chrome` 浏览器。

![image-20230326023436857](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-023437.png)

-   `Show bookmarks`
    
    为了更加方便和快捷的搜索书签，可以设置通过 `via keyword`关键词来直接搜索书签，设置前缀`b`为搜索书签。

-   `Open Bookmarks`
    
    设置搜索结果中的书签，从那个浏览器打开，可以选择默认系统的浏览器，或者书签来源的浏览器打开。

#### 2.2.6 Clipboard History

默认情况下，`Alfred`是没有打开剪贴板历史这个功能的，需要自己手动去开启，可以打开文字、图片、文件的剪贴板功能，自定义一个快捷键，光标在需要粘贴的地方，呼出剪贴板历史，选中需要粘贴的内容，回车即可。

![image-20230326023918372](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-023918.png)

-   `Clipboard History`
    
    可以设置普通文本、图片和文件的保留时间，因为复制内容的内容会占用存储空间，所以需要设置过期的时间。

-   `Viewer Hotkey`
    
    全局快捷键，用于在所有的软件界面都能调用出来，插入所需内容，一般设置为`shift + command + C`。

-   `Viewer Keyword`
    
    用于设置在`Alfred`输入框中输入 `clipboard` 后，可以快速进入到剪切版历史视图。

-   `Clear Keyword`
    
    剪切版的内容不只是占用空间，可能你还包括了敏感的内容，所以你可能想清空剪切版的内容。所以，可以点击右边的`Clear Now`马上清空全部内容，注意此执行没有确认弹窗，清空后剪切版数据不能恢复。或者打开`Alfred`输入`clear`，可以选择清除最近的 `5分钟`、`15分钟`或`全部数据`清除。

-   `Snippets`
    
    是否在剪切板中展示和所搜`Snippets`内容（下节将介绍）。

-   `Universal`
    
    是否忽略从其它苹果设置同步过来的剪切版内容。

-   `Merging`
    
    比如你复制了三段文本，可以你需要把它们组合成一段话，默认情况下需要你按3次快捷键，但是如果你勾选了这个`Merging`功能，就可以实现快速的合并一段剪切的内容。具体的使用也很简单，勾选`Merging`标签的 `Fast append selected text`后就可以，剪切的文本，默认是 `⌘ + c` 剪切一次，再按一次 `⌘ + c`（按住`⌘`不动，再按下`c`键），就是触发合并操作，此时的剪切的内容会与上一次剪切的内容合并为一个。合并内容的格式可以选择是用空格、换行或者不分隔的方式来分隔文本的合并。

-   `Advanced`
    
    这里可以设置一些高级的内容，具体如下。
    -   是否区分少数语言中变音符号，如iphone和 iphóne，aaoäöö和aao等
    -   按回车键后自动粘贴到当前激活的应用的输入框中
    -   复制的内容如果是相同的内容自动移动到最前面（不会重复保存）
    -   剪切版的内容的最大尺寸，`256k`字符串、`512k`或不限制等
    -   忽略的Apps，可以忽略 `Keychain Access`、`1Password`、`Wallet`等可能有敏感内容的剪切内容等。

#### 2.2.7 Snippets

`Snippet`（字符片段）的作用，简单来说就是能够将自定义的文字通过**关键词**或者**快捷键**的方式插入到当前光标之后。

![image-20230326025539006](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-025539.png)

-   `Viewer Hotkey`
    
    这里我们不需要单独设置一个快捷键，因为可以与上一节的 `Clipboard History`共用一个弹窗界面，这样更加高效，只需要勾选上述`Clipboard History`的 `Snippets`相关项即可 。

-   `Snippet Keyword`
    
    用于设置在`Alfred`输入框中输入 `snip` 后，可以快速查找`Snippet`。

-   `Matching`
    
    设置匹配是通过 `Name`和 `Keyword`，还是包括 `Snippet`内容。

-   `Automatically expand snippets by keyword`

    是否在任何输入框中输入`Snippet`关键字时，将其自动替换为`Snippet`的内容。需要`Alfred`启用辅助功能。

![image-20230326030232325](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-030232.png)

这里`Alfred`提供了一些占位符，比如`{time}`（如：16:26:23）、`{date}`（如：2020年2月9日）、`{clipboard}`（当前剪切版的内容）、`{random}`等，其它表示见编辑窗口的左下角`{}`。更强大的替换操作，`Alfred`建议使用`Workflow Snippet Trigger object`。

另外，Alfred还提供了许多有趣使用的`Snippets`，具体查看官网[Snippets - Alfred](https://www.alfredapp.com/extras/snippets/)：

-   Mac Symbols：有很多常用的Mac符号，比如输入`!!cmd`对应`⌘`符号等。
-   ASCII Art：集合了一些好玩的火星文字表情，比如`(╯°□°）╯︵ ┻━┻`等。
-   Currency Symbols：一些常用的货币符号，比如`::cny`代表`¥`等。
-   Dynamic Content examples：一些关于动态占位符的例子，可以学习一下。
-   Emoji Pack：很强大的Emoji表情包。

>   `Alfred4`加入了富文本支持，对于编写邮件等日常事务很有帮助，进一步扩展了使用场景。附加的编辑菜单，支持字体、粗体/斜体/下划线、颜色、复制/粘贴格式等简单的调整选项。
>
>   ![Alfred-Features-Snippets-RichText.png](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-031536.png)

#### 2.2.8 Calculator

![image-20230326031654082](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-031654.png)

-   `Standard`
    
    `Alfred`支持简单的四则运算等，输入`16 * 1024`便能够计算出结果，敲击回车键之后能够自动保存到剪贴板中，方便快速复制和使用。

-   `Advanced`
    
    利用`GCMathParser`模块，`Alfred`还能进行很多高级计算，比如三角运算、平方根等。勾选此选项，然后以 `=` 开头即可，比如：`=log2(256) + sqrt(1024/2*3^4)`。

-   `Input` / `Output`
    
    设置输入和输出的结果表达式的千位分隔符等。

#### 2.2.9 Dictionary

![image-20230326032231502](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-032231.png)

`Alfred`提供了词典查询并使用自带的词典软件，可以通过 `define` 和 `spell` 唤起。并且`spell`支持模糊搜索。

#### 2.2.10 Contacts

![image-20230326032356029](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-032356.png)

`Alfred`也内置了 `Contacts`查询，联动了系统自带的通讯录，输入对应小伙伴的名词能够查看对应名片。但在国内还是微信为主，就不过多介绍，直接禁用即可。

#### 2.2.11 Music

![image-20230326032557836](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-032558.png)

可以从键盘控制`iTunes`与`Alfred`的内置迷你播放器。但国区还是喜欢用其他音乐App，也不会购买 Apple Music 服务，就不过多介绍，直接禁用即可。

#### 2.2.12 1Password

![image-20230326032825909](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-032826.png)

`Alfred`提供的与 `1Password`（需单独下载）集成，暂未使用，也不好详细介绍。

#### 2.2.13 System

![image-20230326033054995](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-033055.png)

`Alfred`支持的系统功能操作。`Screen Saver`（显示待机屏幕）、`Empty Trash`（清空回收站）、`Log Out`（登出当前用户）、`Sleep`（睡眠模式）、`Sleep Displays`（关闭屏幕显示）、`Lock`（锁屏）、 `Restart`（重启）、`Shut Down`（关机）、`Volume Up`（增加音量）、`Volume Down`（减少音量）、`Toggle Mute`（静音）等快捷命令。

针对应用程序可以：`Hide`（隐藏）、`Quit`（退出程序）、`Force Quit`（强制退出）、`Quit All`（退出所有程序）。

`Eject` 是弹出磁盘、存储卡或者虚拟磁盘镜像，如 .dmg 挂载后的磁盘。`Eject All`是全部弹出。

以上的操作，都是可以自定义关键字，另外有一些后面带 `Confirm`的，表示是一些危险的操作，可以勾选，在操作时会先弹窗提示操作的风险。

#### 2.2.14 Terminal

![image-20230326033311357](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-033311.png)

从`Alfred`输入框中输入命令，即可打开终端并运行命令，可以设置使用 `>` 或其它更改为前缀符号来表示当前要执行命令，默认是打开 macOS 的`终端`软件，也可以更改为打开 `iTerm`，只需在将`Application`更改为`custom`，并输入以下`ApplesSript`代码。

```ApplesSript
-- For the latest version:
-- https://github.com/vitorgalvao/custom-alfred-iterm-scripts

-- Set this property to true to always open in a new window
property open_in_new_window : false

-- Set this property to false to reuse current tab
property open_in_new_tab : true

-- Set this property to true if iTerm is configured to launch without opening a new window
property iterm_opens_quietly : true

-- Handlers
on new_window()
  tell application "iTerm" to create window with default profile
end new_window

on new_tab()
  tell application "iTerm" to tell the first window to create tab with default profile
end new_tab

on call_forward()
  tell application "iTerm" to activate
end call_forward

on is_running()
  application "iTerm" is running
end is_running

on has_windows()
  if not is_running() then return false

  tell application "iTerm"
    if windows is {} then return false
    if tabs of current window is {} then return false
    if sessions of current tab of current window is {} then return false

    set session_text to contents of current session of current tab of current window
    if words of session_text is {} then return false
  end tell

  true
end has_windows

on send_text(custom_text)
  tell application "iTerm" to tell the first window to tell current session to write text custom_text
end send_text

-- Main
on alfred_script(query)
  if has_windows() then
    if open_in_new_window then
      new_window()
    else if open_in_new_tab then
      new_tab()
    else
      -- Reuse current tab
    end if
  else
    -- If iTerm is not running and we tell it to create a new window, we get two:
    -- one from opening the application, and the other from the command
    if is_running() or iterm_opens_quietly then
      new_window()
    else
      call_forward()
    end if
  end if

  -- Make sure a window exists before we continue, or the write may fail
  -- "with timeout" does not work with a "repeat"
  -- Delay of 0.01 seconds repeated 500 times means a timeout of 5 seconds
  repeat 500 times
    if has_windows() then
      send_text(query)
      call_forward()
      exit repeat
    end if

    delay 0.01
  end repeat
end alfred_script
```

#### 2.2.15 Large Type

![image-20230326033634487](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-033634.png)

一些执行结果的文本可能需要显示放大的效果。例如执行 `2^10` 的结果，按 `comand + L` 快捷键就可以显示。

![Alfred-Features-Large-Type-Calculator.png](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-034106.png)

#### 2.2.16 Previews

![image-20230326033641557](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-033641.png)

熟悉`macOS`的同学都知道，有一个功能叫 `Quick Look`，就是在访达中选中文件后按下空格键，就能在不打开应用程序的情况下对文件进行快速预览。`Alfred`也提供了这一功能，不过此时的快捷键是 `⇧`键（`shift`键）。在`Alfred`的搜索结果列表中选中想要进行预览的文件，按下`shift`键就能进行预览了。有时候对某些文件或者某些路径下的文件进行预览，如果你不想对这些文件或者路径下的文件进行预览，可以在 `No Previews For` 和 `No Previews In`中设置。

### 2.3 Workflows

![image-20230326034407262](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-034407.png)

`Workflows` 翻译中文为**工作流**，应该也比较好理解。刚才上面介绍的功能，有一部分就是工作流，可以理解为`Alfred`自带的工作流。如果想要使用，需要支付购买`Alfred`的`PowerPack`（动力组），或者使用第三方破解版。

本文主要讲解的是`preferences`配置项，所以关于如何自定 `Workflows`暂时不讲解，后面有需要在回来增加，但后面会推荐一些常用的`Workflows`推荐。

### 2.4 Appearance

![image-20230326034835996](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-034836.png)

`Alfred`默认提供了几个外观样式供大家选择。另外也可以自定义外观，喜欢折腾 样式的小伙伴可以在`Appearance`面板中修改或点击 `+` ，自定义`Alfred`交互面板的样式，另外也可以下载别人的外观样式，比如在网站 [Packal](http://www.packal.org/)。

### 2.5 Remote

![image-20230326035025850](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-035026.png)

Alfred 支持使用移动端版本的`Alfred`来控制桌面端，但是收费，下载链接 [Alfred Remote on the App Store for iOS](https://apps.apple.com/app/alfred-remote/id927944141?ign-mpt=uo=4)。

### 2.6 Advanced

![image-20230326035146131](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/new2023-03-26-035146.png)

配置中有涉及一些高级配置功能，一般不需要更改配置。

-   `Files / Apps`
    
    清除缓存（`Clear Application Cache`）或重建（spotlight）索引（`Rebuild macOS Metadata`） 。重建索引适用于找不到文件或应用是执行，无法点击按钮自动执行，请参考文章：[Alfred 搜索不到文件的问题](https://zhuanlan.zhihu.com/p/412068546)。

-   `History`
    
    是否存储输入历史，在空白`Alfred`输入框内使用上下方向键可以重用之前的输入，或者可以选择直接重用五分钟内的最近输入。

-   `Learning`
    
    是否优先展示最近的搜索结果，右边`clear knowledge`可清除记录。

-   `Notifications`
    
    控制是否展示通知图标，以及点击通知时是否消失。

-   `Network`
    
    控制脚本是否使用代理，以及设置代理地址

-   `Selection Hotkeys`
    
    `Alfred`在使用快捷键获取选定项目时会使用剪贴板，比如点击文件，然后使用`Universal Actions`快捷键（`command + option + \`）对文件进行操作时，文件会放入剪切板。该选项可以控制是否恢复上一项的剪贴板内容，恢复上一项的剪贴板内容会使Alfred使用更多内存。

-   `Force Keyboard`
    
    强制`Alfred`输入框中使用的输入法。

-   `Action Modifiler`
    
    搜索结果项右边的快捷键，默认是 `⌘ + 数字`。

这里还有一些默认的动作快捷键，比如在 Alfred 输入`swift`后，通过下面三个快捷键可以快捷执行操作：

-   `ctrl + ↩︎`：在浏览器默认的搜索引擎来查询`swift`关键词。
-   `alt + ↩︎`：打开访达的搜索框查询`swift`。
-   `cmd + ↩︎`：打开 Alfred 搜索结果项所在的文件目录（如果是本地文件的话）。

-   `Syncing`
    
    设置`Alfred`所有的配置内容，包括`Workflows`和`Appearance`，都可以备份和同步。设置方式很简单，点击`Set preference folder`弹窗中，找到 云盘（如`iCloud`）目录，在目录下新建一个文件夹来存放`Alfred`所有配置的同步文件（Alfred.alfredpreferences），这样，以后如果你有多台`MacOS`系统，登陆同一个`Apple`账号，就可以在所有的设备上同步配置。点击 `Reveal in Finder` 可以打开你设置的同步文件所在的目录。

