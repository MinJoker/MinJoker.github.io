# Markdown

## 什么是&thinsp;Markdown

> Markdown 是一种轻量级标记语言，用于使用纯文本编辑器创建格式化文本。John Gruber 于 2004 年创建了 Markdown，作为一种易于以源代码形式阅读的标记语言。
>
> <p style="text-align: right">———— Wikipedia</p>

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

    以下示例用的 Markdown 解析器为 [commonmark.js](https://github.com/commonmark/commonmark.js)，“解析为 HTML”严格按照 CommonMark 规范，但是“渲染效果”仅供参考，因为事实上这篇笔记是用 MkDocs（python-markdown）解析并发布的。此外，commonmark.js 也提供[在线解析服务](https://spec.commonmark.org/dingus/)，自己亲手试一试吧！

### 不建议使用的语法

当你在阅读 Markdown 基本语法的时候，会发现原来同一个效果可以由多种写法实现（比如分割线既可以是 `---` 也可以是 `***`）。这听起来似乎很方便，但我认为一个人的 Markdown 语法风格应当是一以贯之的，因此非常不建议混用多种写法。

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

### 容器：块引用与列表项

容器（container blocks）是 Markdown 中一个很有意思的块结构，容器内可以使用任何 Markdown 语法和结构（包括嵌套容器本身），看起来就像是容器内装着一篇完整的 Markdown 文本一样。

容器分为两种：块引用（block quotes）与列表项（list items）。下面将分别介绍它们的语法。

---

块引用的标记 `>` 和其后的文本之间原则上需要用一个空格隔开，但是这个空格是可以省略的（但建议养成打空格的习惯，原因见下文“如何落入容器”）：

=== "Markdown 源码"

    ```markdown
    > 这是一句话
    >
    >这也是一句话
    ```

=== "解析为 HTML"

    ```html
    <blockquote>
    <p>这是一句话</p>
    <p>这也是一句话</p>
    </blockquote>
    ```

=== "渲染效果"

    <blockquote>
    <p>这是一句话</p>
    <p>这也是一句话</p>
    </blockquote>

---

列表项的标记 `-` `.` 和其后的文本之间原则上需要用一个空格隔开，并且不同于块引用，这个空格是不可以省略的，而且也最好不要插入过多的空格，以免被解析为缩进式代码块（不要插入过多空格这一点对于几乎所有 Markdown 语法都是成立的）：

=== "Markdown 源码"

    ```markdown
    - 这是一句话

    -这也是一句话

    1. 这是一句话

    0.这也是一句话
    ```

=== "解析为 HTML"

    ```html
    <ul>
    <li>这是一句话</li>
    </ul>
    <p>-这也是一句话</p>
    <ol>
    <li>这是一句话</li>
    </ol>
    <p>0.这也是一句话</p>
    ```

=== "渲染效果"

    <ul>
    <li>这是一句话</li>
    </ul>
    <p>-这也是一句话</p>
    <ol>
    <li>这是一句话</li>
    </ol>
    <p>0.这也是一句话</p>

列表（list）由同种类型的列表项（list items）组成，列表中的空行会影响列表行距的宽窄，这一点有些复杂（注意观察渲染效果的细微差别，以及 HTML 代码中的 `<p>` 标签），下面给出几个例子：

=== "不存在任何空行"

    渲染效果：

    <ul>
    <li>这是第一个母列表项
    <ul>
    <li>这是第一个子列表项</li>
    <li>这是第二个子列表项</li>
    </ul>
    </li>
    <li>这是第二个母列表项</li>
    </ul>

    Markdown 源码：

    ```markdown
    - 这是第一个母列表项
        - 这是第一个子列表项
        - 这是第二个子列表项
    - 这是第二个母列表项
    ```

    解析为 HTML：

    ```html
    <ul>
    <li>这是第一个母列表项
    <ul>
    <li>这是第一个子列表项</li>
    <li>这是第二个子列表项</li>
    </ul>
    </li>
    <li>这是第二个母列表项</li>
    </ul>
    ```

=== "母列表存在空行"

    渲染效果：

    <ul>
    <li>
    <p>这是第一个母列表项</p>
    <ul>
    <li>这是第一个子列表项</li>
    <li>这是第二个子列表项</li>
    </ul>
    </li>
    <li>
    <p>这是第二个母列表项</p>
    </li>
    </ul>

    Markdown 源码：

    ```c
    - 这是第一个母列表项
    // (1)!
        - 这是第一个子列表项
        - 这是第二个子列表项
    - 这是第二个母列表项
    ```

    1. 这里是属于母列表的一个空行

    解析为 HTML：

    ```html
    <ul>
    <li>
    <p>这是第一个母列表项</p>
    <ul>
    <li>这是第一个子列表项</li>
    <li>这是第二个子列表项</li>
    </ul>
    </li>
    <li>
    <p>这是第二个母列表项</p>
    </li>
    </ul>
    ```

=== "子列表存在空行"

    渲染效果：

    <ul>
    <li>这是第一个母列表项
    <ul>
    <li>
    <p>这是第一个子列表项</p>
    </li>
    <li>
    <p>这是第二个子列表项</p>
    </li>
    </ul>
    </li>
    <li>这是第二个母列表项</li>
    </ul>

    Markdown 源码：

    ```c
    - 这是第一个母列表项
        - 这是第一个子列表项
    // (1)!
        - 这是第二个子列表项
    - 这是第二个母列表项
    ```

    1. 这里是属于子列表的一个空行

    解析为 HTML：

    ```html
    <ul>
    <li>这是第一个母列表项
    <ul>
    <li>
    <p>这是第一个子列表项</p>
    </li>
    <li>
    <p>这是第二个子列表项</p>
    </li>
    </ul>
    </li>
    <li>这是第二个母列表项</li>
    </ul>
    ```

=== "母子列表都存在空行"

    渲染效果：

    <ul>
    <li>
    <p>这是第一个母列表项</p>
    <ul>
    <li>
    <p>这是第一个子列表项</p>
    </li>
    <li>
    <p>这是第二个子列表项</p>
    </li>
    </ul>
    </li>
    <li>
    <p>这是第二个母列表项</p>
    </li>
    </ul>

    Markdown 源码：

    ```c
    - 这是第一个母列表项
    // (1)!
        - 这是第一个子列表项
        // (2)!
        - 这是第二个子列表项
    - 这是第二个母列表项
    ```
    
    1. 这里是属于母列表的一个空行

    2. 这里是属于子列表的一个空行

    解析为 HTML：

    ```html
    <ul>
    <li>
    <p>这是第一个母列表项</p>
    <ul>
    <li>
    <p>这是第一个子列表项</p>
    </li>
    <li>
    <p>这是第二个子列表项</p>
    </li>
    </ul>
    </li>
    <li>
    <p>这是第二个母列表项</p>
    </li>
    </ul>
    ```

关于列表还有一点值得注意，就是如何在不落入上一个列表的情况下，另起一个新的列表（注意仔细看渲染效果的行距差别）。更换列表项的标识是可行的，但是我更推荐用 HTML 空白注释来实现：

=== "更换列表项的标识"

    渲染效果：

    <ul>
    <li>这是一句话</li>
    <li>这也是一句话</li>
    </ul>
    <ul>
    <li>这还是一句话</li>
    <li>这仍然是一句话</li>
    </ul>
    
    Markdown 源码：

    ```markdown
    - 这是一句话
    - 这也是一句话
    * 这还是一句话
    * 这仍然是一句话
    ```

=== "HTML 空白注释"

    渲染效果：

    <ul>
    <li>这是一句话</li>
    <li>这也是一句话</li>
    </ul>
    <!-- -->
    <ul>
    <li>这还是一句话</li>
    <li>这仍然是一句话</li>
    </ul>
    
    Markdown 源码：

    ```markdown
    - 这是一句话
    - 这也是一句话
    <!-- -->
    - 这还是一句话
    - 这仍然是一句话
    ```

#### 如何落入容器

前面提到，容器可以包含一切 Markdown 语法和结构，但问题的关键在于如何将一段 Markdown 代码判定为落入容器（成为这个容器内的一部分）。

- 块引用通过 `>` 来标识某一行落入容器，这是很好判断的。
- 列表项通过适当的缩进来标识某一行落入容器，这里很容易产生歧义。
    - 想要落入列表项，必须至少缩进到列表项 `-` `.` 标记后的文本位置，但也不能缩进太多，以免被解析为缩进式代码块。

听起来有点抽象，我们来看几个例子：

=== "块引用"

    Markdown 源码：

    ```c
    > 这是一句话
    >// (1)!
    > 这也是一句话
    // (2)!
    这还是一句话
    ```

    1. 这里是一个落入块引用的空行

    2. 这里是一个没有落入块引用的空行

    解析为 HTML：
    
    ```html
    <blockquote>
    <p>这是一句话</p>
    <p>这也是一句话</p>
    </blockquote>
    <p>这还是一句话</p>
    ```

    渲染效果：

    <blockquote>
    <p>这是一句话</p>
    <p>这也是一句话</p>
    </blockquote>
    <p>这还是一句话</p>

=== "列表项"

    Markdown 源码：

    ```c
    - 这是一句话
      
      这也是一句话// (1)!

        这还是一句话// (2)!

          这仍然是一句话// (3)!
    ```

    1. 这一行前面有两个空格，缩进到和“这是一句话”对齐的位置

    2. 这一行前面有一个制表符（四个空格），这样的缩进也是可以的

    3. 这一行前面有六个空格，相对“这是一句话”缩进了四个空格，被解析为缩进式代码块

    解析为 HTML：
    
    ```html
    <ul>
    <li>
    <p>这是一句话</p>
    <p>这也是一句话</p>
    <p>这还是一句话</p>
    <pre><code>这仍然是一句话
    </code></pre>
    </li>
    </ul>
    ```

    渲染效果：

    <ul>
    <li>
    <p>这是一句话</p>
    <p>这也是一句话</p>
    <p>这还是一句话</p>
    <pre><code>这仍然是一句话
    </code></pre>
    </li>
    </ul>

???+ note "为什么建议不要省略块引用的空格"

    通过一个例子来说明：

    === "不省略空格"

        Markdown 源码：
        
        ```c
        > - 这是一句话
        >
        >   这也是一句话// (1)!
        ```

        1. 这一行缩进到了和“这是一句话”对齐的位置，成功落入列表项

        渲染效果：

        <blockquote>
        <ul>
        <li>
        <p>这是一句话</p>
        <p>这也是一句话</p>
        </li>
        </ul>
        </blockquote>

    === "省略空格"

        Markdown 源码：
        
        ```c
        >- 这是一句话// (1)!
        >
        >   这也是一句话// (2)!
        >
        >  这还是一句话// (3)!
        ```

        1. 这里的块引用 `>` 后省略了空格

        2. 这一行比“这是一句话”多缩进了一格，成功落入列表项

        3. 这一行缩进到了和“这是一句话”对齐的位置，按理说应该成功落入列表项，但是事实并非如此

        渲染效果：

        <blockquote>
        <ul>
        <li>
        <p>这是一句话</p>
        <p>这也是一句话</p>
        </li>
        </ul>
        <p>这还是一句话</p>
        </blockquote>

    发生了什么？仔细思考你就会发现，虽然我们有权省略块引用后的空格，但是在判定是否落入容器的时候，列表项可不惯着你，它默认你的 `>` 后有一个空格，所以要求你在缩进的时候算上这个空格。换言之，直接和 `-` 后的文本对齐并不总是适用，有时会需要多打一个空格。

### 反斜杠转义

在 Markdown 中，所有 ASCII 中的标点符号都可以被 `\` 转义（backslash-escaped）。

但是反斜杠转义在内联代码、代码块、自动链接、纯 HTML 中是无效的，在其他语法和结构中则都是有效的，下面给出几个例子：

=== "内联代码"

    Markdown 源码：

    ```c
    `good \` boy`// (1)!
    ```

    1. 这里要注意，内联是从左往右顺序解析的，所以第一个反引号会和第二个反引号配对

    解析为 HTML：

    ```html
    <p><code>good \</code> boy`</p>
    ```

    渲染效果：

    <p><code>good \</code> boy`</p>

=== "纯 HTML"

    Markdown 源码：

    ```markdown
    <a href="/bar\/">BAR</a>
    ```

    解析为 HTML：

    ```html
    <p><a href="/bar\/">BAR</a></p>
    ```

=== "Markdown 链接"

    Markdown 源码：

    ```markdown
    [BAR](/bar\/)
    ```

    解析为 HTML：

    ```html
    <p><a href="/bar/">BAR</a></p>
    ```

### 内联代码

上文提到反斜杠转义在内联代码中是无效的，那么我们该如何在内联代码中转义 `` ` `` 呢？

答案就是通过不同数量的反引号 `` ` `` 来实现反引号自身的转义。假设内联代码中有最多连续反引号 m 个，最少连续反引号 n 个，则我们可以通过连续使用 m+1 个反引号，或者 n-1 个（n>1）反引号来包裹内联代码实现代码中的反引号转义。

=== "Markdown 源码"

    ```c
    ````im``good```boy````// (1)!

    `im``good```boy`

    `` ` ``// (2)!
    ```

    1. 这里的内联代码含有最多连续反引号 3 个，最少连续反引号 2 个。

    2. 这里展示如何像上文那样转义单个反引号。注意反引号两侧的空格，这是必要的，具体语法原因见 CommonMark。

=== "解析为 HTML"

    ```html
    <p><code>im``good```boy</code></p>
    <p><code>im``good```boy</code></p>
    <p><code>`</code></p>
    ```

=== "渲染效果"

    <p><code>im``good```boy</code></p>

    <p><code>im``good```boy</code></p>
    
    <p><code>`</code></p>
    <!-- blankline is necessary here for parsing??? -->

### 强调与强烈强调

强调 `*` 与强烈强调 `**` 在具体使用中并不像你想象的那么简单，我们通过几个例子来看看：

=== "Markdown 源码"

    ```c
    这是* 一句 *话// (1)!

    This is a sentence*(n.)*.// (2)!
    ```

    1. `*` 包裹的内容两侧不能有空格。

    2. `*` 包裹内容的外侧为字母而内侧为标点符号的时候，强调无效。

=== "解析为 HTML"

    ```html
    <p>这是* 一句 *话</p>
    <p>This is a sentence*(n.)*.</p>
    ```

=== "渲染效果"

    <p>这是\* 一句 \*话</p>
    <p>This is a sentence\*(n.)\*.</p>
    <!-- precedence seems to be wrong here? python-markdown is different from commonmark here! -->

强调和强烈强调会在你意想不到的地方出现错误，而且当你查阅 CommonMark 的时候就会发现，它们的语法真的很复杂且不自然。我的建议是，如果你的强调和强烈强调无法成功渲染，请用 HTML 标签 `<em>` 和 `<strong>` 代替它们。

### 用&thinsp;HTML&thinsp;完善&thinsp;Markdown&thinsp;语法

Markdown 的定位决定了它只会有很少一部分简单常用的语法，但是我们可以通过 HTML 来丰富 Markdown 的语法，这为 Markdown 提供了非常好的扩展功能。

这里笔者推荐一些好用的 HTML 语法。

!!! warning

    值得一提的是，在 Markdown 中使用 HTML 语法，理论上来说没有任何问题。但具体到渲染和发布过程，比如以 Material for MkDocs 为例，有时候 HTML 语法在 Markdown 中确实会渲染出问题。不过这种情况很少发生，具体问题具体分析就好。

---

#### 更好地插入图片

```html
<div style="text-align: center;"><!-- (1)! -->
<img src="url" alt="alternatetext" style="width: 60%;"><!-- (2)! -->
</div>
```

1. 这里的 `center` 也可以改成 `left` 和 `right`；不建议使用 `<center>` 或者 `<div align>`，它们不被 HTML5 支持。
2. 建议像这样用 `style` 来保护 `width` 和 `height`，以免被 CSS 干扰。

Markdown 语法不支持图片的大小缩放和位置调整，我们可以通过 HTML 来实现。

---

#### 文本居中与居右

```html
<p style="text-align: center"><!-- (1)! -->
This is a sentence.
</p>
```

1. 这里的这里的 `center` 也可以改成 `left` 和 `right`。

Markdown 语法也不支持文本的居中与居右，我们可以通过 HTML 来实现。

---

#### 更多的内联标记

```html
这里用于<del>测试</del>删除线。

这里用于<u>测试</u>下划线。
```

渲染效果：

这里用于<del>测试</del>删除线。

这里用于<u>测试</u>下划线。

---

#### 更多的空格与空行

Markdown（以及浏览器）会折叠连续空格与空行，但有的时候我们真的想要更多的空格与空行。

- 更多空格：HTML 转义字符
    - `&ensp;`（半角空格，en-space）
    - `&emsp;`（全角空格，em-space）
    - `&thinsp;`（通常为全角空格的 $\frac{1}{5}$ 或 $\frac{1}{6}$，thin-space）
- 更多空行：HTML 标签 `<br>`

---

#### 注释

可以用 HTML 语法 `<!-- -->`，在 Markdown 中书写注释。

值得注意的是，这种注释写法不仅在 Markdown 源文件中可见，在渲染出的 HTML 文件中也可见，只是不呈现在浏览器上而已；

---

## MkDocs&thinsp;杂记

!!! abstract

    笔者通常使用 MkDocs 来作为发布器（静态网站生成器），从而进行[个人笔记网站](https://minjoker.github.io/)制作。

    接下去的篇幅将会收集一些笔者使用 MkDocs 过程中遇到的值得注意的地方。

!!! info "笔者的工具版本"

    Material for MkDocs：9.4.6 (MkDocs：1.5.3)

### GitHub Actions&thinsp;与版本隐患

很多人会用 GitHub Actions 来自动化笔记网站的部署，但是很可能忽略了其中的版本隐患。我们首先来看看 `.github/workflows/xxx.yml` 中的这部分内容：

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - run: pip install mkdocs-material # (1)!
      - run: mkdocs gh-deploy --force
```

1. 这句命令通过 pip 下载了最新版本的 Material for MkDocs

这里存在的隐患在于，每次运行 GitHub Actions 部署网站的时候，使用的都是最新版本的 Material for MkDocs，但我们在本地通过 `mkdocs serve` 构建网站的时候所使用的却是本地的 Material for MkDocs，这就意味着如果本地版本没有更新到最新版本，那么本地构建和实际部署就可能出现差异。如果你发现自己的本地构建和实际部署出来的网站有出入，不妨考虑一下可能是版本差异问题导致的。

是否要把软件保持最新版本这个问题，见仁见智。而我们也有相应的两种解决方案：

- 在本地通过 `pip install --upgrade --force-reinstall mkdocs-material` 来把本地版本保持最新。
- 在 `.github/workflows/xxx.yaml` 中将相应代码修改为 `run: pip install mkdocs-material==9.4.6`，从而确保 GitHub Actions 用 9.4.6 版本来部署网站。

???+ note "提醒一下"

    Material for MkDocs 中已经包含了相应版本的 MkDocs，如果忘记了这件事的话，上文看起来可能会有些奇怪。

### 文件路径应该怎么写

MkDocs 在 [1.5.0](https://www.mkdocs.org/about/release-notes/#version-150-2023-07-26) 的版本中更新了对链接有效性的约束规则，一些不规范的路径写法会被报告出来，比如：

```
INFO - Doc file 'example.md' contains an absolute link '/foo/bar/', it was left as is. Did you mean 'foo/bar.md'?
INFO - Doc file 'example.md' contains an absolute link '/assets/images/1.jpg', it was left as is. Did you mean '../../assets/images/1.jpg'?
```

这两个例子之所以报错，按照 MkDocs 的说法，是因为这样的以 `/` 开头或者结尾的路径写法是 fragile 的。

- 对于以 `/` 结尾的路径，我觉得确实应该改为 `.md` 结尾，指定文件类型不是一件坏事。
- 对于以 `/` 开头的路径，我想我会继续这样写。
    - `/` 在这里代表根目录 `docs/`，我认为从根目录往下找文件，要比从当前文件所在目录往上找文件更加自然。<br />
      比如在插入一张图片的时候，纠结到底是 `../` 还是 `../../` 真的是件很麻烦的事情。

如果你不想看到这些 INFO 提示，可以在 `mkdocs.yml` 中添加以下内容（尽管 MkDocs 并不推荐这么做）：

```yaml
validation:
  absolute_links: ignore
```

### heti&thinsp;与中西文混排优化

> 原则上，汉字与西文字母、数字间使用不多于四分之一个汉字宽的字距或空白。
>
> <p style="text-align: right">————  [CLReq（中文排版需求）](https://www.w3.org/International/clreq/)</p>

笔者使用 [mkdocs-heti-plugin](https://github.com/TonyCrane/mkdocs-heti-plugin) 来优化中西文混排，在使用这个插件的过程中遇到了一些值得关注的情况：

1. 如何让 heti 忽略某部分文本？<br />
   可以通过对 `<span>`、`<p>`、`<div>` 等标签设置 `style="heti-skip"` 实现相应效果。

2. heti 无法渲染页面左右两侧的目录和索引（nav 和 table of contents）？<br />
   插件本身暂时无法解决该问题，可以手动在中西文混排处插入 `&thinsp;` 凑合用。

### 更美观的标题样式

Material for MkDocs 的标题字重设计得不够合理（对于中文排版而言）。可以通过设置 CSS 来优化标题字重：

=== "`mkdocs.yml`"

    ```yaml
    extra_css:
      - stylesheets/extra.css
    ```

=== "`extra.css`"

    ```css
    .md-typeset h1, .md-typeset h2 {
        font-weight: 600;
    }
    .md-typeset h3 {
        font-weight: 500;
    }
    ```

    source: [https://github.com/TonyCrane/lab-hypotensor/](https://github.com/TonyCrane/lab-hypotensor/)

---

在标题前面添加编号是一种很不错的优化方案。手动添加比较繁琐，而且会导致索引（table of contents）也出现编号，影响观感。可以通过设置 CSS 来实现自动编号：

=== "`mkdocs.yml`"

    ```yaml
    extra_css:
      - stylesheets/extra.css
    ```

=== "`extra.css`"

    ```css
    h1 {
        counter-reset: h2;
    }
    h2 {
        counter-reset: h3;
    }
    h3 {
        counter-reset: h4;
    }
    h4 {
        counter-reset: h5;
    }
    h5 {
        counter-reset: h6;
    }
    h2:before {
        counter-increment: h2;
        content: counter(h2);
        margin-right: 0.8rem;
    }
    h3:before {
        counter-increment: h3;
        content: counter(h2) "." counter(h3);
        margin-right: 0.8rem;
    }
    h4:before {
        counter-increment: h4;
        content: counter(h2) "." counter(h3) "." counter(h4);
        margin-right: 0.8rem;
    }
    h5:before {
        counter-increment: h5;
        content: counter(h2) "." counter(h3) "." counter(h4) "." counter(h5);
        margin-right: 0.8rem;
    }
    h6:before {
        counter-increment: h6;
        content: counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) "." counter(h6);
        margin-right: 0.8rem;
    }
    ```

    source: [https://github.com/TonyCrane/lab-hypotensor/](https://github.com/TonyCrane/lab-hypotensor/)

### 表格居中有那么难吗

<!-- <center> 标签注定要被时代抛弃，要想居中表格的话，直接写 HTML 肯定错不了。或许可以加一些 css 或什么别的（暂时不太懂）起到一个插件或主题的效果，也许可以偷懒一些？ -->

### KaTeX

<!-- KaTeX 经常出一些玄学问题，这个和我使用的 katex 渲染支持有关（yaml 里插进去的那几个网址），其他人用了不同的渲染支持，KaTeX 也会呈现一些细微的差异。 -->

## 参考资料

- [CommonMark](https://commonmark.org/)
- [计算机类实验文档降压宝典（编写规范）](https://hypotensor.tonycrane.cc/)
- [浙江大学系列朋辈辅学「实用技能拾遗」课程资料仓库](https://github.com/TonyCrane/PracticalSkillsTutorial)