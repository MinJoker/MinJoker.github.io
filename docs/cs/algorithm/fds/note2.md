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
- 有不止一个子树的节点称为父亲（parent）
- 父亲的每棵子树的根节点称为儿子（children）
- 有相同父亲的儿子称为兄弟（siblings）
- 没有子树的节点称为树叶（leaf）
- 从节点 $n_1$ 到 $n_k$ 的路径（path）是唯一的，其长度（length）是路径上边的数量
- 节点 $n_i$ 的深度（depth）是从根到 $n_i$ 的唯一路径的长度
- 节点 $n_i$ 的高度（height）是从 $n_i$ 到一个叶节点的最长长度
- 树的高度 / 深度 = 根节点的高度 = 最深叶节点的深度
- 一个节点的祖先（ancestor）是从根到这个节点的路径上的所有节点
- 一个节点的后裔（descendants）是这个节点的子树中的所有节点

### 二叉树

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
    
- 二叉树（binary tree）是每个节点最多有两个儿子的树
    - 二叉树的左右两个儿子是有序的，即交换左右子树后二叉树可能会改变
- 二叉树的性质：
    - 第 $i$ 层的节点数最多为 $2^ {i-1}$
    - 深度为 $k$ 的二叉树最多有 $2^ k$ 个节点
    - 记 $n_0$ 表示叶节点数，$n_2$ 表示度为 $2$ 的节点数，则 $n_0 = n_2 + 1$
- 斜二叉树（skewed binary tree）
    - 所有节点都只有左子树的二叉树称为左斜树
    - 所有节点都只有右子树的二叉树称为右斜树
- 满二叉树（full binary tree）
    - 最后一层都是叶节点，其余每层上的所有节点都有两个子树
    - 一个二叉树是满的，当且仅当其有 $k$ 层和 $2^ k-1$ 个节点
- 完全二叉树（complete binary tree）
    - 除了最后一层，每一层都是满的
    - 最后一层的节点靠左排列
- 补充，二叉树可以通过数组来表示
    - 根为 tree[1]
    - 节点 tree[i] 的左儿子为 tree[2i]，右儿子为 tree[2i+1]
    - 完全二叉树的数组中节点布满 1 ~ n

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
- 查找：
    - 从根节点开始，如果 key 小于当前节点的 key，往左子树找，否则往右子树找
    - 直到找到 key 相等的节点，或者找到空节点
    - 时间复杂度 $\Omicron(h)$，$h$ 为树的高度，即 $\Omicron(\log N)$
- 查询最小值：遍历到最左边的节点
- 查询最大值：遍历到最右边的节点
- 插入：
    - 从根节点开始，如果 key 小于当前节点的 key，往左子树找，否则往右子树找
    - 直到找到空节点，然后插入；或者找到 key 相等的节点，忽略本次插入
    - 时间复杂度 $\Omicron(h)$，$h$ 为树的高度，即 $\Omicron(\log N)$
- 对于同一序列，插入的顺序不同，生成的二叉搜索树的高度也不同
- 删除：
    - 删除叶节点：直接删除即可
    - 删除度为 1 的节点：用唯一的儿子替代它
    - 删除度为 2 的节点：
        - 用左子树的最大值（或右子树的最小值）替代它
        - 对左子树的最大值（或右子树的最小值）执行删除操作
    - 时间复杂度 $\Omicron(h)$，$h$ 为树的高度，即 $\Omicron(\log N)$

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