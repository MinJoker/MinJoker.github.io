# 课程笔记

!!! quote "引用其他人的笔记"

    此「课程笔记」仅作为对以下笔记的补充：

    [Isshiki修's Notebook/课程笔记/[大二秋冬]数字逻辑设计](https://note.isshikih.top/cour_note/D2QD_DigitalDesign/)

## 1 Digital Systems and Information



## 2 Combinational Logic Circuits

### 布尔代数补充公式

**1.香农公式（Shannon formula）:**

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

**2.逻辑函数分解（Shannon Expansion）:**

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

---

### SOM与POM

SOM与POM蕴含着一种精巧的对偶性，具体体现有：

- SOM与POM的自然推导过程中，一个关键点就在于我们对 `1` 和 `0` 谁是主体的理解，两者的推导过程是完全对称的；
- SOM与POM还存在一种取反的对称性，比如假设 $F(x,y,z)=\sum_m(1,3,5,7)$ ，则有 $\overline{F}(x,y,z)=\sum_m(0,2,4,6) = \prod_M(1,3,5,7)$