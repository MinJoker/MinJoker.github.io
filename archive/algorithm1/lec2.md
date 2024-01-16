# Analysis of Algorithms

## 引入

影响程序运行时间的因素有以下两种：

- System independent effects -> **frequency**
    - Algorithm
    - Input

- System dependent effects -> **cost**
    - Machine
    - Compiler

运行时间等于每种基本操作（operation）的成本（cost）与频次（frequency）的乘积。

## 算法理论

### 时间复杂度

![常用的时间复杂度分析符号](/assets/images/cs/algorithms/2.png "常用的时间复杂度分析符号")

对于复杂的问题，确定上下界是困难的，尤其吻合的上下界（$O \Omega$->$\Theta$）。我们通过研究「持续下降的上界」来了解问题的计算难度，一些问题找到了最优算法，而还有很多问题的上下界之间仍有间隔。

很多时候人们错把上界分析当作运行时间的近似模型。在这门课程中我们使用波浪记号（~）来表示近似模型。

>这里补充一些其他人的笔记：
>
>1. [鹤翔万里的笔记本/数据结构基础/算法分析基础](https://note.tonycrane.cc/cs/algorithm/ds/topic1/)
>2. [oneko的语雀/数据结构基础/算法分析](https://www.yuque.com/oneko/something/org92s)

### 空间复杂度

>干红华老师（我在ZJU的FDS老师）认为，空间复杂度相比时间复杂度更为不重要，因为在某种意义上，空间可以被认为是无限的，或者说可拓展至无限的，而时间则是绝对有限的。