# 基础数据结构

## 栈和队列

### 线性表

- 数组实现
- 链表实现
    - 哨兵链表，双向循环链表
- 补充，稀疏矩阵（sparse matrix）是大部分元素均为 0 的矩阵
    - 多维数组实现，空间浪费严重
    - 多重链表（multilist）实现，也称十字链表（orthogonal linked list）
- 补充，链表的游标实现（no pointer）
    - 使用全局结构体数组来实现链表，维护其中一个分区 freelist 来实现 malloc 和 free

### 栈和队列基础

- 栈（stack）是 last-in-first-out (LIFO) 的

    ```c
    struct Stack {
        int capacity;
        int topOfStack;
        ElementType *array;
    }
    ```

- 栈的基本操作：
    - 入栈（push），将元素压入栈顶
    - 出栈（pop），将栈顶元素出栈
    - 查看栈顶元素（top）
- 队列（queue）是 first-in-first-out (FIFO) 的

    ```c
    struct Queue {
        int capacity;
        int front, rear;
        ElementType *array;
    }
    ```

- 队列的基本操作：
    - 入队（enqueue），在队尾插入元素
    - 出队（dequeue），在队首弹出元素
- 循环队列

### 中缀、后缀与前缀

- 后缀（postfix）表达式，所有操作符置于操作数的后面
    - 后缀表达式不再考虑运算符的优先级，也不需要括号
- 后缀表达式求值，$\Omicron(N)$，维护一个操作数栈
    1. 读到操作数，入栈
    2. 读到操作符，将栈顶前两个操作数依次出栈，进行运算，并把运算结果入栈
    3. 表达式读完后，将栈顶元素出栈，即为表达式的值
- 中缀表达式转后缀表达式，维护一个操作符栈
    1. 读到操作数，直接输出
    2. 读到操作符，将其与栈顶操作符比较优先级
        - pre(top) >= pre(tmp)，栈顶操作符出栈，并重复步骤 b.
        - pre(top) < pre(tmp) 或者栈为空，操作符直接入栈
        - 注意，读到左括号直接入栈，读到右括号将栈中元素依次出栈直至左括号，丢弃这对括号
        - 注意，上述针对左结合操作符，对于右结合操作符（如 $\text{\textasciicircum}$），优先级相等时直接入栈
    3. 表达式读完后，将栈中元素全部依次出栈
- 前缀（prefix）表达式，所有操作符置于操作数的前面
    - 前缀表达式不再考虑运算符的优先级，也不需要括号
- 前缀表达式求值，和后缀求值基本一致，唯一区别是前缀自右向左扫描表达式
- 中缀表达式转前缀表达式，和中缀转后缀基本一致，区别如下：
    - 自右向左扫描表达式
    - 左括号和右括号相反
    - 左结合操作符和右结合操作符相反，体现在优先级相等时的处理
- 表达式树（expression tree）是一种二叉树，叶子节点均为操作数，其余节点均为操作符
- 中缀表达式建树，维护两个栈，optr 栈存储操作符，expt 栈存储表达式树根节点
    1. 读到操作数，生成一个只有根节点的表达式树，入栈 expt
    2. 读到操作符，将其与 optr 栈顶操作符比较优先级
        - pre(top) >= pre(tmp)，栈顶操作符出栈，将 expt 栈顶前两棵树出栈，生成以该操作符为根节点、以这两棵树为左右子树的表达式树，入栈 expt；重复步骤 b.
        - pre(top) < pre(tmp) 或者栈为空，操作符直接入栈
        - 注意，读到左括号直接入栈，读到右括号将栈中元素依次出栈直至左括号，丢弃这对括号
        - 注意，上述针对左结合操作符，对于右结合操作符（如 $\text{\textasciicircum}$），优先级相等时直接入栈
    3. 表达式读完后，将 expt 栈顶元素出栈，即为表达式树

## 树

### 基础概念

