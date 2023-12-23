# 课程笔记

!!! quote "引用其他人的笔记"

    此「课程笔记」作为对以下笔记的补充：

    <div class="heti-skip">[Isshiki修's Notebook/课程笔记/[大一春夏]概率论与数理统计](https://note.isshikih.top/cour_note/D1CX_ProbabilityAndStatistics/)</div>

## 概率论的基本概念

### 全概率公式与贝叶斯公式

- 全概率公式是「找结果」，而贝叶斯公式是「找原因」
- 全概率公式解决问题的关键在于如何找到一个合适的「划分」
- 贝叶斯公式中有一些概念值得注意：
    - 先验概率：$P(B)$
    - 后验概率：$P(B|A)$
    - 似然概率：$P(A|B)$

??? note "全概率公式与贝叶斯公式"

    $$
    P(A) = \sum_{j=1}^n{P(B_j) P(A|B_j)}
    $$

    $$
    P(B_i|A) = \frac{P(AB_i)}{P(A)} = \frac{P(B_i) P(A|B_i)}{\sum_{j=1}^n{P(B_j) P(A|B_j)}}
    $$

## 随机变量及其概率分布

### 随机变量补充

- 离散型和连续型是常见的两类随机变量，但随机变量并不只有这两种。
- 书写离散型随机变量的概率分布律时千万不要漏掉随机变量的可能取值。例如：$k=1,2,...$

### 分布函数补充

- 分布函数通常写成大写字母，例如 $F(x)$
- 分布函数的定义域为全体实数
- 分布函数的分段写法，按照「左闭右开」的区间进行分段是一个较好的习惯（主要针对离散型随机变量的分布函数）
- 支撑（support）指的是使概率分布为正的密度的随机变量的取值的集合，定义为 $ \lbrace x: f(x)>0\rbrace $

### 概率密度函数理解

有一个常见的误区，就是错把连续型随机变量的「概率密度函数的函数值」和连续型随机变量的「概率」直接划等号。事实上，概率密度函数的函数值代表的是概率分布函数的变化率（所以概率密度函数的函数值大于 $1$ 也是很合理的）；而连续型随机变量的概率，只有某段区间的概率是有意义的，而某个点的概率恒为 $0$。

那么，概率密度函数和概率的联系到底是什么呢？结论是，某段区间上「概率密度函数的面积（积分）」等于随机变量落在这段区间上的概率。

关于概率密度函数，我们有更加直观的解释。概率密度函数的函数值较大的地方，代表这个点附近的概率「密度」较大，也就是说随机变量落在这个点附近的单位长度区间上的概率较大。用数学公式表示就是 $f(x) \Delta x = P = \Delta F(x)$。

<div style="text-align: center;">
<img src="/assets/images/math/probability/1.png" alt="概率密度函数图示" style="width: 50%;">
</div>

### 指数分布的无记忆性

假设 $t_0>0$，$t>0$，

$$
\begin{aligned}
P(X>t_0+t \;\; | \;\; X>t_0 ) & = \frac{P(X>t_0+t)}{P(X>t_0)} \cr
& = \frac{1-F(t_0+t)}{1-F(t_0)} \cr
& = e^{-\lambda t} = P(X>t)
\end{aligned}
$$

$$
\begin{aligned}
P(X\lt t_0+t \;\; | \;\; X>t_0) & = \frac{P(t_0\lt X\lt t_0+t)}{P(X>t_0)} \cr
& = \frac{F(t_0+t)-F(t_0)}{1-F(t_0)} \cr
& = 1-e^{-\lambda t} = P(X<t)
\end{aligned}
$$

??? example "无记忆性的一个例子"

    假设设备无故障运行的时间 $T$ 服从指数分布。已知设备无故障运行了10个小时，求该设备再无故障至少运行8个小时的概率。

    $$
    P\lbrace T\geq 18 \; | \; T>10 \rbrace = \frac{P\lbrace T>18\rbrace }{P\lbrace T>10\rbrace} = e^{-8\lambda} = P\lbrace T>8\rbrace
    $$

    注意到，这一条件概率与无条件下无故障运行8小时的概率没有区别。

### 正态分布标准化

当 $X\sim N(\mu,\sigma^2)$ 时：

$$
P(X\leq b) = \int_{-\infty}^b \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu) ^2}{2\sigma ^2}} \mathrm{d}x
$$

