# 基础数据结构

## 栈和队列

### 线性表基础

- 数组实现
- 链表实现
    - 哨兵链表，双向循环链表
- 补充，稀疏矩阵（sparse matrix）是大部分元素均为 0 的矩阵
    - 多维数组实现，空间浪费严重
    - 多重链表（multilist）实现，也称十字链表（orthogonal linked list）
- 补充，链表的游标实现（no pointer）
    - 使用全局结构体数组来实现链表，维护其中一个分区 freelist 来实现 malloc 和 free

### 