- 树由根节点（root），以及 0 个或多个非空子树与根通过一条有向边（edge）连接组成
- 一棵树是 $N$ 个节点和 $N-1$ 条边的集合
- 节点的子树数量称为节点的度（degree）
- 一棵树的度是这棵树里所有节点度的最大值
- 至少有一个子树的节点称为父亲（parent）
- 父亲的每棵子树的根节点称为儿子（children）
- 有相同父亲的儿子称为兄弟（siblings）
- 没有子树的节点称为树叶（leaf）
- 从节点 $n_1$ 到 $n_k$ 的路径（path）是唯一的，其长度（length）是路径上边的数量
- 节点 $n_i$ 的深度（depth）是从根到 $n_i$ 的唯一路径的长度
- 节点 $n_i$ 的高度（height）是从 $n_i$ 到一个叶节点的最长长度
- 树的高度 / 深度 = 根节点的高度 = 最深叶节点的深度
- 一个节点的祖先（ancestor）是从根到这个节点的路径上的所有节点
- 一个节点的后裔（descendants）是这个节点的子树中的所有节点
- 树的表示：
    - 列表表示，子节点个数未知，不易表示
    - FirstChild-NextSibling 表示，因为儿子顺序不定，所以一棵树的表示方式不唯一

        ```c
        typedef struct TreeNode *PtrToNode;
        struct TreeNode {
            ElementType element;
            PtrToNode firstChild, nextSibling;
        };
        ```

    - 将 FirstChild-NextSibling 表示的树顺时针旋转 $45\degree$，可以得到二叉树

### 二叉树
    
- 二叉树（binary tree）是每个节点最多有两个儿子的树
    - 二叉树的左右两个儿子是有序的，即交换左右子树后二叉树可能会改变
- 二叉树的性质：
    - 第 $i$ 层的节点数最多为 $2^ i$
    - 高度为 $k$ 的二叉树最多有 $2^ {k+1}-1$ 个节点，此时称为完美二叉树（perfect binary tree）
    - 记 $n_0$ 表示叶节点数，$n_2$ 表示度为 $2$ 的节点数，则 $n_0 = n_2 + 1$
- 斜二叉树（skewed binary tree）
    - 所有节点都只有左子树的二叉树称为左斜树
    - 所有节点都只有右子树的二叉树称为右斜树
- 满二叉树（full binary tree）
    - 每个节点的度都是 0 或 2
- 完全二叉树（complete binary tree）
    - 只有最下面两层节点的度可以小于 2
    - 最后一层的节点都集中在左边的连续位置上
- 补充，二叉树可以通过数组来表示
    - 根为 tree[1]
    - 节点 tree[i] 的父亲为 tree[i/2]
    - 节点 tree[i] 的左儿子为 tree[2i]，右儿子为 tree[2i+1]
    - 完全二叉树的数组中节点布满 tree[1] ~ tree[n]

### 遍历

- 先序遍历（preorder traversal）
    - 根 -> 左 -> 右

        ```c
        void preorder(tree_ptr tree)
        {
            if (tree) {
                visit(tree);
                for (each child C of tree)
                    preorder(C);
            }
        }
        ```

- 后序遍历（postorder traversal）
    - 左 -> 右 -> 根

        ```c
        void postorder(tree_ptr tree)
        {
            if (tree) {
                for (each child C of tree)
                    postorder(C);
                visit(tree);
            }
        }
        ```

- 中序遍历（inorder traversal）
    - 左 -> 根 -> 右
    - 只适用于二叉树

        === "递归实现"

            ```c
            void inorder(tree_ptr tree)
            {
                if (tree) {
                    inorder(tree->left);
                    visit(tree->element);
                    inorder(tree->right);
                }
            }
            ```

        === "迭代实现"

            ```c
            void iter_inorder(tree_ptr tree)
            {
                Stack S = createStack();
                for (;;) {
                    for (; tree; tree = tree->left)
                        push(tree, S);
                    tree = top(S); pop(S);
                    if (!tree) break;
                    visit(tree->element);
                    tree = tree->right;
                }
            }
            ```

- 层序遍历（level order traversal）
    - 从上到下，从左到右

        ```c
        void levelorder(tree_ptr tree)
        {
            enqueue(tree);
            while (queue is not empty) {
                visit(T = dequeue());
                for (each child C of tree)
                    enqueue(C);
            }
        }
        ```