做变换：$\frac{x-\mu}{\sigma} = t$

$$
\begin{aligned}
P(X\leq b) & = \int_{-\infty}^{\frac{b-\mu}{\sigma}} \frac{1}{\sqrt{2\pi}} e^{-\frac{t ^2}{2}} \mathrm{d}t \cr
& = \Phi(\frac{b-\mu}{\sigma})
\end{aligned}
$$

换言之，当 $X\sim N(\mu,\sigma^2)$ 时，$\frac{X-\mu}{\sigma}\sim N(0,1)$

!!! note "标准正态分布表"

    [https://en.wikipedia.org/wiki/Standard_normal_table#Cumulative_(less_than_Z)](https://en.wikipedia.org/wiki/Standard_normal_table#Cumulative_(less_than_Z))

### 随机变量的函数分布

一般地，若已知 $X$ 的概率分布，$Y=g(X)$，求 $Y$ 的概率分布的思路为：先给出 $Y$ 的可能取值，再利用「等价事件」求解 $Y$ 的概率分布函数或概率密度函数。

- 对于离散型随机变量，比较简单，在此不赘述；
- 对于连续型随机变量，可以按照以下步骤求解：
    1. 写出 $Y$ 的概率分布函数 $F_Y(y)=P(Y \leq y)$；
    2. 找出 $(Y \leq y)$ 的等价事件 $(X \in D)$，得到 $F_Y(y)=P(X \in D)$，解出 $Y$ 的概率分布函数；
    3. 进而求解 $Y$ 的概率密度函数 $f_Y(y) = \frac{\mathrm{d}}{\mathrm{d}y} F_Y(y)$；<br />
       特别地，若解出概率分布函数为 $F_Y(y) = F_X(h(y))$，则概率密度函数也可写成 $f_Y(y) = \frac{\mathrm{d}}{\mathrm{d}(h(y))} F_X(h(y)) \cdot \frac{\mathrm{d}(h(y))}{\mathrm{d}y} = f_X(h(y)) \cdot h^{\prime}(y)$；

## 多元随机变量及其分布

### 二维离散型随机变量补充

对于任何分布律都有两条性质（对联合、边际、条件分布律都成立）：

- $p_{ij} \geq 0, \; i,j=1,2,\dots$
- $\sum_{i=1}^{\infty} \sum_{j=1}^{\infty} P_{ij} = 1$

### 二维随机变量的分布函数补充

- 联合分布函数 $F(x,y) = P(X \leq x, Y \leq y)$ 是二元函数
- 边际分布函数 $F_X(x) = P(X \leq x) = lim_{y \rarr +\infty}F(x,y)$ 是关于 $x$ 的一元函数
- 条件分布函数 $F_{X \mid Y}(x \mid y) = P(X \leq x \mid Y=y)$ 是关于 $x$ 的一元函数
    - $\lim_{\epsilon \rarr 0^+}P(X\leq x\mid y<Y\leq y+\epsilon)$ 也记为 $P(X \leq x \mid Y=y)$，请特别注意这个记号所带来的两种理解

联合分布、边际分布、条件分布满足如下关系：

<div style="text-align: left;">
<img src="/assets/images/math/probability/2.png" alt="三个分布的关系图示" style="width: 40%;">
</div>

### 二维连续型随机变量补充

- 条件密度函数的重要性质：
    - $f(x,y) = f_{X|Y}(x|y)f_Y(y) = f_{Y|X}(y|x)f_X(x)$
    - 直观意义：<br />
      $f_{X|Y}(x \mid y) \Delta x = \frac{f(x,y)\Delta x \Delta y}{f_Y(y)\Delta y} \approx \frac{P(x<X\leq x+\Delta x, \; y<Y\leq y+\Delta y)}{P(y<Y\leq y+\Delta y)} = P(x<X\leq x+\Delta x \mid y<Y\leq y+\Delta y)$
- 二维均匀分布的条件分布为一维均匀分布
- 二维正态分布的边际分布和条件分布为一维正态分布（不依赖于 $\rho$）
    - 二维正态分布的记号：$(X,Y) \sim N({\mu}_1, \; {\mu_2}; \; {\sigma}_1^2, \; {\sigma}_2^2; \; \rho)$

### 随机变量的独立性补充

- 若 $(X,Y)$ 是离散型随机变量，则 $X,Y$ 相互独立等价于 $p_{ij} = p_{i\cdot}p_{\cdot j}$ 对一切 $i,j$ 都成立
- 若 $(X,Y)$ 是连续型随机变量，则 $X,Y$ 相互独立等价于 $f(x,y) = f_X(x)f_Y(y)$ 总是成立，平面上“面积”为零的集合除外（可以在不连续点上不相等）
    - 此外，$X,Y$ 相互独立也等价于存在一元函数 $m,n$，使得 $f(x,y)=m(x)n(y)\;,\;|x|<+\infty,|y|<-\infty$（注意：联合密度函数的函数式和定义域均须可分）
    - 二维正态分布是独立的，等价于 $\rho = 0$
- $n$ 维随机变量独立性相关定理：
    - 设 $(X_1,X_2,\cdots ,X_m)$ 与 $(Y_1,Y_2,\cdots ,Y_n)$ 相互独立，则 $X_i(i=1,2,\cdots ,m)$ 与 $Y_j(j=1,2,\cdots ,n)$ 相互独立
    - 设 $(X_1,X_2,\cdots ,X_m)$ 与 $(Y_1,Y_2,\cdots ,Y_n)$ 相互独立，若 $h(x_1,x_2,\cdots ,x_m)$ 与 $g(y_1,y_2,\cdots ,y_n)$ 是连续函数，则 $h(X_1,X_2,\cdots ,X_m)$ 与 $g(Y_1,Y_2,\cdots ,Y_n)$ 相互独立

### 二维随机变量的函数分布

- $Z=X+Y$ 的分布（这里讨论连续型，离散型只需把积分符号换成求和符号即可）
    - $f_Z(z)=\int_{-\infty}^{+\infty}f(z-y,y)\mathrm{d}y=\int_{-\infty} ^ {+\infty}f(x,z-x)\mathrm{d}x$（$x$，$y$是对称的）<!-- pay attention to the second "^", "...^..." is wrong, but "... ^ ..." is right, the two spaces count! -->
    - 当 $X$ 和 $Y$ 相互独立时，$Z$ 的密度函数公式也称为卷积公式：$f_X*f_Y=\int_{-\infty}^{+\infty}f_X(z-y)f_Y(y)\mathrm{d}y=\int_{-\infty} ^ {+\infty}f_X(x)f_Y(z-x)\mathrm{d}x$
- $M=max(X,Y)$ 与 $N=min(X,Y)$ 的分布（基于 $X$ 和 $Y$ 相互独立）
    - $F_{max}(z)=F_X(z)F_Y(z)$
    - $F_{min}(z)=1-(1-F_X(z))(1-F_Y(z))$
    - $n$ 个相互独立的随机变量同理

### 常见分布的卷积

分布的卷积问题，也即是分布的独立可加性问题。

---

1.二项分布的可加性：设 $X\sim B(n,p)$，$Y\sim B(m,p)$，$0<p<1$，$m$，$n$ 均为正整数，若 $X$ 与 $Y$ 独立，则

$$
X+Y\sim B(m+n,p)
$$

二项分布的可加性有助于理解二项分布与两点分布的可加性。

---

2.泊松分布的可加性：设 $X\sim P({\lambda}_1)$，$Y\sim P({\lambda}_2)$，${\lambda}_i>0$，$i=1,2$，若 $X$ 与 $Y$ 独立，则

$$
X+Y\sim P({\lambda}_1+{\lambda}_2)
$$

---

3.正态分布的可加性：设 $X\sim N({\mu}_1,{\sigma}_1^2)$，$Y\sim N({\mu}_2,{\sigma}_2^2)$，$-\infty <{\mu}_i<+\infty$，${\sigma}_i>0$，$i=1,2$，若 $X$ 与 $Y$ 独立，则

$$
X+Y\sim N({\mu}_1+{\mu}_2,{\sigma}_1^2+{\sigma}_2 ^ 2)
$$

此外，若 $(X,Y)\sim N({\mu}_1,{\mu}_2,{\sigma}_1^2 ,{\sigma}_2^2 ,\rho)$，则不论 $X$ 与 $Y$ 是否独立（即 $\rho$ 是否为 $0$），都有：

$$
aX+bY+c\sim N(a{\mu}_1+b{\mu}_2+c,a^2 {\sigma}_1^2 +b^2 {\sigma}_2 ^ 2 + 2ab{\sigma}_1{\sigma}_2\rho)
$$

---

4.$\Gamma$ 分布的可加性：设 $X\sim \Gamma ({\alpha}_1,\beta)$，$Y\sim \Gamma ({\alpha}_2,\beta)$，${\alpha}_i>0$，$i=1,2$，$\beta >0$ 均为正整数，若 $X$ 与 $Y$ 独立，则

$$
X+Y\sim \Gamma ({\alpha}_1+{\alpha}_2,\beta)
$$

指数分布是特殊的 $\Gamma$ 分布，$E(\lambda)=\Gamma (1,\lambda)$。

## 随机变量的数学特征

### 常见分布的期望与方差

| 分布 | 分布律或密度函数 | 数学期望 | 方差 |
| :---: | :---: | :---: | :---: |
| $0-1$ 分布 | $P(X=k)=p^k (1-p)^{1-k},\;\;k=0,1$ | $p$ | $p(1-p)$ |
| 二项分布 $B(n,p)$ | $P(X=k)=C_n^k p^k (1-p)^{1-k},\;\;k=0,1,...,n$ | $np$ | $np(1-p)$ |
| 泊松分布 $P(\lambda)$ | $P(X=k)=\frac{\lambda ^k e^{-k}}{k!},\;\;k=0,1,...$ | $\lambda$ | $\lambda$ |
| 均匀分布 $U(a,b)$ | $f(x)=\begin{cases} \frac{1}{b-a} & ,a<x<b \cr 0 & ,\text{else} \end{cases}$ | $\frac{a+b}{2}$ | $\frac{(b-a)^2}{12}$ |
| 指数分布 $E(\lambda)$ | $f(x)=\begin{cases} \lambda e^{-\lambda x} & ,x>0 \cr 0 & ,\text{else} \end{cases}$ | $\frac{1}{\lambda}$ | $\frac{1}{\lambda ^2}$ |
| 正态分布 $N(\mu ,\sigma ^2)$ | $f(x)=\frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu) ^2 }{2\sigma ^2 }},\;\;-\infty<x<+\infty$ | $\mu$ | $\sigma ^2$ |

