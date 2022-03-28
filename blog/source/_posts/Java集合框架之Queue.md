---
title: Java集合框架之Queue
date: 2022-01-24 20:45:38
categories: Java
tags: 集合
author: 小朱
comments: true
---

# Queue

queue是一种名为队列的数据结构，队列是指符合“先进先出”（FIFO）的一种容器。新元素插入（offer）到队列的尾部，访问元素（poll）操作会返回队列头部的元素。通常，队列不允许随机访问队列中的元素。

deque是双端队列。

<!--more--> 

## PriorityQueue

priorityQueue作为Queue的一个实现类。其保存队列元素的顺序不是按加入队列的顺序，而是按队列元素的大小进行重新排序。因此当调用peek()或poll()方法取出队列中头部的元素时，并不是取出最先进入队列的元素，而是取出队列中的最小的元素。

实际上是一个堆（不指定Comparator时默认为最小堆）

### 底层数据结构

```java
transient Object[] queue; // non-private to simplify nested class access

private int size = 0;
```

底层使用的是Qbject数组以便容纳各种各样的数据类型。可以看出PriorityQueue与ArrayList相似，本质为一个动态数组。而priorityQueue的逻辑结构是一完全二叉树。默认排序为小顶堆。

### 排序方式

之前提到priorityQueue是会按照队列元素的大小进行排序，这里的排序主要分为两种：**自然排序**与**定制排序**。

#### 自然排序（natural ordering）

Comparable接口：

```java
public int compareTo(T o);
```

实现了该接口的类就能进行比较，同时该类也必须重写该接口里的所有的方法。例如，当一个对象调用该方法与另一个对象比较时，例如obj1.compareTo(obj2),如果该方法返回0，则表明两个对象相等；如果该方法返回一个正整数，则表明obj1大于obj2;如果该方法返回一个负整数，则表明oj1小于obj2。

#### 定制排序（堆排序，specified comparator）

Compator接口：

```java
int compare(T o1, T o2);
```

该接口包含一个int compare(T obj1,T obj2)方法，该方法用于比较obj1,obj2的大小：如果该方法返回正整数，则表明obj1大于obj2；如果该方法返回0，则表明o1等于o2;如果该方法返回负整数，则表明obj1小于obj2。

在优先队列中使用定制排序，则需要我们在构造器中传入一个Comparator对象。

PriorityQueue对元素采用的是堆排序，头是按指定排序方式的最小元素。堆排序只能保证根是最大（最小），整个堆并不是有序的。

### 方法剖析

#### add() || offer()

首先来看看add方法的源码:

```java
public boolean add(E e) {
	return offer(e);
}
```

可以看到add方法的底层就是offer()方法。

```java
public boolean offer(E e) {
  if (e == null)
	  throw new NullPointerException();//不允许插入null元素
	modCount++;
	int i = size;
	if (i >= queue.length)
		grow(i + 1);
	size = i + 1;
	if (i == 0)
		queue[0] = e;
	else
		siftUp(i, e);
	return true;
}
```



这里会有判断数组是否越界的if判断语句，如果数组越界则会调用grow()函数，表明了PriorityQueue实现了扩容机制，而真正意义上实现插入的函数是splitUp()函数。

```java
    private void siftUp(int k, E x) {
        if (comparator != null)
            siftUpUsingComparator(k, x);
        else
            siftUpComparable(k, x);
    }

    @SuppressWarnings("unchecked")
    private void siftUpComparable(int k, E x) {
        Comparable<? super E> key = (Comparable<? super E>) x;
        while (k > 0) {
            int parent = (k - 1) >>> 1;
            Object e = queue[parent];
            if (key.compareTo((E) e) >= 0)
                break;
            queue[k] = e;
            k = parent;
        }
        queue[k] = key;
    }
		
		@SuppressWarnings("unchecked")
    private void siftUpUsingComparator(int k, E x) {
        while (k > 0) {
            int parent = (k - 1) >>> 1;
            Object e = queue[parent];
            if (comparator.compare(x, (E) e) >= 0)
                break;
            queue[k] = e;
            k = parent;
        }
        queue[k] = x;
    }
```

siftUpComparable和siftUpUsingComparator这里的逻辑都是相同的。当前元素与父节点不断比较如果比父节点小就交换然后继续向上比较，否则停止比较的过程。

> 关于parent = (k - 1) >>> 1;
>
> 首先>>> 是无符号右移，空出位置全部补0。对于正数来说，这里的>>> 和>> 达到的效果一样，只有对于负数会有所差别。这里因为外层的while循环已经保证了k>0，所以可以直接理解为(k - 1) >>1;