- 线索二叉树（threaded binary tree）
    - 如果一个节点的左儿子为空，那么它的左指针指向它的中序遍历前驱（predecessor）节点
    - 如果一个节点的右儿子为空，那么它的右指针指向它的中序遍历后继（successor）节点
    - 线索二叉树必须有一个 head node，其左儿子为根，右儿子为自身
    - 使用 head node 是为了让中序遍历的始末两个节点分别能有左儿子和右儿子（即 head node）

        ```c
        typedef struct ThreadedTreeNode *PtrToThreadedNode;
        typedef struct PtrToThreadedNode ThreadedTree;
        struct ThreadedTreeNode {
            int leftThread, rightThread;  // 0 for child, 1 for thread.
            ThreadTree left, right;
            ElementType element;
        }
        ```

- 线索二叉树能线性地遍历二叉树，从而比递归的中序遍历更快
- 使用线索二叉树能够方便地找到一个节点的父节点

### 二叉搜索树

- 二叉搜索树（binary search tree）是一种二叉树，非空时满足下列性质：
    - 每个节点都有不同的 key（整数型）
    - 一个节点的左子树中所有节点的 key 都小于该节点的 key
    - 一个节点的右子树中所有节点的 key 都大于该节点的 key
    - 左子树和右子树也都是二叉搜索树
- 二叉搜索树的中序遍历是有序的
- 查找
    - 从根节点开始，如果 key 小于当前节点的 key，往左子树找，否则往右子树找
    - 直到找到 key 相等的节点，或者找到空节点
    - 时间复杂度 $\Omicron(h)$，$h$ 为树的高度
        - 理想情况下即 $\Omicron(\log n)$，最坏情况下退化为 $\Omicron(n)$（例如斜树）
- 查询最小值：遍历到最左边的节点
- 查询最大值：遍历到最右边的节点
- 插入
    - 从根节点开始，如果 key 小于当前节点的 key，往左子树找，否则往右子树找
    - 直到找到空节点，然后插入；或者找到 key 相等的节点，忽略本次插入
    - 时间复杂度 $\Omicron(h)$，$h$ 为树的高度
- 对于同一序列，插入的顺序不同，生成的二叉搜索树的高度也不同
- 删除
    - 删除叶节点：直接删除即可
    - 删除度为 1 的节点：用唯一的儿子替代它
    - 删除度为 2 的节点：
        - 用左子树的最大值（或右子树的最小值）替代它
        - 对左子树的最大值（或右子树的最小值）执行删除操作
    - 时间复杂度 $\Omicron(h)$，$h$ 为树的高度

        ```c
        SearchTree delete(ElementType X, SearchTree T)
        {
            Position TmpCell;
            if (T == NULL) error("not found");
            else if (X < T->element) T->left = delete(X, T->left);
            else if (X > T->element) T->right = delete(X, T->right);
            else {  /* found element to be deleted */
                if (T->left && T->right) {
                    TmpCell = findMin(T->right);
                    T->element = TmpCell->element;
                    T->right = delete(T->element, T->right);
                } else {
                    TmpCell = T;
                    if (T->left == NULL) T = T->right;
                    else if (T->right == NULL) T = T->left;
                    free(TmpCell);
                }
            }
            return T;
        }
        ```

- 当删除操作不多时，可以使用懒惰（lazy）方法
    - 用 flag 标记每个节点是否被删除，访问时忽略，删除时不必 free，重新插入时不必 malloc

## 堆

- 堆（heap）也称作优先队列（priority queue）
- 二叉堆（binary heap）是一种完全二叉树，满足：
    - 父节点的 key 总是不小于（或不大于）其子节点的 key（即大根堆或小根堆）

        ```c
        struct MinHeap {
            int size;
            ElementType elements[MAX_SIZE];  // elements[0] is a sentinel.
        }
        ```

- 大根堆的根节点存有最大 key，小根堆的根节点存有最小 key
- 插入
    - 先放到最后一个位置，然后和父节点比较 key，不满足堆条件则和父节点交换
    - 直到满足堆条件为止
    - 时间复杂度 $\Omicron(\log n)$

        ```c
        void insert(ElementType X, MinHeap H)
        {
            if (isFull(H)) {
                error("full heap");
                return;
            }
            int i = ++H->size;  /* percolate up */
            for (; H->elements[i / 2] > X; i /= 2)
                H->elements[i] = H->elements[i / 2];
            H->elements[i] = X;
        }
        ```
    
