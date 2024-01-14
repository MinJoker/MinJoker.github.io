# 题目集

<style>
    .md-typeset .admonition,
    .md-typeset details {
      border-width: 0 !important;
      border-left-width: 4px !important;
    }
</style>

## 算法分析

1. (HW1) The recurrent equations for the time complexities of program P1 and P2 are:<br />
   &emsp;P1: $T(1)=1, T(N)=T(N/3)+1$<br />
   &emsp;P2: $T(1)=1, T(N)=3T(N/3)+1$<br />
   Then the correct conclusion about their time complexities is:

    <div style="display: flex">
    <div style="width: 100%">A. they are both $\Omicron(\log N)$</div>
    <div style="width: 100%">B. $\Omicron(\log N)$ for P1, $\Omicron(N)$ for P2</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. they are both $\Omicron(N)$</div>
    <div style="width: 100%">D. $\Omicron(\log N)$ for P1, $\Omicron(N\log N)$ for P2</div>
    </div>

    !!! success ""

        使用[主定理](note1.md#_5)，P1 符合主定理第三种情况（取 k = 1），时间复杂度为 $\Theta(\log N)$；P2 符合主定理第一种情况，时间复杂度为 $\Theta(N)$。

2. (HW1) The Fibonacci number sequence {$F_N$} is defined as: $F_0=0$, $F_1=1$, $F_N=F_{N-1}+F_{N-2}$, $N=2,3,...$ The time complexity of the function which calculates $F_N$ recursively is $\Theta(N!)$. (TRUE of FALSE)

    !!! success ""

        考虑到递归树是一棵二叉树，递归计算斐波那契数列的时间复杂度显然有上界 $\Omicron(2^N)$。


        ```c
        int fibonacci(int n) {
            if (n == 1 || n == 2) return 1;  /* count++ */
            return fibonacci(n - 1) + fibonacci(n - 2);
        }
        ```

        上述代码中用 count 来标记 base case，显然 count 等于递归树的叶节点数。稍加思考可以发现，递归树的叶节点数恰好等于斐波那契数列第 n 项的值，即 $f(n)=\frac{1}{\sqrt{5}}((\frac{1+\sqrt{5}}{2})^ n-(\frac{1-\sqrt{5}}{2})^ n)$，所以 $T(N)=\Theta((\frac{1+\sqrt{5}}{2})^ N) \approx \Theta(1.618^ N)$。

3. (HW1) The Fibonacci number sequence {$F_N$} is defined as: $F_0=0$, $F_1=1$, $F_N=F_{N-1}+F_{N-2}$, $N=2,3,...$ The space complexity of the function which calculates $F_N$ recursively is:

    <div style="display: flex">
    <div style="width: 100%">A. $\Omicron(\log N)$</div>
    <div style="width: 100%">B. $\Omicron(N)$</div>
    <div style="width: 100%">C. $\Omicron(F_N)$</div>
    <div style="width: 100%">D. $\Omicron(N!)$</div>
    </div>

    !!! success ""

        函数调用是通过一种栈结构实现的，对于递归而言，调用函数（即“递”）时入栈，返回函数（即“归”）时出栈，所以计算空间复杂度就相当于计算栈中最多有多少个被调函数。

        在这个题目中，栈中被调函数数量最大值恰好等于递归树的深度加 2，也就是 $N$，所以空间复杂度为 $\Omicron(N)$。

## 栈和队列

4. (HW3) Represent a queue by a singly linked list. Given the current status of the linked list as `1->2->3` where `x->y` means `y` is linked after `x`. Now if `4` is enqueued and then a dequeue is done, the resulting status must be:

    <div style="display: flex">
    <div style="width: 100%">A. `1->2->3`</div>
    <div style="width: 100%">B. `2->3->4`</div>
    <div style="width: 100%">C. `4->1->2`</div>
    <div style="width: 100%">D. not unique</div>
    </div>

    !!! success ""

        单向链表实现的队列，入队和出队一定分别在链表的尾和头进行。

        队列用 first 和 last 两个指针来标记头和尾，假设我们把入队和出队的位置互换一下：入队在链表的头进行，这并没有问题；但出队在链表的尾进行则会出错，如果我们把尾节点删除了，那么 last 就没有办法移动到新的尾节点，这是链表的单向性导致的。

5. (Midterm) If a stack is used to convert the infix expression `a+b*c+(d*e+f)*g` into a postfix expression, what will be in the stack (listing from bottom up) when `f` is read?

    <div style="display: flex">
    <div style="width: 100%">A. +(*+</div>
    <div style="width: 100%">B. +(+</div>
    <div style="width: 100%">C. ++(+</div>
    <div style="width: 100%">D. abcde</div>
    </div>

    !!! success ""

        用一个符号栈来实现中缀表达式转后缀表达式，答案是 B，具体步骤参见[中缀、后缀与前缀](note2.md#_5)。

        事实上，这道题也可以直接先写出后缀表达式，再判断出答案。这里介绍一种手搓中缀转后缀的方法：

        1. 给中缀表达式补全括号：`((a+(b*c))+(((d*e)+f)*g))`
        2. 从内到外，逐层将括号内的表达式转为后缀表达式，并去掉括号
        3. 最终得到：`abc*+de*f+g*+`

        这种方法对于手搓中缀转前缀同样适用。类似地，再介绍一种手搓表达式树的方法：

        1. 给中缀表达式补全括号：`((a+(b*c))+(((d*e)+f)*g))`
        2. 去掉最外层括号，括号外的 `+` 作为根节点，其左边作为左子树，右边作为右子树
        3. 重复步骤 b. 即可得到表达式树

## 树

6. (HW4) It is always possible to represent a tree by a one-dimensional integer array. (TRUE of FALSE)

    !!! success ""

        二叉树可以用一维数组表示，而任何树都可以转变成二叉树（使用 FirstChild-NextSibling 表示法，并旋转 $45\degree$ 即得），所以任何树都可以用一维数组表示。

7. (HW4) If a general tree T is converted into a binary tree BT, then which of the following BT traversals gives the same sequence as that of the post-order traversal of T?

    <div style="display: flex">
    <div style="width: 100%">A. pre-order</div>
    <div style="width: 100%">B. in-order</div>
    <div style="width: 100%">C. post-order</div>
    <div style="width: 100%">D. level-order</div>
    </div>

    !!! success ""

        一般树 T 转变为二叉树 BT 的方法就是用 FirstChild-NextSibling 表示法并顺时针旋转 $45\degree$，而用 FisrtChild-NextSibling 表示的树是无法定义中序遍历的。

        T 的先序遍历和 BT 的先序遍历是一样的，T 的后序遍历和 BT 的中序遍历是一样的。

8. (HW4) There exists a binary tree with 2016 nodes in total, and with 16 nodes having only one child. (TRUE of FALSE)

    !!! success ""

        假设没有子节点的节点（叶节点）个数为 $n_0$，只有一个子节点的节点个数为 $n_1$，有两个子节点的节点个数为 $n_2$，则：

        - $n_0+n_1+n_2=2016$
        - $n_0=n_2+1$（二叉树的性质）
        - $n_1=16$

        得到 $2n_2=1999$，除不尽，因此不存在这样的二叉树。

9. (HW4) Given a tree of degree 3. Suppose that there are 3 nodes of degree 2 and 2 nodes of degree 3. Then the number of leaf nodes must be：

    <div style="display: flex">
    <div style="width: 100%">A. 5</div>
    <div style="width: 100%">B. 6</div>
    <div style="width: 100%">C. 7</div>
    <div style="width: 100%">D. 8</div>
    </div>

    !!! success ""

        这道题使用的定理可以视作二叉树性质 $n_0=n_2+1$ 的推广：对于 d 叉树，$n_0=\sum_{i=2}^ {d}(n_i+i-1)$。

        这个性质可以用数学归纳法证明，思路比较简单，对于 d-1 叉树，考虑为各种 $n_i$ 节点添加子节点变成 $n_d$ 节点时公式的变化即可。

10. (HW6) For a binary tree, if its pre-order travel sequence is {4,2,1,3,6,5,7}, and its in-order travel sequence is {1,2,3,4,5,6,7}, then which of the following statement is FALSE?

    <div style="display: flex">
    <div style="width: 100%">A. This is a complete binary tree.</div>
    <div style="width: 100%">B. 4 is the parent of 3.</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. All the odd numbers are at leaf nodes.</div>
    <div style="width: 100%">D. This is a binary search tree.</div>
    </div>

    !!! success ""

        这题需要根据先序遍历和中序遍历结果确定一个二叉树，答案是 B，具体步骤如下：

        1. 先序遍历序列中的第一个数是二叉树的根节点
        2. 中序遍历序列中，根节点左侧的数都在左子树中，右侧的树都在右子树中
        3. 从而从先序遍历和中序遍历序列中分别提取出左/右子树的先序遍历和中序遍历序列
        4. 重复步骤前三个步骤，直到得到最终的二叉树

        这种方法对于根据后序遍历和中序遍历结果确定一个二叉树同样适用（注意后序遍历序列中的最后一个数是二叉树的根节点）。

        然而，根据先序遍历和后序遍历结果是不足以确定一个二叉树的，但可以唯一确定一个满二叉树，具体方法可以参考[这里](https://www.geeksforgeeks.org/full-and-complete-binary-tree-from-given-preorder-and-postorder-traversals/)。

## 堆

