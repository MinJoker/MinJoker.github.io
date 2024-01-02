# 排序与哈希

## 排序

### 插入排序

- 最坏情况：待排序列是逆序的，复杂度 $\Omicron(N^ 2)$
- 最好情况：待排序列是有序的，复杂度 $\Omicron(N)$

```c
void insertionSort(ElementType arr[], int N)
{
    for (int i = 1; i < N; i++) {
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
    void shellSort(ElementType arr[], int N)
    {
        for (int inc = N/2; inc > 0; inc /= 2) {
            for (int i = inc; i < N; i++) {
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