- 删除
    - 先把最后一个叶节点放到根节点，然后和子节点比较 key，不满足堆条件则和最小（或最大）子节点交换
    - 直到满足堆条件为止
    - 时间复杂度 $\Omicron(\log n)$

        ```c
        ElementType deleteMin(MinHeap H)
        {
            if (isEmpty(H)) {
                error("empty heap");
                return H->elements[0];
            }
            int i, child;  /* percolate down */
            ElementType minElement = H->elements[1];
            ElementType lastElement = H->elements[H->size--];
            for (i = 1; i * 2 <= H-> size; i = child) {
                child = i * 2;
                if (child != H->size && H->elements[child + 1] < H->elements[child])
                    child++;
                if (lastElement > H->elements[child])
                    H->elements[i] = H->elements[child];
                else break;
            }
            H->elements[i] = lastElement;
            return minElement;
        }
        ```

- 向上调整（percolate up）：自下而上堆化，即这个元素可能会向上移动
    - 第 $k$ 层节点向上调整的复杂度为 $\Omicron(k)$
- 向下调整（percolate down）：自上而下堆化，即这个元素可能会向下移动
    - 第 $k$ 层节点向下调整的复杂度为 $\Omicron(\log n-k)$
- 增加或减少某个节点的 key，只需要相应地 percolate up 或 down 一次即可
- 删除某个非最大最小值节点
    - 先把这个节点的 key 增加（或减少）到最大（或最小）
    - 删除最大（或最小）节点
- 建堆
    - 从一个空的堆开始，插入 n 个元素，不考虑顺序，时间复杂度为 $\Omicron(n\log n)$
    - 直接将这个序列当作二叉树，原地调整实现堆化
        - 方法一，从根开始，按 BFS 序向上调整，时间复杂度为 $\Omicron(n\log n)$

            ```c
            void buildHeap1(void) {
                for (i = 1; i <= n; i++) percolateUp(i);
            }
            ```

        - 方法二，从最后一个叶节点开始，逐个向下调整，时间复杂度为 $\Omicron(n)$

            ```c
            void buildHeap2(void) {
                for (i = n; i >= 1; i--) percolateDown(i);
                /* for (i = n / 2 to 1) is enough */
            }
            ```

        - 方法二即线性建堆，可以理解为每次合并两个已经调整好的堆
        - 之所以能线性建对，是因为堆性质很弱（不同于排序的强条件），二叉堆不是唯一的

- d-heap，是满足堆性质的 d 叉树
    - 根为 elements[1]
    - 节点 elements[i] 的父亲为 elements[(i+d-2)/d]
    - 节点 elements[i] 的儿子为 elements[(i-1)d+2] ~ elements[id+1]
    - 插入与删除，时间复杂度为 $\Omicron(\log _{d}n)=\Omicron(\log n/\log d)$

## 并查集

- 等价关系满足：
    - 自反性（reflexive）
    - 对称性（symmetric）
    - 传递性（transitive）
- 并查集（disjoint set）用于管理元素所属集合（等价类），支持以下操作：
    - 合并（union）：合并两个集合
    - 查询（find）：查询两个元素是否属于同一集合
- 用树（森林）表示一个集合，树中的节点表示对应集合中的元素
- 用数组实现并查集
    - s[root] = 0
    - s[i] 记录 i 这个节点的父节点
- 查找

    ```c
    SetType find(ElementType X, DisjSet S) {
        for (; S[X] > 0; X = S[X]) ;
        return X;
    }
    ```

- 合并：先查找两个元素所在树的根节点，然后将其中一个根节点设为另一个根节点的儿子

    ```c
    void union(DisjSet S, ElementType X1, ElementType X2){
        S[find(X2, S)] = find(X1, S);
    }
    ```

- 路径压缩（path-compression）
    - 将查找过程中遍历到的所有节点，都直接连接到根节点

        === "递归实现"

            ```c
            SetType find(ElementType X, DisjSet S) {
                return S[X] <= 0 ? X : S[X] = find(S[X], S)
            }
            ```

        === "非递归实现"

            ```c
            SetType find(ElementType X, DisjSet S)
            {
                ElementType root, trail, lead;
                for (root = X; S[root] > 0; root = S[root]) ;
                for (trail = X; trail != root; trail = lead) {
                    lead = S[trail];
                    S[trail] = root;
                }
                return root;
            }
            ```

