# Algorithms I

<style>
    .md-sidebar--secondary .md-nav > .md-nav__list > li > a + .md-nav {
      display: none;
    }
    .md-sidebar--secondary .md-nav > .md-nav__list > li > a.is-active + .md-nav {
      display: block;
    }
</style>
    
## 并查集

- 并查集是一种数据结构，支持如下操作：
    - 合并（union）：合并两个元素所属集合
    - 查询（find）：查询某个元素属于哪个集合，用以判断两个元素是否属于同一集合
- 并查集在数学上基于等价关系和等价类
    - 具有等价关系的问题，都可以抽象成并查集模型
    - 合并与查询就是对元素所属等价类的合并与查询

### 实现

- 思路一：直接用数组 id[] 标记元素所属集合，id[] 值相同的元素属于同一集合
    - 合并操作开销很大（需要遍历数组）
- 思路二：构建森林，用数组 id[] 标记元素的父节点，同一棵树上的元素属于同一集合
    - 树有可能变得很高，导致查询操作开销很大（寻找根节点可能需要遍历数组）
- 优化一：加权合并
    - 总是把树的节点数量 size 小的树合并到 size 大的树上
        - $N$ 个元素生成的树的高度不会超过 $\log N$
        - 用 size 作为权值不够理想，因为加权合并要解决的问题关键在于树的高度，节点数量大小不一定能反映树的高度大小
    - 总是把树的高度 height 小的树合并到 height 大的树上
        - 看起来似乎没问题，但如果同时使用了路径压缩，height 会很难维护，因为查询的时候发生了路径压缩，会多次改变树的高度
    - 总是把树的秩 rank 小的树合并到 rank 大的树上
        - rank 只在合并两棵 rank 一样的树的时候更新，并且每次增加 1
        - 按秩合并是按高度合并的一种优化版本
- 优化二：路径压缩
    - 寻找元素的根节点时，将遍历到的所有节点 id[] 直接指向根节点
        - 需要递归或者再次遍历实现
    - 寻找元素的根节点时，将遍历到的所有节点 id[] 指向其父节点的父节点
        - 较为简单，但表现不错，每次寻找根节点可以将路径长度减半

### 代码

??? quote "Java Implementation"

    ```java linenums="1" title="Union-Find with Union-by-Rank and Path-Compression"
    package edu.princeton.cs.algs4;

    public class UF {

        private int[] parent;  // parent[i] = parent of i
        private byte[] rank;   // rank[i] = rank of subtree rooted at i (never more than 31)
        private int count;     // number of components

        public UF(int n) {
            if (n < 0) throw new IllegalArgumentException();
            count = n;
            parent = new int[n];
            rank = new byte[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                rank[i] = 0;
            }
        }

        public int find(int p) {
            validate(p);
            while (p != parent[p]) {
                parent[p] = parent[parent[p]];    // path compression by halving
                p = parent[p];
            }
            return p;
        }

        public int count() {
            return count;
        }

        /**
         * @deprecated Replace with two calls to {@link #find(int)}
         */
        @Deprecated
        public boolean connected(int p, int q) {
            return find(p) == find(q);
        }

        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;

            // make root of smaller rank point to root of larger rank
            if      (rank[rootP] < rank[rootQ]) parent[rootP] = rootQ;
            else if (rank[rootP] > rank[rootQ]) parent[rootQ] = rootP;
            else {
                parent[rootQ] = rootP;
                rank[rootP]++;
            }
            count--;
        }

        // validate that p is a valid index
        private void validate(int p) {
            int n = parent.length;
            if (p < 0 || p >= n) {
                throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n-1));
            }
        }
    }
    ```

### 复杂度分析

