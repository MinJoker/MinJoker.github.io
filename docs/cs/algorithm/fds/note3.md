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
        } else { /* need O(N) extra space */
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