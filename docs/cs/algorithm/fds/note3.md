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
    - $h_k$-sorted 的序列在 $h_{k-1}$-sort 后仍保持 $h_k$-sorted 的性质
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
        - ((8，0，1)，27，64)，125，(316，343)，512，729
    - 第三轮，按个位数排序
        - ((0，1，8)，27，64)，125，(316，343)，512，729
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
    - 假设用数组 a 存放数据，哈希函数是 f，则 (key, value) 就应该放在 a\[f(key)\] 上
    - 可以把哈希表理解为一种高级的数组，这种数组的下标可以是很大的整数、浮点数、字符串甚至结构体
    - 关键字也称标识符（identifier）
- 通常用一个数组来实现，可以有多个槽（slot），即多个关键字对应同一个位置时，将不同关键字存在同一个位置的不同槽中
- 对于关键字 $x$，定义一个哈希函数 $f(x)$ 表示 $x$ 在哈希表 ht[] 中的位置（索引）
- 设哈希表 ht[] 的大小为 $b$（即 $f(x)$ 值域为 $[0,b-1]$），最多有 $s$ 个槽，则定义以下值：
    - $T$ 表示 $x$ 可能的不同取值个数
    - $n$ 表示 ht[] 中关键字的个数
    - 标识符密度（identifier density）定义为 $\frac{n}{T}$
    - 装载密度（loading density）定义为 $\lambda=\frac{n}{sb}$
- 当存在 $i_1\not =i_2$ 且 $f(i_1)=f(i_2)$ 的情况，则称为发生了冲突（collision）
- 当将一个新的关键字映射到一个满的位置时（即槽已经被填满），则称为发生了溢出（overflow）
    - 当 $s=1$ 时，冲突和溢出将同时发生
- 不考虑溢出，复杂度 $T_{search}=T_{insert}=T_{delete}=\Omicron(1)$

### 哈希函数

- 哈希函数应该易于计算，且减少冲突的可能性
- 哈希函数应该是 unbiased 的，即 $P(f(x)=i)=\frac{1}{b}$，这样的函数称为均匀哈希函数（uniform hash function）
- 对于整数的哈希，可以取 $f(x) = x \bmod tableSize$
    - 其中 tableSize 最好取一个质数，这样对于随机输入，关键字的分布更均匀
- 对于字符串的哈希，可以取 $f(x)=(\sum x[N-i-1] \times 32^ i) \bmod tableSize$
    
    ```c
    Index hash(const char *x, int tableSize) {
        unsigned int hashVal = 0;
        while (*x != '\0')
            hashVal = (hashVal << 5) + *(x++);
        return hashVal % tableSize;
    }
    ```

### 分离链接

- 解决冲突的一种方法是分离链接（separate chaining）
- 在每个存放数据的地方开一个链表，如果有多个关键字索引到同一个位置，只需要把它们都放到那个位置的链表里即可（链表实现的槽）
- 建议：设计哈希表与哈希函数，使 $\lambda \approx 1$

```c
struct ListNode;
typedef struct ListNode *Position;
struct HashTbl;
typedef struct HashTbl *HashTable;
struct ListNode {
    ElementType element;
    Position next;
};
typedef Position List;
struct HashTbl {
    int tableSize;
    List *theLists;
};

HashTable initializeTable(int tableSize) {
    if (tableSize < minTableSize) {
        error("table size too small");
        return NULL;
    }
    HashTable H = malloc(sizeof(struct HashTbl));
    if (H == NULL) fatalError("out of space");
    H->tableSize = nextPrime(tableSize);  // better be prime.
    H->theLists = malloc(sizeof(List) * H->tableSize);
    if (H->theLists == NULL) fatalError("out of space");
    for (int i = 0; i < H->tableSize; i++) {
        H->theLists[i] = malloc(sizeof(struct ListNode));
        if (H->theLists[i] == NULL) fatalError("out of space");
        else H->theLists[i]->next = NULL;
    }
    return H;
}

Position find(ElementType key, HashTable H) {
    List L = H->theLists[hash(key, H->tableSize)];
    Position P = L->next;
    while (P != NULL && P->element != key) P = P->next;
    return P;
}

void insert(ElementType key, HashTable H) {
    Position pos = find(key, H);
    if (pos == NULL) {
        Position newCell = malloc(sizeof(struct ListNode));
        if (newCell == NULL) fatalError("out of space");
        else {
            List L = H->theLists[hash(key, H->tableSize)];
            newCell->next = L->next;
            newCell->element = key;
            L->next = newCell;
        }
    }
}
```

