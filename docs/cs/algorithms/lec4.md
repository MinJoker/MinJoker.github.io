# Elementary Sorts

## 排序问题

一组数据是可排序的，那么它们一定是可以比较大小的，或者说它们满足全序关系（total order）：

- 反对称性（antisymmetry）：如果 $v\leq w$，$w\leq v$，则 $v=w$；
- 传递性（transitivity）：如果 $v\leq w$，$w\leq x$，则 $v\leq x$；
- 完全性（totality）：$v\leq w$ 和 $w\leq v$，至少有一个成立；

我们接下来要讨论的所有数据首先一定是可比较大小的。试想一下，如果我们要写一个`sort()`函数，那么其中必然会涉及数据的大小比较，而且很明显，这样的`sort()`是特化的，只能处理某一类特定类型的数据。我们希望`sort()`函数能够有很好的泛化属性，或者说我们希望它能够排序任何类型的数据。

问题的关键在于，我们调用`sort()`的时候，只是把存储着数据的数组的引用作为参数传递给它，所以在`sort()`内部是无法知道数据的具体类型的。当然，我们可以多传递一个参数，用来描述数据的类型，但这样做还是有点麻烦。我们希望能在只传递数组引用的前提下，让`sort()`能够正常工作。

解决这个问题的方法是使用回调（callback）。回调是一段可执行代码的引用，它使得低层代码能够调用高层代码。Java 通过`Comparable`接口实现回调，客户端把存储着对象的数组的引用作为参数传递给`sort()`，然后`sort()`回调数据对象的`compareTo()`方法来实现数据的大小比较，从而实现排序。

??? note "不同语言的回调实现"

    - Java: interface.
    - C: function pointers.
    - C++: class-type functors.
    - C#: delegates.
    - Python, Perl, ML, JavaScript: first-class functions.

我们来看看怎么实现这个接口：

=== "`Comparable`接口"

    `Comparable`接口有一个方法，能够比较数据的大小，注意这个接口是泛化的。

    ```java title="Comparable interface"
    public interface Comparable<Item>
    {
        public int compareTo(Item that);
    }
    ```

=== "实现了`Comparable`接口的类"

    我们让描述某个数据类型（比如这里的`File`）的类实现`Comparable`接口。

    ```java title="object implementation"
    public class File implements Comparable<File>
    {
        ...
        public int compareTo(File b)
        {
            ...
            return -1;
            ...
            return +1;
            ...
            return 0;
        }
    }
    ```

=== "`sort()`"

    `sort()`函数接受的参数是`Comparable`类型数组，这意味数组中的对象需要实现`Comparable`接口。

    ```java title="sort implementation" hl_lines="6"
    public static void sort(Comparable[] a)
    {
        int N = a.length;
        for (int i=1; i<N; i++){
            for (int j=i; j<N; j++){
                if (a[j].compareTo(a[j-1]) < 0){    // no dependence on File data type!
                    Comparable swap = a[j]; a[j] = a[j-1]; a[j-1] = swap;
                }
            }
        }
    }
    ```

=== "客户端"

    客户端调用`sort()`方法的时候只需要传递数组引用即可。

    ```java title="client"
    File[] files = ...;
    Bubble.sort(files);
    ```

值得强调一下的是，`compareTo()`的返回值分别代表什么意思，比如`v.compareTo(w)`：

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/15.png" alt="v.compareTo(w) 图示" style="width: 80%;">
</div>

为了方便后续的排序函数的书写，我们进行一些封装（这里写这些是为了之后的`sort()`函数可以直接省略这些部分，从而书写得更加清晰简洁）：

=== "`less()`方法"

    ```java
    private static boolean less(Comparable v, Comparable w)
    {
        return v.compareTo(w) < 0;
    }
    ```

=== "`swap()`方法"

    ```java
    private static void swap(Comparable[] a, int i, int j)
    {
        Comparable temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
    ```

---

至此，我们已经做好了所有准备工作。我们通过`Comparable`接口让所有数据类型自身就带上了比较大小的方法`compareTo()`（Java 中的 Integer 等包装类已经实现了`Comparable`接口），这意味着我们的排序函数只需要专注于排序算法本身。下面，就让我们进入排序算法的世界！

