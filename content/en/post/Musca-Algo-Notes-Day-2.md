---
title: "Algo Day 2: Start with Binary Heap"
author: ["Muscaestar"]
date: 2020-02-21T01:39:00+11:00
lastmod: 2020-02-21T01:39:09+11:00
tags: ["Algorithm"]
draft: false
---

Last time, I mentioned heap as an array implementation of a complete tree. This
post I'll look closer to the heap. What is it? Why using it? Any real-life
application? I'll cover these in general, and then implement a simple binary
min-heap with basic operations, learning from the best, Java source code. The
topics are as following:

1.  Heap is not that Heap
2.  Heap is a Tree
3.  Heap and Priority Queue

<!--more-->


## Heap is not that Heap {#heap-is-not-that-heap}

What does this sub-title mean? It comes from one of my stupid questions: There's
a term _JVM Stack_ in JVM, using the data structure stack. So, what does the
data structure heap do with the _Heap_ of JVM?

The answer is 'surprisingly': nothing at all. This heap has nothing to do with that heap.

I don't feel bad about myself with this misunderstanding, as I believe many
people like me, with no fundamental CS background, may come up with the same
problem. The good news is, we can search. If we can search, we finally get the right
answer. After all, 'stupid' questions make us less stupid.


### So, what is Heap? {#so-what-is-heap}

The meanings of heap differ from their context. When we talking about computer
memory management, _Heap_, also called _free store_, is "an area of memory for
dynamic memory allocation". When we talking about algorithms and data structure,
_Heap_ refers to "a specialized tree-based data structure which is essentially an almost
complete tree that satisfies the heap property". (From [WIKIPEDIA](https://en.wikipedia.org/wiki/Heap))

Is it all? No. Because when people talk about _Heap_, they mean _Binary Heap_,
one of the common variants of heap. And also, almost any kinds of heap has two
types, max-heap and min-heap.
**In a max(min) heap, for any non-leaf node, its value must greater(less) than or
equal to any of its descendants.**
So, the top node, or called root node, must be the
greatest(least) node in the heap.

My point, we need to be preciser. I'll use **Binary Min-Heap** or Binary Max-Heap
to describe the exact data structure.


### Why using Heap? {#why-using-heap}

Let me simply list what can a heap or several heaps do. I'll skip the
details. Maybe write about some of them in the future:

1.  Heapsort (I'll definitely cover it when taking about sorting)
2.  Used in Dijkstra's algorithm, and other graph algorithms
3.  Selecting k-th element. Selecting top-k elements. So-called selection algorithms
4.  K-way merge algorithms
5.  Priority Queue (later in this article)


## Heap is a Tree {#heap-is-a-tree}

Now we go a little bit closer. Picturing it. **In mind**.

I know some images in the article may help. I just assuming the readers have
checked the [Wikipedia](https://en.wikipedia.org/wiki/Binary%5Fheap). For simple ADTs like stack, queue, tree, heap... Once you
see the image somewhere, they just stored in your mind. Just treat it as a brain exercise.


### Two properties of Binary Heap {#two-properties-of-binary-heap}

The following properties make an array a heap logically. We need to strictly
follow them, as every time an operation mess it up, these rules help us
re-arrange things. I'll try to make the rules as short as possible.


#### Structure property {#structure-property}

Binary Heap must be a [complete binary tree](https://en.wikipedia.org/wiki/Binary%5Ftree#complete).


#### Heap-order property {#heap-order-property}

Any parent node must `<=` (or `>=`) its children.


### Array implementation {#array-implementation}

I haven't talked about the reason for array implementation. In the post of BST,
linked nodes are preferred. Why array this time?

Because a heap is always a complete tree. And a complete tree is perfect for
array storage. Allow me to explain more:

For every layer of a complete tree, you can count from left to right, start to
end, there's no empty position. In this case, if we store the elements in array, layer
by layer, left to right, there should be no empty entry in the middle of an
array. It utilizes the storage space effectively.

There're two styles of this implementation. Each has its own sweetness. I'll
use "Root-One-Style". Also, I named them on my own, so it makes sense
if you never heard of the name (but the implementations truly exist).


#### Root-One-Style {#root-one-style}

-   `array[0]` is `null`
-   `array[1]` is element of root node
-   given a node at index **i**
    -   parent   index: **i / 2**
    -   children index: **2i**, **2i + 1**


#### Root-Zero-Style {#root-zero-style}

-   `array[0]` is element of root node
-   given a node at index **i**
    -   parent   index: **(i-1) / 2**
    -   children index: **2i + 1**, **2i + 2**


### Basic operations {#basic-operations}

The basic operations of a binary heap is insert, extract and build. I'll sneak in
the Java source code for exact implementation in the next section. Before that,
I want to set the basic concept of the logic flow of all three operations:

1.  Put-in or take-out nodes
2.  Messed up (Current 'heap' is not a heap)
3.  Heapify it


### Heap is unsorted {#heap-is-unsorted}

This is just a fact. I want to bring it up, because when we think about a binary
heap, we tend to compare it with a BST. While BST is sorted, heap is not. Also, heap is
unsorted, so the siblings are not sorted at all. Sometimes, it is easy to
ignore it.


## Heap and Priority Queue {#heap-and-priority-queue}

Finally, we can write code. Let's checkout Java source code of [PriorityQueue](https://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/PriorityQueue.java).
But wait, why priority queue?

**TL;DR. If we are only looking for a way to implement a binary min-heap,
just 'mock' Java's PriorityQueue.**

As we mentioned before, priority queue is an application of a binary heap,
specifically, a binary min-heap. And in fact, the only difference between a priority
queue and a binary min-heap, is just their concepts. If we only compare the
code, they could be exactly the same.

Ok, concepts are different. So how to define a priority queue? In a priority
queue, an element with high priority is served before an element with low
priority (From [WIKIPEDIA](https://en.wikipedia.org/wiki/Priority%5Fqueue)). Quote from [Java API](https://docs.oracle.com/javase/7/docs/api/java/util/PriorityQueue.html), "An unbounded priority queue based on a
priority heap."


### Class structure {#class-structure}

After reading the source code of PriorityQueue times and times, I came up with
this structure. I try to make it simpler, as it is mainly for beginners to
understand the binary heap. Here're some highlights:

1.  I utilize the generic type to define the array, while Java uses `Object[]`.
    Many reasons underneath. My choice only needs type casting once, which
    happens in constructor.
2.  Type bound and wildcard used, to make sure that E must be a subclass of some
    class that implements Comparable Interface. Note that Java source code is
    more complex, as it considers both Comparable and Comparator in reality use cases.
3.  The capacity is fixed. Of course, I can give it a dynamic growing feature, but
    I just leave it be.

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


### Heapify {#heapify}

I mentioned _Heapify_ in the last section. The main function of heapify is,
re-arrange the structure of heap, to make sure it satisfies two properties.
In the end, there're two methods to heapify, sift-up and sift-down. They are the
crucial, because most operations need them. Once you understand them, other operations are easy.

As you can see, `siftUp()` and `siftDown()` are two internal operations of the
Heap class. Before calling them, you need to do proper preprations.

Note that, for one calling of the sift-up/down, there may happen
**0 time or multiple times** of parent-child-swap.


#### Sift-up {#sift-up}

There're lots of names for it: up-heap, bubble-up, percolate-up, sift-up, trickle-up, swim-up, heapify-up, cascade-up.

Let the code speaks. And I also document it, so allow me to just leave it be.

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


#### Sift-down {#sift-down}

Also lots of names: down-heap, bubble-down, percolate-down, sift-down, sink-down, trickle down, heapify-down, cascade-down.

A bit more complicated than sift-up, because it requires a comparison between
two children.

Also just let the code speaks...

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


### Insert operation {#insert-operation}

Steps:

1.  size plus one
2.  pick the empty node and e
    -   empty node: tail
    -   e: argument
3.  sift-up

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


### Extract/Poll operation {#extract-poll-operation}

Steps:

1.  size minus one
2.  pick the empty node and e
    -   empty node: top
    -   e: tail
3.  sift-down

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


### Build operation {#build-operation}

I let the `build()` be a private method. It only gets called by the constructor,
when the argument contains a raw array.
Steps:

1.  for each non-leaf node, index number from big to small
    -   pick empty node and e
        -   empty node: current non-leaf node
        -   e: current non-leaf node
    -   sift-down
2.  until loop ends

<!--listend-->

```java
private boolean build() {
    for (int i = size / 2; i >= 1; i--) {
        siftDown(i, array[i]);
    }
    return true;
}
```


## In the End {#in-the-end}

With all the knowledge mentioned above, I write the complete implementation
code. Checkout my [Github Repo](https://github.com/muscaestar/algo).


## Reference {#reference}

-   Wikipedia
-   Weiss, M. A. (2011). Data structures and algorithm analysis in Java. Pearson.
-   Java™ Platform, Standard Edition 7, API Specification
-   [OpenJDK](https://hg.openjdk.java.net/)
-   数据结构与算法分析：Java 语言描述（原书第 3 版）/ (美)维斯（ Weiss，M.A.)著；冯舜玺，
    陈越 译. -- 北京：机械工业出版社，2016.2
-   [极客时间－数据结构与算法之美](https://time.geekbang.org/column/intro/126)
