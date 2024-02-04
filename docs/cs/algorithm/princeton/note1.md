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

## 符号表

- 符号表是一种关于键值和数据对（key-value pair）的数据结构，支持如下操作：
    - 搜索：给定键值，在符号表中查找相应的数据
    - 插入：将具有特定键值的数据加入符号表
    - 删除：给定键值，删除符号表中相应的键值和数据对

### 实现

- 思路一：顺序搜索
    - 用链表实现符号表，每个节点都包含一对键值和数据
    - 搜索、插入和删除的复杂度均为 $\Omicron(N)$
    - 不支持顺序遍历符号表
- 思路二：二分搜索
    - 用可变数组实现符号表，分别维护一个键值数组和数据数组，用相同的数组索引把键值和数据联系起来；数组中键值是有序的，所以可以支持二分搜索
    - 搜索的复杂度为 $\Omicron(\log N)$，插入和删除的复杂度为 $\Omicron(N)$
    - 支持顺序遍历符号表
- 思路三：二叉搜索树
    - 具体分析请移步[这里](#_32)
    - 最坏情况下搜索、插入和删除的复杂度均为 $\sim N$
    - 平均情况下搜索和插入的复杂度均为 $\sim 1.39\log N$
    - 平均情况下删除的复杂度为 $\sim \sqrt{N}$，且支持删除操作的二叉搜索树平均情况下搜索和插入的复杂度会退化为 $\sim \sqrt{N}$
    - 支持顺序遍历符号表（二叉搜索树的中序遍历）
- 思路四：红黑树
    - 具体分析请移步[这里](#_36)
    - 最坏情况下搜索、插入和删除的复杂度均为 $\sim 2\log N$
    - 平均情况下搜索、插入和删除的复杂度均为 $\sim 1.00\log N$，常数因子的确切数值还没有被证明，但在实践中表现得非常近似于 $1.00$
    - 支持顺序遍历符号表（二叉搜索树的中序遍历）

### 二叉搜索树

- 搜索和插入的复杂度取决于二叉搜索树的高度
- 将 $N$ 个相异元素以随机顺序插入二叉搜索树，搜索和插入的比较次数为 $\sim 2\ln N \approx 1.39\log N$
    - 证明：与快排的划分构成一一映射
- Reed：将 $N$ 个相异元素以随机顺序插入二叉搜索树，树的高度为 $\sim 4.311 \ln N$
- 最坏情况下树的高度为 $N$

### 删除操作

- 思路一：懒惰
    - 直接将需要删除的节点的数据置为 null
    - 如果删除操作很多，会导致内存浪费严重
- 思路二：Hibbard 删除
    - 对于没有子节点的节点，直接删除
    - 对于有一个子节点的节点，用其子节点替代它
    - 对于有两个子节点的节点，用右子树的最小值替代它
    - 弊端：Hibbard 删除是非对称的，会导致树趋于不平衡
- 研究发现，经过足够多次的随机插入和删除后，树的高度趋于 $\sqrt{N}$；即使 Hibbard 删除时尝试在左右子树之间随机选择，也仍然没有效果
- 寻找一个简单高效的二叉搜索树删除方法，至今仍然是一个尚未解决的开放性问题

### 代码

??? quote "BST: Java Implementation"

    ```java linenums="1" title="Binary Search Tree with All Symbol Table Operations"
    package edu.princeton.cs.algs4;
    import java.util.NoSuchElementException;

    public class BST<Key extends Comparable<Key>, Value> {
        private Node root;             // root of BST

        private class Node {
            private Key key;           // sorted by key
            private Value val;         // associated data
            private Node left, right;  // left and right subtrees
            private int size;          // number of nodes in subtree

            public Node(Key key, Value val, int size) {
                this.key = key;
                this.val = val;
                this.size = size;
            }
        }

        // initializes an empty symbol table.
        public BST() { }

        public boolean isEmpty() {
            return size() == 0;
        }

        public int size() {
            return size(root);
        }

        // return number of key-value pairs in BST rooted at x
        private int size(Node x) {
            if (x == null) return 0;
            else return x.size;
        }

        /**
         * Does this symbol table contain the given key?
         */
        public boolean contains(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to contains() is null");
            return get(key) != null;
        }

        /**
         * returns the value associated with the given key.
         */
        public Value get(Key key) {
            return get(root, key);
        }

        private Value get(Node x, Key key) {
            if (key == null) throw new IllegalArgumentException("calls get() with a null key");
            if (x == null) return null;
            int cmp = key.compareTo(x.key);
            if      (cmp < 0) return get(x.left, key);
            else if (cmp > 0) return get(x.right, key);
            else              return x.val;
        }

        /**
         * inserts the specified key-value pair into the symbol table, overwriting the old
         * value with the new value if the symbol table already contains the specified key.
         */
        public void put(Key key, Value val) {
            if (key == null) throw new IllegalArgumentException("calls put() with a null key");
            if (val == null) {
                delete(key);
                return;
            }
            root = put(root, key, val);
        }

        private Node put(Node x, Key key, Value val) {
            if (x == null) return new Node(key, val, 1);
            int cmp = key.compareTo(x.key);
            if      (cmp < 0) x.left  = put(x.left,  key, val);
            else if (cmp > 0) x.right = put(x.right, key, val);
            else              x.val   = val;
            x.size = 1 + size(x.left) + size(x.right);
            return x;
        }

        /**
         * removes the smallest key and associated value from the symbol table.
         */
        public void deleteMin() {
            if (isEmpty()) throw new NoSuchElementException("Symbol table underflow");
            root = deleteMin(root);
        }

        private Node deleteMin(Node x) {
            if (x.left == null) return x.right;
            x.left = deleteMin(x.left);
            x.size = size(x.left) + size(x.right) + 1;
            return x;
        }

        /**
         * removes the largest key and associated value from the symbol table.
         */
        public void deleteMax() {
            if (isEmpty()) throw new NoSuchElementException("Symbol table underflow");
            root = deleteMax(root);
        }

        private Node deleteMax(Node x) {
            if (x.right == null) return x.left;
            x.right = deleteMax(x.right);
            x.size = size(x.left) + size(x.right) + 1;
            return x;
        }

        /**
         * removes the specified key and its associated value from this symbol table
         * (if the key is in this symbol table).
         */
        public void delete(Key key) {
            if (key == null) throw new IllegalArgumentException("calls delete() with a null key");
            root = delete(root, key);
        }

        // Hibbard delete
        private Node delete(Node x, Key key) {
            if (x == null) return null;

            int cmp = key.compareTo(x.key);
            if      (cmp < 0) x.left  = delete(x.left,  key);
            else if (cmp > 0) x.right = delete(x.right, key);
            else {
                if (x.right == null) return x.left;
                if (x.left  == null) return x.right;
                Node t = x;
                x = min(t.right);
                x.right = deleteMin(t.right);
                x.left = t.left;
            }
            x.size = size(x.left) + size(x.right) + 1;
            return x;
        }

        /**
         * returns the smallest key in the symbol table.
         */
        public Key min() {
            if (isEmpty()) throw new NoSuchElementException("calls min() with empty symbol table");
            return min(root).key;
        }

        private Node min(Node x) {
            if (x.left == null) return x;
            else                return min(x.left);
        }

        /**
         * returns the largest key in the symbol table.
         */
        public Key max() {
            if (isEmpty()) throw new NoSuchElementException("calls max() with empty symbol table");
            return max(root).key;
        }

        private Node max(Node x) {
            if (x.right == null) return x;
            else                 return max(x.right);
        }

        /**
         * returns the largest key in the symbol table less than or equal to {@code key}.
         */
        public Key floor(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to floor() is null");
            if (isEmpty()) throw new NoSuchElementException("calls floor() with empty symbol table");
            Node x = floor(root, key);
            if (x == null) throw new NoSuchElementException("argument to floor() is too small");
            else return x.key;
        }

        private Node floor(Node x, Key key) {
            if (x == null) return null;
            int cmp = key.compareTo(x.key);
            if (cmp == 0) return x;
            if (cmp <  0) return floor(x.left, key);
            Node t = floor(x.right, key);
            if (t != null) return t;
            else return x;
        }

        /**
         * returns the smallest key in the symbol table greater than or equal to {@code key}.
         */
        public Key ceiling(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to ceiling() is null");
            if (isEmpty()) throw new NoSuchElementException("calls ceiling() with empty symbol table");
            Node x = ceiling(root, key);
            if (x == null) throw new NoSuchElementException("argument to ceiling() is too large");
            else return x.key;
        }

        private Node ceiling(Node x, Key key) {
            if (x == null) return null;
            int cmp = key.compareTo(x.key);
            if (cmp == 0) return x;
            if (cmp < 0) {
                Node t = ceiling(x.left, key);
                if (t != null) return t;
                else return x;
            }
            return ceiling(x.right, key);
        }

        /**
         * return the key in the symbol table of a given {@code rank}.
         */
        public Key select(int rank) {
            if (rank < 0 || rank >= size()) {
                throw new IllegalArgumentException("argument to select() is invalid: " + rank);
            }
            return select(root, rank);
        }

        // Return key in BST rooted at x of given rank.
        // Precondition: rank is in legal range.
        private Key select(Node x, int rank) {
            if (x == null) return null;
            int leftSize = size(x.left);
            if      (leftSize > rank) return select(x.left,  rank);
            else if (leftSize < rank) return select(x.right, rank - leftSize - 1);
            else                      return x.key;
        }

        /**
         * return the number of keys in the symbol table strictly less than {@code key}.
         */
        public int rank(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to rank() is null");
            return rank(key, root);
        }

        // Number of keys in the subtree less than key.
        private int rank(Key key, Node x) {
            if (x == null) return 0;
            int cmp = key.compareTo(x.key);
            if      (cmp < 0) return rank(key, x.left);
            else if (cmp > 0) return 1 + size(x.left) + rank(key, x.right);
            else              return size(x.left);
        }

        /**
         * Returns all keys in the symbol table in ascending order,
         * as an {@code Iterable}.
         * To iterate over all of the keys in the symbol table named {@code st},
         * use the foreach notation: {@code for (Key key : st.keys())}.
         */
        public Iterable<Key> keys() {
            if (isEmpty()) return new Queue<Key>();
            return keys(min(), max());
        }

        /**
         * returns all keys in the symbol table in the given range
         * in ascending order, as an {@code Iterable}.
         */
        public Iterable<Key> keys(Key lo, Key hi) {
            if (lo == null) throw new IllegalArgumentException("first argument to keys() is null");
            if (hi == null) throw new IllegalArgumentException("second argument to keys() is null");

            Queue<Key> queue = new Queue<Key>();
            keys(root, queue, lo, hi);
            return queue;
        }

        private void keys(Node x, Queue<Key> queue, Key lo, Key hi) {
            if (x == null) return;
            int cmplo = lo.compareTo(x.key);
            int cmphi = hi.compareTo(x.key);
            if (cmplo < 0) keys(x.left, queue, lo, hi);
            if (cmplo <= 0 && cmphi >= 0) queue.enqueue(x.key);
            if (cmphi > 0) keys(x.right, queue, lo, hi);
        }

        /**
         * returns the number of keys in the symbol table in the given range.
         */
        public int size(Key lo, Key hi) {
            if (lo == null) throw new IllegalArgumentException("first argument to size() is null");
            if (hi == null) throw new IllegalArgumentException("second argument to size() is null");

            if (lo.compareTo(hi) > 0) return 0;
            if (contains(hi)) return rank(hi) - rank(lo) + 1;
            else              return rank(hi) - rank(lo);
        }

        /**
         * returns the height of the BST (for debugging).
         */
        public int height() {
            return height(root);
        }
        private int height(Node x) {
            if (x == null) return -1;
            return 1 + Math.max(height(x.left), height(x.right));
        }
    }
    ```

## 平衡搜索树

- 支持 Hibbard 删除操作的二叉搜索树最大的问题在于不平衡，因而树的高度会失去对数保证
- 平衡搜索树可以解决这个问题，有红黑树、B 树等多种实现方式

### 2-3&thinsp;树

- 实现：
    - 存在 2-node 和 3-node 两种节点
    - 搜索操作相当于结合了二叉搜索和三叉搜索
    - 插入操作将叶节点升阶，如果形成 4-node 则分裂该节点并将中间值向上传递
        - 如果根节点形成 4-node 则分裂成三个 2-node，当且仅当此时 2-3 树的高度加一
        - 这种分裂方法是局部的，只需要常数数量的操作即可实现
- 分析：
    - 2-3 树总是保持完全平衡
    - 最坏情况下（全部是 2-node）树的高度为 $\log N$
    - 最好情况下（全部是 3-node）树的高度为 $\log _{3}N \approx 0.631\log N$
    - 搜索、插入和删除的复杂度均为 $\sim c\log N$，常数因子取决于具体实现
- 具体实现：
    - 2-3 树的具体实现比较复杂，需要维护两种节点，而且需要在树上上下移动并进行多次比较
    - 相较之下，红黑树是一种更加简单高效的实现方法

### 红黑树

- 红黑树使用二叉搜索树的结构实现了 2-3 树
    - 用左斜的红色链接来表示 3-node
    - 所有节点至多有一条红色链接与之相连
    - 从根节点到所有叶节点的黑色链接数都是相同的，即“黑色平衡”
- 红黑树与 2-3 树可以构建一一映射
- 实现：
    - 搜索操作和二叉搜索树完全一致，但速度更快（因为更加平衡）
    - 插入操作需要保持红黑树的特性不变，包括以下三种子操作：
        - 与右儿子通过红色链接相连，与左儿子通过黑色链接相连，则向左旋转红色链接
        - 与左儿子通过红色链接相连，左儿子与其左儿子通过红色链接相连，则向右旋转红色链接
        - 与左右儿子均通过红色链接相连，则将这两个链接变换为黑色，将指向自身的连接变换为红色
    - 删除操作更加复杂，但本质上也是需要保持红黑树的特性不变
- 分析：
    - 红黑树的高度在最坏情况下不会超过 $2\log N$
    - 红黑树的典型实现在平均情况下树的高度约为 $\sim 1.00\log N$，常数因子的确切数值还没有被证明，但在实践中表现得非常近似于 $1.00$

### B&thinsp;树

- B 树是一种泛化的 2-3 树，或者说 2-3 树是 B 树的特例（4 阶 B 树）
- B 树适合读写相对较大数据块的存储系统，比如数据库和文件系统
    - 这种存储系统的特点是，数据块以页（page）为单位存储，寻路（probe，查询特定的某一页）的开销是很大的，而在同一页中读取不同键值的开销相比之下几乎可以忽略；因此提高效率的关键在于减少寻路的次数
- 实现：
    - 对于 M 阶 B 树而言，每个节点至多是 M-1 阶的（即有 M-1 个键值和 M 个链接），通常选择一页能存储的键值数作为 M
    - 根节点至少是 2 阶的
    - 其他节点至少是 M/2 阶的（半满）
    - 外节点（external node）是 B 树的叶节点，存储数据块的键值
    - 内节点（internal node）是 B 树的非叶节点，存储其各个子节点数据块的第一个键值，用于支持 B 树的搜索操作
    - 当某个外节点变成 M 阶时（全满），分裂该节点，具体操作类似于 2-3 树；当且仅当根节点分裂时 B 树的高度加一
- 分析：
    - 对于包含 $N$ 个键值的 $M$ 阶 B 树，搜索和插入只需要 $\log _ {M-1} N \sim \log _ {M/2} N$ 次寻路
    - 在实践中，寻路次数通常不超过 4 次，比如 M = 1024，N = 62 billion
    - 优化：总是把根节点那一页数据块存储在内存中
- 应用：
    - 红黑树被广泛应用在系统的符号表中
    - B 树及其变式（B+ 树、B* 树、B# 树等）被广泛应用在数据库和文件系统中

### 代码

??? quote "Red-Black BST: Java Implementation"

    ```java linenums="1" title="Left-Leaning Red-Black Binary Search Tree"
    package edu.princeton.cs.algs4;
    import java.util.NoSuchElementException;

    public class RedBlackBST<Key extends Comparable<Key>, Value> {
        private static final boolean RED   = true;
        private static final boolean BLACK = false;
        private Node root;     // root of the BST

        // BST helper node data type
        private class Node {
            private Key key;           // key
            private Value val;         // associated data
            private Node left, right;  // links to left and right subtrees
            private boolean color;     // color of parent link
            private int size;          // subtree count

            public Node(Key key, Value val, boolean color, int size) {
                this.key = key;
                this.val = val;
                this.color = color;
                this.size = size;
            }
        }

        // initializes an empty symbol table.
        public RedBlackBST() {
        }

        /***************************************************************************
        *  Node helper methods.
        ***************************************************************************/
        // is node x red; false if x is null ?
        private boolean isRed(Node x) {
            if (x == null) return false;
            return x.color == RED;
        }

        // number of node in subtree rooted at x; 0 if x is null
        private int size(Node x) {
            if (x == null) return 0;
            return x.size;
        }

        public int size() {
            return size(root);
        }

        public boolean isEmpty() {
            return root == null;
        }

        /***************************************************************************
        *  Standard BST search.
        ***************************************************************************/
        /**
         * returns the value associated with the given key.
         */
        public Value get(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to get() is null");
            return get(root, key);
        }

        // value associated with the given key in subtree rooted at x; null if no such key
        private Value get(Node x, Key key) {
            while (x != null) {
                int cmp = key.compareTo(x.key);
                if      (cmp < 0) x = x.left;
                else if (cmp > 0) x = x.right;
                else              return x.val;
            }
            return null;
        }

        /**
         * does this symbol table contain the given key?
         */
        public boolean contains(Key key) {
            return get(key) != null;
        }

        /***************************************************************************
        *  Red-black tree insertion.
        ***************************************************************************/
        /**
         * inserts the specified key-value pair into the symbol table, overwriting the old
         * value with the new value if the symbol table already contains the specified key.
         */
        public void put(Key key, Value val) {
            if (key == null) throw new IllegalArgumentException("first argument to put() is null");
            if (val == null) {
                delete(key);
                return;
            }
            root = put(root, key, val);
            root.color = BLACK;
        }

        // insert the key-value pair in the subtree rooted at h
        private Node put(Node h, Key key, Value val) {
            if (h == null) return new Node(key, val, RED, 1);

            int cmp = key.compareTo(h.key);
            if      (cmp < 0) h.left  = put(h.left,  key, val);
            else if (cmp > 0) h.right = put(h.right, key, val);
            else              h.val   = val;

            // fix-up any right-leaning links
            if (isRed(h.right) && !isRed(h.left))      h = rotateLeft(h);
            if (isRed(h.left)  &&  isRed(h.left.left)) h = rotateRight(h);
            if (isRed(h.left)  &&  isRed(h.right))     flipColors(h);
            h.size = size(h.left) + size(h.right) + 1;

            return h;
        }

        /***************************************************************************
        *  Red-black tree deletion.
        ***************************************************************************/
        /**
         * removes the smallest key and associated value from the symbol table.
         */
        public void deleteMin() {
            if (isEmpty()) throw new NoSuchElementException("BST underflow");

            // if both children of root are black, set root to red
            if (!isRed(root.left) && !isRed(root.right))
                root.color = RED;

            root = deleteMin(root);
            if (!isEmpty()) root.color = BLACK;
        }

        // delete the key-value pair with the minimum key rooted at h
        private Node deleteMin(Node h) {
            if (h.left == null)
                return null;

            if (!isRed(h.left) && !isRed(h.left.left))
                h = moveRedLeft(h);

            h.left = deleteMin(h.left);
            return balance(h);
        }

        /**
         * removes the largest key and associated value from the symbol table.
         */
        public void deleteMax() {
            if (isEmpty()) throw new NoSuchElementException("BST underflow");

            // if both children of root are black, set root to red
            if (!isRed(root.left) && !isRed(root.right))
                root.color = RED;

            root = deleteMax(root);
            if (!isEmpty()) root.color = BLACK;
        }

        // delete the key-value pair with the maximum key rooted at h
        private Node deleteMax(Node h) {
            if (isRed(h.left))
                h = rotateRight(h);

            if (h.right == null)
                return null;

            if (!isRed(h.right) && !isRed(h.right.left))
                h = moveRedRight(h);

            h.right = deleteMax(h.right);

            return balance(h);
        }

        /**
         * removes the specified key and its associated value from this symbol table
         * (if the key is in this symbol table).
         */
        public void delete(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to delete() is null");
            if (!contains(key)) return;

            // if both children of root are black, set root to red
            if (!isRed(root.left) && !isRed(root.right))
                root.color = RED;

            root = delete(root, key);
            if (!isEmpty()) root.color = BLACK;
        }

        // delete the key-value pair with the given key rooted at h
        private Node delete(Node h, Key key) {
            if (key.compareTo(h.key) < 0)  {
                if (!isRed(h.left) && !isRed(h.left.left))
                    h = moveRedLeft(h);
                h.left = delete(h.left, key);
            }
            else {
                if (isRed(h.left))
                    h = rotateRight(h);
                if (key.compareTo(h.key) == 0 && (h.right == null))
                    return null;
                if (!isRed(h.right) && !isRed(h.right.left))
                    h = moveRedRight(h);
                if (key.compareTo(h.key) == 0) {
                    Node x = min(h.right);
                    h.key = x.key;
                    h.val = x.val;
                    // h.val = get(h.right, min(h.right).key);
                    // h.key = min(h.right).key;
                    h.right = deleteMin(h.right);
                }
                else h.right = delete(h.right, key);
            }
            return balance(h);
        }

        /***************************************************************************
        *  Red-black tree helper functions.
        ***************************************************************************/
        // make a left-leaning link lean to the right
        private Node rotateRight(Node h) {
            Node x = h.left;
            h.left = x.right;
            x.right = h;
            x.color = h.color;
            h.color = RED;
            x.size = h.size;
            h.size = size(h.left) + size(h.right) + 1;
            return x;
        }

        // make a right-leaning link lean to the left
        private Node rotateLeft(Node h) {
            Node x = h.right;
            h.right = x.left;
            x.left = h;
            x.color = h.color;
            h.color = RED;
            x.size = h.size;
            h.size = size(h.left) + size(h.right) + 1;
            return x;
        }

        // flip the colors of a node and its two children
        private void flipColors(Node h) {
            // h must have opposite color of its two children
            h.color = !h.color;
            h.left.color = !h.left.color;
            h.right.color = !h.right.color;
        }

        // assuming that h is red and both h.left and h.left.left
        // are black, make h.left or one of its children red.
        private Node moveRedLeft(Node h) {
            flipColors(h);
            if (isRed(h.right.left)) {
                h.right = rotateRight(h.right);
                h = rotateLeft(h);
                flipColors(h);
            }
            return h;
        }

        // assuming that h is red and both h.right and h.right.left
        // are black, make h.right or one of its children red.
        private Node moveRedRight(Node h) {
            flipColors(h);
            if (isRed(h.left.left)) {
                h = rotateRight(h);
                flipColors(h);
            }
            return h;
        }

        // restore red-black tree invariant
        private Node balance(Node h) {
            if (isRed(h.right) && !isRed(h.left))    h = rotateLeft(h);
            if (isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);
            if (isRed(h.left) && isRed(h.right))     flipColors(h);

            h.size = size(h.left) + size(h.right) + 1;
            return h;
        }

        /***************************************************************************
        *  Utility functions.
        ***************************************************************************/
        /**
         * returns the height of the BST (for debugging).
         */
        public int height() {
            return height(root);
        }
        private int height(Node x) {
            if (x == null) return -1;
            return 1 + Math.max(height(x.left), height(x.right));
        }

        /***************************************************************************
        *  Ordered symbol table methods.
        ***************************************************************************/
        /**
         * returns the smallest key in the symbol table.
         */
        public Key min() {
            if (isEmpty()) throw new NoSuchElementException("calls min() with empty symbol table");
            return min(root).key;
        }

        // the smallest key in subtree rooted at x; null if no such key
        private Node min(Node x) {
            if (x.left == null) return x;
            else                return min(x.left);
        }

        /**
         * returns the largest key in the symbol table.
         */
        public Key max() {
            if (isEmpty()) throw new NoSuchElementException("calls max() with empty symbol table");
            return max(root).key;
        }

        // the largest key in the subtree rooted at x; null if no such key
        private Node max(Node x) {
            if (x.right == null) return x;
            else                 return max(x.right);
        }


        /**
         * returns the largest key in the symbol table less than or equal to {@code key}.
         */
        public Key floor(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to floor() is null");
            if (isEmpty()) throw new NoSuchElementException("calls floor() with empty symbol table");
            Node x = floor(root, key);
            if (x == null) throw new NoSuchElementException("argument to floor() is too small");
            else           return x.key;
        }

        // the largest key in the subtree rooted at x less than or equal to the given key
        private Node floor(Node x, Key key) {
            if (x == null) return null;
            int cmp = key.compareTo(x.key);
            if (cmp == 0) return x;
            if (cmp < 0)  return floor(x.left, key);
            Node t = floor(x.right, key);
            if (t != null) return t;
            else           return x;
        }

        /**
         * returns the smallest key in the symbol table greater than or equal to {@code key}.
         */
        public Key ceiling(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to ceiling() is null");
            if (isEmpty()) throw new NoSuchElementException("calls ceiling() with empty symbol table");
            Node x = ceiling(root, key);
            if (x == null) throw new NoSuchElementException("argument to ceiling() is too large");
            else           return x.key;
        }

        // the smallest key in the subtree rooted at x greater than or equal to the given key
        private Node ceiling(Node x, Key key) {
            if (x == null) return null;
            int cmp = key.compareTo(x.key);
            if (cmp == 0) return x;
            if (cmp > 0)  return ceiling(x.right, key);
            Node t = ceiling(x.left, key);
            if (t != null) return t;
            else           return x;
        }

        /**
         * return the key in the symbol table of a given {@code rank}.
         */
        public Key select(int rank) {
            if (rank < 0 || rank >= size()) {
                throw new IllegalArgumentException("argument to select() is invalid: " + rank);
            }
            return select(root, rank);
        }

        // Return key in BST rooted at x of given rank.
        // Precondition: rank is in legal range.
        private Key select(Node x, int rank) {
            if (x == null) return null;
            int leftSize = size(x.left);
            if      (leftSize > rank) return select(x.left,  rank);
            else if (leftSize < rank) return select(x.right, rank - leftSize - 1);
            else                      return x.key;
        }

        /**
         * return the number of keys in the symbol table strictly less than {@code key}.
         */
        public int rank(Key key) {
            if (key == null) throw new IllegalArgumentException("argument to rank() is null");
            return rank(key, root);
        }

        // number of keys less than key in the subtree rooted at x
        private int rank(Key key, Node x) {
            if (x == null) return 0;
            int cmp = key.compareTo(x.key);
            if      (cmp < 0) return rank(key, x.left);
            else if (cmp > 0) return 1 + size(x.left) + rank(key, x.right);
            else              return size(x.left);
        }

        /***************************************************************************
        *  Range count and range search.
        ***************************************************************************/
        /**
         * Returns all keys in the symbol table in ascending order as an {@code Iterable}.
         * To iterate over all of the keys in the symbol table named {@code st},
         * use the foreach notation: {@code for (Key key : st.keys())}.
         */
        public Iterable<Key> keys() {
            if (isEmpty()) return new Queue<Key>();
            return keys(min(), max());
        }

        /**
         * returns all keys in the symbol table in the given range in ascending order,
         * as an {@code Iterable}.
         */
        public Iterable<Key> keys(Key lo, Key hi) {
            if (lo == null) throw new IllegalArgumentException("first argument to keys() is null");
            if (hi == null) throw new IllegalArgumentException("second argument to keys() is null");

            Queue<Key> queue = new Queue<Key>();
            keys(root, queue, lo, hi);
            return queue;
        }

        // add the keys between lo and hi in the subtree rooted at x
        // to the queue
        private void keys(Node x, Queue<Key> queue, Key lo, Key hi) {
            if (x == null) return;
            int cmplo = lo.compareTo(x.key);
            int cmphi = hi.compareTo(x.key);
            if (cmplo < 0) keys(x.left, queue, lo, hi);
            if (cmplo <= 0 && cmphi >= 0) queue.enqueue(x.key);
            if (cmphi > 0) keys(x.right, queue, lo, hi);
        }

        /**
         * returns the number of keys in the symbol table in the given range.
         */
        public int size(Key lo, Key hi) {
            if (lo == null) throw new IllegalArgumentException("first argument to size() is null");
            if (hi == null) throw new IllegalArgumentException("second argument to size() is null");

            if (lo.compareTo(hi) > 0) return 0;
            if (contains(hi)) return rank(hi) - rank(lo) + 1;
            else              return rank(hi) - rank(lo);
        }
    }
    ```

## BST&thinsp;的几何应用

- 前面讨论的 BST 都是以数值作为键值的，下面讨论的 BST 会以几何对象作为键值，比如点、线段、区间等

### 一维范围搜索

- 数据结构：二叉搜索树（BST）
- 一维范围搜索（1d range search）研究的是一维分布的点，每个点对应一个 BST 节点
- 除了基本的搜索、插入和删除操作外，还支持以下两种操作：
    - 范围搜索：查询处于区间 [m, n] 之间的所有点
    - 范围计数：计算处于区间 [m, n] 之间点的个数
- 复杂度分析，$R$ 表示处于区间之间的点的个数
    - 范围搜索复杂度为 $\Omicron(R+\log N)$
    - 范围计数复杂度为 $\Omicron(\log N)$，通过 BST 的 [rank 操作](#_34)实现

### 二维正交线段相交问题

- 算法：通过扫描线算法（sweep-line）转化为一维范围搜索问题
- 二维正交线段相交问题（orthogonal line segment intersection）研究的是二维空间上正交的线段的交点
- 具体实现：
    - 用一条竖直的线自左向右扫描（以左右端点的横坐标定义事件，可以用优先队列或者排序实现）
    - 遇到水平线段的左端点，将其纵坐标插入 BST
    - 遇到水平线段的右端点，将其纵坐标从 BST 中删除
    - 遇到竖直线段，以其两端点纵坐标作为范围，对 BST 进行一维范围搜索，找到所有与其相交的线段
- 在 $N$ 条正交线段中查询 $R$ 个交点的时间复杂度为 $\Omicron(R+N\log N)$

### 一维区间搜索

- 数据结构：[区间搜索树](https://en.wikipedia.org/wiki/Interval_tree){target="_blank"}（BST 的一种变式）
- 一维区间搜索（1d interval search）研究的是一维分布的区间，每个区间对应一个 BST 节点
- 除了基本的搜索、插入和删除操作外，还支持以下操作：
    - 相交区间查询：给定一个区间 [lo, hi]，找到所有与这个区间有交集的区间
- 具体实现：
    - 区间搜索树以区间左端点作为搜索的键值（假设所有区间的左端点都是相异的）
    - 区间搜索树上的每个节点需要额外存储一个值，表示其子树中所有区间的右端点的最大值
- 复杂度分析，$R$ 表示与给定区间有交集的区间个数
    - 区间搜索树实现的一维区间搜索，查询任一满足条件的区间所需复杂度为 $\Omicron(\log N)$
    - 区间搜索树实现的一维区间搜索，查询所有满足条件的区间所需复杂度为 $\Omicron(R\log N)$
        - 理论上最优复杂度为 $\Omicron(R+\log N)$，不过 $\Omicron(R\log N)$ 的实践表现也非常有效

### 二维正交矩形相交问题

- 算法：通过扫描线算法（sweep-line）转化为一维区间搜索问题
- 二维正交矩形相交问题（orthongonal rectangle intersection）研究的是二维空间上正交的矩形的相交关系
- 具体实现：
    - 用一条竖直的线自左向右扫描（以左右边线的横坐标定义事件，可以用优先队列或者排序实现）
    - 将与扫描线相交的矩形维护在区间搜索树上（以纵坐标区间来表示）
    - 遇到矩形的左边线，以其纵坐标区间作为给定区间，对区间搜索树进行一维区间搜索，找到所有与其相交的矩形，并将其插入区间搜索树
    - 遇到矩形的右边线，将其从区间搜索树中删除
- 在 $N$ 个正交矩形中查询 $R$ 对相交关系的时间复杂度为 $\Omicron(R\log N+N\log N)$

### Kd&thinsp;树

- 前面讨论的符号表都是以数值作为键值的，下面讨论一种以二维空间中的点作为键值的符号表，并进一步推广到 K 维
- 以二维空间中的点作为键值的符号表，除了基本的搜索、插入和删除操作，还支持[范围搜索和范围计数](#_38)
- 实现思路一：网格法
    - 将平面分割成 $M\times M$ 的网格（通常取 $M=\sqrt{N}$）
    - 对每个网格维护一个列表来存储点，用二维数组直接索引所有网格
    - 范围搜索时只对与给定范围有交集的网格进行搜索
    - 最好情况下（所有的点均匀分散）插入和范围搜索的复杂度均为 $1$
    - 最坏情况下会很糟糕，几乎所有的点都聚集在同一个网格附近（cluster），列表维护和范围搜索会开销很大
- 实现思路二：2d 树
    - 递归地将平面不断分割成 2 个半平面
    - 维护一个 BST，交替使用横坐标和纵坐标作为键值，奇数层的节点根据横坐标大小分划左右子树（左半平面和右半平面），偶数层的节点根据纵坐标大小分划左右子树（下半平面和上半平面）
    - 范围搜索：
        - 检查当前节点是否满足条件（即处在给定范围之中），然后递归地搜索可能包含满足条件的节点的左子树和右子树
        - 平均情况下复杂度为 $\sim R+\log N$，最坏情况下复杂度为 $\sim R+\sqrt{N}$（即使树是平衡的）
    - 最近邻搜索：
        - 给定一个查询点，搜索平面中与其距离最近的点
        - 搜索思路和范围搜索相似，朝着查询点所在方向不断递归，并不断更新最近邻（额外维护一个数值来存储最近邻的信息）
        - 注意对于任一节点，一侧子树是查询点所在方向，需要递归，另一侧子树不是查询点所在方向，可能需要递归（判断方法是，比较以查询点为中心、以当前最近邻距离为半径的圆是否与当前节点表示的平面分割线相交，若相交，则该侧平面可能存在更近邻，需要递归）
        - 平均情况下复杂度为 $\sim \log N$，最坏情况下复杂度为 $\sim N$（即使树是平衡的），此时相当于所有节点都被访问了，试考虑一个平面内所有点都在一个圆上且查询点为圆心这一情形
    - 思路二本质上是用树的结构不断递归地对平面作划分，除了 2d 树之外，还可以使用四叉树、BSP 树等
- Kd 树：是 2d 树的推广，用以表示 K 维空间中的点作为键值的符号表
    - 递归地将 K 维空间不断分割成 2 个半空间
    - 维护一个 BST，按照同余关系轮换使用 K 维坐标作为键值，对于一个处于第 level 层的节点而言，设 level $\equiv$ i (mod) K，其左子树表示第 i 维坐标小于该节点的半空间，其右子树表示第 i 维坐标大于该节点的半空间
    - 最近邻搜索的递归判断：比较以查询点为中心、以当前最近邻距离为半径的超球面是否与当前节点表示的分割空间的超平面相交
- Kd 树的应用：
    - Kd 树将 N 体模拟中的每一步操作时间复杂度降到 $N\log N$，极大促进了 N 体模拟及其衍生研究的发展