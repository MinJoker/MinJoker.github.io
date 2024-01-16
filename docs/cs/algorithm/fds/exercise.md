# 题目集

<style>
    .md-typeset .admonition,
    .md-typeset details {
      border-width: 0 !important;
      border-left-width: 4px !important;
    }
</style>

这篇笔记收集一些值得留意的题目，用于 FDS 应试，题目来源：

- 课程配套 PTA 习题，包括 Homework 1 ~ 15 和 Midterm Examination
- 王道《2023年数据结构考研复习指导》，作为补充

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

1. (HW3) Represent a queue by a singly linked list. Given the current status of the linked list as `1->2->3` where `x->y` means `y` is linked after `x`. Now if `4` is enqueued and then a dequeue is done, the resulting status must be:

    <div style="display: flex">
    <div style="width: 100%">A. `1->2->3`</div>
    <div style="width: 100%">B. `2->3->4`</div>
    <div style="width: 100%">C. `4->1->2`</div>
    <div style="width: 100%">D. not unique</div>
    </div>

    !!! success ""

        单向链表实现的队列，入队和出队一定分别在链表的尾和头进行。

        队列用 first 和 last 两个指针来标记头和尾，假设我们把入队和出队的位置互换一下：入队在链表的头进行，这并没有问题；但出队在链表的尾进行则会出错，如果我们把尾节点删除了，那么 last 就没有办法移动到新的尾节点，这是链表的单向性导致的。

2. (Midterm) If a stack is used to convert the infix expression `a+b*c+(d*e+f)*g` into a postfix expression, what will be in the stack (listing from bottom up) when `f` is read?

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

1. (HW4) It is always possible to represent a tree by a one-dimensional integer array. (TRUE of FALSE)

    !!! success ""

        二叉树可以用一维数组表示，而任何树都可以转变成二叉树（使用 FirstChild-NextSibling 表示法，并旋转 $45\degree$ 即得），所以任何树都可以用一维数组表示。

2. (HW4) If a general tree T is converted into a binary tree BT, then which of the following BT traversals gives the same sequence as that of the post-order traversal of T?

    <div style="display: flex">
    <div style="width: 100%">A. pre-order</div>
    <div style="width: 100%">B. in-order</div>
    <div style="width: 100%">C. post-order</div>
    <div style="width: 100%">D. level-order</div>
    </div>

    !!! success ""

        一般树 T 转变为二叉树 BT 的方法就是用 FirstChild-NextSibling 表示法并顺时针旋转 $45\degree$，而用 FisrtChild-NextSibling 表示的树是无法定义中序遍历的。

        T 的先序遍历和 BT 的先序遍历是一样的，T 的后序遍历和 BT 的中序遍历是一样的。

3. (HW4) There exists a binary tree with 2016 nodes in total, and with 16 nodes having only one child. (TRUE of FALSE)

    !!! success ""

        假设没有子节点的节点（叶节点）个数为 $n_0$，只有一个子节点的节点个数为 $n_1$，有两个子节点的节点个数为 $n_2$，则：

        - $n_0+n_1+n_2=2016$
        - $n_0=n_2+1$（二叉树的性质）
        - $n_1=16$

        得到 $2n_2=1999$，除不尽，因此不存在这样的二叉树。

4. (HW4) Given a tree of degree 3. Suppose that there are 3 nodes of degree 2 and 2 nodes of degree 3. Then the number of leaf nodes must be：

    <div style="display: flex">
    <div style="width: 100%">A. 5</div>
    <div style="width: 100%">B. 6</div>
    <div style="width: 100%">C. 7</div>
    <div style="width: 100%">D. 8</div>
    </div>

    !!! success ""

        这道题使用的定理可以视作二叉树性质 $n_0=n_2+1$ 的推广：对于度为 $d$ 的树，$n_0=1+\sum_{i=2}^ {d}((i-1)\times n_i)$。

        证明如下：

        - $n=1+\sum_{i=1}^ {d}(i\times n_i)$（$n$ 为树中的节点总数，等于总边数加 1）
        - $n=\sum_{i=0}^ {d}n_i$
        
        联立上述两式即可得证。