- 按大小合并（union-by-size）
    - 始终将小的树合并到大的树上
    - s[root] = -size，size 表示树的大小（树上的节点总数）
    - 按大小合并得到的树，高度不超过 $\lfloor \log N \rfloor +1$
    - $N$ 次 union 和 $M$ 次 find 操作的时间复杂度为 $\Omicron(N+M\log N)$
- 按高度合并（union-by-height）
    - 始终将矮的树合并到高的树上
    - 只有当两棵等高的树合并时，树的高度才会增加 1
    - $N$ 次 union 和 $M$ 次 find 操作的时间复杂度为 $\Omicron(N+M\log N)$
    - 注意，路径压缩会改变树的高度，导致 height 频繁更新
        - 可以用估计值 rank 来替代确切的 height，rank 是 height 的一个上界
        - 只在合并时更新 rank，查找操作会改变 height，但不会引起 rank 的更新
- 使用路径压缩和按秩合并的并查集，时间复杂度为 $\Theta(N+M\alpha (M,N))$
    - $\alpha$ 是 Ackermann 函数的反函数
    - $\alpha$ 的增长比迭代对数 $\log ^*$ 更缓慢

## 图

### 基础概念

- 图（graph）常用 $G(V,E)$ 表示，$V$ 和 $E$ 分别表示有限的点集和边集
- 无向图（undirected graph）：$E$ 中的元素为无序数对 $(u,v)$
- 有向图（directed graph）：$E$ 中的元素为有序数对 $\lang u,v\rang$
- 不考虑自环（self loop）和多重边（multigraph），即只考虑简单图（simple graph）
- 完全图（complete graph）：任意两点之间都有一条无向边或两条有向边
    - 相同节点数的简单图中，完全图边数最多
- $u,v$ 之间有一条无向边 $(u,v)$，称：
    - $u,v$ 是相邻的（adjacent）
- 有一条从 $u$ 到 $v$ 的有向边 $\lang u,v\rang$，称：
    - $u$ is adjacent to $v$
    - $v$ is adjacent from $u$
- 子图（subgraph）：点集和边集都是原图的子集
- 简单路径（simple path）：路径上没有重复的点
- 环（cycle）：起点和终点相同的简单路径
- 对于无向图：
    - 如果存在一条从 $u$ 到 $v$ 的路径，则称 $u,v$ 是连通的（connected）
    - 如果任意一对点都是连通的，则称这个无向图是连通的
    - 连通分量（connected component）：无向图的极大连通子图
- 对于有向图：
    - 如果存在一条从 $u$ 到 $v$ 的路径，则称从 $u$ 到 $v$ 是可达的（reachable）
    - 如果任意一对点都相互可达，则称这个有向图是强连通的（strongly connected）
    - 强连通分量：有向图的极大强连通子图
    - 如果一张有向图不是强连通的，但把它的边替换为无向边后可以得到一张连通图，则称原来这个图是弱连通的（weakly connected）
- 树是连通的无环图
- DAG（directed acyclic graph）即有向无环图
- 度（degree）
    - 节点的度被定义为与这个节点关联（incident）的边的条数
    - 对于无向图，节点 $v$ 的度等于与 $v$ 相邻的节点数
    - 对于有向图，节点 $v$：
        - 入度（in-degree）是以 $v$ 为终点的边的条数
        - 出度（out-degree）是以 $v$ 为起点的边的条数
        - 所有节点的总入度和总出度相等，有向图的总度数是总入度和总出度的和
    - 所有节点的度的和等于图的总边数的两倍，即 $\sum_{v\in V}d(v)=2|E|$
- 图的邻接矩阵表示
    - 如果存在一条从 $v_i$ 到 $v_j$ 的边，则 $a_{ij}=1$，否则 $a_{ij}=0$
    - 无向图的邻接矩阵是对称矩阵，可以只存储一半矩阵的信息：
        - $a^{\prime}[\frac{n(n+1)}{2}]=\lbrace a_{11},a_{21},a_{22},...,a_{n1},...,a_{nn} \rbrace$
    - 对于非稠密图来说非常浪费空间