- Hopcroft-Ulman，Tarjan：从空的内含 $N$ 个元素的数据结构开始，任何 $M$ 次合并与查找操作都至多需要 $c(N+M \lg^{\*} N)$ 次数组访问
    - $\lg^{\*}$ 是[迭代对数](https://en.wikipedia.org/wiki/Iterated_logarithm)，现实中可以看做是一个小于 $5$ 的数
    - 复杂度分析可进一步精确到 $N+M\alpha (M,N)$，$\alpha$ 是 Ackermann 函数的反函数，证明可以参考[这里](https://oi-wiki.org/ds/dsu-complexity/)
- Fredman-Saks：并查集问题没有线性时间复杂度算法
- - [ ] 上述复杂度分析的证明

### 思考题

- 设计并查集，使其支持查询任一元素所在集合的最大元，且合并、查询操作不能超过对数时间复杂度
    - 思路一：额外用一个数组存储每个集合的最大元
    - 思路二：总是把根节点小的树合并到根节点大的树上，查询根节点即可得到最大元
        - 实际上每棵树都是一个堆（优先队列）
        - 树有可能变得很高，导致查询开销超过对数时间复杂度
    - 思路三：加权合并，每次合并比较两棵树的根节点大小，如果较大的根节点将被合并到另一棵树上，则交换两个根节点
        - 是对思路二的一种优化，每棵树并不是堆，但是根节点存储最大元

## 栈和队列

- 栈和队列都是线性的数据结构
    - 栈是后进先出的（LIFO: last in first out）
    - 队列是先进先出的（FIFO: first in first out）

### 实现

- 链表实现
- 可变数组实现
    - 当数组满了的时候，新建一个两倍大小的数组，并把数据迁移到新数组
    - 当数组四分之一满的时候，新建一个一半大小的数组，并把数据迁移到新数组
        - 当数组半满的时候 resize 是不合理的，比如在半满位置反复执行 push 和 pop 操作会导致反复 resize，效率极低
- 链表：
    - 每个操作总是消耗常数时间，即使是在最坏的情况下；
    - 需要消耗额外的时间和空间来处理链表结构；
- 可变数组：
    - 均摊分析后具有更高的平均效率，但是消耗会集中在在临界状态（需要倍增或倍减数组时）发生；
    - 浪费更少的空间；

### 代码

- 注意：以下栈和队列的实现涉及 generic 泛型和 iterable 接口等 Java 特性

??? quote "Stack: Java Implementation"

    ```java linenums="1" title="Linked-List Stack of Generic Items"
    package edu.princeton.cs.algs4;
    import java.util.Iterator;
    import java.util.NoSuchElementException;

    public class Stack<Item> implements Iterable<Item> {
        private Node<Item> first;     // top of stack
        private int n;                // size of the stack

        // helper linked list class
        private static class Node<Item> {
            private Item item;
            private Node<Item> next;
        }

        public Stack() {
            first = null;
            n = 0;
        }

        public boolean isEmpty() {
            return first == null;
        }

        public int size() {
            return n;
        }

        public void push(Item item) {
            Node<Item> oldfirst = first;
            first = new Node<Item>();
            first.item = item;
            first.next = oldfirst;
            n++;
        }

        public Item pop() {
            if (isEmpty()) throw new NoSuchElementException("Stack underflow");
            Item item = first.item;        // save item to return
            first = first.next;            // delete first node
            n--;
            return item;                   // return the saved item
        }

        public Item peek() {
            if (isEmpty()) throw new NoSuchElementException("Stack underflow");
            return first.item;
        }

        //returns the sequence of items in this stack in LIFO order, separated by spaces
        public String toString() {
            StringBuilder s = new StringBuilder();
            for (Item item : this) {
                s.append(item);
                s.append(' ');
            }
            return s.toString();
        }

        //returns an iterator that iterates through the items in this stack in LIFO order.
        public Iterator<Item> iterator() {
            return new LinkedIterator(first);
        }

        // the iterator
        private class LinkedIterator implements Iterator<Item> {
            private Node<Item> current;

            public LinkedIterator(Node<Item> first) {
                current = first;
            }

            // is there a next item?
            public boolean hasNext() {
                return current != null;
            }

            // returns the next item
            public Item next() {
                if (!hasNext()) throw new NoSuchElementException();
                Item item = current.item;
                current = current.next;
                return item;
            }
        }
    }
    ```

??? quote "Queue: Java Implementation"

    ```java linenums="1" title="Linked-List Queue of Generic Items"
    package edu.princeton.cs.algs4;
    import java.util.Iterator;
    import java.util.NoSuchElementException;

    public class Queue<Item> implements Iterable<Item> {
        private Node<Item> first;    // beginning of queue
        private Node<Item> last;     // end of queue
        private int n;               // number of elements on queue

        // helper linked list class
        private static class Node<Item> {
            private Item item;
            private Node<Item> next;
        }

        public Queue() {
            first = null;
            last  = null;
            n = 0;
        }

        public boolean isEmpty() {
            return first == null;
        }

        public int size() {
            return n;
        }

        public Item peek() {
            if (isEmpty()) throw new NoSuchElementException("Queue underflow");
            return first.item;
        }

        public void enqueue(Item item) {
            Node<Item> oldlast = last;
            last = new Node<Item>();
            last.item = item;
            last.next = null;
            if (isEmpty()) first = last;
            else           oldlast.next = last;
            n++;
        }

        public Item dequeue() {
            if (isEmpty()) throw new NoSuchElementException("Queue underflow");
            Item item = first.item;
            first = first.next;
            n--;
            if (isEmpty()) last = null;   // to avoid loitering
            return item;
        }

        //returns the sequence of items in FIFO order, separated by spaces
        public String toString() {
            StringBuilder s = new StringBuilder();
            for (Item item : this) {
                s.append(item);
                s.append(' ');
            }
            return s.toString();
        }

        //returns an iterator that iterates through the items in this queue in FIFO order.
        public Iterator<Item> iterator()  {
            return new LinkedIterator(first);
        }

        // the iterator
        private class LinkedIterator implements Iterator<Item> {
            private Node<Item> current;

            public LinkedIterator(Node<Item> first) {
                current = first;
            }

            // is there a next item?
            public boolean hasNext() {
                return current != null;
            }

            // returns the next item
            public Item next() {
                if (!hasNext()) throw new NoSuchElementException();
                Item item = current.item;
                current = current.next;
                return item;
            }
        }
    }
    ```

## 排序基础

- 排序算法的前提是数据是可排序的，在数学上基于全序关系

## 希尔排序

- 希尔排序基于一个增量序列，不断对原始数据按一定间隔取子序列进行排序
- 为什么希尔排序对于每个子序列的具体排序算法是插入排序？
    - 对于增量序列中的较大值，取出的每个子序列中数据数量都很少，小规模数据插入排序的表现并不差
    - 对于增量序列中的较小值，此时的数据已经几乎有序了，插入排序的表现非常好
        - 基于一个事实：大增量有序的数据在小增量排序后仍然是大增量有序的
- 希尔排序的时间复杂度和增量序列的选取有关
    - Knuth：1,4,13,40,121,364,...
        - 比较容易计算实现，最坏情况比较次数为 $\Omicron(N^ {3/2})$
    - Sedgewick：1,5,19,41,109,209,...
        - 在现有研究中几乎是表现最好的
- 希尔排序在实际应用中很有价值
    - 对于小规模的数据，希尔排序的速度非常快
    - 希尔排序的代码规模很小，常用于嵌入式系统等