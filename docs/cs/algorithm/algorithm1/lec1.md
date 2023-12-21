# Union-Find

## 并查集

Union-Find，也就是并查集，是一种存储不相交集合（disjoint set）的数据结构。

顾名思义，并查集支持两种操作：

- 合并（Union）：连接两个节点；
- 查询（Find）：查询两节点是否连通；

「连通」是一种等价关系，具有以下性质：

1. 自反性：节点`p`和`p`是连通的；
2. 对称性：如果节点`p`和`q`连通，那么`q`和`p`也连通；
3. 传递性：如果节点`p`和`q`连通，`q`和`r`连通，那么`p`和`r`也连通；


## 算法实现

基本的 Union-Find API 如下：

| public class UF | Descriptions |
| :---: | :---: |
| **UF(int N)** | initialize union-find data structure with N objects |
| **void union(int p, int q)** | add connection between p and q |
| **boolean isConnected(int p, int q)** | check if p and q are connected |

??? example "客户端测试程序"

    ```java linenums="1" title="Union-Find Test Client"
    import java.util.Scanner;

    public class UnionFindTestClient
    {
        public static void main(String[] args)
        {
            Scanner scanner = new Scanner(System.in);
            int N = scanner.nextInt();
            UF uf = new UF(N);                          // UF: QuickFind, QuickUnion, WeightedQuickUnionWithPathCompression.
            while(scanner.hasNext()){
                int p = scanner.nextInt();
                int q = scanner.nextInt();
                if(!uf.isConnected(p, q)){
                    uf.union(p, q);
                    System.out.println(p + " " + q + " connected.");
                } else{
                    System.out.println(p + " " + q + " already connected.");
                }
            }
            scanner.close();
        }
    }
    ```

### 快速查询

Quick-Find 算法是一种很自然的并查集实现思路。

- Data structure：
    - 大小为`N`的数组`id[]`；
    - 节点`p`和节点`q`是连通的，当且仅当`id[p]`等于`id[q]`；

- Union:
    - 将所有值等于`id[p]`的数组元素的值修改为`id[q]`，从而实现节点`p`和节点`q`的连接；

- Find:
    - 检查`id[p]`和`id[q]`是否相等；

```java linenums="1" title="Quick-Find: Java Implementation"
public class QuickFindUF
{
    private int[] id;

    public QuickFindUF(int N)
    {
        id = new int[N];
        for(int i = 0; i < N; i++){         // set id of each object to itself.
            id[i] = i;
        }
    }

    public boolean isConnected(int p, int q)
    {
        return id[p] == id[q];
    }

    public void union(int p, int q)
    {
        int pid = id[p];
        int qid = id[q];
        for(int i = 0; i < id.length; i++){ // change all entries with id[p] to id[q].
            if(id[i] == pid){
                id[i] = qid;
            }
        }
    }
}    
```

### 快速合并

Quick-Union 算法用「森林」来表达图的动态连通性。

- Data structure:
    - 大小为`N`的数组`id[]`；
    - `id[i]`指向节点`i`的父节点（parent）；
    - 节点`i`的根节点（root）是`id[id[id[...id[i]...]]]`；
    - 节点`i`为根节点，当且仅当`i=id[i]`（自指的）；

- Union:
    - 将`p`的根节点指向`q`的根节点，从而实现节点`p`和节点`q`的连通；

- Find:
    - 检查节点`p`和节点`q`的根节点是否相同；

```java linenums="1" title="Quick-Union: Java Implementation"
public class QuickUnionUF
{
    private int[] id;

    public QuickUnionUF(int N)
    {
        id = new int[N];
        for(int i = 0; i < N; i++){         // set id of each object to itself.
            id[i] = i;
        }
    }

    private int root(int i)
    {
        while(i != id[i]){                  // chase parent pointers until reach root.
            i = id[i];
        }
        return i;
    }

    public boolean isConnected(int p, int q)
    {
        return root(p) == root(q);          // check if p and q have same root.
    }

    public void union(int p, int q)
    {
        int i = root(p);
        int j = root(q);
        id[i] = j;                          // change root of p to point to root of q.
    }
}
```

---

#### 按秩合并

Quick-Union 算法的一种优化方式是，通过衡量两棵树的大小（size of objects）来决定树的连接方式，从而避免生成过高的树状结构，甚至退化成链表。

!!! tip
    No reason not to. Keep tree almost completely flat.

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/1.png" alt="按秩合并的优化效果图示" style="width: 90%;">
</div>

- Data structure:
    - 和 Quick-Union 一样, 但是需要额外维护一个数组`sz[]`，用来存储节点`i`所在的树的大小（size of objects）;

- Union:
    - 将较小的树的根节点指向较大的树的根节点，从而连接两棵树；
    - 更新数组`sz[]`；

```java linenums="1"
public void union(int p, int q)
{
    int i = root(p);
    int j = root(q);
    if(sz[i] < sz[j]){      // link root of smaller tree to root of larger tree.
        id[i] = j;
        sz[j] += sz[i];
    } else {
        id[j] = i;
        sz[i] += sz[j];
    }
}
```

---

#### 路径压缩