## 选择排序

选择排序（Selection sort）和插入排序（Insertion sort）都比较简单，或许你已经基本掌握了。但是，这里我们将采用另一个角度来解读选择排序和插入排序，或许会对你有所启发。

我们来考虑选择排序的不变量（invariants）：

- $\uparrow$ 及其左边的数据是固定的，不会再发生改变；
- $\uparrow$ 右边的任意数据都大于 $\uparrow$ 左边的任意数据；

<div style="text-align: left;">
<img src="/assets/images/cs/algorithms/16.png" alt="选择排序的不变量图示" style="width: 50%;">
</div>

为了维持算法的不变量，每当 $\uparrow$ 向右移动一位的时候，就必须从右边所有待排数据中选出最小的，并把它交换到 $\uparrow$ 所在的位置，从而维持前面提到的不变量。这就是选择排序。

---

```java linenums="1" title="Selection Sort: Java Implementation"
public class Selection
{
    public static void sort(Comparable[] a)
    {
        int N = a.length;
        for (int i=0; i<N; i++){
            int min = i;
            for (int j=i+1; j<N; j++){
                if (less(a[j], a[min])){
                    min = j;
                }
            }
            swap(a, i, min);
        }
    }

    private static boolean less(Comparable v, Comparable w)
    { /* as before */ }

    private static void swap(Comparable[] a, int i, int j)
    { /* as before */ }
}
```

---

我们来对选择排序进行一些算法分析：

- 选择排序需要 $\sim N^2/2$ 次比较和 $N$ 次交换；
- 运行时间与数据原本顺序无关，恒定为 $O(N^2)$，即使数据原本已经有序；

下面通过动画来演示几种典型情况下选择排序的效率（点击动画上方的名称可以重播，点击 Play All 可以同步重播所有动画）：

<!-- CSS for align .gif side by side -->

<style>
.row {
  display: flex;
}
.column {
  flex: 25%;
  padding: 5px;
}
</style>

<!-- end CSS -->

<p style="text-align: center;">
<a onclick="document.getElementById('selection_1').src='/assets/images/cs/algorithms/17.gif'; document.getElementById('selection_2').src='/assets/images/cs/algorithms/18.gif'; document.getElementById('selection_3').src='/assets/images/cs/algorithms/19.gif'; document.getElementById('selection_4').src='/assets/images/cs/algorithms/20.gif'">Play All</a>
</p>

<div class="row">
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('selection_1').src='/assets/images/cs/algorithms/17.gif'">Random</a>
    </div>
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('selection_2').src='/assets/images/cs/algorithms/18.gif'">Nearly Sorted</a>
    </div>
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('selection_3').src='/assets/images/cs/algorithms/19.gif'">Reversed</a>
    </div>
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('selection_4').src='/assets/images/cs/algorithms/20.gif'">Few Unique</a>
    </div>
</div>

<div class="row">
    <div class="column" style="text-align: center;">
        <img id="selection_1" src="/assets/images/cs/algorithms/17.gif" style="width: 60%;">
    </div>
    <div class="column" style="text-align: center;">
        <img id="selection_2" src="/assets/images/cs/algorithms/18.gif" style="width: 60%;">
    </div>
    <div class="column" style="text-align: center;">
        <img id="selection_3" src="/assets/images/cs/algorithms/19.gif" style="width: 60%;">
    </div>
    <div class="column" style="text-align: center;">
        <img id="selection_4" src="/assets/images/cs/algorithms/20.gif" style="width: 60%;">
    </div>
</div>

!!! quote "Source: <a href="https://www.toptal.com/developers/sorting-algorithms/selection-sort">https://www.toptal.com/developers/sorting-algorithms/selection-sort</a>"

## 插入排序

我们继续用不变量来考虑插入排序：

- $\uparrow$ 及其左边的数据是升序的；
- $\uparrow$ 右边的数据目前是不可见的；

<div style="text-align: left;">
<img src="/assets/images/cs/algorithms/21.png" alt="插入排序的不变量图示" style="width: 50%;">
</div>

