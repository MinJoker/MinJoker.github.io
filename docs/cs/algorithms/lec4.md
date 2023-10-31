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

## 插入排序

## 希尔排序

## 排序的简单应用

### 随即洗牌

### 凸包