- 图的邻接表表示
    - 对于每个节点，存储一个链表来记录所有和它相邻的节点
    - 无向图的邻接表会将每个边重复存储两次
    - 有向图的邻接表无法遍历入度，解决思路有：
        - 额外存储一个逆邻接表
        - 使用多重链表 multilist
- 带权图
    - 邻接矩阵：$a_{ij}$ 存储边 $(v_i,v_j)$ 的权值
    - 邻接表：每个链表节点存储边的权值

### 拓扑排序

- AOV（activity on vertex）网络是一种有向无环图，节点表示事件，边表示事件的先后关系
- 如果存在一条从 $u$ 到 $v$ 的路径，则称 $u$ 是 $v$ 的前驱（predecessor），$v$ 是 $u$ 的后继（successor）
- 如果存在一条边 $\lang u,v\rang$，则称 $u$ 是 $v$ 的直接（immediate）前驱，$v$ 是 $u$ 的直接后继
- AOV 网络中事件的先后关系是一种偏序关系，具有传递性与非自反性（无环）
- AOV 网络中不应该出现环，否则网络表示的工程是不可实现的
    - 检查 AOV 网络是否带环的方式是构造拓扑序列，观察是否包含所有节点
- 拓扑排序（topological order）是一个图的点集的线性序列，满足：
    - 对于任意一对节点 $u,v$，如果 $u$ 是 $v$ 的前驱，序列中 $u$ 排在 $v$ 的前面
- 注意，对于同一张图，拓扑排序不一定是唯一的
- 拓扑排序算法，时间复杂度为 $\Omicron(|V|+|E|)$

    ```c
    void topSort(Graph G)
    {
        Queue Q = createQueue();
        int cnt = 0; Vertex V, W;
        for (each vertex V)
            if (indegree[V] == 0) enqueue(V);
        while (!isEmpty(Q)) {
            V = dequeue(Q);
            topNum[V] = ++cnt;
            for (each W adjacent to V)
                if (--indegree[W] == 0) enqueue(W, Q);
        }
        if (cnt != numVertex)
            error("graph has a cycle");
        free(Q);
    }
    ```

### 最短路

- 单源最短路：给定一个有向图和一个源点，求从源点到图中每个点的最短路径
- 无权图的单源最短路：BFS 即可

    === "队列优化"

        ```c
        void unweighted(Table T)
        {   /* T is initialized with the source vertex S given */
            Queue Q = createQueue(); Vertex V, W;
            enqueue(S, Q);
            while (!isEmpty(Q)) {
                V = dequeue(Q);
                for (each W adjacent to V) {
                    if (T[W].dist == INFINITY) {
                        T[W].dist = T[V].dist + 1;
                        T[W].path = V;
                        enqueue(W, Q);
                    }
                }
            }
            free(Q);
        }
        ```

    === "广度优先搜索（BFS）"

        ```c
        void unweighted(Table T)
        {
            int currDist = 0; Vertex V, W;
            for (; currDist < numVertex; currDist++) {
                for (each vertex V) {
                    if (!T[V].known && T[V].dist == currDist) {
                        T[V].known = true;
                        for (each W adjacent to V) {
                            if (T[W].dist == INFINITY) {
                                T[W].dist = currDist + 1;
                                T[W].path = V;
                            }
                        }
                    }
                }
            }
        }
        ```

- 带权图的单源最短路：Dijkstra 算法（只适用于非负权图）
    1. 将节点分为两个集合：已确定最短路的点集 S 和未确定最短路的点集 T；起初所有节点都属于 T
    2. 初始化 dis[s] = 0，其他 dis = $\infty$
    3. 重复以下操作，直到 T 为空
        1. 从 T 中选取一个最短路长度最小的节点，移到 S 中
        2. 对刚刚加入 S 的节点的所有出边执行松弛（relax）操作：<br />
           对于边 (u,v)，松弛操作为 dis[v] = min(dis[v], dis[u] + weight(u,v))
