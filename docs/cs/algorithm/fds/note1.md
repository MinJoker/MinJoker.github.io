# 算法分析基础

## 算法与分析

- 算法是一个被定义好的、计算机可执行的、解决某一问题的有限步骤
- 算法须满足以下特性：
    - Input
    - Output
    - Definiteness
    - Finiteness
    - Effectiveness (Feasibility)
- 注意，和 algorithm 不同，program 不需要 finite（例如操作系统）
- 算法分析内容：
    - 运行时间：与机器和编译器有关
    - 时间、空间复杂度：与机器和编译器无关
- 复杂度分析假设：
    - 指令按顺序执行
    - 每条指令都是简单的，只需要一个时间单位运行
    - 数据规模是给定的，且有无限空间
- 常见的分析内容有 $T_{avg}(N)$ 和 $T_{worst}(N)$，其中 $N$ 是输入的数据规模（可以有多个输入）

## 复杂度渐进符号

### 定义

- $T(N)=\Omicron(f(N))$，当存在正的常数 $c$ 和 $n_0$，使得 $\forall N\geq n_0,\;T(N)\leq c\cdot f(N)$
    - 上界，即 $T(N)$ 的阶小于等于 $f(N)$
- $T(N)=\Omega(g(N))$，当存在正的常数 $c$ 和 $n_0$，使得 $\forall N\geq n_0,\;T(N)\geq c\cdot f(N)$
    - 下界，即 $T(N)$ 的阶大于等于 $g(N)$
- $T(N)=\Theta(h(N))$，当且仅当 $T(N)=\Omicron(h(N))$ 且 $T(N)=\Omega(h(N))$
    - 紧界，即 $T(N)$ 与 $h(N)$ 同阶
- $T(N)=\omicron(p(N))$，当且仅当 $T(N)=\Omicron(p(N))$ 且 $T(N)\not =\Theta(p(N))$
    - 严格上界，即 $T(N)$ 的阶严格小于 $p(N)$
- $T(N)=\omega(q(N))$，当且仅当 $T(N)=\Omega(q(N))$ 且 $T(N)\not =\Theta(q(N))$
    - 严格下界，即 $T(N)$ 的阶严格大于 $q(N)$

### 规则

- 如果 $T_1(N)=\Omicron(f(N))$ 且 $T_2(N)=\Omicron(g(N))$，则：
    - $T_1(N)+T_2(N)=max(\Omicron(f(N)),\Omicron(g(N)))$
    - $T_1(N)\cdot T_2(N)=\Omicron(f(N)\cdot g(N))$
- 如果 $T(N)$ 是最高次数为 $k$ 的多项式，则 $T(N)=\Theta(N^ k)$
- 对于任意常数 $k$，均有 $\log^ {k}N=\Omicron(N)$
- 通用的分析规则：
    - for 循环的运行时间是循环内部语句的最长时间（含 for 判断）乘循环次数
    - 嵌套 for 循环要逐次相乘
    - 顺序语句只需关注耗时最长的部分
    - if else 语句的运行时间不超过判断时间 + 耗时最长的语句块的运行时间
- 补充，主定理（Master Theorom），假设有递推关系 $T(n)=aT(\frac{n}{b})+f(n)$，则：
    - 如果存在正的常数 $\epsilon$，使得 $f(n)=\Omicron(n^ {\log_{b}a-\epsilon})$，则 $T(n)=\Theta(n^ {\log_{b}a})$
    - 如果存在非负常数 $\epsilon$，使得 $f(n)=\Omega(n^ {\log_{b}a+\epsilon})$，且存在常数 $c<1$ 使得对于充分大的 $n$ 有 $af(\frac{n}{b})\leq cf(n)$，则 $T(n)=\Theta(f(n))$
    - 如果存在非负常数 $k$，使得 $f(n)=\Theta(n^ {\log_{b}a}\log^ {k}n)$，则 $T(n)=\Theta(n^ {\log_{b}a}\log^ {k+1}n)$

## 例：最大子序列和

- $\Omicron(N^ 3)$，直接枚举开头结尾，并计算中间子序列和

    ```c
    int MaxSubsequenceSum(const int a[], int N)
    {
        int res = 0;
        for (int i = 0; i < N; i++) {
            for (int j = i; j < N; j++) {
                int tmp = 0;
                for (int k = i; k <= j; k++) {
                    tmp += a[k];
                }
                res = max(res, tmp);
            }
        }
        return res;
    }
    ```

- $\Omicron(N^ 2)$，同样枚举开头结尾，但在枚举的同时计算子序列和，省去最内层循环

    ```c
    int MaxSubsequenceSum(const int a[], int N)
    {
        int res = 0;
        for (int i = 0; i < N; i++) {
            int tmp = 0;
            for (int j = i; j < N; j++) {
                tmp += a[j];
                res = max(res, tmp);
            }
        }
        return res;
    }
    ```

  - $\Omicron(N\log N)$，使用分治算法，$T(N)=2T(N/2)+cN$，符合主定理第三种情况，取 $k=0$

    ```c
    int MaxSubsequenceSum(const int a[], int N)
    {
        return MaxSubSum(a, 0, N-1);
    }

    int MaxSubSum(const int a[], int left, int right)
    {
        if (left == right){
            if (a[left] > 0){
                return a[left];
            } else {
                return 0;
            }
        }
        int mid = (left + right) / 2;
        int sumLeft = MaxSubSum(a, left, mid);
        int sumRight = MaxSubSum(a, mid+1, right);
        int sumLeftBorder = 0, sumRightBorder = 0;
        for (int i = mid, int tmp = 0; i >= left; --i) {
            tmp += a[i];
            sumLeftBorder = max2(sumLeftBorder, tmp);
        }
        for (int i = mid+1, int tmp = 0; i <= right; i++) {
            tmp += a[i];
            sumRightBorder = max2(sumRightBorder, tmp);
        }
        return max3(sumLeft, sumRight, sumLeftBorder + sumRightBorder);
    }
    ```

- $\Omicron(N)$，动态规划思想

    ```c
    int MaxSubsequenceSum(const int a[], int N)
    {
        int res = 0, tmp = 0;
        for (int i = 0; i < N; i++) {
            tmp += a[i];
            res = max(res, tmp);
            tmp = max(tmp, 0);
        }
        return res;
    }
    ```

## 例：对数复杂度有关问题

- 对分查找：$\Omicron(\log(N))$
- 欧几里得算法：$\Omicron(\log(N))$，因为 $\text{if}\;m>n,\;\text{then}\;m\;\text{mod}\;n<m/2$

    ```c title="gcd"
    int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }
    ```
    
- 快速幂：$\Omicron(\log(N))$，即二进制取幂

    === "非递归实现"

        ```c
        long long binPow(long long a, long long b)
        {
            long long res = 1;
            while (b > 0) {
                if (b & 1) res = res * a;
                a = a * a;
                b >>= 1;
            }
            return res;
        }
        ```

    === "递归实现"

        ```c
        long long binPow(long long a, long long b)
        {
            if (0 == b) return 1;
            long long res = binPow(a, b / 2);
            if (b % 2) {
                return res * res * a;
            } else {
                return res * res;
            }
        }
        ```