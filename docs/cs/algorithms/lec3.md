# Bags, Queues and Stacks

## 栈与队列

- 栈（stack）是后进先出的（LIFO: last in first out）
- 队列（queue）是先进先出的（FIFO: first in first out）

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/3.png" alt="栈和队列图示" style="width: 60%;">
</div>

栈和队列作为两种抽象数据类型（Abstract Data Type, or ADT），它们的思想和实现都非常相似，主要区别就只有后进先出还是先进先出。

接下来我们将分析栈和队列的具体实现方法，你可以更深刻地认识到它们的相似性和差异性。

???+ note "在开始之前……"

    或许你会对标题中的 bags 感到疑惑。*Algorithms I* 的这一节内容中，仅在讲解迭代器的时候稍微提了一下这个概念：

    > 事实上很多客户端并不关心我们返回元素的顺序，我们经常做的是直接插入元素，元素的顺序并不重要，接下来遍历已有的元素。这样的数据结构叫做背包……<br />
    > 只需要将栈的出栈操作和队列的出队操作去掉，我们就能获得背包这个有用的数据结构的良好实现。

    笔者并不满意这样的解释。下面是参考了 [Stack Overflow](https://stackoverflow.com/questions/43428114/reasons-for-using-a-bag-in-java) 后的一些观点：

    > 任何能容纳元素的结构都是 Collection，其中不允许元素重复的称为 Set，允许元素重复的称为 Bag。<br />
    > Stack 和 Queue 都属于 Bag，他们允许元素重复，但是按照一定的顺序组织数据。

    笔者认为，最值得关心的是，「是否允许重复」是区别不同 ADT 的一个重要特性，「数据的组织顺序」也是区别不同 ADT 的一个重要特性。

### 栈

基本的 Stack API 如下：

| public class StackOfStrings | Descriptions |
| :---: | :---: |
| **StackOfStrings()** | create an empty stack |
| **void push(String item)** | insert a new string onto stack |
| **String pop()** | remove and return the string most recently added |
| **boolean isEmpty()** | check if the stack is empty |

??? example "客户端测试程序"

    ```java linenums="1" title="Stack Test Client"
    import java.util.Scanner;

    public class StackTestClient
    {
        public static void main(String[] args)
        {
            StackOfStrings stack = new StackOfStrings();    // StackOfStrings: Linked, FixedCapacity, ResizingArray.
            Scanner scanner = new Scanner(System.in);
            while (scanner.hasNext()){
                String s = scanner.next();
                if (s.equals("-")){                         // when input is "-", pop the top string from stack.
                    System.out.print(stack.pop() + " ");
                } else{
                    stack.push(s);
                }
            }
            scanner.close();
        }
    }
    ```

---

#### 链表实现

- Data Structure：
    - 链表（linked-list）的每个节点（node）存储着节点的值和指向下个节点的引用（在 Java 中是引用，在 C 中应该是指针）；
    - 维护一个引用`first`，使其始终指向链表的头节点（如果链表为空，则`first`为空；如果链表非空，则`first`为头节点的一个引用）；
- Push：
  <div style="text-align: left;">
  <img src="/assets/images/cs/algorithms/5.png" alt="Push 图示" style="width: 50%;">
  </div>
- Pop：
  <div style="text-align: left;">
  <img src="/assets/images/cs/algorithms/4.png" alt="Pop 图示" style="width: 50%;">
  </div>

```java linenums="1" title="Linked-List Stack: Java Implementation"
public class LinkedStackOfStrings
{
    private Node first = null;  // top of stack (most recently added node), null if empty.

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

#### 数组实现

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

#### 可变数组实现

前面使用数组来实现栈的时候存在一个很大的问题，因为数组必须在使用之初就定义大小`capacity`，而`capacity`的值不可能是固定的，需要客户端根据数据量进行估计后输入`capacity`，这显然是不合理的，我们不希望客户端关心任何具体实现的问题。

我们可以利用可变数组（resizing-array）来解决这个问题。

如何让数组自动变化大小呢？核心思路是，当数组满了的时候，我们创建一个新的两倍大小的数组，并将数据搬迁到新数组存储。这样的做法使得我们并不需要消耗很多的运行时间，就可以解决数组可变的问题。这里我们运用了均摊分析（amoritized analysis）来衡量时间消耗，虽然每次改变数组大小的时候都需要 $O(N)$ 的时间，但均摊到每个数组元素上其实只需要 $O(1)$ 的时间。

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
        if (N > 0 && N == s.length / 4){    // N > 0 to avoid resizing to 0.
            resize(s.length / 2);           // If the array is one-quarter full, resize it to half the size.
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

### 队列

基本的 Queue API 如下：

| public class QueueOfStrings | Descriptions |
| :---: | :---: |
| **QueueOfStrings()** | create an empty queue |
| **void enqueue(String item)** | insert a new string onto queue |
| **String dequeue()** | remove and return the string least recently added |
| **boolean isEmpty()** | check if the queue is empty |

??? example "客户端测试程序"

    ```java linenums="1" title="Queue Test Client"
    import java.util.Scanner;

    public class QueueTestClient
    {
        public static void main(String[] args)
        {
            QueueOfStrings queue = new QueueOfStrings();    // QueueOfStrings: Linked, ResizingArray
            Scanner scanner = new Scanner(System.in);
            while (scanner.hasNext()){
                String s = scanner.next();
                if (s.equals("-")){                         // when input is "-", dequeue the top string from queue.
                    System.out.print(queue.dequeue() + " ");
                } else{
                    queue.enqueue(s);
                }
            }
            scanner.close();
        }
    }
    ```

---

#### 链表实现

- Data Structure：
    - 链表结构和 Linked-List Stack 一致；
    - 维护两个引用`first`和`last`，分别指向链表的头节点和尾节点；
- Enqueue：
  <div style="text-align: left;">
  <img src="/assets/images/cs/algorithms/8.png" alt="Push 图示" style="width: 50%;">
  </div>
- Dequeue：
  <div style="text-align: left;">
  <img src="/assets/images/cs/algorithms/7.png" alt="Pop 图示" style="width: 50%;">
  </div>

值得注意的是，当我们使用单向链表的时候，enqueue 和 duqueue 分别位于哪里是很重要的。试想一下，如果我们不按上面图示的那样来做，而是改为在链表的头进行 enqueue，在链表的尾进行 dequeue。enqueue 并不会有什么问题，就像我们在 stack 中做的那样；但是 dequeue 出错了，如果我们把尾节点 dequeue 了，那么`last`就没有办法移动到新的尾节点，这是链表的单向性导致的。

```java linenums="1" title="Linked-List Queue: Java Implementation"
public class LinkedQueueOfStrings
{
    private Node first, last;       // null by default.

    private class Node
    {
        String item;
        Node next;
    }

    public boolean isEmpty()
    {
        return first == null;
    }

    public void enqueue(String item)
    {
        Node oldlast = last;
        last = new Node();
        last.item = item;
        last.next = null;
        if (isEmpty()){             // special case for empty queue.
            first = last;
        } else{
            oldlast.next = last;
        }
    }

    public String dequeue()
    {
        String item = first.item;
        first = first.next;
        if (isEmpty()){             // special case for empty queue.
            last = null;
        }
        return item;
    }
}
```

---

#### 可变数组实现

- Data Structure：
    - 数组`q[]`，并用`head`和`tail`标记队列的头与尾，用`N`记录当前队列中的元素数量；
    - 数组可变，
- Enqueue：
    - 添加一项`q[tail]`；
- Dequeue：
    - 移除一项`q[head]`；

<div style="text-align: center;">
<img src="/assets/images/cs/algorithms/9.png" alt="Resizing-Array Queue 图示" style="width: 80%;">
</div>

值得注意的是，队列的可变数组实现比栈要多一些细节。因为队列的头和尾都是会移动的，为了充分利用数组空间，我们使`head`和`tail`在抵达数组结尾的时候，重新回到数组开头。这非常有趣，你可以想象数组的头和尾之间有一个传送门，队列就像一条蛇一样，从数组的尾出去，又从数组的头回来。

```java linenums="1" title="Resizing-Array Queue: Java Implementation"
public class ResizingArrayQueueOfStrings
{
    private String[] s;
    private int N = 0;                      // number of items in queue.
    private int head = 0;
    private int tail = 0;

    public ResizingArrayQueueOfStrings()
    {
        s = new String[1];
    }

    public boolean isEmpty()
    {
        return N == 0;
    }

    public void enqueue(String item)
    {
        if (N == s.length){
            resize(2 * s.length);
        }
        s[tail++] = item;
        N++;
        if (tail == s.length){              // wrap-around to the beginning.
            tail = 0;
        }
    }

    public String dequeue()
    {
        String item = s[head];
        s[head] = null;
        head++;
        N--;
        if (head == s.length){              // wrap-around to the beginning.
            head = 0;
        }
        if (N > 0 && N == s.length / 4){
            resize(s.length / 2);
        }
        return item;
    }

    private void resize(int capacity)
    {
        String[] copy = new String[capacity];
        for (int i = 0; i < N; i++){        // copy elements from head to tail, wrap-around.
            copy[i] = s[(head + i) % s.length];
        }
        s = copy;
        head = 0;
        tail = N;
    }
}
```

### 链表&thinsp;vs.&thinsp;可变数组

- 链表：
    - 每个操作总是消耗常数时间，即使是在最坏的情况下；
    - 需要消耗额外的时间和空间来处理链表结构；
- 可变数组：
    - 均摊分析后具有更高的平均效率，但是消耗会集中在在临界状态（需要倍增或倍减数组时）发生；
    - 浪费更少的空间；

同样是栈或者队列，链表实现和可变数组实现的确存在差别，客户端可以根据需求做出选择。如果想要保证每个操作能够较快完成，就使用链表实现；如果只是关心总的时间，那就可以用可变数组实现，因为这样总的时间效率会更高，绝大部分时候单个操作非常快。

> 以下情形你不会想用可变数组实现：<br />
> 有一驾飞机进场等待降落，你不会想系统突然间不能高效运转；或者互联网上的一个路由器，数据包以很高的速度涌进来，你不会想因为某个操作突然变得很慢而丢失一些数据。

## 泛型

你可能已经发现了，上文中我们实现的栈和队列，都把元素类型设定为字符串，比如说 StackOfStrings。那么，如果我们还想要 StackOfURLs，StackOfInts，StackOfVans 等其他类型呢？

=== "简单粗暴"

    最简单粗暴的方法就是为每种数据类型进行相应的代码实现，听起来就很麻烦而且愚蠢。

    但不幸的是，早期的 Java 就是陷在这种模式里，事实上很多编程语言都无法摆脱这样的模式。

=== "强制类型转换"

    另一种广泛采用的方法是，使用强制类型转换，从而对不同数据类型重用代码。我们用 Object 类实现数据结构，而由于 Java 中所有的类都是 Object 的子类，当客户端使用强制类型转换的时候，就能将结果转为对应的类型。

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/10.png" alt="强制类型转换代码示例" style="width: 45%;">
    </div>

    这样做的弊端很明显：

    - 强制类型转换需要在客户端实现，这并不能让人满意；
    - 当类型不匹配的时候，编译不会报错，把错误留到运行时再发生是很糟糕的；

=== "泛型"

    Java 给出了一种很优秀的解决方法，那就是使用泛型（generics）。

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/11.png" alt="泛型代码示例" style="width: 60%;">
    </div>

    泛型解决了强制类型转换带来的弊端：

    - 避免客户端进行强制类型转换；
    - 当类型不匹配的时候，编译会报错，从而避免在运行时发生错误；

---

Java 泛型的优势是显著的。下面让我们来看看怎么用泛型来修改上文中的栈实现：

=== "链表"

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/12.png" alt="泛型栈的链表实现" style="width: 100%;">
    </div>

    非常简单，只需要把所有出现数据类型的地方更换成 Item 即可。

=== "数组"

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/13.png" alt="泛型栈的数组实现（错误版）" style="width: 100%;">
    </div>

    <div style="text-align: left;">
    <img src="/assets/images/cs/algorithms/14.png" alt="泛型栈的数组实现（正确版）" style="width: 100%;">
    </div>

    发生了什么？为什么泛型栈的数组实现不能和链表实现那样简洁自然？

    原因在于，Java 不允许创建泛型数组！对于这个问题，有各种技术方面的问题，网络上也争议不断，这个我们不去讨论。我们的解决方案是，创建 Object 数组，然后强制类型转换为 Item 数组。

    !!! tip

        A good code has no cast.

    笔者赞同这个观点，优秀的代码不应该使用强制类型转换，因为这确实在我们的实现中留下隐患。但在泛型这个问题上我们不得不这么做。

    > 当我们编译这个程序的时候，Java 会发出警告信息，说我们在使用未经检查的强制类型转换。
    > 好吧，他们应该在警告信息里加上“我们很抱歉让你这么做”。

---

上文中我们使用的泛型类型是针对 Object 及其子类的（比如 String）。那么基本数据类型呢（比如 int）？

这时候我们需要使用 Java 的包装类（wrapper class），比如 Integer 是 int 类型的包装类。凭借 Java 的自动打包（autoboxing），我们可以实现同样的泛型功能，唯一的区别在于我们不使用 int 来作为 Item，而是使用其包装类 Integer：

```java
Stack<Integer> s = new Stack<Integer>();
s.push(17);         // s.push(Integer.valueOf(17));
int a = s.pop();    // int a = s.pop().intValue();
```

---

总而言之，我们能实现适用于任何数据类型的泛型 API，而且有链表和数组的两种实现方式，并且对任何数据类型都能有良好的性能。

## 迭代器

- [ ] ToDo

## 栈和队列的应用

- [ ] ToDo