# Markdown

## 什么是&thinsp;Markdown

>    Markdown 是一种轻量级标记语言，用于使用纯文本编辑器创建格式化文本。John Gruber 于 2004 年创建了 Markdown，作为一种易于以源代码形式阅读的标记语言。
>
>    <p align="right">———— Wikipedia</p>

Markdown 的优势是显著的，它足够轻巧，易读易写；但是，其缺陷也是显著的，那就是[初代版本](https://daringfireball.net/projects/markdown)的语法规范性不足，在实现中经常产生偏离作者意图的错误（最重要的是 Markdown 本身没有任何内容会被判定为“语法错误”，所以不会产生任何报错）。

随着 Markdown 的广泛应用，许多更为细致的语法规范应运而生。其中最为著名的是 [CommonMark](https://spec.commonmark.org/)（标准化），常见的 GFM（GitHub 风格的 Markdown）也是基于它改编的。

!!! note "Markdown 的本质"

    Markdown 的本质是一种标记语言，可以轻松转换成 HTML（映射到 HTML 的子集）。Markdown 只决定解析出的 HTML 是什么，不会决定任何视觉上的样式。最终视觉上的效果由 HTML、CSS 等决定。

    <p align="right">———— <a href="https://github.com/TonyCrane">TonyCrane</a></p>

    <div style="text-align: center;">
    <img src="/assets/images/cs/tools/markdown/1.png" width="80%" style="margin: 0 auto;">
    </div>

    <center>
    image from <a href="https://github.com/TonyCrane/PracticalSkillsTutorial">https://github.com/TonyCrane/PracticalSkillsTutorial</a>
    </center>

### Markdown&thinsp;有什么用

- 编写文档，支持包括 PDF 在内的多种导出格式
    - [VSCode](https://code.visualstudio.com/) + [Markdown Preview Enhanced](https://shd101wyy.github.io/markdown-preview-enhanced/#/)
    - [Typora](https://typora.io/)（*not recommend*）
- 搭建网站，极大程度减小 HTML 网页编写工作量
    - 笔记类网站制作：[MkDocs](https://www.mkdocs.org/)
    - 博客类网站制作：[Hexo](https://hexo.io/)
    - 在线 Slides 制作：[reveal-md](https://github.com/webpro/reveal-md)

## Markdown&thinsp;语法规范

这里不再赘述基本的 Markdown 语法，如果想要快速入门，建议阅读 [CommonMark 官网的教程](https://commonmark.org/help/)。

Markdown 还有许多常用的扩展语法，例如表格、脚注、任务列表、流程图、数学公式等。这些扩展语法在 Markdown Extra、LiaScript 等变体中引入，目前许多主流的 Markdown 编辑器或解析器已经可以识别它们。

!!! abstract

    接下去的篇幅将会列举一些值得关注的 Markdown 语法规范，许多人在使用 Markdown 的时候可能并没有注意到这些规范性问题。

!!! info

    以下规范基于 [CommonMark 3.0](https://spec.commonmark.org/0.30/) 标准。

    以下示例用的 Markdown 解析器为 [commonmark.js](https://github.com/commonmark/commonmark.js)，渲染效果直接由 Markdown 源码中嵌入 HTML 实现（不受任何其他解析器干扰）。此外，commonmark.js 也提供[在线解析服务](https://spec.commonmark.org/dingus/)，自己亲手试一试吧！

### 不建议使用的语法

当你在阅读 Markdown 基本语法的时候，会发现原来同一个效果可以由多种写法实现（比如分割线既可以是 `---` 也可以是 `***`）。这听起来似乎很方便，但我认为一个人的 Markdown 语法应当是一以贯之的，因此非常不建议混用多种写法。

这里罗列一下笔者几乎不用的语法：

Setext 式标题、`*` `_` 式分割线、`+` `*` 式无序列表、`)` 式有序列表、缩进式代码块、`< >` 自动链接（autolinks）、引用式链接（reference links）、`_` 式强调等。

### 段落、硬换行与软换行

在很多时候，人们往往倾向于减少空行的使用（比如写代码的压行）。但在 Markdown 语法中，空行是很关键的，我们甚至建议大家多用空行。因为如果在应该空行的地方不空行，会引起歧义和错误，而多用几个空行是完全没问题的（Markdown 会折叠连续空行，最终只会有空一行的效果）。

???+ note "Markdown 空行的定义"

    空行指的是，没有任何字符，或者只包含空格（`U+0020`）和制表符（`U+0009`）的一行。

Markdown 中最基本的文本片段是段落（paragraphs），很多人会混淆另起一段和另起一行，这里给出他们的区别：

=== "另起一段"

    Markdown 源码：

    ```markdown
    这是第一段

    这是第二段
    ```

    解析为 HTML：

    ```html
    <p>这是第一段</p>
    <p>这是第二段</p>
    ```

    渲染效果：

    <p>这是第一段</p>
    <p>这是第二段</p>

=== "空格硬换行"

    Markdown 源码：

    ```c
    这是第一段  // (1)!
    这是第二段
    ```

    1. 这里行末需要两个或更多的空格

    解析为 HTML：

    ```html
    <p>这是第一段<br />
    这是第二段</p>
    ```

    渲染效果：

    <p>这是第一段<br />
    这是第二段</p>

=== "反斜杠硬换行"

    Markdown 源码：

    ```c
    这是第一段\// (1)!
    这是第二段
    ```

    1. 这里行末需要一个反斜杠 `\`

    解析为 HTML：

    ```html
    <p>这是第一段<br />
    这是第二段</p>
    ```

    渲染效果：

    <p>这是第一段<br />
    这是第二段</p>

=== "软换行"

    Markdown 源码：

    ```c
    这是第一段// (1)!
    这是第二段
    ```

    1. 这里行末没有空格或只有一个空格

    解析为 HTML：

    ```html
    <p>这是第一段
    这是第二段</p>
    ```

    渲染效果：

    <p>这是第一段
    这是第二段</p>

这几种情形的区别还是挺明显的。当你想要另起一段的时候，你需要插入至少一个空行；当你仅仅想要换行的时候，你需要用两个或更多的空格，或者一个反斜杠来实现。那么，软换行呢？

???+ note "中文排版中不建议使用软换行"

    这个是真的不建议，除非你清楚地知道你在用软换行做什么。

    软换行的关键在于，它在衔接上下文的时候自动补上了一个空格。如果这是西文排版，那这个空格就非常合适；但如果这是中文排版，那这个空格就非常突兀。

    === "西文排版的软换行"

        Markdown 源码：
        
        ```markdown
        This is line 1.
        And this is line 2, but this sentence is ended
        in line 3.
        ```

        渲染效果：

        <p>This is line 1.
        And this is line 2, but this sentence is ended
        in line 3.</p>

    === "中文排版的软换行"

        Markdown 源码：

        ```markdown
        这是第一行。
        这是第二行，但这个句子会在
        第三行结束。
        ```

        渲染效果：

        <p>这是第一行。
        这是第二行，但这个句子会在
        第三行结束。</p>

关于段落和换行，还有一些值得注意的地方：

- 一定要养成添加空行的习惯，从而确保你的 Markdown 源码在任何地方都能被正确解析。
    - 除了段落之外，建议在不同的结构之间都插入空行，比如段落和列表之间、段落和引用之间、段落和代码块之间等。<br />
      这些空行并不都是必须的，但是本着宁多不少、一以贯之的原则，还是建议养成添加空行的习惯。
- 是否使用硬换行是个见仁见智的问题。
    - 就我个人而言，我不喜欢用空格来换行（太不明显了），也不喜欢用反斜杠来换行（有时候无法正确解析，比如 MkDocs）。<br />
      我会直接在行末加上 `<br />`。
    - 不过更多时候，我还是习惯于只用段落不用换行。笔者认为一段话在 Markdown 源码里就应该同一行写完，这是更符合 Markdown 语法理念的习惯。

### 容器：块引用与列表

### 代码块与内联代码

### 强调与强烈强调

### 反斜杠转义

### 用&thinsp;HTML&thinsp;完善&thinsp;Markdown&thinsp;语法

## Markdown&thinsp;排版建议

!!! abstract

    遵守语法规范可以保证你能编写出没有歧义的 Markdown，但是如果要将这些内容导出，用于分享与交流，那你一定还需要关注排版问题。换言之，这不是对与错的问题，而是是否符合主流审美的问题。

    接下去的篇幅将会归纳一些笔者认为值得参考的排版建议。

\# TODO

## 如何优雅地构建&thinsp;Markdown&thinsp;工作流

\# TODO

## MkDocs&thinsp;杂记

!!! abstract

    笔者通常使用 MkDocs 来作为发布器（静态网站生成器），从而进行[个人笔记网站](https://minjoker.github.io/)制作。

    接下去的篇幅将会收集一些笔者使用 MkDocs 过程中遇到的值得注意的地方。

!!! info "笔者的工具版本"

    - MkDocs：
    - Material for MkDocs：

\# TODO

## 参考资料

- [CommonMark](https://commonmark.org/)
- [计算机类实验文档降压宝典（编写规范）](https://hypotensor.tonycrane.cc/)
- [浙江大学系列朋辈辅学「实用技能拾遗」课程资料仓库](https://github.com/TonyCrane/PracticalSkillsTutorial)
- [中文排版需求（Requirements for Chinese Text Layout）](https://www.w3.org/International/clreq/)