### 数学期望与分布函数的关系

设随机变量 $X$ 的分布函数为 $F(x)$，则分部积分法易证：

$$
E(X) = \int_{0}^{+\infty} (1-F(x))\mathrm{d}x-\int_{-\infty}^{0} F(x)\mathrm{d}x
$$

- 当 $X$ 为非负随机变量（即 $P(X\geq 0)=1$）时，有：$E(X)=\int _0^{+\infty} (1-F(x))\mathrm{d}x$；
- 当 $X$ 为取非负整数值得随机变量时，有：$E(X)=\sum _{k=1}^{+\infty} P(X\geq k)$；

### 二维随机变量函数的数学期望

设 $Z$ 是随机变量 $X,Y$ 的函数：$Z=h(X,Y)$（$h$ 是连续函数）：

- 若二维离散型随机变量 $(X,Y)$ 的分布律为 $P(X=x_i,Y=y_j)=p_{ij},\;\;i,j=1,2,...$，则有：$E(Z)=E[h(X,Y)]=\sum_{i=1}^{\infty} \sum_{i=1}^{\infty} h(x_i,y_j)p_{ij}$，这里设上式右边的级数绝对收敛；
- 若二维连续型随机变量 $(X,Y)$ 的概率密度为 $f(x,y)$，则有：$E(Z)=E[h(X,Y)]=\int_{-\infty}^{+\infty} \int_{-\infty}^{+\infty} h(x,y)f(x,y)\mathrm{d}x\mathrm{d}y$，这里设上式右边的积分绝对收敛；
- 特别地：
    - $E(XY)=\int_{-\infty}^{+\infty} \int_{-\infty}^{+\infty} xyf(x,y)\mathrm{d}x\mathrm{d}y$；
    - $E(X)=\int_{-\infty}^{+\infty} \int_{-\infty}^{+\infty} xf(x,y)\mathrm{d}x\mathrm{d}y$；
    - $E(Y)=\int_{-\infty}^{+\infty} \int_{-\infty}^{+\infty} yf(x,y)\mathrm{d}x\mathrm{d}y$；