Quick-Union 算法的另一种优化方式是，由于寻找根节点时遍历了同一条路径上的所有节点，可以同时更新节点的父节点，从而将路径压缩。

- Root:
    - 将路径上的所有节点指向它的父节点的父节点（grandparent），从而将路径长度减半；

=== "with path compression"

    ```java linenums="1"
    private int root(int i)
    {
        while(i != id[i]){
            id[i] = id[id[i]];      // only one extra line of code!
            i = id[i];
        }
        return i;
    }
    ```

=== "without path compression"

    ```java linenums="1"
    private int root(int i)
    {
        while(i != id[i]){
            i = id[i];
        }
        return i;
    }
    ```

---

以下为按秩合并和路径压缩优化后的快速合并算法的 Java 实现。

```java linenums="1" title="Weighted Quick-Union with Path Compression (WQUPC)"
public class WeightedQuickUnionWithPathCompressionUF
{
    private int[] id;
    private int[] sz;

    public WeightedQuickUnionWithPathCompressionUF(int N)
    {
        id = new int[N];
        sz = new int[N];
        for(int i = 0; i < N; i++){
            id[i] = i;                      // set id of each object to itself.
            sz[i] = 1;                      // set size of each object to 1.
        }
    }

    private int root(int i)
    {
        while(i != id[i]){                  // chase parent pointers until reach root.
            id[i] = id[id[i]];              // path compression by halving.
            i = id[i];
        }
        return i;
    }

    public boolean isConnected(int p, int q)
    {
        return root(p) == root(q);          // check if p and q have same root.
    }

    public void union(int p, int q)
    {
        int i = root(p);
        int j = root(q);
        // if(i == j){                      // isConnected(p, q) already.
        //     return;
        // }
        if(sz[i] < sz[j]){                  // link root of smaller tree to root of larger tree.
            id[i] = j;
            sz[j] += sz[i];
        } else {
            id[j] = i;
            sz[i] += sz[j];
        }
    }
}
```

## 算法分析

我们先来看看仅进行一次初始化、合并与查找操作所需的至多数组访问次数（$\intercal$ 指包含了寻找 root 的消耗）：

| algorithm | initialize | union | isConnected |
| :---: | :---: | :---: | :---: |
| quick-find | $N$ | $N$ | 1 |
| quick-union | $N$ | $N^{\;\intercal}$ | $N$ |
| weighted QU | $N$ | $\lg N^{\;\intercal}$ | $\lg N$ |

???+ note "为什么 weighted QU 的至多数组访问次数是对数呢？"

    为什么 weighted QU 对于任何节点的访问深度都不会超过以 2 为底的对数呢？理解这个问题的关键在于节点的深度是在何时增加的。当某个节点所在的树合并到另一棵树上的时候，该节点的深度加一，而树的大小至少翻倍（因为只有当该节点所在的树小于等于另一棵树的时候才会发生这种合并）。所以，一棵树最多翻倍以二为底的对数次，就会把所有元素都纳入这棵树中，因而节点深度的加一也是至多发生以二为底的对数次。

---

对 $N$ 个元素进行 $M$ 次合并与查找操作所需的数组访问次数：

| algorithm | worst-case time |
| :---: | :---: |
| quick-find | $M N$ |
| quick-union | $M N$ |
| weighted QU | $N + M \log N$ |
| QU + path compression| $N + M \log N$ |
| weighted QU + path compression | $N + M \lg^{\*} N$ |

[Hopcroft-Ulman, Tarjan]<br />
从空的内含 $N$ 个元素的数据结构开始，任何 $M$ 次合并与查找操作都至多需要 $c(N+M \lg^{\*} N)$ 次数组访问。其中，$\lg^{\*}$ 是[迭代对数](https://en.wikipedia.org/wiki/Iterated_logarithm)，现实中可以看做是一个小于 $5$ 的数。

事实上，WQUPC 算法的时间复杂度可以用 $N+M\alpha (M,N)$ 进一步逼近，$\alpha$ 为 Ackermann 函数的反函数，其增长比迭代对数还要缓慢。要证明这一点就更为复杂了，可以参考[这篇文章](https://oi-wiki.org/ds/dsu-complexity/)。至此我们意识到，原来一个简单的并查集算法竟然可以蕴含如此深刻的数学问题！

[Fredman-Saks]<br />
并查集问题不存在线性时间算法。

> In theory, WQUPC is not quite linear.<br />
> In practice, WQUPC is linear.

## 并查集的应用

并查集有很多应用，其中最为著名的是最小生成树的 Kruskal 算法，最小生成树的重要性意味着并查集是许多算法的基础。并查集的其他应用列举如下：

- Percolation.
- Games (Go, Hex).
- Dynamic connectivity.
- Least common ancestor.
- Equivalence of finite state automata.
- Hoshen-Kopelman algorithm in physics.
- Hinley-Milner polymorphic type inference.
- Kruskal's minimum spanning tree algorithm.
- Compiling equivalence statements in Fortran.
- Morphological attribute openings and closings.
- Matlab's `bwlabel()` function in image processing.

## Project 1

- [ ] Todo: [Percolation](https://coursera.cs.princeton.edu/algs4/assignments/percolation/specification.php)