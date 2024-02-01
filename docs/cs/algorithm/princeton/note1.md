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

- 快速排序基本步骤
    - 对数组进行随机洗牌（shuffle），第一个元素作为基准元素
    - 对数组进行划分（partition），使得基准元素位于其最终位置，其左边的元素都不大于它，其右边的元素都不小于它
    - 递归地处理划分得到的两部分
- 注意：对于重复键值的处理，应当在遇到和基准元素有相同键值的元素时停止
    - 虽然有些反直觉，但这确实是更优的选择，详细解释请移步[重复键值](#_24)
- 优化一：递归到小规模数据时改用插入排序
- 优化二：取样本中位数作为基准元素
    - 最优的情况是，总是选择数据的中位数作为基准元素
    - 用若干个样本的中位数来近似代替所有数据的中位数，也能取得不错的效果，比如使用 3 个随机元素的中位数作为基准元素

### 复杂度分析

- 对于含有 $N$ 个相异元素的数组，快排的平均比较次数为 $\sim 2(N+1)\ln N \approx 1.39N\log N$
- 最坏情况下（数据已经有序或逆序），快排的比较次数 $\sim \frac{1}{2}N^ 2$
- 平均情况下，快排虽然比归并多消耗 39% 的比较次数，但运行速度比归并更快，因为快排对数据的移动操作比较少

### 选择算法

- 选择算法用于找第 k 大的数
- 基于快速排序的选择算法，平均情况下可以在线性时间内找到第 k 大的数
    - 最坏情况下，比较次数退化为 $\sim \frac{1}{2}N^ 2$
- Blum，Floyd，Pratt，Rivest，Tarjan：基于比较的选择算法即使在最坏情况下也可以是线性的
    - 但是，目前已知的能在最坏情况下保持线性的选择算法都具有较大的常数因子，所以运行速度较慢，在实践中很少被使用
- 寻找具有实用价值的能在最坏情况下保持线性的选择算法，至今仍然是一个尚未解决的问题；而在找到这样一个算法之前，基于快速排序的选择算法仍然在大多数情况下具有较高的实用价值

### 重复键值

- 实践中，很多时候需要排序的数据会具有大量的重复键值
- 实现一：快排，把所有和基准元素相等的元素都放在基准的某一侧
    - 当所有元素都相等时，比较次数退化为 $\sim \frac{1}{2}N^ 2$
    - 这是错误的快排实现
- 实现二：快排，当遇到和基准元素相等的元素时停止
    - 当所有元素都相等时，比较次数仍保持 $\sim N\log N$
    - 这是标准的快排实现
- 实现三：三路快排
    - 将数组划分为 3 个部分，小于基准、等于基准和大于基准
    - 源自 Dijkstra 提出的[荷兰国旗问题](https://en.wikipedia.org/wiki/Dutch_national_flag_problem){target="_blank"}
- 对于含有 $n$ 个相异元素且第 $i$ 个元素重复了 $x_i$ 次的数组，任何基于比较的排序算法在最坏情况下都需要至少 $\log (\frac{N!}{x_1!x_2!\cdots x_n!}) \sim -\sum_{i=1}^ {n}x_i\log \frac{x_i}{N}$ 次比较
    - 当所有元素都相异时，上式的值为 $N\log N$
    - 当数组中仅有常数种相异元素时，上式的值是线性的
- Sedgewick-Bentley：三路快排是熵最优的（可以理解为拥有最小的下界）
- 补充：Bentley-McIlroy 三路快排
    - 对于小规模数据，改用插入排序
    - 对于大规模数据，使用 Tukey's ninther 方法进行划分，即取 3 个样本（每个样本含有 3 个元素）的中位数的中位数作为基准元素，相当于取这 9 个元素的中位数的估计值作为基准元素
    - 这种三路快排方法不需要随机洗牌，开销较小；但是缺点是容易受到 killer input 的攻击，即程序对于一些特别设计的输入会直接崩溃
    - 随机洗牌在很大程度上可以避开这种攻击，但很多系统设计者不喜欢随机洗牌，因为他们觉得这种做法改变了数据的固有顺序，且开销太大

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

## 优先队列

### 实现

- 数组结构的二叉堆是一种常用的优先队列实现
- d 叉堆是二叉堆优先队列的一种优化，在一些时候可以达到更好的效果
- [斐波那契堆](https://en.wikipedia.org/wiki/Fibonacci_heap){target="_blank"}是一种高级数据结构，可以在常数时间内实现插入、在均摊 $\log N$ 时间内实现删除最大元，但由于实现太复杂而很少在实践中被使用

### 堆排序

- 优点：堆排序是一种能在最坏情况下保证 $N\log N$ 复杂度的原地排序
- 缺点：
    - 堆排序和归并的循环内操作太多，所以两者的速度都不如快排
    - 堆排序需要跳跃式地访问数组，从而不像快排那样具有很好的内存访问局部性（make good use of cache memory）
    - 堆排序和快排都是不稳定的

### 代码

??? quote "Max Heap: Java Implementation"

    ```java linenums="1" title="Max Priority Queue with a Binary Heap"
    package edu.princeton.cs.algs4;
    import java.util.NoSuchElementException;

    public class MaxPQ<Key> {
        private Key[] pq;   // store items at indices 1 to n
        private int n;      // number of items on priority queue

        public MaxPQ(int initCapacity) {
            pq = (Key[]) new Object[initCapacity + 1];
            n = 0;
        }

        public boolean isEmpty() {
            return n == 0;
        }

        public int size() {
            return n;
        }

        public Key max() {
            if (isEmpty()) throw new NoSuchElementException("Priority queue underflow");
            return pq[1];
        }

        // resize the underlying array to have the given capacity
        private void resize(int capacity) {
            assert capacity > n;
            Key[] temp = (Key[]) new Object[capacity];
            for (int i = 1; i <= n; i++) {
                temp[i] = pq[i];
            }
            pq = temp;
        }

        public void insert(Key x) {
            // double size of array if necessary
            if (n == pq.length - 1) resize(2 * pq.length);
            // add x, and percolate it up to maintain heap invariant
            pq[++n] = x;
            swim(n);
        }

        public Key delMax() {
            if (isEmpty()) throw new NoSuchElementException("Priority queue underflow");
            Key max = pq[1];
            exch(1, n--);
            sink(1);
            pq[n+1] = null;     // to avoid loitering and help with garbage collection
            if ((n > 0) && (n == (pq.length - 1) / 4)) resize(pq.length / 2);
            return max;
        }

        private void swim(int k) {
            while (k > 1 && less(k/2, k)) {
                exch(k/2, k);
                k = k/2;
            }
        }

        private void sink(int k) {
            while (2*k <= n) {
                int j = 2*k;
                if (j < n && less(j, j+1)) j++;
                if (!less(k, j)) break;
                exch(k, j);
                k = j;
            }
        }

        private boolean less(int i, int j) {
            return pq[i].compareTo(pq[j]) < 0;
        }

        private void exch(int i, int j) {
            Key swap = pq[i];
            pq[i] = pq[j];
            pq[j] = swap;
        }
    }
    ```

??? quote "Heap Sort: Java Implementation"

    ```java linenums="1" title="Heap Sort"
    package edu.princeton.cs.algs4;

    public class Heap {
        // This class should not be instantiated.
        private Heap() { }

        public static void sort(Comparable[] pq) {
            int n = pq.length;

            // heapify phase
            for (int k = n/2; k >= 1; k--)
                sink(pq, k, n);

            // sortdown phase
            int k = n;
            while (k > 1) {
                exch(pq, 1, k--);
                sink(pq, 1, k);
            }
        }

        private static void sink(Comparable[] pq, int k, int n) {
            while (2*k <= n) {
                int j = 2*k;
                if (j < n && less(pq, j, j+1)) j++;
                if (!less(pq, k, j)) break;
                exch(pq, k, j);
                k = j;
            }
        }

        // indices are "off-by-one" to support 1-based indexing
        private static boolean less(Comparable[] pq, int i, int j) {
            return pq[i-1].compareTo(pq[j-1]) < 0;
        }

        // indices are "off-by-one" to support 1-based indexing
        private static void exch(Object[] pq, int i, int j) {
            Object swap = pq[i-1];
            pq[i-1] = pq[j-1];
            pq[j-1] = swap;
        }
    }
    ```