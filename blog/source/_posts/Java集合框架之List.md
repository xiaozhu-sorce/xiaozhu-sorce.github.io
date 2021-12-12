---
title: Java集合框架之List
date: 2021-12-12 00:34:16
categories: Java
tags: 集合
author: 小朱
comments: true
---

# 集合框架

## 数组与集合

Java 中常用的存储容器就是数组和集合，二者有以下区别：

- 存储大小是否固定
  - 数组的**长度固定**；
  - 集合的**长度可变**。
- 数据类型
  - **数组可以存储基本数据类型，也可以存储引用数据类型**；
  - **集合只能存储引用数据类型**，基本数据类型的变量要转换成对应的包装类才能放入容器类中。

> Java 集合框架主要分为 `Collection` 和 `Map` 两种。其中，`Collection` 又分为 `List`、`Set` 以及 `Queue`。
>
> - `Collection` - 一个独立元素的序列，这些元素都服从一条或者多条规则。
>   - `List` - 必须按照插入的顺序保存元素。
>   - `Set` - 不能有重复的元素。
>   - `Queue` - 按照排队规则来确定对象产生的顺序（通常与它们被插入的顺序相同）。
> - `Map` - 一组成对的“键值对(key-value)”对象，允许你使用键来查找值。

## 集合类之间的关系

Java的集合类主要由两个接口派生而出：Collection和Map,Collection和Map是Java集合框架的根接口。这里我们主要介绍由Collection接口派生出的List类



<img src="https://tva1.sinaimg.cn/large/008i3skNgy1gxadyfdc2nj30q30e60ti.jpg" alt="Collection接口衍生" style="zoom: 80%;" />

在开始之前，我们先来了解一些基本概念。

## Collection接口

集合类的基本接口是collection接口，Collection接口是Set,Queue,List的父接口。Collection接口中定义了多种方法可供其子类进行实现，以实现数据操作。

```java
public interface Collection<E> extends Iterable<E>
{
	boolean add(E element);
	Interator<E> iterator();
	···
  int size();
  boolean isEmpty();
  boolean equals();
  Object[] toArray();
  ···
}
```

- `add()`用于添加元素，如果添加成功则返回true，相反为false。
- `iterator()`方法用于返回一个实现了 Iterator 接口的对象，可以使用这个迭代器对象依次访问集合中的元素。
- ···（可自行查阅Java API 这里不在赘述）

## 迭代器

Iterator接口经常被称作迭代器，它是Collection接口的父接口。但Iterator主要用于遍历集合中的元素。

它包含的方法有：

```java
//泛型接口
public interface Iterator<E>
{
  E next();
  boolean hasNext();
  void remove();
  default void forEachRemaining(Consumer<? super T> action);
}
```

> 迭代器：在 C++ STL中被认为是一种广义的指针。用于指向容器中某个位置的数据元素。

- `next()`通过反复调用next方法就可以逐个访问集合中的每个元素。当到达集合的末尾是会抛异常NoSuchElementException。所以要配合hasNext方法判断是否还有元素可以返回。

> 在传统C++迭代器中，我们可以通过类似数组索引下标通过i++来查找数据元素。与C++不同，Java调用迭代器只能通过`next()`方法去查找元素。而Java迭代器指向的也是两个元素之间的位置。即：当调用 next 时，迭代器就越过下一个元素，并返回刚刚越过的那个元素的引用。

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gxb5tynqxej31840kq75c.jpg" alt="迭代器移动" style="zoom: 45%;" />

- `hasNext()`可以集合中是否还有元素，配合next通过迭代器返回数据元素。

例如：

```java
Collection<String> s = . . .;
Iterator<String> iterator = c.iterator();
while(iter.hasNext())
{
	String element = iter.next();
	···
}
```

- `remove()`删除上次调用next方法时候返回的元素。从而使得remove和next之间存在依赖性。再调用next之后确定要删除的元素，在使用remove进行删除操作。

当使用Iterator对集合元素进行迭代时，Iterator并不是把集合元素本身传给了迭代变量，而是把集合元素的值传给了迭代变量（就如同参数传递是值传递，基本数据类型传递的是值，引用类型传递的仅仅是对象的引用变量），所以修改迭代变量的值对集合元素本身没有任何影响。