- Dijkstra 算法堆优化复杂度为 $\Omicron(|E|\log |V|)$
    1. 用标记节点是否已访问来表示 S，用一个小根堆来表示 T
    2. 初始化 dis[s] = 0，其他 dis = $\infty$
    3. 重复以下操作，直到堆为空
        1. 从堆中选取 dis[u] 最小的且未被访问过的 u，将 u 标记为已访问
        2. 扫描 u 的所有出边 (u,v)，执行松弛操作：<br />
           若 dis[v] > dis[u] + weight(u,v)，则用后者更新 dis[v]，并将 {v, dis[v]} 压入堆中（或者直接更新堆中的 v 并向上调整）
    
    ```c
    void Dijkstra(Table T)
    {
        Heap H = createHeap(T); Vertex V, W;
        while (!isEmpty(H)) {
            V = deleteMin(H);
            if (T[V].known) continue;
            T[V].known = true;
            for (each W adjacent to V) {
                if (T[W].dist > T[V].dist + weight(V, W)) {
                    T[W].dist = T[V].dist + weight(V, W);
                    T[W].path = V;
                    insert({W, T[W].dist}, H);
                    /* or: decrease(T[W].dist to T[V].dist + weight(V, W)) */
                }
            }
        }
        free(H);
    }
    ```

- 带负权边：SPFA 算法，复杂度为 $\Omicron(|E||V|)$
    - 上文无权图单源最短路问题的队列优化算法，可以看作 SPFA 的特殊情形

    ```c
    void negweighted(Table T)
    {   /* T is initialized with the source vertex S given */
        Queue Q = createQueue(); Vertex V, W;
        enqueue(S, Q);
        while (!isEmpty(Q)) {
            V = dequeue(Q);
            for (each W adjacent to V) {
                if (T[W].dist > T[V].dist + weight(V, W)) {
                    T[W].dist = T[V].dist + weight(V, W);
                    T[W].path = V;
                    if (W is not already in Q) enqueue(W, Q);
                }
            }
        }
        free(Q);
    }
    ```

- AOE（activity on edge）网络是一个带权的有向无环图，节点表示事件，带权边表示活动持续的时间
- AOE 网络用于解决工程最短时间问题
    - 完成整个工程的最短时间是从源点到汇点的最长活动路径长度
- 每个节点代表的事件存在一个最早完成时间（EC）和最晚完成时间（LC）
    - EC 从源点开始向后计算，$EC[w]=max_{\lang v,w\rang \in E}\lbrace EC[v] + weight(v,w)\rbrace$
    - LC 从汇点开始向前计算，$LC[w]=min_{\lang v,w\rang \in E}\lbrace LC[v] - weight(v,w)\rbrace$
- 每条边代表的活动存在一个持续时间（即边权）和一个松弛时间（slack time）
    - 边 $\lang v,w\rang$ 的松弛时间为 $LC[w]-EC[v]-weight(v,w)$
- 关键路径（critical path）是 AOE 网络中的最长活动路径
    - 关键路径中所有边的松弛时间都是零
- 任意两点间最短路
    - 以每个节点作为源点，分别计算单源最短路，适合于稀疏图
    - Floyd 算法，三个 for 循环即可实现，适合于任何图（无负环）
        - 定义 f[k][v][w] 表示在只允许经过节点 1~k 的限制下，从节点 v 到 w 的最短路
        - f[k][v][w] = min(f[k-1][v][w], f[k][v][k] + f[k][k][w])

### 网络流

- 网络流定义在一个有向图上：
    - 每条边都存在一个被称为容量（capacity）的权值，当 $(u,v)\notin E$ 时，定义 $c(u,v)=0$
    - 网络中存在唯一的源点（source）和唯一的汇点（sink）
- 最大流问题：给定一个有向图，求从源点到汇点的最大流量
    1. 建立残差图（residual graph），设 $f$ 是图 $G=(V,E)$ 的一个流，则残差图的边权为：
        
        $$
        c_f(u,v)=\begin{cases}
        c(u,v)-f(u,v)\;\;&(u,v)\in E \cr
        f(v,u)&(v,u)\in E \cr
        0&\text{else}
        \end{cases}
        $$
    
    2. 重复进行如下的增广操作，不断叠加网络的流，直到找不到增广路为止：
        - 寻找残差图中从源点到汇点的一条路径，称为增广路（augmenting path）
        - 增广路的流量为增广路上的最小边权，创建这样一条流，并更新残差图

