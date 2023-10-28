# Bags, Queues and Stacks

## 栈与队列

- 栈（stack）是后进先出的（LIFO: last in first out）
- 队列（queue）是先进先出的（FIFO: first in first out）

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/3.png" alt="栈和队列图示" style="width: 60%;">
</div>

## 算法实现

### Stack

基本的 Stack API 如下：

| public class StackOfStrings | Descriptions |
| :---: | :---: |
| **StackOfStrings()** | create an empty stack |
| **void push(String item)** | insert a new string onto stack |
| **String pop()** | remove and return the string most recently added |
| **boolean isEmpty()** | check if the stack is empty |

#### Linked-List

- Data Structure：
    - 链表（linked-list）的每个节点（node）存储着节点的值和指向下个节点的引用（在 Java 中是引用，在 C 中应该是指针）；
    - 维护一个引用`first`，使其始终指向链表的头节点；

???+ note "Push and Pop"

    **Push:**

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/5.png" alt="Push 图示" style="width: 50%;">
    </div>

    **Pop:**

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/4.png" alt="Pop 图示" style="width: 50%;">
    </div>

```java linenums="1" title="Linked-List Stack: Java Implementation"
public class LinkedStackOfStrings
{
    private Node first = null;

    private class Node          // private inner class.
    {
        String item;
        Node next;
    }

    public boolean isEmpty()
    {
        return first == null;
    }

    public void push(String item)
    {
        Node oldfirst = first;
        first = new Node();
        first.item = item;
        first.next = oldfirst;
    }

    public String pop()
    {
        String item = first.item;
        first = first.next;
        return item;
    }
}
```

---

#### Array

- Data Structure：
    - 数组`s[]`，并用`N`来记录当前栈中的元素数量；
    - 数组有固定的大小`capacity`；

- Push：
    - 添加一项`s[N]`；

- Pop：
    - 移除一项`s[N-1]`；
    - pop 的实现有个小细节，如果我们仅仅写一句`return s[--N];`，就会导致被 pop 的数据发生游离（loitering），意思是这个数据已经作废了，但我们却依然维护着它（仍然能通过数组访问到它）；<br />
    合理的解决方案是在 pop 的同时，把游离数据设置为`null`；

```java linenums="1" title="Array Stack: Java Implementation"
public class FixedCapacityStackOfStrings
{
    private String[] s;
    private int N = 0;                                  // N is the number of items in the stack.

    public FixedCapacityStackOfStrings(int capacity)    // capacity is the size of the stack.
    {
        s = new String[capacity];
    }

    public boolean isEmpty()
    {
        return N == 0;
    }

    public void push(String item)
    {
        s[N++] = item;
    }

    public String pop()
    {
        String item = s[--N];
        s[N] = null;                                    // Avoid loitering.
        return item;
    }
}
```

---

#### Resizing-Array

前面使用数组来实现栈的时候存在一个很大的问题，因为数组必须在一开始就定义大小`capacity`，而让用户来决定要使用多大的数组是不合理的。

我们可以利用可变数组（resizing-array）来解决这个问题。

如何让数组自动变化大小呢？核心思路是，当数组满了的时候，我们创建一个新的两倍大小的数组，并将数据搬迁到新数组存储。这样的做法使得我们并不需要消耗很多的运行时间，就可以解决数组可变的问题。这里我们运用了均摊分析（amoritized analysis）来衡量时间消耗，虽然每次改变数组大小的时候都需要 $O(2^N)$ 的时间，但均摊到每个数组元素上其实只需要 $O(1)$ 的时间。

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/6.png" alt="均摊分析图示" style="width: 80%;">
</div>

此外，我们也可以让可变数组在数据较少的时候把数据搬迁到另一个新的一半大小的数组。值得思考的是，什么时候需要缩小数组？一种很自然的想法是以数组半满为界，当数据少于数组大小的一半时，我们缩减数组到原来大小的一半。但是这种方法在最坏情况下消耗会很大，比如在数组半满附近反复执行 push 和 pop 操作。一种更好的解决方法是以四分之一满为界。

---

总结一下 resizing-array 的特征：

- 当数组满了的时候，倍增数组大小；
- 当数组四分之一满的时候，倍减数组大小；
- 从而保证数组总是保持在 25% - 100% full 的状态；

```java linenums="1" title="Resizing-Array Stack: Java Implementation"
public class ResizingArrayStackOfStrings
{
    private String[] s;
    private int N = 0;

    public ResizingArrayStackOfStrings()
    {
        s = new String[1];
    }

    public boolean isEmpty()
    {
        return N == 0;
    }

    public void push(String item)
    {
        if (N == s.length){                 // If the array is full, resize it to twice the size.
            resize(2 * s.length);
        }
        s[N++] = item;
    }

    public String pop()
    {
        String item = s[--N];
        s[N] = null;
        if (N > 0 && N == s.length / 4){    // If the array is one-quarter full, resize it to half the size.
            resize(s.length / 2);
        }
        return item;
    }

    private void resize(int capacity)
    {
        String[] copy = new String[capacity];
        for (int i = 0; i < N; i++){
            copy[i] = s[i];
        }
        s = copy;
    }
}
```

---

#### L-List vs. R-Array

- 链表：
    - 每个操作总是消耗常数时间，即使是在最坏的情况下；
    - 需要消耗额外的时间和空间来处理链表结构；
- 可变数组：
    - 均摊分析后具有更高的平均效率，但是消耗会集中在在临界状态（需要倍增或倍减数组时）发生；
    - 浪费更少的空间；

同样是栈，链表实现和可变数组实现的确存在差别，客户端可以根据需求做出选择。如果想要保证每个操作能够较快完成，就使用链表实现；如果只是关心总的时间，那就可以用可变数组实现，因为这样总的时间效率会更高，单个操作非常快。

> 以下情形你不会想用可变数组实现：<br />
> 你有一驾飞机进场等待降落，你不会想系统突然间不能高效运转；或者互联网上的一个路由器，数据包以很高的速度涌进来，你不会想因为某个操作突然变得很慢而丢失一些数据。