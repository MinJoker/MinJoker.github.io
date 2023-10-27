# 课程笔记

!!! quote "引用其他人的笔记"

    此「课程笔记」作为对以下笔记的补充：

    <div class="heti-skip">[Isshiki修's Notebook/课程笔记/[大二秋冬]数字逻辑设计](https://note.isshikih.top/cour_note/D2QD_DigitalDesign/)</div>

## Digital Systems and Information

### 两种逻辑系统

- 组合逻辑（Combinational Logic）：任意时刻的输出仅取决于该时刻的输入
- 时序逻辑（Suquential Logic）：任意时刻的输出既取决于该时刻的输入，也取决于电路原来的状态
    - 同步（Synchronous）：State updated at discrete times
    - 异步（Asynchronous）：State updated at any time

### Number System 与 Codes

数字系统与编码的概念是不一样的，仔细思考就可以发现两者的不同之处：

- 数字系统（Number System）：指数字的不同进制系统，如十进制、二进制、八进制、十六进制
    - 数字系统的前导 `0` 可以省略
- 编码（Codes）：较为灵活，只要求一一映射即为合法编码
    - 编码系统的前导 `0` 不能省略

### BCD 码与余三码

余三码的一个关键点在于其解决了 BCD 码的加减法进位问题，我们可以通过一个例子来明白余三码做了什么：

??? example "BCD 码的加法示例"

    ![](/assets/images/cs/digital_logic/1.jpg){width="60%"}

### 格雷码

格雷码的一种简单粗暴的写法，是通过「镜像」的技巧来倍增已知的格雷编码。

具体来说，如果现在有 `0` - `3` 的格雷码，分别是 `00`，`01`，`11`，`10`，那么我们可以将其增添一位前导 `0`，然后镜面对称地书写出带有前导 `1` 的 `4` - `7` 的格雷码。

最终我们得到的 `0` - `7` 的格雷码是：`000`，`001`，`011`，`010`，`110`，`111`，`101`，`100`

## Combinational Logic Circuits

### 布尔代数补充公式

- 香农公式（Shannon formula）
- 逻辑函数分解（Shannon Expansion）

#### 香农公式

基于 $X \overline{X} = 0$ 和 $X X = 1$ ，我们可以得到以下公式：

$$
x f( x, \overline{x}, y,... ) = x f( 1, 0, y,... )
$$

$$
\overline{x} f( x, \overline{x}, y,... ) = \overline{x} f( 0, 1, y,... )
$$

基于 $X + \overline{X}Y = X + Y$ 和 $X + XY = X$ ，我们可以得到以下公式（或者通过上面的公式对偶得到）：

$$
x + f( x, \overline{x}, y,... ) = x + f( 0, 1, y,... )
$$

$$
\overline{x} + f(  x, \overline{x}, y,... ) = \overline{x} + f( 1, 0, y,... )
$$

---

#### 逻辑函数分解

$$
\begin{aligned}
F(x,\overline{x},y,...) & = xf_1(x,\overline{x},y,...) + \overline{x}f_2(x,\overline{x},y,...) \cr
& = xf_1(1,0,y,...) + \overline{x}f_2(0,1,y,...)
\end{aligned}
$$

这种分解思路可以将任何同时含有 $x$ 和 $\overline{x}$ 的函数进行分解，并转化为可以应用香农公式的形式，从而化简函数。我们可以通过给不含 $x$ 或 $\overline{x}$ 的项“与”上 $(x+\overline{x})$ 来实现该转化。

同样的，该公式也有对偶形式，这里不再赘述。

??? note "一个有趣的推论"

    $$
    \begin{aligned}
    f(x,y,z) & = xyzf(1,1,1)+xy\overline{z}f(1,1,0) \cr
    & + x\overline{y}zf(1,0,1)+x\overline{y}\overline{z}f(1,0,0) \cr
    & + \overline{x}yzf(0,1,1)+\overline{x}y\overline{z}f(0,1,0) \cr
    & + \overline{x}\overline{y}zf(0,0,1)+\overline{x}\overline{y}\overline{z}f(0,0,0)
    \end{aligned}
    $$

### 标准形式与规范形式

- 标准形式提供了一种布尔表达式化简的方向（但其形式并不唯一确定，即一个表达式可以有多个标准形式）
- 规范形式提供了一种唯一确定的表达形式（这对于把复杂的逻辑运算交由机器去完成是很重要的）

#### SOP 与 POS

任何表达式都可以化成 SOP 和 POS 这样的标准形式。

- SOP（Standard Sum-of-Products）即把表达式写成 `OR of AND` 的形式；
- POS（Standard Product-of-Sums）即把表达式写成 `AND of OR` 的形式；

#### SOM 与 POM

SOM（Sum of Minterms）与 POM（Product of Maxterms）蕴含着一种精巧的对称性，具体体现有：

- SOM 与 POM 的自然推导过程中，一个关键点就在于我们对 `1` 和 `0` 谁是主体的理解，两者的推导过程是完全对称的
- SOM 与 POM 还存在一种取反的对称性，比如假设 $F(x,y,z)=\sum_m(1,3,5,7)$ ，则有 $\overline{F}(x,y,z)=\sum_m(0,2,4,6) = \prod_M(1,3,5,7)$，以及 $F(x,y,z)=\prod_M(0,2,4,6)$

任何表达式都可以化成 SOM 和 POM 这样的规范形式，以由 SOP 得到 SOM 为例：

$$
\begin{aligned}
f & = x + \overline{x}\overline{y} \cr
& = x(y+\overline{y}) + \overline{x}\overline{y} \cr
& = xy + x\overline{y} + \overline{x}\overline{y}
\end{aligned}
$$

### 主蕴含项选择规则

主蕴含项选择规则（Prime Implicant Selection Rule）是一种卡诺图优化步骤，其主要思路是尽可能减少蕴含项的重合（overlap）：

1. 找到所有主蕴含项；
2. 选中所有「基本主蕴含项」；
3. 选择尽量少的非基本主蕴含项来覆盖所有最小项（或最大项）；

???+ warning

    请注意，基本主蕴含项也是主蕴含项，需要满足「极大」这个特征。

    我们在做题的时候，往往不会按部就班地先找出所有主蕴含项，再筛选出基本主蕴含项。我们往往直接开始框选基本主蕴含项和非基本主蕴含项。有时候容易犯一些小错误，比如误会 overlap 的内涵，从而框选出了不够极大的蕴含项：

    ![](/assets/images/cs/digital_logic/7.jpg)

### 经典组合电路补充

- 缓冲器（Buffer）
- 三态门（3-State Buffer）
- 传输门（Transmission Gate）
- 异或门 同或门

#### 缓冲器

Buffer：$F=X$

- buffer 可以作为放大器，提高电路的电压水平（回忆数字信号的宽进严出）
- buffer 可以提高电路运行的速度

#### 三态门

??? example "三态门解决多路输出互联问题（multiplexed output line）的一个例子"
    
    ![](/assets/images/cs/digital_logic/2.jpg){width="60%"}

#### 传输门

传输门可以视为一个开关，值得注意的是：

- 传输门与三态门并不相同，尽管两者在某种意义上都可以起到开关的作用
    - 关于两者具体有何区别，我暂时没有找到很合适的答案
- 传输门这个概念放在这里似乎并不合适，但是马德老师讲解到了，遂提一笔

![](/assets/images/cs/digital_logic/3.jpg)

#### 异或门 同或门

- 异或和同或可以用来实现奇校验和偶校验。
- 而且观察其卡诺图的形状（棋盘形），可以发现，它们是天然优化的，即不可优化的。

### 逻辑合成

逻辑合成（Logic Synthesis）是所设计数字电路的高抽象级表达，经过化简、优化后，转换到逻辑门级别的电路连线网表（netlist）的过程。

逻辑合成是电子设计自动化（EDA）中电路设计的一个步骤。

<center>

![](/assets/images/cs/digital_logic/6.jpg){width="50%"}

</center>

通常通过硬件描述语言（HDL）来实现电路的高级抽象。以 Verilog 为例，逻辑合成的过程可以理解为 behavioral description 转化为 structural description 的过程。

??? example "Verilog: Structural & Behavioral Description"

    Structural Description:

    ![](/assets/images/cs/digital_logic/4.jpg){width="80%"}

    Behavioral Description:

    ![](/assets/images/cs/digital_logic/5.jpg){width="80%"}

## Combinational Logic Design

### 基本功能块补充

#### 译码器

译码器（Decoder）能将信息从 $n$ 个输入转换为 $2^n$ 个或更少的唯一输出。具体是怎么实现的呢？译码器实际上是在枚举 $n$ 个输入的所有排列方式（共 $2^n$ 种）。更进一步的，这可以理解为是在枚举最小项（minterms）。比如 $3-to-8$ 译码器，当输入为某个特定组合 `101` 时，只有相应的表示 `101`（或者说 $\sum_m(5)$）这个组合的输出是 `1`。

如何构造一个 $n-to-2^n$ 译码器？我们用递归的思路来求解：

1. 设 $k=n$；
2. 如果 $k$ 为偶数，问题分解为设计两个 $\frac{k}{2}-to-2^{\frac{k}{2}}$ 译码器，并将它们用 $2^k$ 个与门连接起来；
3. 如果 $k$ 为奇数，问题分解为设计一个 $\frac{k-1}{2}-to-2^{\frac{k-1}{2}}$ 和一个 $\frac{k+1}{2}-to-2^{\frac{k+1}{2}}$ 译码器，并将它们用 $2^k$ 个与门连接起来；
4. 对每个译码器重复第二步，直到 $k=1$，这时候我们使用一个 $1-to-2$ 译码器；

???+ example "比如这个 $3-to-8$ 译码器"

    ![](/assets/images/cs/digital_logics/8.png)

前面提到译码器本质上完成了枚举最小项的工作，如果我们在译码器的输出后面接上或门，就可以实现 SOM 的逻辑表达。因为 SOM 可以表达任何逻辑函数，所以译码器就可以实现任何的逻辑函数。

译码器的应用有很多，比如设计加法器、7 段数码管等。

???+ example "加法器"

    ![]()

???+ example "用 7 段数码管显示 BCD 码"

    ![]()

#### 编码器

编码器（Encoder）是与译码器对称地，能将信息从 $2^n$ 个或更少的输入转换为 $n$ 个输出。但和译码器不同的是，普通编码器必须要求输入是 one-hot 的，即只允许存在一个输入为 `1`，否则无法判断得出唯一输出。

???+ example "$10-to-4$ 编码器实现十进制转 BCD 码"

    ![]()
    
    ![]()

优先编码器（Priority Encoder）可以解决上述问题。在优先编码器中，如果多个输入处在活动状态（输入为 `1`），则优先级高的输入将优先，而优先级低的输入将成为不定项（Don't Cares）。

???+ example ""

    ![]()

编码器的逻辑表达式和具体电路实现，通常都比译码器更为复杂。

#### 多路复用器

多路复用器（Multiplexer，或称为数据选择器）可以通过 $n$ 个控制信号输入，对 $2^n$ 个或更少的数据信号输入做选择，并得到 $1$ 个选择结果输出。

通常，一个 $2^n-to-1$ MUX 的组成为：

- 一个 $n-to-2^n$ 译码器；
- $2^n \times 2$ AND-OR；

???+ example "比如这个 $4-to-1$ MUX"

    ![]()

当然，多路复用器不止可以选择单个数据信号，也可以选择一组数据信号（或称向量形式的数据信号），这组信号由 $m$ 个单个数据信号组成。这时候我们需要使用 $2^n \times m$ AND-OR 来实现。

???+ example "比如这个 $4-to-1$ MUX"

    ![]()

除了多路复用器，选择器还有其他的电路实现方法，比如三态门和传输门。

=== "Three-state logic in place of AND-OR"

    ![]()

=== "Distributing the decoding across the three-state drivers"

    ![]()

---

下面让我们来看看多路复用器的具体实现方法：

马德老师扬言这里真的有两种方法，但是他这节课只讲了第一种（以格雷码转二进制码为例），并打趣称要把另一种留给下节课，以免下节课没东西讲。故先不更新这块内容。