### 条件数学期望

若 $(X,Y)$ 为二维离散型随机变量，且在给定 $X=x$ 下，$Y$ 的条件分布律为 $P(Y=y_j | X=x)=p_j(x),\;\;j=1,2,...$；或者 $(X,Y)$ 为二维连续型随机变量，且在给定 $X=x$ 下，$Y$ 的条件概率密度函数为 $f_{Y|X}(y|x)$，则 $Y$ 的条件期望为：

$$
E(Y|X=x)= \begin{cases}
\sum_{j=1}^{+\infty} y_j p_j(x) & ,\text{discrete} \cr
\int_{-\infty}^{+\infty} y f_{Y|X}(y|x) \mathrm{d}y & ,\text{continuous}
\end{cases}
$$

$(X,Y)$ 为二维随机变量，若 $E(Y)$ 存在，则有全期望公式（Total Expectation Formula）：

$$
E(Y) = E[E(Y|X)]
$$

### 标准化变量

设随机变量 $X$ 具有数学期望 $E(X)=\mu$，方差 $D(X)=\sigma ^2 \neq 0$，记 $X^{\*} = \frac{X-\mu}{\sigma}$。$E(X^{\*} )=0$，$D(X^{\*})=1$，称 $X^{\*}$ 为 $X$ 的标准化变量。

