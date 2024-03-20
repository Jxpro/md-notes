# Zotero配置Bib文献引用

[TOC]

Zotero Better BibTeX（简称ZBB）是一个`Zotero`插件，专为改善其在处理`BibTeX`和`BibLaTeX`文献引用方面的体验而设计，它可以生成更精确和个性化的`cited key`，具有自动更新的功能和更丰富的文献信息。

## 一、安装插件

-   下载`xpi`文件https://github.com/retorquere/zotero-better-bibtex/releases
-   在Zotero中选择，工具 > 附加组件 > lnstall Add-on From File，选择并安装下载好的ZBB插件文件

## 二、配置插件

在Zotero中选择，工具 > Better BibTeX > 打开Better BibTeX首选项，进行如下配置：

1.   修改`cited key`格式为：`auth + shorttitle(2) + shortyear`，然后进入主目录，选择“我的文库”，全选右侧的全部文献，右键选择Better Bibtex > Refresh Bibtex Key，手动刷新一下`cited key`。

     更详细的配置见官网教程：https://retorque.re/zotero-better-bibtex/citing/index.html

     ![image-20240320221333551](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/03-20-221334.png)

2.   设置导出时不包含摘要、注释、附件、关键词，避免.bib文件过于臃肿：`abstract,note,file,keywords`

     ![image-20240320221445000](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/03-20-221445.png)

## 三、配置Zotero

在Zotero中选择，编辑> 首选项 > 导出，设置Zotero默认便捷复制格式为`Better BibTex`

![image-20240320221720722](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/03-20-221721.png)

这样使用`Ctrl+Shift+C`即可快速复制如下bib内容进行粘贴：

```tex
@article{WangEnhancingEarth22,
  title = {Enhancing {{Earth Observation Throughput Using Inter-Satellite Communication}}},
  author = {Wang, Peng and Li, Hongyan and Chen, Binbin and Zhang, Shun},
  year = {2022},
  month = oct,
  journal = {IEEE Transactions on Wireless Communications},
  volume = {21},
  number = {10},
  pages = {7990--8006},
  issn = {1536-1276, 1558-2248},
  doi = {10.1109/TWC.2022.3163389},
  urldate = {2023-08-08},
  langid = {english},
  annotation = {rate: 3}
}
```

## 四、引用文献

在需要文献引用的`tex`文件同目录下，新建`references.bib`，然后在文档的末尾，但在`\end{document}`之前，使用`\bibliography{references}`来指定你的文献数据库。最后再使用`\bibliographystyle{style}`指定参考文献样式，其中`style`可以是`plain`、`abbrv`、`alpha`等。

常见的预设样式的可选项有8种，分别是：

-   plain，按字母的顺序排列，比较次序为作者、年度和标题；
-   unsrt，样式同plain，只是按照引用的先后排序；
-   alpha，用作者名首字母+年份后两位作标号，以字母顺序排序；
-   abbrv，类似plain，将月份全拼改为缩写，更显紧凑；
-   ieeetr，国际电气电子工程师协会期刊样式；
-   acm，美国计算机学会期刊样式；
-   siam，美国工业和应用数学学会期刊样式；
-   apalike，美国心理学学会期刊样式；

## 五、基本框架

```tex
\documentclass[fontset=windows]{ctexart}
\begin{document}
\section{Related Work}

Chenxi Li etc\cite{LiKnowledgeGraph23} proposed ...

\bibliographystyle{plain}
\bibliography{route}

\end{document}
```

## 六、BiTex和BiLaTex

>   `VSCode`中如果报警告`empty journal/empty year`则需要将导出格式换为`BibTeX`

`BibTeX`和`BibLaTeX`都是用于`LaTeX`文档中引用文献的工具，但它们在功能和使用方式上有一些区别。

`BibLaTeX`采用了更细致的数据模型来描述文献信息，与`BibTeX`相比，它引入了一些不同的字段名和更多的条目类型来更准确地反映不同的文献资源。这种设计旨在提供更灵活和详尽的文献描述能力，以适应更广泛的文献引用需求。

1.  **不同的字段名称**：
    -   对于期刊文章，`BibLaTeX`使用`journaltitle`来代替传统的`journal`字段。这是因为`BibLaTeX`旨在更清晰地区分出版物的标题和它所属的集合或期刊的名称。
    -   而`year`字段通常被包含在更通用的`date`字段中。`BibLaTeX`鼓励使用`date`字段来表示出版日期，因为它允许更详细的日期信息（如月份和日子），并且更好地支持没有具体年份的文献（例如，仅有季度或月份的出版物）。
3.  **功能和灵活性**：
    -   `BibTeX`功能相对基础，主要支持生成和格式化文献列表。它有一套预定义的文献类型和格式化样式，但在自定义方面较为有限。
    -   `BibLaTeX`提供了更广泛的文献类型支持和更复杂的参考文献格式化选项。它允许用户更灵活地自定义文献列表的样式，并且可以更好地处理多语言文献、引用样式等复杂情况。