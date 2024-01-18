# 数理统计基础

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
> [https://www.obhrm.net/index.php/卡方分布表_Chi-Square_Probabilities](https://www.obhrm.net/index.php/%E5%8D%A1%E6%96%B9%E5%88%86%E5%B8%83%E8%A1%A8_Chi-Square_Probabilities){target="_blank"}

$t$ 分布（学生氏分布），关注 one-sided 部分即可：<br />
> [https://en.wikipedia.org/wiki/Student%27s_t-distribution](https://en.wikipedia.org/wiki/Student%27s_t-distribution#Table_of_selected_values){target="_blank"}

$F$ 分布：<br />
> [https://blog.csdn.net/sinat_34439107/article/details/78577412](https://blog.csdn.net/sinat_34439107/article/details/78577412){target="_blank"}

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

## 假设检验