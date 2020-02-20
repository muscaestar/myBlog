---
title: "算法 Day 2: 二叉堆的初入门"
author: ["Muscaestar"]
date: 2020-02-21T01:39:00+11:00
lastmod: 2020-02-21T01:39:12+11:00
tags: ["Algorithm"]
draft: false
---

上一篇博文中，我提到了一种通常用数组实现的完全树，那就是堆。今天这篇文章，我就来关注一下堆的知识。什么是堆？为什么使用堆？堆的实际应用？我会尝试一一解答。最后当然是，通过代码去实现一个简单的最小二叉堆，并且参考一下 Java 源码。文章内容如下：

1.  此堆非彼堆
2.  堆是一颗树
3.  堆和优先队列

<!--more-->


## 此堆非彼堆 {#此堆非彼堆}

从何说起呢？一切都始于我的一个小小的无知：JVM 里有个方法栈，用了 栈 这个数据结构；这么说来 JVM 里还有个堆，那它就用了 堆 这个数据结构咯？

正确答案‘竟然’是：错错错错。这两个堆，半毛钱关系都没有。

自从开始认真学习 IT 以来，我已经习惯于每天都认识到自己的无知了。我觉得知道自己无知，不是什么坏事，没什么大不了的。我猜大概很多像我一样，不是科班出身的朋友，都会遇到类似的问题。这也没什么不好的，不懂就去查嘛，越查就懂得越多了。所以我觉得，认识到自己无知，是变聪明的开始。


### 所以，堆是什么？ {#所以-堆是什么}