### 开放寻址

- 解决冲突的另一种方法是开放寻址（open addressing）
- 当有冲突发生时，尝试选择其他位置，直到找到空的位置为止
- 建议：设计哈希表与哈希函数，使 $\lambda < 0.5$
- 可以视作使用了多个哈希函数 $h_0(x),h_1(x),...$，其中 $h_i(x)=(hash(x)+f(i)) \bmod tableSize$
    - 其中 $f(i)$ 为增量函数，有多种选取的方式

```c
void insert(ElementType key) {
    index = hash(key); int i = 0;
    while (collision at index) {
        index = (hash(key) + f(i)) % tableSize;
        if (table is full) break;
        else i++;
    }
    if (table is full) error("no space left");
    else table[index] = key;
}
```

#### 线性探测

- 线性探测（linear probing）使用增量函数 $f(i)=i$
- 即冲突了就往后一个一个找，直到找到空的位置为止
- 会导致一次聚集（primary clustering），即如果某个位置发生了冲突，那么后面插入在附近的元素也都很容易发生冲突，需要不断往后寻址，导致插入和搜索开销都大幅增加
    - 理解：any key that hashes into the cluster will add to the cluster after several attempts to resolve the collision.
    - 对于插入和不成功的搜索，探测次数约为 $\frac{1}{2}(1+\frac{1}{(1-\lambda)^ 2})$
    - 对于成功的搜索，探测次数约为 $\frac{1}{2}(1+\frac{1}{1-\lambda})$

#### 平方探测

- 平方探测（quadratic probing）使用增量函数 $f(i)=i^ 2$
- 对于使用平方探测的开放寻址法，如果 tableSize 为质数，且至少一半为空，则总能成功插入一个新的元素
- 如果 tableSize 为形如 4k+3 的质数，则平方探测的探测范围可以覆盖整个表
- 搜索
    - $f(i) = f(i-1) + i^ 2 - (i-1)^ 2 = f(i-1) + 2i - 1$

    ```c
    Position find(ElementType key, HashTable H) {
        Position currentPos = hash(key, H->tableSize); int collisionNum = 0;
        while (H->theCells[currentPos].info != Empty && H->theCells[currentPos].element != key) {
            currentPos += 2 * ++collisionNum - 1;
            if (currentPos >= H->tableSize) currentPos -= H->tableSize;  // faster than mod.
        }
        return currentPos;
    }
    ```

- 插入

    ```c
    void insert(ElementType key, HashTable H) {
        Position pos = find(key, H);
        if (H->theCells[pos].info != Legitimate) {
            H->theCells[pos].info = Legitimate;
            H->theCells[pos].element = key;
        }
    }
    ```

- 会导致二次聚集（secondary clustering），即如果某个位置发生多次冲突，后来发生的冲突会需要不断往后寻址，导致插入和搜索开销都大幅增加
    - 理解：keys that hash to the same position will probe the same alternative cells.

#### 双哈希

- 双哈希（double hashing）使用增量函数 $f(i)=i\times hash_2(x)$，其中 $hash_2$ 是另一个哈希函数
- 一个不错的选取方案：$hash_2(x)=R-(x\bmod R)$，其中 $R$ 为一个小于 tableSize 的质数
- 线性探测与平方探测都会导致 cluster 的出现，这对于开放寻址法的性能有很大的影响，双哈希可以避免这个问题
- 但在实际应用中，由于平方探测不需要使用第二个哈希函数，所以通常更加简单快速

### 再哈希

- 开放寻址法对于 $\lambda < 0.5$ 是适用的，但当表中的元素过多时，操作时间会很长，而且很可能插入失败
- 解决方法是使用再哈希（rehashing）
    1. 建立一个两倍大的哈希表
    2. 扫描原始哈希表
    3. 用一个新的哈希函数把原始哈希表中的元素映射到新的哈希表中，即 $hash_{new}(key)$
- 如果原始哈希表中有 $N$ 个元素，则再哈希的时间复杂度为 $\Omicron(N)$
- 什么时候进行再哈希：
    - 表填满一半了
    - 插入失败
    - 当哈希表达到某个特定的装载密度时