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

???+ quote "Java Implementation"

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
