# Union-Find

## 1. 动态连通性问题

Union-Find算法，也就是并查集算法，主要用于解决「动态连通性」问题。

- union：连接两个节点；
- find：查询两节点是否连通；

「连通」是一种等价关系，具有以下性质：
1. 自反性：节点`p`和`p`是连通的；
2. 对称性：如果节点`p`和`q`连通，那么`q`和`p`也连通；
3. 传递性：如果节点`p`和`q`连通，`q`和`r`连通，那么`p`和`r`也连通；

Union-Find算法的API：
```c
void UF(int N);                      // initialize union-find data structure
void union_command(int p, int q);    // add connection between p and q
int connected(int p, int q);         // check if p and q are in the same component
```

## 2. 算法实现

### 2.1 quick-find

### 2.2 quick-union

### 2.3 improvements

#### 2.3.1 weighting

#### 2.3.2 path compression