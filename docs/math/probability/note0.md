# 课程笔记

!!! quote "引用其他人的笔记"

    此「课程笔记」作为对以下笔记的补充：

    [Isshiki修's Notebook/课程笔记/[大一春夏]概率论与数理统计](https://note.isshikih.top/cour_note/D1CX_ProbabilityAndStatistics/)

## 1 概率论的基本概念

### 1.1 全概率公式与贝叶斯公式

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

## 2 随机变量及其概率分布

### 2.1 随机变量补充

- 离散型和连续型是常见的两类随机变量，但随机变量并不只有这两种。
- 书写离散型随机变量的概率分布律时千万不要漏掉随机变量的可能取值。例如：$k=1,2,...$

### 2.2 分布函数补充

- 分布函数通常写成大写字母，例如 $F(x)$
- 分布函数的定义域为全体实数
- 分布函数的分段写法，按照「左闭右开」的区间进行分段是一个较好的习惯（主要针对离散型随机变量的分布函数）
- 支撑（support）指的是使概率分布为正的密度的随机变量的取值的集合，定义为 $ \lbrace x: f(x)>0\rbrace $

### 2.3 指数分布的无记忆性

假设 $t_0>0，t>0，$

$$
\begin{aligned}
P(X>t_0+t \;\; | \;\; X>t_0 ) & = \frac{P(X>t_0+t)}{P(X>t_0)} \cr
& = \frac{1-F(t_0+t)}{1-F(t_0)} \cr
& = e^{-\lambda t} = P(X>t)
\end{aligned}
$$

??? example "无记忆性的一个例子"

    假设设备无故障运行的时间 $T$ 服从指数分布。已知设备无故障运行了10个小时，求该设备再无故障至少运行8个小时的概率。

    $$
    P\lbrace T\geq 18 \; | \; T>10 \rbrace = \frac{P\lbrace T>18\rbrace }{P\lbrace T>10\rbrace} = e^{-8\lambda} = P\lbrace T>8\rbrace
    $$

    注意到，这一条件概率与无条件下无故障运行8小时的概率没有区别。

### 2.4 正态分布标准化

当 $X\sim N(\mu,\sigma^2)$ 时：

$$
P(X\leq b) = \int_{-\infty}^b \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x+\mu) ^2}{2\sigma ^2}} \mathrm{d}x
$$

做变换：$\frac{x-\mu}{\sigma} = t$

$$
\begin{aligned}
P(X\leq b) & = \int_{-\infty}^{\frac{b-\mu}{\sigma}} \frac{1}{\sqrt{2\pi}} e^{-\frac{t ^2}{2}} \mathrm{d}t \cr
& = \Phi(\frac{b-\mu}{\sigma})
\end{aligned}
$$

换言之，当 $X\sim N(\mu,\sigma^2)$ 时，$\frac{X-\mu}{\sigma}\sim N(0,1)$