不同知识领域下，堆指代了不同的事物。在计算机内存领域里，堆，是一块服务于动态内存管理的内存区域。而在算法与数据结构领域里，堆，指一种特别的树状数据结构，满足完全树的特征，和堆序性质。（参见[维基百科](https://en.wikipedia.org/wiki/Heap)）

这还不是全部。通常来说，在算法里提起堆这个词，其实指的是
**二叉堆**, 它是比较常用到的一个堆的变体。还有，一个堆也分最小堆和最大堆，也叫小顶堆和大顶堆。
**给定小顶堆中的任意节点，那么该节点一定小于或等于它的所有后代节点；反之，给定大顶堆，那么该节点一定大于等于所有后代**
也就是说，顶节点，或称根节点，一定是这个堆中最小（大）的。

我觉得严谨不会有什么坏处，因此接下来我会使用完整的描述来指代特定堆，比如最小二叉堆，最大二叉堆。


### 为什么用堆？ {#为什么用堆}

这是很大的话题，这里我就简单罗列一下堆的应用。将来有机会，我会针对性地写博文：

1.  堆排序（写排序时，我一定会涉及堆排）
2.  狄克斯特拉算法，和其他图的算法
3.  选择算法，如第 k 个值，前 k 个值
4.  k 路归并算法
5.  优先队列（本文会涉及）


## 堆是一颗树 {#堆是一颗树}

下面我们说得更细一些，需要在
**脑海中**
想象。

虽然我也承认插个图片更方便些，不过我还是不插图了。在我的观念中，像栈，队列，树，堆这些简单的数据结构，第一次学看过一遍后，到哪里都不会忘的。就算忘了，这种图随便一搜都是...等敲代码的时候，一般都是自己在脑子里想象。所以，就当脑力锻炼吧。


### 二叉堆的两个性质 {#二叉堆的两个性质}

在逻辑上构建数据结构，必须需要严格的规则。性质决定了具体的规则，它能帮助你确保数据结构在动态变化中，保持应有的结构。二叉堆的性质有两个，为了方便记忆，我用最短的语言描述它们：


#### 结构性质 {#结构性质}

二叉堆必须是一个[完全二叉树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%8F%89%E6%A0%91#%E5%AE%8C%E5%85%A8%E4%BA%8C%E5%8F%89%E6%A0%91)。


#### 堆序性质 {#堆序性质}

任何父节点必须 `<=` （或 `>=` ）它的子节点。


### 数组实现 {#数组实现}

说了这么多，还没有讲到究竟为什么是使用数组实现。在入门二叉搜索树的文章中，使用了链式储存的思路实现了树。为什么这次用堆？

因为堆始终是一个完全树，而一个完全树是最适合使用的数组实现的。再进一步解释一下：

对于一个完全树的每一层，从层的头部到尾部，从左依次到右，不会遇到空位，即层是满的。就算是‘不满’的最底层，从最左节点到最右边的节点之间，也不会有空位。在这种情况下，我们一层一层地把节点值存入数组中，从第一个值到最后一个值之间，都不会出现空位。这种结构最大限度地利用了数组的每一个空间。

数组实现有两种方案，各有各的优缺点。接下来的实现中我会选择从下标 1 开始储存的方案。


#### 从下标 1 开始储存 {#从下标-1-开始储存}

-   `array[0]` 是 `null`
-   `array[1]` 是根节点
-   对于在下标 **i** 处的节点
    -   父节点下标: **i / 2**
    -   子节点下标: **2i**, **2i + 1**


#### 从下标 0 开始储存 {#从下标-0-开始储存}

-   `array[0]` 是根节点
-   对于在下标 **i** 处的节点
    -   父节点下标: **(i-1) / 2**
    -   子节点下标: **2i + 1**, **2i + 2**


### 基本操作 {#基本操作}

二叉堆的基本操作为：插入，删除堆顶，建堆。我准备下一节结合 Java 源码描述每个操作的实现。在这之前，我想先把最基本的操作思路流程写在这里，因为每种基本操作都是这个思路：

1.  插入或移出节点
2.  基本性质被破坏
3.  堆化（恢复基本性质）


### 堆的内部是无序的 {#堆的内部是无序的}

这是一个很自然的事实。不过我还是想要提一遍，因为当我们思考二叉堆的时候，会不自觉地把二叉堆和二叉搜索树联系起来。二叉搜索树内部是有序的，而二叉堆的内部是无序的。由于堆内是无序的，所以堆内的兄弟节点间没有排序。一不小心很容易忽略这个事实。


## 堆和优先队列 {#堆和优先队列}

终于要轮到代码了。先去看看千锤百炼的 Java 源码的优先队列：[PriorityQueue](https://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/PriorityQueue.java). 不过在这之前，还是得自问，为什么看优先队列呢？

**太长不看版：如果仅仅是为了学习最小二叉堆的代码实现，直接模仿 Java 的优先队列没问题。**

刚刚提到过，优先队列是二叉堆，准确说，最小二叉堆。事实上，优先队列和最小二叉堆的唯一区别，那就是概念上的区别。如果单单看代码，这两个的代码实现可以是几乎一模一样的。

前面说到是概念上的区别，那么这里借用[维基百科](https://zh.wikipedia.org/wiki/%E5%84%AA%E5%85%88%E4%BD%87%E5%88%97)定义一下优先队列：优先队列中的每个元素都有各自的优先级，优先级最高的元素最先得到服务；优先级相同的元素按照其在优先队列中的顺序得到服务。而在 [Java API](https://docs.oracle.com/javase/7/docs/api/java/util/PriorityQueue.html) 中也写道，"An unbounded priority queue based on a
priority heap."


### 类的结构 {#类的结构}

经过对源码的阅读和理解，我写出了如下的最小二叉堆结构。力求简洁，主要目的还是核心概念的理解，因此省去了一些功能。提几个要点：

1.  我的数组使用了泛型类型，而 Java 源码实际上直接用 `Object[]` 。无知如我，我只能大致猜想开发者的意图。不过我直接用泛型的原因是，这种实现只需要在构造器中做一次强制类型转换即可。
2.  对泛型使用类型界限和通配符，确保 E 类的父类一定实现了 Comparable 接口。Java 源码在这方面考虑更全面，Comparable 和 Comparator 的情况都覆盖到了。
3.  没有实现动态括容。因为我觉得这不是必备功能，为了简洁，就放弃了。实际情况下当然是动态扩容更好些。

<!--listend-->

```java
public class BinaryMinHeap<E extends Comparable<? super E>> {
    private E[] array; // store elements, start from index 1
    private int size; // number of elements stored
    private int capacity; // length of array - 1

    public BinaryMinHeap(int capacity) {/*  */}
    public BinaryMinHeap(int capacity, E[] rawArray) {/* build() */}

    public E add(E e) {/* siftUp() */}
    public E poll() {/* siftDown() */}

    private int siftUp(int i, E e) {/*  */}
    private int siftDown(int i, E e) {/*  */}
    private boolean build() {/* siftDown() */}

    // other methods
}
```


### 堆化 {#堆化}

上一节我提到了堆化这个词。堆化的主要功能是，修复被破坏的两个基本性质。堆化这个操作再细分下来，分为上滤（Sift-up）和下滤（Sift-down），也可以叫向上堆化和向下堆化。在我看来，这是二叉堆一切操作的核心，因为几乎所有的操作都要用到它。掌握好它，其他操作就变得简单多了。

在我写的类结构中， `siftUp()` 和 `siftDown()` 作为 private 方法，也符合它们本身作为内部操作的定义。在调用它们之前，实例内部需要达到一定的条件，这也是封装它们的理由之一。

有一点需要注意，调用一次上滤（或下滤）时，内部发生的“父子调换”，可能的次数是
**0 次或多次**.


#### 上滤 {#上滤}

上滤的英文称呼很多，这里特别记录一下：up-heap, bubble-up, percolate-up, sift-up,
trickle-up, swim-up, heapify-up, cascade-up。

代码该注释的都注释了，我觉得这比文字图片都更好理解。

```java
/**
 * Heapify-up the heap.
 * At the start of this method, the 'heap' has an empty node.
 * This method re-allocate the empty node by keeping comparing it
 * with its parent, then swap if need.
 * After re-allocation, fill element e into it.
 *
 * @param i the index of empty node
 * @param e the element going to fill in
 * @return the final index of original empty node
 */
private int siftUp(int i, E e) {
    while (i > 1) { // while the empty node is not the top
        int parent = i / 2;

        int cmp = e.compareTo(array[parent]);
        if (cmp < 0) {
            array[i] = array[parent];
            i = parent;
        } else { // cmp >= 0
            break;
        }
    }
    array[i] = e; // fill the empty node with e
    return i;
}
```


#### 下滤 {#下滤}

相对应上滤，以下是下滤的英文称呼：down-heap, bubble-down, percolate-down,
sift-down, sink-down, trickle down, heapify-down, cascade-down。

下滤稍微更麻烦一点，因为需要对比两个子节点。

注释完整的代码如下。

```java
/**
 * Heapify-down the heap.
 * At the start of this method, the 'heap' has an empty node.
 * This method re-allocate the empty node by keeping comparing it
 * with its least child, then swap if need.
 * After re-allocation, fill element e into it.
 *
 * @param i the index of the empty node
 * @param e the element going to fill in
 * @return the final index of the original empty node
 */
private int siftDown(int i, E e) {
    while (i <= size / 2) { // make sure empty node is non-leaf
        int ch1 = i * 2; // index of child 1
        int ch2 = ch1 + 1; // index of child 2, if exist

        int leastChild = ch1;
        // if ch2 exist, then compare children to get the least one
        if (ch2 <= size && array[ch1].compareTo(array[ch2]) > 0) {
            leastChild = ch2;
        }

        int cmp = e.compareTo(array[leastChild]);
        if (cmp > 0) {
            E tmp = array[leastChild];
            array[leastChild] = array[i];
            array[i] = tmp;
            i = leastChild;
        } else {
            break;
        }
    }
    array[i] = e; // fill the empty node with e
    return i;
}
```


### 插入新元素 {#插入新元素}

步骤简述：

1.  size 加一
2.  选择两个上滤的参数
    -   目标节点：尾部节点
    -   e：插入方法的参数
3.  上滤

<!--listend-->

```java
public E add(E e) {
    /* validation checking */

    // size plus one, and make tail be the empty node
    int i = size + 1; // index of empty node
    if (size++ == 0) { // check if heap is empty, then add size
        array[i] = e;
    }
    i = siftUp(i, e);
    return array[i]; // the e just added
}
```


### 删除堆顶元素 {#删除堆顶元素}

步骤简述：

1.  size 减一
2.  选择两个下滤参数
    -   目标节点：顶部节点
    -   e：尾部节点元素
3.  下滤

<!--listend-->

```java
public E poll() {
    E res = array[1];
    array[1] = null; // make top be the empty node

    E e = array[size]; // the value of tail node will be fill in later
    array[size--] = null; // remove the tail node, size - 1

    siftDown(1, e);
    return res;
}
```


### 建堆 {#建堆}

我的代码实现里， `build()` 是 private 方法。它只会在一个构造器里被调用。步骤简述：

1.  遍历每一个非叶子节点，顺序是下标从大到小
    -   选择两个下滤参数
        -   目标节点：当前非叶子节点
        -   e：当前非叶子节点的元素
    -   下滤
2.  遍历完成

<!--listend-->

```java
private boolean build() {
    for (int i = size / 2; i >= 1; i--) {
        siftDown(i, array[i]);
    }
    return true;
}
```


## 写在最后 {#写在最后}

根据本文的知识点，我写了完整的代码实现，放在[Github Repo](https://github.com/muscaestar/algo).


## 参考资料 {#参考资料}

-   Wikipedia
-   Weiss, M. A. (2011). Data structures and algorithm analysis in Java. Pearson.
-   Java™ Platform, Standard Edition 7, API Specification
-   [OpenJDK](https://hg.openjdk.java.net/)
-   数据结构与算法分析：Java 语言描述（原书第 3 版）/ (美)维斯（ Weiss，M.A.)著；冯舜玺，陈越 译. -- 北京：机械工业出版社，2016.2
-   [极客时间－数据结构与算法之美](https://time.geekbang.org/column/intro/126)
