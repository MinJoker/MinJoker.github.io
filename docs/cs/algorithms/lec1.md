# Union-Find

## 动态连通性问题

Union-Find算法，也就是并查集算法，主要用于解决「动态连通性」问题。

- Union: 连接两个节点；
- Find: 查询两节点是否连通；

「连通」是一种等价关系，具有以下性质：

1. 自反性：节点`p`和`p`是连通的；
2. 对称性：如果节点`p`和`q`连通，那么`q`和`p`也连通；
3. 传递性：如果节点`p`和`q`连通，`q`和`r`连通，那么`p`和`r`也连通；


## 算法实现

### Quick-Find

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

### Quick-Union

Quick-Union算法用「森林」来表达图的动态连通性。

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

#### Weighting

Union-Find算法的一种优化方式是，通过衡量两棵树的大小(size of objects)来决定树的连接方式，从而避免生成过高的树状结构，甚至退化成链表。

!!! tip
    No reason not to. Keep tree almost completely flat.

![Weighting对树的优化效果](/assets/images/cs/algorithms/1.png "Weighting对树的优化效果")

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

#### Path Compression

Union-Find算法的另一种优化方式是，由于寻找根节点时遍历了同一条路径上的所有节点，可以同时更新节点的父节点，从而将路径压缩。

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

### Java Implementation

以下为加权和路径压缩优化后的并查集算法的 Java 实现。

```java linenums="1" title="Weighted Quick-Union with Path Compression (WQUPC)"
public class WQUPC
{
    private int[] id;
    private int[] sz;

    public WQUPC(int N)
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

## 算法分析及应用

### 时间复杂度

$M$ union-find operations on a set of $N$ objects:

| algorithm | worst-case time |
| :---: | :---: |
| quick-find | M N |
| quick-union | M N |
| weighted QU | N + M log N |
| QU + path compression| N + M log N |
| weighted QU + path compression | N + M lg\* N |

[Hopcroft-Ulman,Tarjan]<br />
从空的内含 $N$ 个元素的数据结构开始，任何 $M$ 次合并与查找操作都至多需要 $c(N+M lg^{\*} N)$ 次数组访问。其中，$lg^{\*}$ 是[迭代对数](https://en.wikipedia.org/wiki/Iterated_logarithm)，现实中可以看做是一个小于 $5$ 的数。

[Fredman-Saks]<br />
并查集问题不存在线性时间算法。

> - In theory, WQUPC is not quite linear.
> - In practice, WQUPC is linear.

关于并查集算法，还有更多值得分析的地方... e.g. [OI Wiki](https://oi-wiki.org/ds/dsu-complexity/)

- [ ] ToDo

### 应用

- [ ] ToDo