# List

List是一个有序集合。List作为Collection接口的子接口，可以使用Collection接口里的全部方法。而且由于List是有序集合，因此List集合里增加了一些根据索引来操作集合元素的方法。元素会增加到容器的特定位置，有两种方式访问元素。

1. 使用迭代器来访问（顺序访问）
2. 使用一个整数索引来访问（随机访问）

```java
public interface List<E> extends Collection<E>
{
	void add(int index,E element);
	void remove(int index);
	E get(int index);
	E set(int index,E element);
}
```

> 我们都知道一个对象只要实现了Serilizable接口，这个对象就可以被序列化。而看看 ArratList && LinkedList 源码你就会发现。这两个接口都实现了 java.io.Serializable，也就不难理解为什么List是一个有序集合。

## LinkedList

基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。此外由于 LinkedList 同时实现了 List 接口和 Deque 接口，也就是说它既可以看作一个顺序容器，又可以看作一个队列 (Queue)，同时又可以看作一个栈 (Stack)。

LinkedList 的实现方式决定了所有跟下标相关的操作都是线性时间，而在首段或者末尾删除元素只需要常数时间。为追求效率 LinkedList没有实现同步 (synchronized)，如果需要多个线程并发访问，可以先采用`Collections.synchronizedList()`方法对其进行包装。

### 底层数据结构

LinkedList 底层通过双向链表来实现，其中的每个节点用内部类 Node 表示。LinkedList 通过`first`和`last`引用分别指向链表的第一个和最后一个元素。

```java
transient int size = 0;
transient Node<E> first;
transient Node<E> last;
```

> java 的transient关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。

其中Node类为静态内部类。

```java
private static class Node<E> {
  E item;
  Node<E> next;
  Node<E> prev;

  Node(Node<E> prev, E element, Node<E> next) {
    this.item = element;
    this.next = next;
    this.prev = prev;
  }
}
```

### 方法剖析

#### add()

add 方法有两个重载方法。一个是`add(E e)`，该方法在 LinkedList 的末尾插入元素，因为有`last`指向链表末尾，在末尾插入元素的花费是常数时间。只需要简单修改几个相关引用即可；

```java
public boolean add(E e) {
  linkLast(e);
  return true;
}

void linkLast(E e) {
  final Node<E> l = last;
  final Node<E> newNode = new Node<>(l, e, null);
  last = newNode;
  if (l == null)
  	first = newNode;
  else
  	l.next = newNode;
  size++;
  modCount++;
}
```

另一个是`add(int index, E element)`，该方法是在指定下表处插入元素，需要先通过线性查找找到具体位置，然后修改相关引用完成插入操作。

```java
public void add(int index, E element) {
  checkPositionIndex(index);

  if (index == size)//判断是否是最后一个元素
    linkLast(element);
  else
    linkBefore(element, node(index));//特别注意这里的node(index)，下边说明。
}

void linkBefore(E e, Node<E> succ) {
  // assert succ != null;
  final Node<E> pred = succ.prev;
  final Node<E> newNode = new Node<>(pred, e, succ);
  succ.prev = newNode;
  if (pred == null)
    first = newNode;
  else
    pred.next = newNode;
  size++;
  modCount++;
}
```

特别注意这里的`node(index)`我们先来看看他的源码。

```java
Node<E> node(int index) {
  // assert isElementIndex(index);

  if (index < (size >> 1)) {//从前往后找
    Node<E> x = first;
    for (int i = 0; i < index; i++)
      x = x.next;
    return x;
  } else {//从后往前找
    Node<E> x = last;
    for (int i = size - 1; i > index; i--)
      x = x.prev;
    return x;
  }
}
```

在寻找相应index的节点时候，具体朝那个方向找取决于条件`index < (size >> 1)` ，也即是通过size()/2判断index是靠近前端还是后端。

#### remove()

remove方法也有两个重载函数。一个是删除跟指定元素相等的第一个元素`remove(Object o)`。

```java
public boolean remove(Object o) {
  if (o == null) {
    for (Node<E> x = first; x != null; x = x.next) {
      if (x.item == null) {
        unlink(x);
        return true;
      }
    }
  } else {
    for (Node<E> x = first; x != null; x = x.next) {
      if (o.equals(x.item)) { //使用.equals方法
        unlink(x);
        return true;
      }
    }
  }
  return false;
}
```