特别地，正态变量的标准化变量服从标准正态分布。

### 独立性与相关性

随机变量之间的相关性仅针对「线性关系」而言，$X,Y$ 不相关等价于 $Cov(X,Y)=0$。

而独立性是一个很强的条件，随机变量相互独立，定义上要求其中任一变量的存在与否，都不会对其他变量的概率分布产生影响。$X,Y$ 独立等价于 $Cov(f(X),g(Y))=0$ 对任何函数 $f,g$ 都成立，也就是说要求 $X,Y$ 没有任何关系，包括线性的和非线性的。

所以我们发现，独立性比相关性强得多，独立是不相关的充分不必要条件。通俗地讲，独立才是我们所说的“没有关系”，而不相关仅仅表达“没有线性关系”。

二维正态变量 $(X,Y)$ 的独立性与相关性：

- $\rho _{XY} = \rho$；
- $X,Y$ 不相关 $\Harr$ $X,Y$ 相互独立；

### 其它数字特征

设 $X,Y$ 是随机变量：

- 若 $E(X^k),\;\;k=1,2,...$ 存在，则称之为 $X$ 的 $k$ 阶（原点）矩；
- 若 $E\lbrace [X-E(X)]^k \rbrace,\;\;k=1,2,...$ 存在，则称之为 $X$ 的 $k$ 阶中心矩；
- 若 $E(X^k Y^l),\;\;k,l=1,2,...$ 存在，则称之为 $X,Y$ 的 $k+l$ 阶混合（原点）矩；
- 若 $E\lbrace [X-E(X)]^k [Y-E(Y)]^l \rbrace,\;\;k,l=1,2,...$ 存在，则称之为 $X,Y$ 的 $k+l$ 阶混合中心矩；