#### poll()

poll()方法将输出整个队列中的最值，我们知道优先队列的底层逻辑是堆排序，整个数组中的最值存在于queue[0]中，由于移除了最值，所以整个堆要重新进行排序，相当于将最后一个元素进行重新插入offer(e)。

```java
		@SuppressWarnings("unchecked")
    public E poll() {
        if (size == 0)
            return null;
        int s = --size;
        modCount++;
        E result = (E) queue[0];
        E x = (E) queue[s];
        queue[s] = null;
        if (s != 0)
            siftDown(0, x);
        return result;
    }

		private void siftDown(int k, E x) {
        if (comparator != null)
            siftDownUsingComparator(k, x);
        else
            siftDownComparable(k, x);
    }
```

从siftDown中可以看出这里的内部逻辑其实就是offer()的内部逻辑。

#### peek()

获取但不移除此队列的头，如果此队列为空，则返回null。

```java
		public E peek() {
        return (size == 0) ? null : (E) queue[0];
    }
```

### 实现大顶堆

PriorityQueue默认是一个小顶堆，然而可以通过传入自定义的Comparator函数来实现大顶堆。如下代码：

```java
	private static final int DEFAULT_INITIAL_CAPACITY = 11;
	PriorityQueue<Integer> maxHeap=new PriorityQueue<Integer>(DEFAULT_INITIAL_CAPACITY, new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {                
            return o2-o1;
        }
    });
```

### 注意❕

1. PriorityQueue不是线程安全的**。**如果多个线程中的任意线程从结构上修改了列表， 则这些线程不应同时访问 PriorityQueue 实例，这时请使用线程安全的PriorityBlockingQueue 类。
2. 当插入null元素的时候会抛出异常。
3. 方法iterator()中提供的迭代器并不保证以有序的方式遍历优PriorityQueue中的元素。

## ArrayDeque

从我们之前的[Java集合框架之List](https://xiaozhu-sorce.github.io/2021/12/12/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E4%B9%8BList/)最开始的图中可以看到，Queue有一个直接子类PriorityQueue，还有一个子接口Deque，真正实现了Deque接口的两个类是ArrayDeque和LinkedList。其中LinkedList以及在**Java集合框架之List**里面提及到。这里我们做详细说明的是ArrayDeque实现类。

ArrayDeque是无初始容量的双端队列，LinkedList则是双向链表。而我们还能看到，ArrayDeque作为队列时的效率比LinkedList要高，而在栈的使用场景下，无疑具有尾结点不需判空的LinkedList较高效。 

### 底层数据结构

ArrayDeque为了满足可以同时在数组两端插入或删除元素的需求，其内部的动态数组必须是循环的，即循环数组（circular array），也就是说数组的任何一点都可能被看作起点或者终点。

ArrayDeque维护了两个变量head和tail，表示ArrayDeque的头和尾

```java
transient Object[] elements; // non-private to simplify nested class access
transient int head;
transient int tail;
private static final int MIN_INITIAL_CAPACITY = 8;
```

### 方法剖析

#### addFirst()

```java
public void addFirst(E e) {
  if (e == null)
    throw new NullPointerException();
  elements[head = (head - 1) & (elements.length - 1)] = e;
  if (head == tail)
    doubleCapacity();
}
```

- 插入的元素不可以为空
- 头插实际上是减一。当减到0时在从数组末尾开始
- 如果头尾相等，则二倍扩容。在这里也反映出来，head和tail在插入之前指向的位置都一定是空的，一定可以进行插入操作，在插入之后在会进行判段数组是否满

#### doubleCapacity()

扩容函数doubleCapacity

```java
private void doubleCapacity() {
  assert head == tail;
  int p = head;
  int n = elements.length;
  int r = n - p; // number of elements to the right of p
  int newCapacity = n << 1;
  if (newCapacity < 0)
    throw new IllegalStateException("Sorry, deque too big");
  Object[] a = new Object[newCapacity];
  System.arraycopy(elements, p, a, 0, r);
  System.arraycopy(elements, 0, a, r, p);
  elements = a;
  head = 0;
  tail = n;
}
```

这个过程可以用如下图来理解。

复制分两次进行，第一次复制head右边的元素，第二次复制head左边的元素。

![](https://tva1.sinaimg.cn/large/e6c9d24ely1h0q1kfg6aqj20xc0c8js7.jpg)

ArrayDeque不是线程安全的。 当作为栈使用时，性能比Stack好；当作为队列使用时，性能比LinkedList好。