另一个是删除指定下标处的元素`remove(int index)`。

```java
public E remove(int index) {
  checkElementIndex(index);
  return unlink(node(index)); //寻找node的位置。同add方法
}

/**
     * Unlinks non-null node x.
     */
E unlink(Node<E> x) {
  // assert x != null;
  final E element = x.item;
  final Node<E> next = x.next;
  final Node<E> prev = x.prev;

  if (prev == null) {
    first = next;
  } else {
    prev.next = next;
    x.prev = null;
  }

  if (next == null) {
    last = prev;
  } else {
    next.prev = prev;
    x.next = null;
  }

  x.item = null;
  size--;
  modCount++;
  return element;
}
```

两个删除操作都要 1.先找到要删除元素的引用，2.修改相关引用，完成删除操作。在寻找被删元素引用的时候`remove(Object o)`调用的是元素的`equals`方法，而`remove(int index)`使用的是下标计数，两种方式都是线性时间复杂度。此外两个`revome()`方法都是通各过`unlink(Node<E> x)`方法完成的。这里需要考虑删除元素是第一个或者最后一个时的边界情况。

### ListIterator

当我们在考虑往链表中的某个位置插入元素时候，除了可以考虑到使用上述提到的 `add(int index,E element)`之外，还可以使用本文最开始提到的迭代器。由于迭代器描述了集合中的位置，所以这种依赖于位置的 add 方法将由迭代器负责。只有对自然有序的集合使用迭代器才有意义。但是对于 集（set） 数据类型中，元素是完全无序的。因此，Iterator 接口中没有 add 方法。实际上集合类库提供了一个子接口 ListIterator ，其中包含有 add 方法。

```java
public interface ListIterator<E> extends Iterator<E> {
  void add(E element);
  E previous();
  boolean hasPrevious();
  ···
}
```

- 与`Collection.add`不同，这里的 add 方法不返回 boolean 类型的值，它假定 add 操作总是能够改变链表。
- 除了Iterator中的 next 和 hasNext，ListIterator 还提供了 previous 和 hasPrevious。与 next 方法一样，previous 方法返回越过的对象。

LinkedList类 listIterator 方法返回了一个实现了 ListIterator 接口的迭代器对象。

```java
public ListIterator<E> listIterator(int index) {
  checkPositionIndex(index);
  return new ListItr(index);
}
private class ListItr implements ListIterator<E> {
  private Node<E> lastReturned;
  private Node<E> next;
  private int nextIndex;
  private int expectedModCount = modCount;

  ListItr(int index) {
    // assert isPositionIndex(index);
    next = (index == size) ? null : node(index);
    nextIndex = index;
  }
  ···
	public void add(E e) {
    checkForComodification();
    lastReturned = null;
    if (next == null)
      linkLast(e); //和LinkedList中的add方法一样。最终回到了linkLast(E element)
    else
      linkBefore(e, next);
    nextIndex++;
    expectedModCount++;
  }
}
```

使用：

```java
var staff = new LinkedList<String>(); 
staff.add("A");
Staff.add("B");
Staff.add("C");
ListIterator<String> iter = staff.listIterator();
iter.next();
iter.add("D");
```

想想这个结果是什么

ADBC

## ArrayList

ArrayList 实现了 List 接口，是顺序容器，即元素存放的数据与放进去的元素相同，允许放入null元素，底层通过**数组实现**。每个ArrayList都有一个容量（capacity），表示底层数组的实际大小，而 size 表示的是你当前与容器内存储的元素个数，容器内存储元素的个数不能多于当前的容量。当向容器中添加元素时，如果容量不足，容器会自动增大底层数组的大小。为追求效率，ArrayList没有实现同步（synchronized），如果需要多个线程并发访问，用户可以手动同步，也可以使用vector替代。

### 底层数据结构

```java
transient Object[] elementData; // non-private to simplify nested class access

private int size;
```

这里的数组是一个Object数组，以便能够容纳任何类型的对象。

### 自动扩容