---

设 $X$ 为连续型随机变量，其分布函数和概率密度函数分别为 $F(x)$ 和 $f(x)$，称满足条件 $P(X>x_{\alpha})=1-F(x_{\alpha})=\int_{x_{\alpha}}^{+\infty} f(x) \mathrm{d}x = \alpha$ 的实数 $x_{\alpha}$ 为随机变量 $X$ 的上 $x_{\alpha}$ 分位数。

<div style="text-align: center;">
<img src="/assets/images/math/probability/3.png" alt="上α分位数图示" style="width: 50%;">
</div>

- 当 $\alpha = 1/2$ 时，$x_{1/2}$ 称为 $X$ 的中位数；
- 对于标准正态分布，$x_{1/2}=0$，$x_{1-\alpha}=-x_{\alpha}$；

## 大数定律及中心极限定理

### 大数定律补充

大数定律揭示了一些随机事件在大数条件下呈现出的稳定性。不论是独立重复试验下的事件发生频率（伯努利大数定律），还是随机变量序列的算术平均（辛钦大数定律），都能依概率收敛到一个稳定值（分别是概率和期望）。

> In probability theory, the law of large numbers (LLN) is a mathematical theorem that states that the average of the results obtained from a large number of independent and identical random samples converges to the true value, if it exists.

在概率论与数理统计这门课程中：

- 两个重要不等式中，切比雪夫不等式更为常用；
- 常见的几种大数定律中，辛钦大数定律最为常用；
    - 尤其是辛钦大数定律的推论，直接指出了一个随机变量的函数依概率收敛到其均值（不严谨，需要加上一些条件，详见其严格定义）；

### 其他常见的大数定律

马尔可夫大数定律

设 $\lbrace X_i,i \geq 1 \rbrace$ 为一随机变量序列，若对所有的 $i \geq 1$，$X_i$ 的方差都存在，且 $lim_{n \rarr +\infty} \frac{1}{n^2} Var(\sum_{i=1}^{n} X_i) = 0$，则对 $\forall \epsilon > 0$，有：

$$
lim_{n \rarr +\infty} P \lbrace | \frac{1}{n} \sum_{i=1}^{n}X_i - \frac{1}{n}E(X_i) | \geq \epsilon \rbrace = 0
$$

成立，即随机变量 $\lbrace X_i,i \geq 1 \rbrace$ 服从大数定律。

---

切比雪夫大数定律

设 $\lbrace X_i,i \geq 1 \rbrace$ 为相互独立的随机变量序列，若存在常数 $C$，使得 $Var(X_i) \leq C , i=1,2,...$，即所有的 $X_i$ 的方差有共同的上界，则对 $\forall \epsilon > 0$，有：

$$
lim_{n \rarr +\infty} P \lbrace | \frac{1}{n} \sum_{i=1}^{n}X_i - \frac{1}{n}E(X_i) | \geq \epsilon \rbrace = 0
$$

成立，即随机变量 $\lbrace X_i,i \geq 1 \rbrace$ 服从大数定律。

---

切比雪夫大数定律的推论：

设 $\lbrace X_i,i \geq 1 \rbrace$ 为相互独立的随机变量序列，若它们的方差存在且相同（记为 ${\sigma}^2$），则随机变量 $\lbrace X_i,i \geq 1 \rbrace$ 服从大数定律。

特别地（较常用，需要掌握），$\lbrace X_i,i \geq 1 \rbrace$ 为相互独立的随机变量序列，且它们具有相同的期望 $\mu$ 和相同的方差 ${\sigma}^2$，则 $\frac{1}{n} \sum_{i=1}^{n}X_i \xrightarrow{P} \mu \; , \; n \rarr +\infty$。

### 中心极限定理补充

