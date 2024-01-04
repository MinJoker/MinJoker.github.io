# 排序与哈希

## 排序

### 插入排序

- 最坏情况：待排序列是逆序的，复杂度 $\Omicron(N^ 2)$
- 最好情况：待排序列是有序的，复杂度 $\Omicron(N)$

```c
void insertionSort(ElementType arr[], int n)
{
    for (int i = 1; i < n; i++) {
        ElementType tmp = arr[i];
        int j = i;
        for (; j > 0 && arr[j - 1] > tmp; j--)
            arr[j] = arr[j-1];
        arr[j] = tmp;
    }
}
```

- 逆序对（inversion）是序列中的有序数对 (i,j)，满足 i < j 且 arr[i] > arr[j]
- 对于 $N$ 个元素的随机的待排序列，平均逆序对数为 $\frac{N(N-1)}{4}$
- 基于交换相邻元素的排序算法（例如冒泡、插入），平均情况复杂度下界为 $\Omega(N^ 2)$

### 希尔排序

- 希尔排序（Shell sort）是插入排序的一种改进版本
    - 希尔排序对不相邻的元素进行比较和移动
- 希尔排序基于一个增量序列 $h_1<h_2<\cdots <h_t$，其中 $h_1 = 1$
- 在原始序列中隔 $h_k-1$ 个元素取子序列，进行插入排序，称为 $h_k$-sort
- 逐次进行 $h_t$-sort，$h_{t-1}$-sort，...，$h_1$-sort，排序完成
    - $h_k$-sorted 的序列在 $h_{k-1}$-sort 后仍保持 $h_i$-sorted 的性质
- 希尔排序的复杂度和增量序列的选取有关
- 希尔增量序列：$h_t=\lfloor N/2 \rfloor$，$h_k=\lfloor h_{k+1}/2 \rfloor$
    - 最坏情况：只在 $h_1$-sort 时进行了排序，复杂度 $\Omicron(N^ 2)$

    ```c
    void shellSort(ElementType arr[], int n)
    {
        for (int inc = n / 2; inc > 0; inc /= 2) {
            for (int i = inc; i < n; i++) {
                ElementType tmp = arr[i];
                int j = i;
                for (; j >= inc; j -= inc) {
                    if (arr[j - inc] > tmp)
                        arr[j] = arr[j - inc];
                    else
                        break;
                }
                a[j] = tmp;
            }
        }
    }
    ```

- Hibbard 增量序列：$h_k=2^ k-1$
    - 最坏情况复杂度为 $\Omicron(N^ {3/2})$
    - 平均情况复杂度为 $\Omicron(N^ {5/4})$
- Sedgewick 增量序列：1,5,19,41,109,...
    - 最坏情况复杂度为 $\Omicron(N^ {4/3})$
    - 平均情况复杂度为 $\Omicron(N^ {7/6})$
- 希尔排序是一种简单的排序算法（即使其分析相当复杂），适合于中等规模（数万）数据的排序

### 堆排序

- 算法一：
    - 将待排数据以线性时间建小根堆，然后依次从堆顶取出最小元素
    - 时间复杂度 $\Omicron(N\log N)$
    - 但是空间消耗翻倍了（从堆中取最小元素后需要额外的空间存放排好序的序列）
- 算法二：
    - 将待排数据以线性时间建大根堆，并重复如下操作：
        - 将堆顶元素与堆尾元素交换（相当于删除了最大值），并进行向下调整
        - 将堆顶元素与堆尾元素交换（相当于删除了次大值），并进行向下调整
        - 如此循环 N-1 次后，得到一个从小到大的有序序列
    
    ```c
    void heapSort(ElementType arr[], int n)
    {   /* arr[0] is a sential */
        for (int i = n / 2; i >= 1; i--)
            percolateDown(arr, n, i);
        for (int i = n; i > 1; i--) {
            swap(&arr[1], &arr[i]);
            percolateDown(arr, i - 1, 1);
        }
    }
    ```

    - 对于相异元素的随机序列，堆排序的平均比较次数为 $2N\log N-\Omicron(N\log \log N)$
    - 算法二实现的堆排序是原地排序，没有消耗额外的空间

- 堆排序的平均、最好、最坏复杂度均为 $\Omicron(N\log N)$
- 堆排序的平均复杂度是优秀的，但在实际应用时往往不如基于 Sedgewick 增量序列的希尔排序

### 归并排序

