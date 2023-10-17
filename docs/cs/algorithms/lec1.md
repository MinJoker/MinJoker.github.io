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
    - Integer array `id[]` of length `N`.
    - Interpretation: `p` and `q` are connected iff they have the same id.

- Union:
    - To merge components containing `p` and `q`, change all entries whose id equals `id[p]` to `id[q]`.

- Find:
    - Check if `p` and `q` have the same id.

### Quick-Union

Quick-Union算法用「森林」来表达图的动态连通性。

- Data structure:
    - Integer array `id[]` of length `N`.
    - Interpretation: `id[i]` is parent of `i`.
    - Root of `i` is `id[id[id[...id[i]...]]]`

- Union:
    - To merge components containing `p` and `q`, set the id of `p`'s root to the id of `q`'s root.

- Find:
    - Check if `p` and `q` have the same root.

#### Weighting

Union-Find算法的一种优化方式是，通过衡量两棵树的大小(*number of objects*)来决定树的连接方式，从而避免生成过高的树状结构，甚至退化成链表。

!!! tip
    No reason not to. Keep tree almost completely flat.

![Weighting对树的优化效果](/assets/images/cs/algorithms/1.png "Weighting对树的优化效果")

- Data structure:
    - Same as Quick-Union, but maintain extra array `sz[i]` to count number of objects in the tree rooted at `i`.

- Union:
    - Link root of smaller tree to root of larger tree.
    - Update the `sz[]` array.

#### Path Compression

Union-Find算法的另一种优化方式是，由于寻找根节点时遍历了同一条路径上的所有节点，可以同时更新节点的父节点，从而将路径压缩。

- Root:
    - Make every other node in path point to its grandparent (thereby halving path length).

=== "with path compression"

    ```c linenums="1"
    int root(int i)
    {
        while(i!=id[i]){
            id[i] = id[id[i]];      // only one extra line of code!
            i = id[i];
        }
        return i;
    }
    ```

=== "without path compression"

    ```c linenums="1"
    int root(int i)
    {
        while(i!=id[i]){
            i = id[i];
        }
        return i;
    }
    ```

### C Implementation

以下为加权和路径压缩优化后的并查集算法的 C 实现。

最终还是用了全局变量。本着对全局变量的厌恶，一开始是用函数传递参数写的，但是感觉会使并查集算法的典型API变得不纯粹而且很丑陋（多一个参数）。

或许还有更好的解决方法？

```c linenums="1" title="weighted Union-Find with path compression"
#include <stdio.h>
#define N 10
                                    // Data Structure
int id[N];                              // id[i] is parent of i.
int sz[N];                              // sz[i] is the size ( number of objects ) in the tree rooted at i.
                                    // API
void Initialize(void);                  // initialize id[] and sz[].
void Union(int p, int q);               // connect p and q.
int isConnected(int p, int q);          // check if p and q are connected.
int root(int i);                        // find the root of i in the tree.

void Initialize(void)
{
    for(int i=0; i<N; i++){
        id[i] = i;                      // initial id[] is self-reference.
        sz[i] = 1;
    }
}

void Union(int p, int q)
{
    int i = root(p);
    int j = root(q);
    // if( i == j ){                    // isConnected() guarantees i is not equal to j.
    //     return;
    // }
    if( sz[i] < sz[j] ){                // link root of smaller tree to root of larger tree.
        id[i] = j; sz[j] += sz[i];
    } else{
        id[j] = i; sz[i] += sz[j];
    }
}

int isConnected(int p, int q)
{
    return root(p)==root(q);            // p and q are connected iff they have the same root.
}

int root(int i)
{
    while(i!=id[i]){                    // i is a root iff id[i] is equal to i ( self-reference ).
        id[i] = id[id[i]];              // set id[i] i's grandparent, no more parent.
        i = id[i];
    }
    return i;
}
```

## 算法分析及应用

### 时间复杂度

`M` union-find operations on a set of `N` objects.

<center>

| algorithm | worst-case time |
| :---: | :---: |
| quick-find | M N |
| quick-union | M N |
| weighted QU | N + M log N |
| QU + path compression| N + M log N |
| weighted QU + path compression | N + M lg\* N |

</center>

[Hopcroft-Ulman,Tarjan] 证明了从空的内含 `N` 个元素的数据结构开始，任何 `M` 次合并与查找操作都至多需要 c ( `N` + `M` lg\* `N` ) 次数组访问。其中，lg\* 是[迭代对数](https://en.wikipedia.org/wiki/Iterated_logarithm)，现实中可以看做是一个小于 5 的数。

[Fredman-Saks] 证明了并查集问题不存在线性时间算法。

- In theory, WQUPC is not quite linear.
- In practice, WQUPC is linear.

关于并查集算法，还有更多值得分析的地方... e.g. [OI Wiki](https://oi-wiki.org/ds/dsu-complexity/)

### 应用

更新中...