有许多随机变量，它们是由大量的相互独立的随机变量的综合影响所形成的，而其中每个个别因素的作用都很小。这种随机变量往往服从或近似服从正态分布，或者说它的极限分布是正态分布。中心极限定理正是从数学上论证了这一现象。

<div class="heti-skip">
对于概率论与数理统计这门课程，我们只需要掌握独立同分布的中心极限定理（林德伯格-莱维中心极限定理）及其推论（二项分布的正态近似，即棣莫弗-拉普拉斯定理）即可。
</div>

## 统计量与抽样分布

### 从概率论到数理统计

概率论告诉我们，现实中的许多随机事件都会在大数条件下呈现出稳定性（规律性），因而理论上只要对随机现象进行足够多次观察，一定能清楚地计算出各种结果的规律性。但是，实际上所允许的观察永远是有限的，甚至是少量的。

从有限的观察中推测无限观察才能得到的规律，以样本的信息来推断总体的信息，这就是数理统计学研究的问题之一。根据 Glivenko-Cantelli 定理，经验分布函数的收敛行为随着独立同分布的观测值数量的增加而增强，这也证明了数理统计学通过样本信息推断总体信息这一方法的可行性和正确性。

上文提到的从样本出发推断总体，正是所谓的「统计推断」。R.A Fisher 将统计推断分为以下三种：

- 抽样分布（统计量的分布）；
- 参数估计；
- 假设检验；

### 三大抽样分布表

