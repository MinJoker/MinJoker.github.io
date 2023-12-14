# 课程笔记

!!! quote "引用其他人的笔记"

    此「课程笔记」作为对以下笔记的补充：

    <div class="heti-skip">[Isshiki修's Notebook/课程笔记/[大二秋冬]数字逻辑设计](https://note.isshikih.top/cour_note/D2QD_DigitalDesign/)</div>

## Digital Systems and Information

### 基本概念补充

两种逻辑系统：

- 组合逻辑（Combinational Logic）：任意时刻的输出仅取决于该时刻的输入
- 时序逻辑（Suquential Logic）：任意时刻的输出既取决于该时刻的输入，也取决于电路原来的状态
    - 同步（Synchronous）：State updated at discrete times
    - 异步（Asynchronous）：State updated at any time

数字系统与编码的概念是不一样的，仔细思考就可以发现两者的不同之处：

- 数字系统（Number System）：指数字的不同进制系统，如十进制、二进制、八进制、十六进制
    - 数字系统的前导 `0` 可以省略
- 编码（Codes）：较为灵活，只要求一一映射即为合法编码
    - 编码系统的前导 `0` 不能省略

### 常见编码补充

余三码的一个关键点在于其解决了 BCD 码的加减法进位问题，我们可以通过一个例子来明白余三码做了什么：

??? example "BCD 码的加法示例"

    ![](/assets/images/cs/digital_logic/1.jpg){width="60%"}

---

格雷码的一种简单粗暴的写法，是通过「镜像」的技巧来倍增已知的格雷编码。

具体来说，如果现在有 `0` - `3` 的格雷码，分别是 `00`，`01`，`11`，`10`，那么我们可以将其增添一位前导 `0`，然后镜面对称地书写出带有前导 `1` 的 `4` - `7` 的格雷码。

最终我们得到的 `0` - `7` 的格雷码是：`000`，`001`，`011`，`010`，`110`，`111`，`101`，`100`

## Combinational Logic Circuits

### 布尔代数补充公式

香农公式（Shannon formula）：

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

逻辑函数分解（Shannon Expansion）：

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

---

SOP 与 POS：

任何表达式都可以化成 SOP 和 POS 这样的标准形式。

- SOP（Standard Sum-of-Products）即把表达式写成 `OR of AND` 的形式；
- POS（Standard Product-of-Sums）即把表达式写成 `AND of OR` 的形式；

---

SOM 与 POM：

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

缓冲器（Buffer）：$F=X$

- buffer 可以作为放大器，提高电路的电压水平（回忆数字信号的宽进严出）
- buffer 可以提高电路运行的速度

---

三态门（3-State Buffer）

??? example "三态门解决多路输出互联问题（multiplexed output line）的一个例子"
    
    ![](/assets/images/cs/digital_logic/2.jpg){width="60%"}

---

传输门（Transmission Gate）

传输门可以视为一个开关，值得注意的是：

- 传输门与三态门并不相同，尽管两者在某种意义上都可以起到开关的作用
    - 关于两者具体有何区别，我暂时没有找到很合适的答案
- 传输门这个概念放在这里似乎并不合适，但是马德老师讲解到了，遂提一笔

![](/assets/images/cs/digital_logic/3.jpg)

---

异或门 同或门

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

译码器：

译码器（Decoder）能将信息从 $n$ 个输入转换为 $2^n$ 个或更少的唯一输出。具体是怎么实现的呢？译码器实际上是在枚举 $n$ 个输入的所有排列方式（共 $2^n$ 种）。更进一步的，这可以理解为是在枚举最小项（minterms）。比如 $3-to-8$ 译码器，当输入为某个特定组合 `101` 时，只有相应的表示 `101`（或者说 $\sum_m(5)$）这个组合的输出是 `1`。

如何构造一个 $n-to-2^n$ 译码器？我们用递归的思路来求解：

1. 设 $k=n$；
2. 如果 $k$ 为偶数，问题分解为设计两个 $\frac{k}{2}-to-2^{\frac{k}{2}}$ 译码器，并将它们用 $2^k$ 个与门连接起来；
3. 如果 $k$ 为奇数，问题分解为设计一个 $\frac{k-1}{2}-to-2^{\frac{k-1}{2}}$ 和一个 $\frac{k+1}{2}-to-2^{\frac{k+1}{2}}$ 译码器，并将它们用 $2^k$ 个与门连接起来；
4. 对每个译码器重复第二步，直到 $k=1$，这时候我们使用一个 $1-to-2$ 译码器；

???+ example "比如这个 $3-to-8$ 译码器"

    ![](/assets/images/cs/digital_logic/8.png){width="60%"}

前面提到译码器本质上完成了枚举最小项的工作，如果我们在译码器的输出后面接上或门，就可以实现 SOM 的逻辑表达。因为 SOM 可以表达任何组合逻辑，所以译码器就可以实现任何的组合逻辑。

译码器的应用有很多，比如设计加法器、7 段数码管等。

=== "加法器"

    ![](/assets/images/cs/digital_logic/9.png){width="60%"}

=== "用 7 段数码管显示 BCD 码"

    ![](/assets/images/cs/digital_logic/10.png){width="60%"}

---

编码器：

编码器（Encoder）与译码器是对称的，能将信息从 $2^n$ 个或更少的输入转换为 $n$ 个输出。但和译码器不同的是，普通编码器必须要求输入是 one-hot 的，即只允许存在一个输入为 `1`，否则无法判断得出唯一输出。此外，编码器的逻辑表达式和具体电路实现，通常都比译码器更为复杂。

!!! note "译码器和编码器"

    译码器和编码器在原理上是对称的，两者的区别在于交换了 $n$ 端和 $2^n$ 端。值得注意的是，$2^n$ 端每次只允许一个信号是活动的（一般来说是 `1`，其他信号则为 `0`），这是因为 $2^n$ 端的含义是 $n$ 端信号的某个排列方式（最小项），比如 $2^n$ 端的 `101` 与 $n$ 端的 `1` `0` `1` 是唯一对应的。

??? example "$10-to-4$ 编码器实现十进制转 BCD 码"

    ![](/assets/images/cs/digital_logic/11.png){width="60%"}
    
    ![](/assets/images/cs/digital_logic/12.png){width="60%"}

优先编码器（Priority Encoder）可以解决上述问题。在优先编码器中，如果多个输入处在活动状态（输入为 `1`），则优先级高的输入将优先，而优先级低的输入将成为不定项（Don't Cares）。

???+ example "优先编码器示例"

    ![](/assets/images/cs/digital_logic/13.png){width="80%"}

---

多路复用器：

多路复用器（Multiplexer，或称为数据选择器）可以通过 $n$ 个控制信号输入，对 $2^n$ 个或更少的数据信号输入做选择，并得到 $1$ 个选择结果输出。

MUX 和译码器一样，都可以表达任意组合逻辑。这是因为 MUX 的实现内部就存在一个译码器，我们只需要将 MUX 的控制端（也就是译码器）用作输入，将组合逻辑的真值表写入 MUX 的选项端进行选择，就可以表达任何组合逻辑。

通常，一个 $2^n-to-1$ MUX 的组成为：

- 一个 $n-to-2^n$ 译码器（MUX 利用了译码器每次只有一个输出为 `1` 的特性，从而实现选择功能）；
- $2^n \times 2$ AND-OR；

???+ example "比如这个 $4-to-1$ MUX"

    ![](/assets/images/cs/digital_logic/14.png){width="60%"}

---

当然，多路复用器不止可以选择单个数据信号，也可以选择一组数据信号（或称向量形式的数据信号），这组信号由 $m$ 个单个数据信号组成。这时候我们用同一个译码器来控制 $m$ 个 $2^n \times 2$ AND-OR。

???+ example "比如这个 $m-$wide $4-to-1$ MUX"

    ![](/assets/images/cs/digital_logic/15.png){width="80%"}

---

除了多路复用器，选择器还有其他的电路实现方法，比如三态门和传输门。

=== "Three-state logic in place of AND-OR"

    ![](/assets/images/cs/digital_logic/16.png){width="50%"}

=== "Distributing the decoding across the three-state drivers"

    ![](/assets/images/cs/digital_logic/17.png){width="50%"}

    这个电路设计相当于，先进行四选二，再进行二选一，从而实现四选一。

---

MUX 的电路实现有一种优化方案，就是通过把一部分的控制端的输入写到选项端作为常量来被选择，从而简化元件（降维）。我们通过一个例子来看看怎么实现：

???+ example "MUX 的降维优化"

    ![](/assets/images/cs/digital_logic/18.png){width="50%"}

    ![](/assets/images/cs/digital_logic/19.png){width="50%"}

    ![](/assets/images/cs/digital_logic/20.png){width="50%"}

    注：上图有误，应该是 $4-to-1$ MUX。

    我们可以通过组合逻辑表达式来更深刻地理解为什么可以这样做（以 $Y$ 举例）：

    $$
    \begin{aligned}
    Y & = \overline{A}\overline{B}C+\overline{A}B\overline{C}+A\overline{B}C+AB\overline{C} \cr
    & = \overline{A}\overline{B}\overline{C}\cdot 0+\overline{A}\overline{B}C\cdot 1+\overline{A}B\overline{C}\cdot 1+\overline{A}BC\cdot 0+A\overline{B}\overline{C}\cdot 0+A\overline{B}C\cdot 1+AB\overline{C}\cdot 1+ABC\cdot 0 \cr
    & = \overline{A}\overline{B}(\overline{C}\cdot 0+C\cdot 1)+\overline{A}B(\overline{C}\cdot 1+C\cdot 0)+A\overline{B}(\overline{C}\cdot 0+C\cdot 1)+AB(\overline{C}\cdot 1+C\cdot 0)
    \end{aligned}
    $$

!!! note "译码器与多路复用器"

    译码器和多路复用器的原理与实现都有相似之处。如果我们要实现任何组合逻辑，大部分情况下使用译码器会更好，因为它只需要把枚举出的最小项用一个或门连接即可，而 MUX 则需要用很多的与门来达到相同的效果。当然，在某些情况下 MUX 可以进行降维优化，有时候会比译码器效果更好。

    事实上，这样的比较不算公平，因为它们虽然很相似，但设计的目的是差别很大的。译码器用于译码（interpret a coded data），而 MUX 用于选择并传输数据（select and transmit data）。虽然 MUX 中经常用译码器来作为控制端，但 MUX 中的译码器和一个正常的译码器的用途相去甚远：MUX 中的译码器只用于控制，它用 $n$ 个输入信号来表示选择 $n$ 个待选项中的哪一项，译码器信号本身并不传递除了“选择”之外的任何意义；而在一个正常的译码器中，$n$ 个输入显然是有意义的，它们就是译码器所“译”的“码”。这就是两者的区别。

### 算术逻辑电路补充

超前进位加法器：

首先我们先把注意力放在一个全加器上。要把多个全加器连接起来形成能够计算更大数据的加法器，关键在于如何处理好全加器之间「进位」的问题。对于一个全加器而言，它向后一个全加器的进位（$C$，carry）有两个来源，一个是自身加法产生的进位，记为 $G$（generate），另一个是前一个全加器传递过来的进位，记为 $P$（propagate），我们有：

$$
C_{i+1} = A_i B_i + (A_i \oplus B_i) C_i = G_i + P_i \cdot C_i
$$

具体电路实现如下图：

<div style="text-align: center;">
<img src="/assets/images/cs/digital_logic/21.png" alt="全加器电路实现图" style="width: 25%;">
</div>

最自然的想法是把全加器直接链式连接，即直接把前一个全加器的进位连接到后一个全加器上，这样的做法叫做行波加法器（ripple-carry adder）。行波加法器最大的弊端在于，后一个全加器需要等待前一个全加器的计算完毕后，才能把进位传递过来，当处理较大数据的加法时，行波加法器的效率就太低了。

所以，有没有办法提前把进位传递下去，而不需要等待前面的全加器完全计算完毕呢？超前进位加法器（carry-lookahead adder, or CLA）就是用来解决这个问题的。那么超前进位加法器是怎么做到提前把进位传递下去的呢？让我们再把注意力转向公式推导：

$$
C_1 = G_0 + P_0 C_0
$$

$$
\begin{aligned}
C_2 & = G_1 + P_1 C_1 = G_1 + P_1 (G_0 + P_0 C_0) \cr
& = G_1 + P_1 G_0 + P_1 P_0 C_0
\end{aligned}
$$

$$
\cdots
$$

$$
C_4 = G_3 + P_3 G_2 + P_3 P_2 G_1 + P_3 P_2 P_1 G_0 + P_3 P_2 P_1 P_0 C_0
$$

我们发现，$C_4$ 不再依赖于 $C_3$，而是直接依赖于 $C_0$。我们只需要并行计算每个全加器的 $P$ 和 $G$，然后把 $C_0$ 与它们结合即可计算得出每个全加器向后传递的进位 $C_{i+1} = G_{0\sim i} + P_{0\sim i} C_0$。

具体电路实现如下图，我们发现所有全加器的 $P$ 和 $G$ 都是并行计算的，而对于每个进位的计算，只需要额外消耗 $C_0$ 经过一个与门和一个或门的时间：

<div style="text-align: center;">
<img src="/assets/images/cs/digital_logic/22.png" alt="超前进位加法器电路实现图" style="width: 60%;">
</div>

!!! quote "Source: <a href="https://en.wikipedia.org/wiki/Carry-lookahead_adder">https://en.wikipedia.org/wiki/Carry-lookahead_adder</a>"

但我们发现，这样的超前进位加法器虽然解决了进位延迟的问题，但是仍然无法大规模使用，问题的关键在于电路中的多输入与门和或门，如果我们要连接 $n$ 个全加器，那么就需要 $n+1$ 输入的与门和或门，这是不现实的。所以我们考虑把这一个 4-bit 超前进位加法器作为一个模块，并在此基础上组建更大的超前进位加法器。

$$
C_4 = G_{0\sim 3} + P_{0\sim 3} C_0
$$

$$
C_8 = G_{4\sim 7} + P_{4\sim 7} C_4
$$

$$
\cdots
$$

$$
\begin{aligned}
C_{16} & = G_{12\sim 15} + P_{12\sim 15} C_12 \cr
& = G_{12\sim 15} + P_{12\sim 15} G_{8\sim 11} + P_{12\sim 15} P_{8\sim 11} G_{4\sim 7} \cr
& + P_{12\sim 15} P_{8\sim 11} P_{4\sim 7} G_{0\sim 3} + P_{12\sim 15} P_{8\sim 11} P_{4\sim 7} P_{0\sim 3} C_0
\end{aligned}
$$

我们惊讶地发现，这个 $C_{16}$ 表达式的写法和之前推导超前进位加法器时 $C_4$ 的写法是完全一致的，只需要做一些下标变换即可。这就启发我们如何组建更大规模的超前进位加法器：我们把若干个小的超前进位加法器连接起来，就像我们当初把若干个全加器连接成超前进位加法器一样！

=== "4-bit CLA"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/23.png" alt="超前进位加法器电路实现图" style="width: 60%;">
    </div>

=== "16-bit CLA"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/24.png" alt="超前进位加法器电路实现图" style="width: 60%;">
    </div>

=== "64-bit CLA"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/25.png" alt="超前进位加法器电路实现图" style="width: 60%;">
    </div>

!!! quote "Source: <a href="https://en.wikipedia.org/wiki/Lookahead_carry_unit">https://en.wikipedia.org/wiki/Lookahead_carry_unit</a>"

---

加减法的溢出检查：

常见的加减法溢出情况是，同号相加和异号相减。下面给出两种最简单的溢出检查（overflow detection）：

- 检查输出的符号位和加数或减数（top operand）的符号位是否一致
- 对于 $n$ 位加减法，溢出可以表示为 $V=C_n\oplus C_{n-1}$，$V$ 为`1`表示溢出

<div style="text-align: center;">
<img src="/assets/images/cs/digital_logic/26.png" alt="溢出检查" style="width: 80%;">
</div>

---

其他算数函数：

- 自增（incrementing）与自减（decrementing）
    - 自增与自减运算可以通过对加减法器进行收缩（contraction）得到
    - 具体而言就是把加减法器的其中一个输入设为常量（自增自减的步长）
- 乘法与除法
    - 与 $2^n$ 的乘数是最简单的，只需要通过移位就可以实现
    - 任意常数的乘除的一种实现思路是，拆分成与若干个 $2^n$ 的乘除
- 零扩展（zero fill）与符号位扩展（extension）

## Sequential Circuits

### 时序逻辑理解

以下是 Wikipedia 对于时序逻辑、同步与异步的定义：

> Sequential logic is a type of logic circuit whose output depends on the present value of its input signals and on the sequence of past inputs.<br />
> That is, sequential logic has state (memory) while combinational logic does not.
>
> Digital sequential logic circuits are divided into synchronous and asynchronous types.<br />
> In synchronous sequential circuits, the state of the device changes only at discrete times in response to a clock signal.<br />
> In asynchronous circuits the state of the device can change at any time in response to changing inputs.

很自然地，异步时序逻辑是 input-driven 的，每当输入发生变化，就要进行状态的更新。但是，异步时序逻辑的弊端在于，当输入包含多个变量而它们的抵达有先后区分的时候，电路可能会偏离预期而进入错误的状态。

同步时序逻辑可以解决这个问题。我们使用一个时钟信号来同步（synchronize, or clock）所有输入变量产生作用的时间，即使输入变量先后抵达，它们也得等待时钟信号来临时才能发生作用，从而更新电路的状态。

工程应用中，同步时序逻辑往往更为普遍，因为其设计简单而且鲁棒性强。异步时序逻辑的设计通常更为复杂，多用于需要更高更新速度的场合（不像同步时序逻辑那样受限于时钟信号的频率）。

=== "时序逻辑"

    <div style="text-align: left;">
    <img src="/assets/images/cs/digital_logic/27.png" alt="时序逻辑电路抽象图示" style="width: 50%;">
    </div>

=== "同步时序逻辑"

    <div style="text-align: left;">
    <img src="/assets/images/cs/digital_logic/28.png" alt="同步时序逻辑电路抽象图示" style="width: 50%;">
    </div>

=== "异步时序逻辑"

    <div style="text-align: left;">
    <img src="/assets/images/cs/digital_logic/29.png" alt="异步时序逻辑电路抽象图示" style="width: 50%;">
    </div>

### 从锁存器到触发器

=== "Buffer as a Storage"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/30.png" alt="buffer 用作存储元件图示" style="width: 50%;">
    </div>

=== "$SR$ Latch with NOR Gates"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/31.png" alt="SR Latch 图示" style="width: 60%;">
    </div>

=== "$\overline{S} \overline{R}$ Latch with NAND Gates"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/32.png" alt="S_R_ Latch 图示" style="width: 60%;">
    </div>

=== "$SR$ Latch with Control Input"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/33.png" alt="Clocked SR Latch 图示" style="width: 60%;">
    </div>

=== "$D$ Latch"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/34.png" alt="D Latch 图示" style="width: 60%;">
    </div>

某种意义上，延迟可以用于实现信息存储。对于一个存在 $t_G$ 延迟的 buffer 而言，我们可以认为信息在 $t_G$ 这段时间内是不变的，即被短暂地存储了（如图 a）。进一步地，把 buffer 的输出和输入相连，可以实现信息的长久存储（如图 bc）。但是，对于这个信息存储单元而言，修改存储的信息内容是不方便的。

我们把组成 buffer 的非门（如图 d）替换成或非门或者与非门，就可以方便地修改存储的信息内容，这就是 $SR$ Latch。稍加思考不难发现，这样的一个 $SR$ Latch 其实是异步的，因而输入 S 和 R 的先后抵达可能导致电路偏离预期而进入错误的状态。我们可以通过增加一个使能控制端 C 来确保 S 和 R 都到位时再使能，这就是 $SR$ Latch with Control Input（注意这并不是严格意义上的同步，当 C 置`1`时其仍然是异步的）。但是，这个 Latch 仍然存在一种未定义状态，即 C、S 和 R 均置`1`。

对于 $SR$ Latch with Control Input 而言，用 S 和 R 均置`0`来表示保持态是多余的（因为 C 置`0`即可表示保持态），而且上述的未定义状态也很麻烦。所以，我们把 S 和 R 强制设定为相反的两个输入，用 $D$ 和 $\overline{D}$ 来代替 $S$ 和 $R$，这就是 $D$ Latch。

总结一下，我们从 buffer 出发，逐步解决信息存储和修改的问题，最终设计出了 $D$ Latch。然而，$D$ Latch 也并不是完美的。事实上，$D$ Latch 存在的问题，也是所有的 Latch 都无法规避的问题，即透明（transparent, which means its input value can be seen from the outputs while the control input is `1`）。

透明为什么会产生问题呢？在时序逻辑电路中，总是存在从信息存储单元到组合逻辑电路的反馈路径，因此对于一个 Latch 而言，其输入总是部分来自自身或其他 Latch 的输出。由于 Latch 是透明的，在同一个时钟周期内，输出可能会反过来修改输入，从而导致 Latch 的状态更新发生了不止一次。这个问题是很严重的，对于透明的 Latch 而言，一个时钟周期内状态更新的次数，取决于时钟周期的长短和反馈路径的组合电路延迟，导致我们无法确定这个时钟周期结束后 Latch 究竟处在什么状态。

---

=== "$SR$ Master-Slave Flip-Flop"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/35.png" alt="SR Master-Slave Flip-Flop 图示" style="width: 50%;">
    </div>

=== "Negative-Edge-Triggered $D$ Flip-Flop"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/36.png" alt="Negative-Edge-Triggered D Flip-Flop 图示" style="width: 50%;">
    </div>

=== "Positive-Edge-Triggered $D$ Flip-Flop"

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/37.png" alt="Positive-Edge-Triggered D Flip-Flop 图示" style="width: 50%;">
    </div>

上文提到，Latch 是透明的，所以无法确定一个时钟周期内状态更新的次数。我们希望设计一个非透明的信息存储单元，确保一个时钟周期内仅发生一次状态更新。通常的解决方法是，把两个 Latch 串联起来，当时钟信号为`1`时，主 Latch 写入，从 Latch 只读；当时钟信号为`0`时，主 Latch 只读，从 Latch 写入。因此，在一个时钟周期内，前一半时间更新主 Latch，后一半时间更新从 Latch，从而对于整体而言，仅发生一次状态更新，这就是主从触发器（Master-Slave Flip-Flop）。

???+ note "教材中的传统观点"

    *Logic and Computer Design Fundamentals* 教材中认为，主从触发器有两种实现方法：pulse-triggered 和 edge-triggered。
    
    $SR$ 触发器就属于前者。但是，$SR$ 触发器存在一个很大的问题，即 1s-catching。当时钟信号为`1`时，S 或 R 短暂的置`1`均会被 Master 捕捉到，并在时钟信号为`0`时传递给从 Slave。由于时序逻辑电路总是存在反馈路径，所以同一个时钟周期内 Master 的状态可能会更新多次，虽然对于触发器整体而言仍然仅有一次状态更新（因为 Slave 仅更新一次），但是这种情况下我们无法确保 Master 传递给 Slave 的信息和我们起初写入 Master 的信息是否一致。

    为了更好地理解 1s-catching，让我们回忆一下时序逻辑延迟分析中提到的 setup-time（如下图）。pulse-triggered 触发器之所以需要比 edge-triggered 触发器更长的 setup-time，就是为了确保时钟信号为`1`时触发器输入端总是稳定的，从而确保不会发生 1s-catching。

    更进一步地，1s-catching 可以被认为是亚稳态（metastability）的一种表现。当我们没能在 setup-time 期间维持数据稳定，就会导致触发器陷入亚稳态。通常的解决思路是串联两层甚至更多层的触发器。

    <div style="text-align: center;">
    <img src="/assets/images/cs/digital_logic/38.png" alt="触发器延迟分析图示" style="width: 70%;">
    </div>

    ---

    读到这里，或许你也和笔者一样，产生了如下的疑惑：<br />
    为什么我感觉 pulse-triggered 和 1s-catching 的概念更像是 Latch 的特性？<br />
    如果我把注意力集中在时钟信号边沿，难道 pulse-triggered 不也具有 edge-triggered 特性吗？<br />
    难道 pulse-triggered flip-flop 不是伪命题吗？

---

现代观点认为，触发器并不存在所谓的 pulse-triggered（or level-triggered）类型。正如马德老师在课堂上一再强调的，真正存在实用价值的是边沿触发式（edge-triggered）触发器，比如广泛使用的 $D$ 触发器。严格来说，在数字逻辑设计这门课中，我们所说的 $D$ 触发器其实是边沿触发式 $D$ 主从触发器（Master-Slave Edge-Triggered $D$ Flip-Flop）。

以下是 Wikipedia 对于触发器的描述：

> The term flip-flop has historically referred generically to both level-triggered (asynchronous, transparent, or opaque) and edge-triggered (synchronous, or clocked) circuits that store a single bit of data using gates.
>
> Modern authors reserve the term flip-flop exclusively for edge-triggered storage elements and latches for level-triggered ones.

总结一下：

- Latch 是异步的、透明的、脉冲触发的，在整个时钟信号为`1`的过程中均可写入信息并多次更新状态；
- Flip-Flop 是同步的、非透明的、边沿触发的，一个时钟周期内仅在时钟信号的上升沿或下降沿进行一次状态的更新；

除此以外，还有很多种其他的触发器设计，比如数逻课堂上提到的 $T$ Flip-Flop 和 $JK$ Flip-Flop，更多相关知识可以参考 [https://en.wikipedia.org/wiki/Flip-flop_(electronics)](https://en.wikipedia.org/wiki/Flip-flop_(electronics))。

### 时序逻辑电路设计

相关的题目并不难，套路性很强。建议考前找一些经典时序逻辑电路设计来复习。

- 马德老师的数逻课件上的 Recognize 1101；
- 数逻第四章作业中的几个经典的时序逻辑电路设计题；