# Markdown

## 什么是 Markdown ？

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

## Markdown 语法规范

这里不再赘述基本的 Markdown 语法，如果想要快速入门，建议阅读 [CommonMark 官网的教程](https://commonmark.org/help/)。

Markdown 还有许多常用的扩展语法，例如表格、脚注、任务列表、流程图、数学公式等。这些扩展语法在 Markdown Extra、LiaScript 等变体中引入，目前许多主流的 Markdown 编辑器或解析器已经可以识别它们。

!!! abstract

    接下去的篇幅将会列举一些值得关注的 Markdown 语法规范，许多人在使用 Markdown 的时候可能并没有注意到这些规范性问题。

!!! info

    以下规范基于 [CommonMark 3.0](https://spec.commonmark.org/0.30/) 标准。

\# TODO

## Markdown 排版建议

!!! abstract

    遵守语法规范可以保证你能编写出没有歧义的 Markdown，但是如果要将这些内容导出，用于分享与交流，那你一定还需要关注排版问题。换言之，这不是对与错的问题，而是是否符合主流审美的问题。

    接下去的篇幅将会归纳一些笔者认为值得参考的排版建议。

\# TODO

## 如何优雅地构建 Markdown 工作流

\# TODO

## MkDocs 杂记

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