5. (HW6) For a binary tree, if its pre-order travel sequence is {4,2,1,3,6,5,7}, and its in-order travel sequence is {1,2,3,4,5,6,7}, then which of the following statement is FALSE?

    <div style="display: flex">
    <div style="width: 100%">A. This is a complete binary tree.</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">B. 4 is the parent of 3.</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. All the odd numbers are at leaf nodes.</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">D. This is a binary search tree.</div>
    </div>

    !!! success ""

        这题需要根据先序遍历和中序遍历结果确定一个二叉树，答案是 B，具体步骤如下：

        1. 先序遍历序列中的第一个数是二叉树的根节点
        2. 中序遍历序列中，根节点左侧的数都在左子树中，右侧的树都在右子树中
        3. 从而从先序遍历和中序遍历序列中分别提取出左/右子树的先序遍历和中序遍历序列
        4. 重复步骤前三个步骤，直到得到最终的二叉树

        这种方法对于根据后序遍历和中序遍历结果确定一个二叉树同样适用（注意后序遍历序列中的最后一个数是二叉树的根节点）。

        然而，根据先序遍历和后序遍历结果是不足以确定一个二叉树的，但可以唯一确定一个满二叉树，具体方法可以参考[这里](https://www.geeksforgeeks.org/full-and-complete-binary-tree-from-given-preorder-and-postorder-traversals/){target="_blank"}。

6. 一棵二叉树的先序遍历序列和后序遍历序列分别为 1,2,3,4 和 4,3,2,1，该二叉树的中序遍历序列不会是（  ）。

    <div style="display: flex">
    <div style="width: 100%">A. 1,2,3,4</div>
    <div style="width: 100%">B. 2,3,4,1</div>
    <div style="width: 100%">C. 3,2,4,1</div>
    <div style="width: 100%">D. 4,3,2,1</div>
    </div>

    !!! success ""

        这道题目很巧妙，因为先序和后序遍历序列恰好是相反的，所以不存在度为 2 的节点（否则先序和后序遍历序列不可能相反）。

        1 是这棵树的根节点，而它只能有左子树或者右子树，因此一定位于中序遍历的第一个或者最后一个，ABCD 都满足；再考虑以 1 的子节点 2 为根节点的子树，其也只能有左子树或右子树，因此一定位于子树中序遍历的第一个或最后一个，ABD 满足，C 不满足。

7. 若一棵二叉树的先序遍历序列为 a,e,b,d,c，后序遍历序列为 b,c,d,e,a，则根节点的子节点（  ）。

    <div style="display: flex">
    <div style="width: 100%">A. 只有 e</div>
    <div style="width: 100%">B. 有 e、b</div>
    <div style="width: 100%">C. 有 e、c</div>
    <div style="width: 100%">D. 无法确定</div>
    </div>

    !!! success ""

        先序和后序遍历序列无法唯一确定一棵二叉树，但能确定二叉树中节点的祖先关系。当两个节点的先序和后序遍历序列分别为 u,v 和 v,u 时，u 是 v 的祖先。

        首先，a 是根节点，e 是 a 的一个子节点。然后观察先序和后序遍历序列，发现 e 是 b,d,c 的祖先，所以根节点 a 的子节点只有 e 一个。

8. 先序序列为 a,b,c,d 的不同二叉树的个数是（  ）。

    <div style="display: flex">
    <div style="width: 100%">A. 13</div>
    <div style="width: 100%">B. 14</div>
    <div style="width: 100%">C. 15</div>
    <div style="width: 100%">D. 16</div>
    </div>

    !!! success ""

        这道题目很有技巧性。

        首先根据先序遍历和中序遍历的递归算法可以得出：先序序列和中序序列的关系可以视作，以先序序列为入栈次序，以中序序列为出栈次序（中序和后序同理）。

        由于先序和中序序列可以唯一确定一棵二叉树，原题等价于给定入栈顺序为 a,b,c,d，求可能的出栈顺序的个数。

        最后运用结论：$n$ 个不同元素入栈，出栈序列个数为 $\frac{1}{n+1}C_{2n}^ n=14$。这个结论的证明可以参考[这里](https://www.cnblogs.com/wsgxg/p/16651567.html){target="_blank"}。

9. 若一棵完全二叉树有 768 个节点，则该二叉树中叶节点的个数是（  ）。

    <div style="display: flex">
    <div style="width: 100%">A. 257</div>
    <div style="width: 100%">B. 258</div>
    <div style="width: 100%">C. 384</div>
    <div style="width: 100%">D. 385</div>
    </div>

    !!! success ""

        二叉堆的结构本质是完全二叉树，因此完全二叉树的题目也可以用堆相关知识来解决。

        由堆（完全二叉树）的性质，最后一个有儿子的节点序号是 768 / 2 = 384，所以叶节点的个数是 768 - 384 = 384。

## 堆

1. (Midterm) For binary heaps with $N$ elements, the BuildHeap function (which adjust an array of elements into a heap in linear time) does at most $2N−2$ comparisons between elements. (TRUE or FALSE)

    !!! success ""

        结论是，线性建堆最多需要 $N-1$ 次 percolate down 操作。每次 percolate down 操作都需要进行两次比较，所以最多需要 $2N-2$ 次比较。

        关于这个结论的证明，我还没有完全复现，总觉得有一些小问题没解决，这里先贴几个参考链接罢：

        - [OI Wiki](https://oi-wiki.org/ds/binary-heap/#%E6%96%B9%E6%B3%95%E4%BA%8C%E4%BD%BF%E7%94%A8%E5%90%91%E4%B8%8B%E8%B0%83%E6%95%B4){target="_blank"}
        - [Stack Overflow](https://stackoverflow.com/questions/49774237/prove-that-binary-heap-build-max-comparsion-is-2n-2){target="_blank"}
        - [Wikipedia](https://en.wikipedia.org/wiki/Binary_heap#Building_a_heap){target="_blank"}

## 图

1. (HW8) Given an undirected graph G with 16 edges, where 3 vertices are of degree 4, 4 vertices are of degree 3, and all the other vertices are of degrees less than 3. Then G must have at least __ vertices.

    <div style="display: flex">
    <div style="width: 100%">A. 10</div>
    <div style="width: 100%">B. 11</div>
    <div style="width: 100%">C. 13</div>
    <div style="width: 100%">D. 15</div>
    </div>

    !!! success ""

        利用无向图的度的性质 $\sum_{v\in V}d(v)=2|E|$，最多有 11 个节点。

2. (HW8) If a graph G is NOT connected and has 35 edges, then it must have at least __ vertices.

    <div style="display: flex">
    <div style="width: 100%">A. 7</div>
    <div style="width: 100%">B. 8</div>
    <div style="width: 100%">C. 9</div>
    <div style="width: 100%">D. 10</div>
    </div>

    !!! success ""

        要确保 9 个节点的图在任何情况下都是连通的，至少需要 C(8,2) + 1 = 29 条边，所以题目中的 35 条边一定会把 9 个节点的图连通。
        
        同理，要确保 10 个节点的图在任何情况下都是连通的，至少需要 C(9,2) + 1 = 37 条边，所以题目中的 35 条边可能使 10 个节点的图是非连通的。

3. (HW8) A graph with 90 vertices and 20 edges must have at least __ connected component(s).

    <div style="display: flex">
    <div style="width: 100%">A. 69</div>
    <div style="width: 100%">B. 70</div>
    <div style="width: 100%">C. 84</div>
    <div style="width: 100%">D. 85</div>
    </div>

    !!! success ""

        为了使连通分量最少，用 20 条边去连通尽可能多的节点。考虑用 20 条边形成一个生成树，即连通 21 个节点，还剩下 69 个孤立节点，所以共有 1 + 69 = 70 个连通分量。

        或者也可以使用平面图的[欧拉定理](https://en.wikipedia.org/wiki/Planar_graph#Euler's_formula){target="_blank"}来做（超纲）：

        - 对于一个连通图，有 $v-e+f=2$
        - 设题目中的图由 $k$ 个连通分量组成，则有 $V-E+(F+k-1)=2k$
        - 由于 $F\geq 1$，故 $k\geq 70$

4. (HW9) If besides finding the shortest path from `S` to every other vertices, we also need to count the number of different shortest paths, we can modify the Dijkstra algorithm in the following way: add an array `count[]` so that `count[V]` records the number of different shortest paths from `S` to `V`. Then `count[V]` shall be initialized as:

    <div style="display: flex">
    <div style="width: 100%">A. `count[S]=1` and `count[V]=0` for other `V`</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">B. `count[V]=1` for all vertices</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. `count[S]=0` and `count[V]=1` for other `V`</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">D. `count[V]=0` for all vertices</div>
    </div>

    !!! success ""

        这题的关键是要理解最短路算法的更新操作（松弛操作）：对于某个节点，总是用其直接前驱的信息去更新该节点的信息（信息包括最短路、次短路等）。

        设 u 是 v 的直接前驱，则更新操作在这道题中的表现为，用 count[u] 去更新 count[v]，即 count[v] += count[u]，稍加思考可以得出答案为 A。

5. (HW11) Apply DFS to a directed acyclic graph, and output the vertex before the end of each recursion. The output sequence will be:

    <div style="display: flex">
    <div style="width: 100%">A. unsorted</div>
    <div style="width: 100%">B. topologically sorted</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. reverse topologically sorted</div>
    <div style="width: 100%">D. none of the above</div>
    </div>

    !!! success ""

        题目中的 DFS 在递归返回时访问节点（类比后序遍历），因此输出序列是逆拓扑序列。

## 排序

1. (HW13) During the sorting, processing every element which is not yet at its final position is called a "run". Which of the following cannot be the result after the second run of quicksort?

    <div style="display: flex">
    <div style="width: 100%">A. 5, 2, 16, 12, 28, 60, 32, 72</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">B. 2, 16, 5, 28, 12, 60, 32, 72</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. 2, 12, 16, 5, 28, 32, 72, 60</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">D. 5, 2, 12, 28, 16, 32, 72, 60</div>
    </div>

    !!! success ""

        根据题目定义的一趟（run）的含义，快排中每一趟都会将 pivot 放置在其最终位置。因此两趟之后应该已经有 1 + 2 = 3 个 pivot 处于其最终位置（注意，这里第二趟的时候左右两段序列视作并行，因此第二趟能确定 2 个 pivot 最终位置）或者 1 + 1 = 2 个 pivot 处于其最终位置（这种情况要求第一趟的 pivot 是第一个或最后一个元素）。
        
        pivot 的特点是其左侧元素都比它小，其右侧元素都比它大，四个选项的 pivot 如下：

        - A. 5, 2, 16, 12, ***28***, 60, 32, ***72***
        - B. ***2***, 16, 5, 28, 12, 60, 32, ***72***
        - C. ***2***, 12, 16, 5, ***28***, ***32***, 72, 60
        - D. 5, 2, ***12***, 28, 16, ***32***, 72, 60

        所以 D 选项是不可能的。

2. 在内部排序过程中，对尚未确定最终位置的所有元素进行一遍处理称为一趟排序，下列排序方法中，每趟排序结束都至少能够确定一个元素最终位置的方法是（  ）。<br />
   &#x2160; 简单选择排序 &emsp; &#x2161; 希尔排序 &emsp; &#x2162; 快速排序 &emsp; &#x2163; 堆排序 &emsp; &#x2164; 2路归并排序

    <div style="display: flex">
    <div style="width: 100%">A. 仅 &#x2160;、&#x2162;、&#x2163;</div>
    <div style="width: 100%">B. 仅 &#x2160;、&#x2162;、&#x2164;</div>
    <div style="width: 100%">C. 仅 &#x2161;、&#x2162;、&#x2163;</div>
    <div style="width: 100%">D. 仅 &#x2162;、&#x2163;、&#x2164;</div>
    </div>

    !!! success ""

        这道题目比较简单，答案是 A，值得注意的是“一趟”的含义。

3. 下列排序方法中，若将顺序存储更换为链式存储，则算法的时间效率会降低的是（  ）。<br />
   &#x2160; 插入排序 &emsp; &#x2161; 选择排序 &emsp; &#x2162; 起泡排序 &emsp; &#x2163; 希尔排序 &emsp; &#x2164; 堆排序

    <div style="display: flex">
    <div style="width: 100%">A. 仅 &#x2160;、&#x2161;</div>
    <div style="width: 100%">B. 仅 &#x2161;、&#x2162;</div>
    <div style="width: 100%">C. 仅 &#x2162;、&#x2163;</div>
    <div style="width: 100%">D. 仅 &#x2163;、&#x2164;</div>
    </div>

    !!! success ""

        这道题目比较简单，答案是 D，希尔排序和堆排序都利用了顺序存储的随机访问特性，因此更换为链式存储时效率会降低。

## 哈希

1. (HW14) The average search time of searching a hash table with $N$ elements is:

    <div style="display: flex">
    <div style="width: 100%">A. $\Omicron(1)$</div>
    <div style="width: 100%">B. $\Omicron(\log N)$</div>
    <div style="width: 100%">C. $\Omicron(N)$</div>
    <div style="width: 100%">D. not determined</div>
    </div>

    !!! success ""

        这道题目有争议，稍微有点牵强。

        > 根据课堂 PPT 的说法，不考虑溢出的情况下，平均搜索时间复杂度为 $\Omicron(1)$。

        答案是 D，一种较为合适的解释是，与解决冲突的方法有关，不一定能达到 $\Omicron(1)$。

2. (HW15) Suppose that the numbers {4371, 1323, 6173, 4199, 4344, 9679, 1989} are hashed into a table of size 10 with the hash function $h(X)=X\% 10$, and hence have indices {1, 3, 4, 9, 5, 0, 2}. What are their indices after rehashing using $h(X)=X\% TableSize$ with linear probing?

    <div style="display: flex">
    <div style="width: 100%">A. 11, 3, 13, 19, 4, 0, 9</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">B. 1, 3, 4, 9, 5, 0, 2</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">C. 1, 12, 9, 13, 20, 19, 11</div>
    </div>
    <div style="display: flex">
    <div style="width: 100%">D. 1, 12, 17, 0, 13, 8, 14</div>
    </div>

    !!! success ""

        这道题目有争议，极其令人无语。

        答案是 C，目前看到的一种解释是，再哈希将 tableSize 变成两倍，即 20，并向上取一个质数，因此新的 tableSize 是 23。

        值得注意的是，再哈希的对象应当是 key 值 {4371, 1323, ...}，而不是第一次哈希后得到的哈希值 {1, 3, ...}。