为了维持算法的不变量，每当 $\uparrow$ 向右移动一位的时候，就必须把多出来的这一位数据与 $\uparrow$ 左边的数据从右往左逐个比较并交换，直到找到比自己小的数据才停下，从而确保 $\uparrow$ 左边保持升序。这就是插入排序。

---

```java linenums="1" title="Insertion Sort: Java Implementation"
public class Insertion
{
    public static void sort(Comparable[] a)
    {
        int N = a.length;
        for (int i=1; i<N; i++){
            for (int j=i; j>0; j--){
                if (less(a[j], a[j-1])){
                    swap(a, j, j-1);
                } else{
                    break;
                }
            }
        }
    }

    private static boolean less(Comparable v, Comparable w)
    { /* as before */ }

    private static void swap(Comparable[] a, int i, int j)
    { /* as before */ }
}
```

---

我们来对插入排序进行一些算法分析：

- 一般情况，排序一组随机顺序的无重复的数据，插入排序需要 $\sim N^2/4$ 次比较和 $\sim N^2/4$ 次交换；
- 最优情况，数据原本有序，插入排序只需要 $N-1$ 次比较和 $0$ 次交换；
- 最差情况，数据原本逆序，插入排序会需要 $\sim N^2/2$ 次比较和 $\sim N^2/2$ 次交换；

??? note "如何证明插入排序在一般情况下的效率"

    我们不去进行仔细的计算，也可以很好地理解为什么插入排序在一般情况下需要 $\sim N^2/4$ 次比较和交换。我们通过一幅图来直观地理解这个事情，图中黑色表示被比较过的，红色表示最终达到的位置。我们会发现对于随机顺序的大数组，移动到最终位置所需的距离平均是移动到最左端位置所需距离的一半，这意味着对角线下的元素有一半是黑色的。对角线下面有 $N^2/2$ 个元素，从而我们比较的次数是 $\sim N^2/4$，交换次数要比这个数多一点，但也是 $\sim N^2/4$。

    <div style="text-align: center;">
    <img src="/assets/images/cs/algorithms/26.png" alt="插入排序的不变量图示" style="width: 70%;">
    </div>

下面通过动画来演示几种典型情况下插入排序的效率（点击动画上方的名称可以重播，点击 Play All 可以同步重播所有动画）：

<p style="text-align: center;">
<a onclick="document.getElementById('insertion_1').src='/assets/images/cs/algorithms/22.gif'; document.getElementById('insertion_2').src='/assets/images/cs/algorithms/23.gif'; document.getElementById('insertion_3').src='/assets/images/cs/algorithms/24.gif'; document.getElementById('insertion_4').src='/assets/images/cs/algorithms/25.gif'">Play All</a>
</p>

<div class="row">
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('insertion_1').src='/assets/images/cs/algorithms/22.gif'">Random</a>
    </div>
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('insertion_2').src='/assets/images/cs/algorithms/23.gif'">Nearly Sorted</a>
    </div>
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('insertion_3').src='/assets/images/cs/algorithms/24.gif'">Reversed</a>
    </div>
    <div class="column" style="text-align: center;">
        <a onclick="document.getElementById('insertion_4').src='/assets/images/cs/algorithms/25.gif'">Few Unique</a>
    </div>
</div>

<div class="row">
    <div class="column" style="text-align: center;">
        <img id="insertion_1" src="/assets/images/cs/algorithms/22.gif" style="width: 60%;">
    </div>
    <div class="column" style="text-align: center;">
        <img id="insertion_2" src="/assets/images/cs/algorithms/23.gif" style="width: 60%;">
    </div>
    <div class="column" style="text-align: center;">
        <img id="insertion_3" src="/assets/images/cs/algorithms/24.gif" style="width: 60%;">
    </div>
    <div class="column" style="text-align: center;">
        <img id="insertion_4" src="/assets/images/cs/algorithms/25.gif" style="width: 60%;">
    </div>
</div>

!!! quote "Source: <a href="https://www.toptal.com/developers/sorting-algorithms/insertion-sort">https://www.toptal.com/developers/sorting-algorithms/insertion-sort</a>"

## 希尔排序

## 排序的简单应用

### 随机洗牌

### 凸包