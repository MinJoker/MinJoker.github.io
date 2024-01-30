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

??? quote "Stack: Java Implementation"

    ```java linenums="1" title="Linked-List Stack of Generic Items"
    package edu.princeton.cs.algs4;
    import java.util.NoSuchElementException;

    public class Stack<Item> {
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
    }
    ```

??? quote "Queue: Java Implementation"

    ```java linenums="1" title="Linked-List Queue of Generic Items"
    package edu.princeton.cs.algs4;
    import java.util.NoSuchElementException;

    public class Queue<Item> {
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
    }
    ```

### 思考题

- 栈和队列可以相互实现
    - 用双栈实现一个队列，要求出入队操作的均摊开销为常数时间
    - 用双队列实现一个栈，要求出入栈操作的均摊开销为常数时间

## 排序基础

- 排序算法的前提是数据是可排序的，在数学上基于全序关系
- 分享一个很有意思的排序算法可视化网站：<br />
  [https://www.toptal.com/developers/sorting-algorithms](https://www.toptal.com/developers/sorting-algorithms){target="_blank"}

### 排序复杂度

- 基于比较的排序算法在最坏情况下至少需要 $\log (N!) \sim N\log N$ 次比较
    - 证明：决策树 + [Stirling 近似](https://en.wikipedia.org/wiki/Stirling%27s_approximation){target="_blank"}

## 希尔排序

- 希尔排序基于一个增量序列，不断对原始数据按一定间隔取子序列进行排序
- 为什么希尔排序对于每个子序列的具体排序算法是插入排序？
    - 对于增量序列中的较大值，取出的每个子序列中数据数量都很少，小规模数据插入排序的表现并不差
    - 对于增量序列中的较小值，此时的数据已经几乎有序了，插入排序的表现非常好
        - 基于一个事实：大增量有序的数据在小增量排序后仍然是大增量有序的，证明可以参考[这里](https://oi-wiki.org/basic/shell-sort/#%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6){target="_blank"}

### 代码

??? quote "Java Implementation"

    ```java linenums="1" title="Shell Sort with Knuth's Increment Sequence"
    package edu.princeton.cs.algs4;

    public class Shell {
        // This class should not be instantiated.
        private Shell() { }

        public static void sort(Comparable[] a) {
            int n = a.length;

            // 3x+1 increment sequence:  1, 4, 13, 40, 121, 364, 1093, ...
            int h = 1;
            while (h < n/3) h = 3*h + 1;

            while (h >= 1) {
                // h-sort the array
                for (int i = h; i < n; i++) {
                    for (int j = i; j >= h && less(a[j], a[j-h]); j -= h) {
                        exch(a, j, j-h);
                    }
                }
                h /= 3;
            }
        }

        // is v < w ?
        private static boolean less(Comparable v, Comparable w) {
            return v.compareTo(w) < 0;
        }

        // exchange a[i] and a[j]
        private static void exch(Object[] a, int i, int j) {
            Object swap = a[i];
            a[i] = a[j];
            a[j] = swap;
        }
    }
    ```

### 复杂度分析

- 希尔排序的时间复杂度和增量序列的选取有关
    - Knuth：1,4,13,40,121,364,...
        - $h_{k}=3h_{k-1}+1$
        - 比较容易计算实现，最坏情况比较次数为 $\Omicron(N^ {3/2})$
    - Sedgewick：1,5,19,41,109,209,...
        - 奇偶交替由 $(9\times 4^ i)-(9\times 2^ i)+1$ 和 $4^ i-(3\times 2^ i)+1$ 计算得到
        - 在现有研究中几乎是表现最好的
- 希尔排序的最佳增量序列和最佳时间复杂度仍然是一个尚未解决的开放性问题

### 应用

- 希尔排序在实际应用中很有价值
    - 对于小规模的数据，希尔排序的速度非常快
    - 希尔排序的代码规模很小，常用于嵌入式系统等

## 归并排序

- 归并排序是一种高效的、通用的、稳定的基于比较的算法
- 关于归并排序的研究已经比较成熟，应用也非常广泛
- 通常的归并排序需要 $\Omicron(N)$ 的辅助空间，但已经有很多研究提出了空间复杂度为 $\Omicron(1)$ 的原地归并，但它们的实现都非常繁琐复杂，在现实中很少被应用

### 实现

- 自上而下（top-down）递归实现
    - 优化一：递归到小规模数据时改用插入排序
    - 优化二：如果待合并的两串数据已经有序，则直接返回，减少合并操作的开销
        - 比较前一串数据的最大值和后一串数据的最小值
        - 处理几乎有序的数组效果很好
    - 优化三：每次递归时交换输入数组和辅助数组的身份，可以节省多次复制数组的时间开销
        - 未优化时，每次合并操作都需要进行一次完整的数组复制，从而保证合并后的数据总是存储在输入数组中
        - 优化后，合并操作不再需要数组复制，只需要在排序之初将输入数组完整复制到辅助数组，并不断交换两者的身份即可；自上而下递归能保证最终有序的数据存储在输入数组中
    - 下文的代码同时实现了这三种优化
- 自下而上（bottom-up）迭代实现
    - 一般情况下，自下而上迭代实现的归并排序要比自上而下递归实现的归并排序慢 10%

### 代码

??? quote "Java Implementation"

    ```java linenums="1" title="Merge Sort with Optimization"
    package edu.princeton.cs.algs4;

    public class Merge {
        private static final int CUTOFF = 7;  // cutoff to insertion sort

        // This class should not be instantiated.
        private Merge() { }

        private static void merge(Comparable[] src, Comparable[] dst, int lo, int mid, int hi) {
            int i = lo, j = mid+1;
            for (int k = lo; k <= hi; k++) {
                if      (i > mid)              dst[k] = src[j++];
                else if (j > hi)               dst[k] = src[i++];
                else if (less(src[j], src[i])) dst[k] = src[j++];   // to ensure stability
                else                           dst[k] = src[i++];
            }
        }

        private static void sort(Comparable[] src, Comparable[] dst, int lo, int hi) {
            // if (hi <= lo) return;
            if (hi <= lo + CUTOFF) {
                insertionSort(dst, lo, hi);
                return;
            }
            int mid = lo + (hi - lo) / 2;
            sort(dst, src, lo, mid);
            sort(dst, src, mid+1, hi);

            // if (!less(src[mid+1], src[mid])) {
            //    for (int i = lo; i <= hi; i++) dst[i] = src[i];
            //    return;
            // }

            // using System.arraycopy() is a bit faster than the above loop
            if (!less(src[mid+1], src[mid])) {
                System.arraycopy(src, lo, dst, lo, hi - lo + 1);
                return;
            }

            merge(src, dst, lo, mid, hi);
        }

        public static void sort(Comparable[] a) {
            Comparable[] aux = a.clone();
            sort(aux, a, 0, a.length-1);
        }

        // sort from a[lo] to a[hi] using insertion sort
        private static void insertionSort(Comparable[] a, int lo, int hi) {
            for (int i = lo; i <= hi; i++)
                for (int j = i; j > lo && less(a[j], a[j-1]); j--)
                    exch(a, j, j-1);
        }

        // exchange a[i] and a[j]
        private static void exch(Object[] a, int i, int j) {
            Object swap = a[i];
            a[i] = a[j];
            a[j] = swap;
        }

        // is a[i] < a[j]?
        private static boolean less(Comparable a, Comparable b) {
            return a.compareTo(b) < 0;
        }
    }
    ```

### 思考题

- 用归并排序计算数据中逆序对的数量
    - 每次合并操作中，后段首元素被作为当前最小值取出时，前段剩余元素个数即为这次合并操作减少的逆序对数

## 快速排序

- 快速排序被誉为二十世纪最伟大的十大算法之一
- 关于快速排序的研究已经比较成熟，应用也非常广泛

### 实现

### 复杂度分析

### 选择算法

### 重复键值

### 代码

??? quote "Quick Sort & Quick Selection: Java Implementation"

    ```java linenums="1" title="Quick Sort and Quick Selection"
    package edu.princeton.cs.algs4;

    public class Quick {
        // This class should not be instantiated.
        private Quick() { }

        public static void sort(Comparable[] a) {
            StdRandom.shuffle(a);
            sort(a, 0, a.length - 1);
        }

        // quicksort the subarray from a[lo] to a[hi]
        private static void sort(Comparable[] a, int lo, int hi) {
            if (hi <= lo) return;
            int j = partition(a, lo, hi);
            sort(a, lo, j-1);
            sort(a, j+1, hi);
        }

        // partition the subarray a[lo..hi] so that a[lo..j-1] <= a[j] <= a[j+1..hi]
        // and return the index j.
        private static int partition(Comparable[] a, int lo, int hi) {
            int i = lo;
            int j = hi + 1;
            Comparable v = a[lo];
            while (true) {
                // find item on lo to swap
                while (less(a[++i], v)) {
                    if (i == hi) break;
                }
                // find item on hi to swap
                while (less(v, a[--j])) {
                    if (j == lo) break;      // redundant since a[lo] acts as sentinel
                }
                // check if pointers cross
                if (i >= j) break;
                exch(a, i, j);
            }
            // put partitioning item v at a[j]
            exch(a, lo, j);
            // now, a[lo .. j-1] <= a[j] <= a[j+1 .. hi]
            return j;
        }

        /**
         * Rearranges the array so that {@code a[k]} contains the kth smallest key;
         * {@code a[0]} through {@code a[k-1]} are less than (or equal to) {@code a[k]}; and
         * {@code a[k+1]} through {@code a[n-1]} are greater than (or equal to) {@code a[k]}.
         *
         * @return the key of rank {@code k}
         */
        public static Comparable select(Comparable[] a, int k) {
            if (k < 0 || k >= a.length) {
                throw new IllegalArgumentException("index is not between 0 and " + a.length + ": " + k);
            }
            StdRandom.shuffle(a);
            int lo = 0, hi = a.length - 1;
            while (hi > lo) {
                int i = partition(a, lo, hi);
                if      (i > k) hi = i - 1;
                else if (i < k) lo = i + 1;
                else return a[i];
            }
            return a[lo];
        }

        // is v < w ?
        private static boolean less(Comparable v, Comparable w) {
            if (v == w) return false;   // optimization when reference equals
            return v.compareTo(w) < 0;
        }

        // exchange a[i] and a[j]
        private static void exch(Object[] a, int i, int j) {
            Object swap = a[i];
            a[i] = a[j];
            a[j] = swap;
        }
    }
    ```

??? quote "Quick Sort with 3-way Partitioning: Java Implementation"

    ```java linenums="1" title="Quick Sort with Dijkstra 3-way Partitioning"
    package edu.princeton.cs.algs4;

    public class Quick3way {
        // This class should not be instantiated.
        private Quick3way() { }

        public static void sort(Comparable[] a) {
            StdRandom.shuffle(a);
            sort(a, 0, a.length - 1);
        }

        // quicksort the subarray a[lo .. hi] using 3-way partitioning
        private static void sort(Comparable[] a, int lo, int hi) {
            if (hi <= lo) return;
            int lt = lo, gt = hi;
            Comparable v = a[lo];
            int i = lo + 1;
            while (i <= gt) {
                int cmp = a[i].compareTo(v);
                if      (cmp < 0) exch(a, lt++, i++);
                else if (cmp > 0) exch(a, i, gt--);
                else              i++;
            }

            // a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi].
            sort(a, lo, lt-1);
            sort(a, gt+1, hi);
        }

        // is v < w ?
        private static boolean less(Comparable v, Comparable w) {
            return v.compareTo(w) < 0;
        }

        // exchange a[i] and a[j]
        private static void exch(Object[] a, int i, int j) {
            Object swap = a[i];
            a[i] = a[j];
            a[j] = swap;
        }
    }
    ```

??? quote "Quick Sort with Bentley-McIlroy 3-way Partitioning: Java Implementation"

    ```java linenums="1" title="Quick Sort with Bentley-McIlroy 3-way Partitioning"
    package edu.princeton.cs.algs4;

    public class QuickBentleyMcIlroy {
        // cutoff to insertion sort, must be >= 1
        private static final int INSERTION_SORT_CUTOFF = 8;
        // cutoff to median-of-3 partitioning
        private static final int MEDIAN_OF_3_CUTOFF = 40;

        // This class should not be instantiated.
        private QuickBentleyMcIlroy() { }

        public static void sort(Comparable[] a) {
            sort(a, 0, a.length - 1);
        }

        private static void sort(Comparable[] a, int lo, int hi) {
            int n = hi - lo + 1;

            // cutoff to insertion sort
            if (n <= INSERTION_SORT_CUTOFF) {
                insertionSort(a, lo, hi);
                return;
            }

            // use median-of-3 as partitioning element
            else if (n <= MEDIAN_OF_3_CUTOFF) {
                int m = median3(a, lo, lo + n/2, hi);
                exch(a, m, lo);
            }

            // use Tukey ninther as partitioning element
            else  {
                int eps = n/8;
                int mid = lo + n/2;
                int m1 = median3(a, lo, lo + eps, lo + eps + eps);
                int m2 = median3(a, mid - eps, mid, mid + eps);
                int m3 = median3(a, hi - eps - eps, hi - eps, hi);
                int ninther = median3(a, m1, m2, m3);
                exch(a, ninther, lo);
            }

            // Bentley-McIlroy 3-way partitioning
            int i = lo, j = hi+1;
            int p = lo, q = hi+1;
            Comparable v = a[lo];
            while (true) {
                while (less(a[++i], v))
                    if (i == hi) break;
                while (less(v, a[--j]))
                    if (j == lo) break;

                // pointers cross
                if (i == j && eq(a[i], v))
                    exch(a, ++p, i);
                if (i >= j) break;

                exch(a, i, j);
                if (eq(a[i], v)) exch(a, ++p, i);
                if (eq(a[j], v)) exch(a, --q, j);
            }

            i = j + 1;
            for (int k = lo; k <= p; k++)
                exch(a, k, j--);
            for (int k = hi; k >= q; k--)
                exch(a, k, i++);

            sort(a, lo, j);
            sort(a, i, hi);
        }

        // sort from a[lo] to a[hi] using insertion sort
        private static void insertionSort(Comparable[] a, int lo, int hi) {
            for (int i = lo; i <= hi; i++)
                for (int j = i; j > lo && less(a[j], a[j-1]); j--)
                    exch(a, j, j-1);
        }

        // return the index of the median element among a[i], a[j], and a[k]
        private static int median3(Comparable[] a, int i, int j, int k) {
            return (less(a[i], a[j]) ?
                   (less(a[j], a[k]) ? j : less(a[i], a[k]) ? k : i) :
                   (less(a[k], a[j]) ? j : less(a[k], a[i]) ? k : i));
        }

        // is v < w ?
        private static boolean less(Comparable v, Comparable w) {
            if (v == w) return false;    // optimization when reference equal
            return v.compareTo(w) < 0;
        }

        // does v == w ?
        private static boolean eq(Comparable v, Comparable w) {
            if (v == w) return true;    // optimization when reference equal
            return v.compareTo(w) == 0;
        }

        // exchange a[i] and a[j]
        private static void exch(Object[] a, int i, int j) {
            Object swap = a[i];
            a[i] = a[j];
            a[j] = swap;
        }
    }
    ```