- 归并排序基于分治思想，将数据分段排序后再依次合并
- 关键的操作是合并两个有序序列成为一个有序序列，可以在 $\Omicron(N)$ 时间内完成
- 需要额外的空间来辅助合并，通常使用与原数组等长的辅助数组
- $T(N)=2T(\frac{N}{2})+\Omicron(N)$，时间复杂度为 $\Omicron(N\log N)$
- 递归实现

    ```c
    void mergeSort(ElementType arr[], int n) {
        ElementType *tmp = malloc(sizeof(ElementType) * n);
        if (tmp != NULL) {
            mergeSortHelper(arr, tmp, 0, n - 1);
            free(tmp);
        } else {  /* need O(N) extra space */
            printf("no space for tmp array");
        }
    }

    void mergeSortHelper(ElementType arr[], ElementType tmp[], int left, int right) {
        if (left < right) {
            int center = (left + right) / 2;
            mergeSortHelper(arr, tmp, left, center);
            mergeSortHelper(arr, tmp, center + 1, right);
            merge(arr, tmp, left, center + 1, right);
        }
    }

    void merge(ElementType arr[], ElementType tmp[], int leftPos, int rightPos, int rightEnd) {
        int leftEnd = rightPos - 1;
        int tmpPos = leftPos;
        int numElements = rightEnd - leftPos + 1;
        while (leftPos <= leftEnd && rightPos <= rightEnd) {
            if (arr[leftPos] <= arr[rightPos])
                tmp[tmpPos++] = arr[leftPos++];
            else
                tmp[tmpPos++] = arr[rightPos++];
        }
        while (leftPos <= leftEnd)
            tmp[tmpPos++] = arr[leftPos++];
        while (rightPos <= rightEnd)
            tmp[tmpPos++] = arr[rightPos++];
        for (int i = 0; i < numElements; i++, rightEnd--)
            arr[rightEnd] = tmp[rightEnd];
    }
    ```

- 也可以迭代实现，从长度 1 开始不断倍增分割长度，并依次合并
- 归并排序需要额外的辅助空间并多次复制整个数组，所以通常不用于内排序（internal sort），但常用于外排序（external sort）

### 快速排序

- 快速排序是已知的实际运行最快的排序算法
- 选择一个基准元素（pivot），将待排序列分成两部分，左边的元素都小于等于基准元素，右边的元素都大于等于基准元素，然后递归地对左右两部分继续使用快速排序
    - 注意，左右两部分分别进行排序之后，不需要合并操作，因为此时序列已经完全有序
- 选取 pivot
    - 错误方法：pivot = arr[0]，对于已经有序的序列仍会消耗 $\Omicron(N^ 2)$ 的时间
    - 安全方法：pivot = random element in arr，然而随机数的生成也有开销
    - 三数中值：pivot = (left + center + right) / 3
- 递归实现

    ```c
    void quickSort(ElemenType arr[], int n) {
        quickSortHelper(arr, 0, n - 1);
    }

    void quickSortHelper(ElementType arr[], int left, int right) {
        ElementType pivot = median3(arr, left, right);
        int i = left, j = right - 1;
        for (;;) {
            while (arr[++i] < pivot) continue;
            while (arr[--j] > pivot) continue;
            if (i < j)
                swap(&arr[i], &arr[j]);
            else
                break;
        }
        swap(&arr[i], &arr[right - 1]);
        quickSortHelper(arr, left, i - 1);
        quickSortHelper(arr, i + 1, right);
    }

    ElementType median3(ElementType arr[], int left, int right) {
        int center = (left + right) / 2;
        if (arr[left] > arr[center])  swap(&arr[left], &arr[center]);
        if (arr[left] > arr[right])   swap(&arr[left], &arr[right]);
        if (arr[center] > arr[right]) swap(&arr[center], &arr[right]);
        swap(&arr[center], &arr[right - 1]);    /* hide pivot */
        /* only need to sort arr[left + 1] ... arr[right - 2] */
        return arr[right - 1];
    }
    ```

- 最坏情况：每次选取的 pivot 总是序列的最值，复杂度 $\Omicron(N^ 2)$
- 最好情况：每次选取的 pivot 总是序列的中位数，复杂度 $\Omicron(N\log N)$
- 平均情况：每次选取的 pivot 是随机的，复杂度 $\Omicron(N\log N)$
- 对于小规模数据（$N\leq 20$），快排慢于插入排序
    - 可以在快排递归到 $N$ 较小的情况下改用插入排序
- 线性找第 k 大的数
    - 利用快排，每次用 pivot 划分数据的时候，根据左右的元素个数，判断第 k 大的数是在哪一边，从而只对左边或右边继续递归快排
    - 如果每次选取的 pivot 是随机的，复杂度为 $\Omicron(N)$