- 寻找增广路
    - BFS，时间复杂度为 $\Omicron(|V||E|^ 2)$
    - Dinic 算法，当前弧优化下时间复杂度为 $\Omicron(|V|^ 2|E|)$
- 最小费用流：费用指流经过边的代价，对于所有可能的最大流，求其中总费用最小的一条流

### 最小生成树

- 生成树（spanning tree）是连通无向图的一个生成子图，同时满足树的特征
    - 生成子图是图的一个子图，包含原图的所有节点
    - 生成树相当于在原图的边集中选择 n-1 条，将所有节点连通
- 最小生成树：给定一个连通无向图，找一个边权和最小的生成树
- 连通图一定存在一个最小生成树
- 向生成树中添加一个额外的边，则一定会存在一个环
- Prim 算法
    - 从一个节点开始，不断向 T 中添加节点，直到包含所有节点
    - 添加节点时，总是选择与 T 中的某个节点相邻且加入 T 后不会形成环的最小权边对应的节点
    - 寻找最小边权节点，可以进行堆优化（类似于 Dijkstra 算法）
- Kruskal 算法
    - 按边权从小到大向 T 中添加边，直到加满 n-1 条边
    - 添加边时，总是选择加入 T 后不会形成环的最小权边
    - 可以通过并查集来维护，时间复杂度为 $\Omicron(|E|\log |E|)$
        - 按边权从小到大遍历所有边，若边两端的节点不在同一集合，则将它们合并，并将边加入 T
- Prim 算法适合稠密图（dense graph），Kruskal 算法适合稀疏图（sparse graph）

### 深度优先搜索

- 深度优先搜索（DFS）是一种用于遍历或搜索树或图的算法
- 深度优先搜索是先序遍历的一种推广

    ```c
    void dfs(Vertex V) {
        visited[V] = true;
        for (each W adjacent to V)
            if (!visited[W]) dfs(W);
    }
    ```

- 双连通性（biconnectivity）
    - 如果删去一个点，剩下的图可以分成至少两个连通分量，则称这个点是割点（articulation point）
    - 如果一个图是连通的，且不存在割点，则称这个图是双连通的
    - 双连通分量（biconnected component）是极大的双连通子图
- 寻找连通无向图的割点：Tarjan 算法
    - 首先对原图进行 DFS，得到一个生成树
        - dfn[x] 表示 x 在 DFS 中是第几个被访问的，从 0 开始
    - 追溯值 low[x]
        - 初始 low[x] = dfn[x]
        - 如果从 x 到 y 的边在生成树上，则 low[x] = min(low[x], low[y])
        - 如果从 x 到 y 的边不在生成树上（回边），则 low[x] = min(low[x], dfn[y])
    - 判断割点
        - 如果 u 是生成树的根，则 u 是割点当且仅当 u 有至少两个儿子
        - 如果 u 不是生成树的根，则 u 是割点当且仅当存在一个儿子 v，满足 dfn[u] <= low[v]
- 欧拉回路（Euler circuit）是通过图中每条边恰好一次的环
- 欧拉通路（Euler tour）是通过图中每条边恰好一次的路径
- 对于无向图
    - 无向图有欧拉回路当且仅当图是连通的且每个节点的度都是偶数
    - 无向图有欧拉通路当且仅当图是连通的且有且仅有两个节点的度是奇数
- 对于有向图
    - 有向图有欧拉回路当且仅当图是弱连通的且每个节点出度等于入度
    - 有向图有欧拉通路当且仅当图是弱连通的且有且仅有一个节点的出度比入度大 1，有且仅有一个节点的入度比出度大 1，其余节点出度等于入度
- 寻找欧拉回路或通路：DFS
    - 从某个节点出发，进行 DFS
    - 每次沿着某条边从一个节点移动到另一个节点时，删除这条边
    - 如果某个节点没有可走的边，则将该节点入队，并返回
    - DFS 结束后，队列中的节点序列即欧拉回路或通路

    ```c
    void dfs(Vertex V) {
        for (each W adjacent to V) {
            if (!visited(V, W)) {
                visited(V, W) = true;
                dfs(W);
            }
        }
        enqueue(V);
    }
    ```

- 哈密顿回路（Hamilton circuit）是通过图中所有节点恰好一次的环
- 哈密顿通路（Hamilton tour）是通过图中所有节点恰好一次的路径