$\chi^{2}$ 分布（卡方分布）：<br />
> [https://www.obhrm.net/index.php/卡方分布表_Chi-Square_Probabilities](https://www.obhrm.net/index.php/%E5%8D%A1%E6%96%B9%E5%88%86%E5%B8%83%E8%A1%A8_Chi-Square_Probabilities)

$t$ 分布（学生氏分布），关注 one-sided 部分即可：<br />
> [https://en.wikipedia.org/wiki/Student%27s_t-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution#Table_of_selected_values)

$F$ 分布：<br />
> [https://blog.csdn.net/sinat_34439107/article/details/78577412](https://blog.csdn.net/sinat_34439107/article/details/78577412)

!!! note "碎碎念"

    这三张分布表找得有点难受，很奇怪为什么 Wikipedia 不给出完整的分布表。以我的强迫症，如果网址和分布表不够优雅的话，我也许会自己来做这件事（逃。

## 参数估计

<!-- "^ {}" is right, while "^{}" isn't. 这句话本应写在 ### 正态总体参数区间估计表 中，写在这里是因为一些玄学问题，只有写到该二级标题下的第一个三级标题或直接写到所有三级标题前才能渲染成功，Material 版本为 9.5.2 -->

### 纠偏方法

无偏性是对估计量的一个最常见的重要要求，是“好”估计的标准之一。当估计量 $\hat{\theta}$ 不满足无偏性时，即 $E(\hat{\theta}) \not = \theta$ 时，我们可以对 $\hat{\theta}$ 进行微调，以使其满足无偏性，这一过程称为纠偏。

这里给出一个最简单的纠偏情形：如果 $E(\hat{\theta})=a\theta +b$，其中 $a,b$ 为常数，且 $a\not =0$，则 $\frac{1}{a}(\hat{\theta}-b)$ 是对 $\theta$ 的无偏估计。

### 枢轴量法补充

用枢轴量发求解置信区间时，若常数 $a,b$ 是不唯一的，则根据 Neyman 原则，我们应该选择能使区间平均长度最短的 $a,b$。

在实际应用中，为了方便，常取 $a,b$ 满足：

$$
P(G(X;\theta)\leq a) = P(G(X;\theta)\geq b) = \alpha /2
$$

这样得到的置信区间称为等尾置信区间。

### 双侧置信与单侧置信

注意区分双侧置信和单侧置信，在相同条件下，双侧置信区间的下限（或上限）并不等同于单侧置信下限（或上限）。

特别地，在下表中我们发现，这几个双侧置信限与单侧置信限的区别，恰恰是上 $x_{\alpha /2}$ 分位数和上 $x_{\alpha}$ 分位数的区别。

### 正态总体参数区间估计表

前三行为一个正态总体，后三行为两个正态总体：

| 待估参数 | 其他参数 | 枢轴量及其分布 | 置信区间 | 单侧置信限 |
| :---: | :---: | :---: | :---: | :---: |
| $\mu$ | $\sigma^ {2}$ 已知 | $\frac{\overline{X}-\mu}{\sigma /\sqrt{n}} \sim N(0,1)$ | $\left( \overline{X} \pm \frac{\sigma}{\sqrt{n}}z_{\alpha /2} \right)$ | $\overline{X} \pm \frac{\sigma}{\sqrt{n}}z_{\alpha}$ |
| $\mu$ | $\sigma^ {2}$ 未知 | $\frac{\overline{X}-\mu}{S /\sqrt{n}} \sim t(n-1)$ | $\left( \overline{X} \pm \frac{S}{\sqrt{n}}t_{\alpha /2}(n-1) \right)$ | $\overline{X} \pm \frac{S}{\sqrt{n}}t_{\alpha}(n-1)$ |
| $\sigma^ {2}$ | $\mu$ 未知 | $\frac{(n-1)S^ 2}{\sigma^{2}} \sim \chi^{2}(n-1)$ | $\left( \frac{(n-1)S^ 2}{\chi_{\alpha /2}^{2}(n-1)}, \frac{(n-1)S^2}{\chi_{1-\alpha /2}^{2}(n-1)} \right)$ | $\frac{(n-1)S^ 2}{\chi_{\alpha}^{2}(n-1)}, \frac{(n-1)S^ 2}{\chi_{1-\alpha}^{2}(n-1)}$ |
| $\mu_{1}-\mu_{2}$ | $\sigma_{1}^ {2}, \sigma_{2}^ {2}$ 已知 | $\frac{(\overline{X}-\overline{Y})-(\mu_{1}-\mu_{2})}{\sqrt{\frac{\sigma_{1}^ {2}}{n_1}+\frac{\sigma_{2}^ {2}}{n_2}}} \sim N(0,1)$ | $\left( (\overline{X}-\overline{Y}) \pm z_{\alpha /2}\sqrt{\frac{\sigma_{1}^ {2}}{n_1}+\frac{\sigma_{2}^ {2}}{n_2}} \right)$ | $(\overline{X}-\overline{Y}) \pm z_{\alpha}\sqrt{\frac{\sigma_{1}^ {2}}{n_1}+\frac{\sigma_{2}^ {2}}{n_2}}$ |
| $\mu_{1}-\mu_{2}$ | $\sigma_{1}^ {2}, \sigma_{2}^ {2}$ 未知 | $\frac{(\overline{X}-\overline{Y})-(\mu_{1}-\mu_{2})}{S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}}} \sim t(n_1+n_2-2)$ | $\left( (\overline{X}-\overline{Y}) \pm t_{\alpha /2}(n_1+n_2-2)S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}} \right)$ | $(\overline{X}-\overline{Y}) \pm t_{\alpha}(n_1+n_2-2)S_w\sqrt{\frac{1}{n_1}+\frac{1}{n_2}}$ |
| $\frac{\sigma_{1}^ {2}}{\sigma_{2}^ {2}}$ | $\mu_{1}, \mu_{2}$ 未知 | $\frac{S_1^ 2/S_2^ 2}{\sigma_{1}^ {2}/\sigma_{2}^ {2}} \sim F(n_1-1,n_2-1)$ | $\left( \frac{S_1^ 2}{S_2^ 2}\frac{1}{F_{\alpha /2}(n_1-1,n_2-1)}, \frac{S_1^ 2}{S_2^ 2}\frac{1}{F_{1-\alpha /2}(n_1-1,n_2-1)} \right)$ | $\frac{S_1^ 2}{S_2^ 2}\frac{1}{F_{\alpha}(n_1-1,n_2-1)}, \frac{S_1^ 2}{S_2^ 2}\frac{1}{F_{1-\alpha}(n_1-1,n_2-1)}$ |