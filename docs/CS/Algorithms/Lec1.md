# Union-Find

## 1 动态连通性问题

Union-Find算法，也就是并查集算法，主要用于解决「动态连通性」问题。

- Union: 连接两个节点；
- Find: 查询两节点是否连通；

「连通」是一种等价关系，具有以下性质：

1. 自反性：节点`p`和`p`是连通的；
2. 对称性：如果节点`p`和`q`连通，那么`q`和`p`也连通；
3. 传递性：如果节点`p`和`q`连通，`q`和`r`连通，那么`p`和`r`也连通；


## 2 算法实现

### 2.1 Quick-Find

- Data structure：
    - Integer array `id[]` of length `N`.
    - Interpretation: `p` and `q` are connected iff they have the same id.

- Union:
    - To merge components containing `p` and `q`, change all entries whose id equals `id[p]` to `id[q]`.

- Find:
    - Check if `p` and `q` have the same id.

### 2.2 Quick-Union

Quick-Union算法用「森林」来表达图的动态连通性。

- Data structure:
    - Integer array `id[]` of length `N`.
    - Interpretation: `id[i]` is parent of `i`.
    - Root of `i` is `id[id[id[...id[i]...]]]`

- Union:
    - To merge components containing `p` and `q`, set the id of `p`'s root to the id of `q`'s root.

- Find:
    - Check if `p` and `q` have the same root.

#### 2.2.1 Weighting

Union-Find算法的一种优化方式是，通过衡量两棵树的大小(*number of objects*)来决定树的连接方式，从而避免生成过高的树状结构，甚至退化成链表。

![Weighting对树的优化效果](https://raw.githubusercontent.com/MinJoker/ImageHost/main/CS/Algorithms/1.png "Weighting对树的优化效果")

- Data structure:
    - Same as Quick-Union, but maintain extra array `sz[i]` to count number of objects in the tree rooted at `i`.

- Union:
    - Link root of smaller tree to root of larger tree.
    - Update the `sz[]` array.

#### 2.2.2 Path Compression

Union-Find算法的另一种优化方式是，由于寻找根节点时遍历了同一条路径上的所有节点，可以同时更新节点的父节点，从而将路径压缩。

- Root:
    - Make every other node in path point to its grandparent (thereby halving path length).

```c
int root(int i)
{
    while(i!=id[i]){
        id[i] = id[id[i]];      // only one extra line of code!
        i = id[i];
    }
    return i;
}
```

!!! tip
    
    No reason not to. Keep tree almost completely flat.