- 基于比较的排序算法，最坏情况下复杂度下界为 $\Omega(N\log N)$
    - 基于比较的排序算法：冒泡、选择、插入、希尔、堆排、归并、快排等
    - 非基于比较的排序算法：桶排、基数等

### 桶排序

- 桶排序的原理是将待排数据分到有限数量的桶里，每个桶再分别排序（可以使用其他排序算法，也可以继续递归地桶排序）
    1. 设置一个定量的数组作为空桶
    2. 遍历序列，并将元素一个个放到对应的桶里
    3. 对每个非空的桶里的序列进行排序
    4. 从非空的桶里把元素再放回到原来的序列中
- 桶排序适合待排数据值域较大且分布比较均匀的情况
- 桶排序的一种简单运用：
    - 如果输入数据都小于 $M$，则可以用一个大小为 $M$ 的数组来记录某个值出现的次数，这个数组称为桶
    - 初始化空桶，遍历输入数据，将每个数据对应的桶加 1
    - 最后遍历桶中的所有元素，对于 bucket[x] = y，将 x 输出 y 次
    - 时间复杂度为 $\Omicron(N+M)$

### 基数排序

- 基数排序（radix sort）将待排元素拆分为 k 个关键字，逐一基于关键字对元素进行排序
- 如果从第 1 关键字到第 k 关键字顺序进行，则称为 MSD（most significant digit first）
- 如果从第 k 关键字到第 1 关键字顺序进行，则称为 LSD（least significant digit first）
- 例如对 64，8，343，512，27，729，0，1，316，125 进行 LSD 排序
    - 使用十个桶，分别代表数字 0~9
    - 第一轮，按个位数排序
        - 0，1，512，343，64，125，316，27，8，729
    - 第二轮，按十位数排序
        - (0，1，8)，(512，316)，(125，27，729)，343，64
    - 第三轮，按百位数排序
        - (0，1，8，27，64)，125，(316，343)，512，729
    - 排序完成
    - 时间复杂度为 $\Omicron(P(N+B))$，其中 $P$ 为轮数，$N$ 为元素个数，$B$ 为桶个数
- 例如对 64，8，343，512，27，729，0，1，316，125 进行 MSD 排序
    - 使用十个桶，分别代表数字 0~9
    - 第一轮，按百位数排序
        - (64，8，27，0，1)，125，(343，316)，512，729
    - 第二轮，按十位数排序
        - (8，0，1，27，64)，125，(316，343)，512，729
    - 第三轮，按个位数排序
        - (0，1，8，27，64)，125，(316，343)，512，729
    - 排序完成
- 字典序（lexicographical order）也蕴含着关键字和基数排序
- LSD 不需要像 MSD 那样需要借助递归来实现，通常拥有更快的排序速度

### 其他

- 当待排数据的每个元素都在内存中占据很大的空间时，交换元素的开销是巨大的，可以为每个元素添加一个索引，将对元素排序改为对索引排序
- 排序算法的稳定性是指相等的元素经过排序后相对顺序是否发生了改变
    - 稳定排序：冒泡、插入、归并、基数等
    - 不稳定排序：选择、希尔、堆排、快排等

## 哈希

### 哈希表

- 哈希表（hash table）也称为散列表，以 (key, value) 的形式存储数据，即任意的关键字 key 都唯一对应到内存中的某个位置，只要输入查找的关键字，就可以快速地找到相应的 value
    - 可以把哈希表理解为一种高级的数组，这种数组的下标可以是很大的整数、浮点数、字符串甚至结构体
    - 关键字也称标识符（identifier）
- 通常用一个数组来实现，可以有多个槽（slot），即多个关键字对应同一个位置时，将不同关键字存在同一个位置的不同槽中
- 对于关键字 $x$，定义一个哈希函数 $f(x)$ 表示 $x$ 在哈希表 ht[] 中的位置（索引）
- 设哈希表 ht[] 的大小为 $b$（即 $f(x)$ 值域为 $[0,b-1]$），最多有 $s$ 个槽，则定义以下值：
    - $T$ 表示 $x$ 可能的不同取值个数
    - $n$ 表示 ht[] 中关键字的个数
    - 标识符密度（identifier density）定义为 $\frac{n}{T}$
    - 装载密度（loading density）定义为 $\lambda=\frac{n}{sb}$
- 当存在 $i_1\not =i_2$ 且 $f(i_1)=f(i_2)$ 的情况，则称为发生了碰撞（collision）
- 当将一个新的关键字映射到一个满的位置时（即槽已经被填满），则称为发生了溢出（overflow）
    - 当 $s=1$ 时，碰撞和溢出将同时发生