由于数组是有大小的每次想数组中添加元素的时候，都需要去检查添加后元素的个数是否会超出当前数组的长度，如果超出，数组将会进行自动扩容，以满足添加元素的需求。

数组扩容通过一个公开的方法 ensureCapacity(int minCapacity)来实现。

```java
public void ensureCapacity(int minCapacity) {
  int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
    // any size if not default element table
    ? 0
    // larger than default for default empty table. It's already
    // supposed to be at default size.
    : DEFAULT_CAPACITY;

  if (minCapacity > minExpand) {
    ensureExplicitCapacity(minCapacity);
  }
}

private void ensureExplicitCapacity(int minCapacity) {
  modCount++; //fail-fast机制

  // overflow-conscious code
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}

private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

private void grow(int minCapacity) {
  // overflow-conscious code
  int oldCapacity = elementData.length;
  int newCapacity = oldCapacity + (oldCapacity >> 1); //扩容的数量是一点五倍
  if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
  if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);
  // minCapacity is usually close to size, so this is a win:
  elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
  if (minCapacity < 0) // overflow
    throw new OutOfMemoryError();
  return (minCapacity > MAX_ARRAY_SIZE) ?
    Integer.MAX_VALUE :
  MAX_ARRAY_SIZE;
}
```

数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的1.5倍。这种操作的代价是很高的，因此在实际使用时，我们应该尽量避免数组容量的扩张。当我们可预知要保存的元素的多少时，要在构造ArrayList实例时，就指定其容量，以避免数组扩容的发生。或者根据实际需求，通过调用ensureCapacity方法来手动增加ArrayList实例的容量。

### 方法剖析

#### add()

add 方法有两个重载方法。一个是`add(E e)`，默认为添加到数组的尾部，另外一个是 `add(int index,E element)`，将元素添加到指定位置。在对容器进行元素添加时，可能会导致capacity不足，因此在添加元素之前，都需要进行剩余的空间检查。如果空间不足将会进行自动扩容，扩容操作最终也是通过 `grow()`方法完成的。

```java
public boolean add(E e) {
  ensureCapacityInternal(size + 1);  // Increments modCount!!
  elementData[size++] = e;
  return true;
}

public void add(int index, E element) {
  rangeCheckForAdd(index);

  ensureCapacityInternal(size + 1);  // Increments modCount!!
  System.arraycopy(elementData, index, elementData, index + 1,
                   size - index);
  elementData[index] = element;
  size++;
}
```

空间检查使用的是ensureCapacityInternal方法。

#### remove()

remove 方法也有两个重载，一个是`remove(int index)`删除指定位置的元素，另一个是`remove(Object o)`删除第一个满足`o.equals(elementData[index])`的元素。删除操作是`add()`操作的逆过程，需要将删除点之后的元素向前移动一个位置。需要注意的是为了让GC起作用，必须显式的为最后一个位置赋`null`值。

```java
public E remove(int index) {
  rangeCheck(index);

  modCount++;
  E oldValue = elementData(index);

  int numMoved = size - index - 1;
  if (numMoved > 0)
    System.arraycopy(elementData, index+1, elementData, index,
                     numMoved);
  elementData[--size] = null; // clear to let GC do its work

  return oldValue;
}

public boolean remove(Object o) {
  if (o == null) {
    for (int index = 0; index < size; index++)
      if (elementData[index] == null) {
        fastRemove(index);
        return true;
      }
  } else {
    for (int index = 0; index < size; index++)
      if (o.equals(elementData[index])) {
        fastRemove(index);
        return true;
      }
  }
  return false;
}
```

对象能否被GC的依据是是否还有引用指向它，上面代码中如果不手动赋`null`值，除非对应的位置被其他元素覆盖，否则原来的对象就一直不会被回收。

## Fail-Fast机制

由于 LinkedList 和 ArrayList 都没有实现同步，故二者都采用了快速失败的机制，通过记录modCount参数来实现。在面对**并发**的修改时，迭代器很快就会完全失败，而不是冒着在将来某个不确定时间发生任意不确定行为的风险。

> 不要在遍历（for each循环）里面进行元素的 remove/add 操作。remove元素需要使用 Iterator 方式，如果并发操作，需要对 Iterator 对象加锁。