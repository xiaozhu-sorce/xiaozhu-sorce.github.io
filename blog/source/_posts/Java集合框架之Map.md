---
title: Java集合框架之Map
date: 2022-04-18 18:42:38
categories: Java
tags: 集合
author: 小朱
comments: true
---

# Map接口

Map实现类用于保存具有映射关系的数据。Map保存的每项数据都是key-value对，也就是由key和value两个值组成。Map里的key是不可重复的，key用于标识集合里的每项数据。

<!--more--> 

![map](https://tva1.sinaimg.cn/large/008i3skNly1gyp0sw7mirj30n70bzdga.jpg)

## Map接口实现类的比较

- HashMap：线程不安全，效率较高；存储null的key和value
  - LinkedHashMap：保证在遍历map元素时，可以按照添加的顺序实现遍历

- TreeMap：保证按照添加的key-value对进行排序，实现排序遍历。
- Hashtable：古老的实现类；线程是安全的，效率低；不能存储null的key和value

# HashMap

## 底层数据结构

HashMap在Jdk8之后就采用了**数组+链表+红黑树**的存储方式进行存储键值对。

```java
		transient Node<K,V>[] table;
    
		transient Set<Map.Entry<K,V>> entrySet;

    transient int size;

    transient int modCount;

    int threshold;

    final float loadFactor;

    /**
     * The default initial capacity - MUST be a power of two.
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * The maximum capacity, used if a higher value is implicitly specified
     * by either of the constructors with arguments.
     * MUST be a power of two <= 1<<30.
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;

```

- table：是一个存放元素类型为Node<K,V>的一个数组，通过Key哈希之后的值作为数组索引可以快速找到这个Node。由于这个Node可以作为链表的头或者红黑树的节点，成为一个桶。所以这个数组也叫哈希桶数组。
- entrySet：存储具体键值对的集
- size：是HashMap中具体存放的元素（也就是一个Node<K,V>即键值对）的大小
- modCount：fail-fast机制
- threshold：阀值，其实就是作为一个临界值去判断是否要对hashMap进行扩容
- loadFactor：负载因子。一般值为0.75

> threshold=容量*负载因子
>
> 即threshold=capacity \* loadFactor

### Node<K,V>

Node<K,V>作为一个静态内部类存在于HashMap中，其实现了Map.Entry<K,V>接口，本质上就是一个Key-Value键值对

```java
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
```

### 概述

![HashMap](https://tva1.sinaimg.cn/large/e6c9d24egy1h1hhdfzyl8j20st0akjsg.jpg)

当实例化一个HashMap时，会初始化initialCapacity和loadFactor，在put第一对映射关系时，系统会创建一个长度为initialCapacity的Node数组，这个长度在哈希表中被称为容量(Capacity)，在这个数组中可以存放元素的位置我们称之为**“桶”(bucket)**，每个bucket都有自己的索引，系统可以根据索引快速的查找bucket中的元素

  每个bucket中存储一个元素，即一个Node对象，但每一个Node对象可以带一个引用变量next，用于指向下一个元素，因此，**在一个桶中，就有可能生成一个Node链。也可能是一个一个TreeNode对象**。每一个TreeNode对象可以有两个叶子结点left和right，因此，在一个桶中，就有可能生成一个TreeNode树。而新添加的元素作为链表的last，或树的叶子结点。

## 哈希算法和哈希冲突

**Hash算法**:就是根据设定的Hash函数H(key)和处理冲突方法，将一组关键字映射到一个有限的地址区间上的算法。所以Hash算法也被称为散列算法、杂凑算法。

**Hash表**:通过Hash算法后得到的有限地址区间上的集合。数据存放的位置和key之前存在一定的关系(`H(key)=stored_value_hash(数据存放位置)`),可以实现快速查询。与之相对的，如果数据存放位置和key之间不存在任何关联关系的集合，称之为`非Hash表`。

**Hash冲突**:由于用于计算的数据是无限的`H(key),key属于(-∞,+∞)`,而映射到区间是有限的，所以肯定会存在两个key:key1,key2，H(key1)=H(key2)，这就是hash冲突。一般的解决Hash冲突方法有:开放定址法、再哈希法、链地址法（拉链法）、建立公共溢出区。

### hash(Object key)

```java
	static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }

```

hash()方法计算key在桶数组中应该存放的位置。这里调用了key所属类的hashcode方法，同时将这个数字的高低16位进行了异或运算。

> 如果key类是一个自定义类，需要override hashCode()和equals()方法，从这里也可以解释Object作为一个所有类的超类拥有hashCode和equals方法的原因。

⚠️注意：

1. 为什么要进行高低16位的异或运算
2. 计算了key的哈希之后，在往table数组存放时索引 **i = (n - 1) & hash**

这里做一个简要的回答。详细解答请移步[学姐博客](https://iulay1007.github.io/2021/11/19/HashMap/)

1. 作高低的16位异或是为了满足数组table数组长度一般小于16的特性，并且为了减少哈希冲突而将高16位的特性也在低16位中体现出来。
2. 这里的 i 其实就是计算哈希值对数组长度取模的结果，但是为了使得运算效率更高所以采用了 哈希值和数组长度减一进行逻辑与运算。而可以这样运算的一个重要的原因也是table的长度永远是2的指数次方。

### 链地址法（拉链法）

将哈希值相同的元素构成一个同义词的单链表,并将单链表的头指针存放在哈希表的第i个单元中，查找、插入和删除主要在同义词链表中进行。链表法适用于经常进行插入和删除的情况。HashMap采用的就是链地址法来解决hash冲突。(链表长度大于等于8时转为红黑树)

## put(K,V)

```java
		public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    /**
     * Implements Map.put and related methods.
     *
     * @param hash hash for key
     * @param key the key
     * @param value the value to put
     * @param onlyIfAbsent if true, don't change existing value
     * @param evict if false, the table is in creation mode.
     * @return previous value, or null if none
     */
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
       //node存放的位置i是将长度-1和hash作逻辑与运算
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

给HashMap中存放元素，其过程可以大概分为以下：

假设现在new的一个HashMap 变量名为map，现在调用map.put(key1,value2)

1. 根据key1所自类的hashcode计算key1的哈希值，此哈希值经过某种计算之后得到一个在Node数组中存放的具体位置

2. 如果此位置中的元素为空则直接进行插入

3. 如果此位置的元素不为空

   1. 判断该位置的第一个数据和我们要插入的数据，key 1是不是"相等"，如果是，取出这个节点

   2. 如果这个元素的类型为TreeNode（也就是这个桶现在变为了红黑树），则交给TreeNode内部类中的`putTreeVal`方法进行红黑树中的节点添加

   3. 否则（这个桶是链表）则进行链表的遍历

      1. 如果链表的下一个元素为空，则进行插入（这里链表的插入是一个尾插）。如果插入的元素刚好为第八个（取决于常数：TREEIFY_THRESHOLD）则执行 treeifyBin(tab, hash)决定最后是否需要树形化。
      2. 如果在该链表中找到了"相等"的 key，则退出循环，对该Node中的value进行更新（这里对这个if做一个详细介绍）

      > if (e.hash == hash &&
      >                         ( (k = e.key) == key || (key != null && key.equals(k) ) ) )
      >
      > 这里先对两个hash和key进行判断，若二者都相同，则表示原先的map中有该key
      >
      > 还有另外一种可能是，key不是空的，但是二者在逻辑上是相同的（根据用户重写的equals方法或者key所属类的equals方法）
      >
      > 好比new了两个"A"，这个时候A在逻辑上是相同的，但他们各自哈希其实是不同的结果。
      >
      > 总而言之，这里说的找到了“相等”其实就是“==”或者“equals”两者之一成立即可。

### 小结

- 首次调用put()方法时，底层创建长度为16的数组（这个数组的长度必须为2的整数次方）
- 当数组的某一个索引位置上的元素以链表形式存在的数据个数 >= 8 且当前table数组的长度 > 64时，此时此索引位置上的所数据改为使用红黑树存储。若数组长度<64则只是进行扩容，先不进行树形化。（`treeifyBin()`的源码。）

## 扩容机制

### resize()

在hashmap第一次放入元素，集合的长度为0或者在添加元素之后map的size>threshold（阀值）的时候都会调用resize方法。

### 过程

将旧数组中的元素复制到新数组（2倍的旧数组长度）中，但是这里的table放入元素的位置是通过上述说的 tab[i = (n - 1) & hash]，所以在复制到新数组的时候，每个元素的位置也需要重新计算。而不可以简单的进行复制。

```java
		final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                      //重新计算放入的位置。
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
										// 需要将此链表拆成两个链表，放到新的数组中，并且保留原来的先后顺序
                    // loHead、loTail 对应一条链表，hiHead、hiTail 对应另一条链表
                        do {
                            next = e.next;
                          //存放在旧的index,loHead为头，loTail为尾
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                          //存放在新的index,hiHead为头，hiTail为尾
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                      //该段链表放入旧index处
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                      //该段链表放入新的index处
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

对这里的do-while循环处理链表的详细介绍可以看[这里](https://xie.infoq.cn/article/d8424c9d9350ca28cc9f2daf8)，需要关注的就是`if ((e.hash & oldCap) == 0)`这个if语句为什么可以判断元素在新哈希桶位置。

由于put时计算hash数组下标是通过`i = (n - 1) & hash`计算的，其中n是的2的x次幂，扩容时，容量变为原来的两倍,`n-1 == (n-1)<<1 & 1`，所以hash桶中的元素，要么还在原来的index位置，要么在`oldIndex+oldCap`的位置。所以放入新的index时，元素下标:`newTab[j + oldCap]`。

## 负载因子 loadFactor

- 负载因子的大小决定了HashMap的数据密度。
- 负载因子越大密度越大，发生碰撞的几率越高，数组中的链表越容易长,造成查询或插入时的比较次数增多，性能会下降。
- 负载因子越小，就越容易触发扩容，数据密度也越小，意味着发生碰撞的几率越小，数组中的链表也就越短，查询和插入时比较的次数也越小，性能会更高。但是会浪费一定的内容空间。而且经常扩容也会影响性能，建议初始化预设大一点的空间。
- 按照其他语言的参考及研究经验，会考虑将负载因子设置为0.7~0.75，此时平均检索长度接近于常数

# TreeMap

## 底层数据结构--红黑树

TreeMap不仅可以对key-value进行存储，还可以对其进行排序，使用二叉树的存储使对元素的查找更加高效。时间复杂度为O（logn）。但是二叉树会发生一些极端的情况，比如全部元素为做为父节点的左孩子，这时候，与其说他是个树，不如说是一条链表，查找效率并没有得到提升。

在此基础上，我们又采用了AVL（平衡二叉搜索树），虽然二叉搜索树使得左孩子的值小于根节点而右孩子的值大于根节点，优化了搜索，由于二叉平衡树高度平衡（左右孩子的深度<=1），使得在插入删除元素的时候需要进行不停的调整，在我们实际使用过程中反而不是很方便。

所以最后采用了红黑树，我们先来看看红黑树的特性：

### 特性

一棵树可以被叫做红黑树，必须满足一下几点要求：

- 树中的每个节点必须是有颜色的，要么红色，要么黑色
- 树中的根节点必须是黑色的
- 树中的叶节点必须是黑色的
- 树中的任意一个节点如果是红色的，那么它的两个子节点一定是黑色的、
- 任意节点到达叶节点的每一条路径包含的黑色节点数目一定相同

这些约束强制了红黑树的关键性质: 从根到叶子的最长的可能路径不多于最短的可能路径的两倍长。结果是这棵树大致上是平衡的。因为操作比如插入、删除和查找某个值的最坏情况时间都要求与树的高度成比例，这个在高度上的理论上限允许红黑树在最坏情况下都是高效的，而不同于普通的二叉查找树。所以红黑树它是复杂而高效的，其检索效率O(log n)。

基于这些特性，而我们要对TreeMap进行分析，其实也就是对红黑树进行增删查改。本文这里就不再进行赘述，可以看一下这篇[博客](https://www.jianshu.com/p/e136ec79235c)。

## 排序

hashMap排序是根据key值的大小进行排序的

- 自然排序：TreeMap的所有Key必须实现Comparable接口
- 定制排序：创建Treemap对象时，该对象负责对TreeMap中的所有key进行排序。此时不需要Map的key实现Comparable接口
