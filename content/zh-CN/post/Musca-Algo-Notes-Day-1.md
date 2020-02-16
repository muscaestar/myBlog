---
title: "算法 Day 1: 二叉树初入门"
author: ["Muscaestar"]
date: 2020-02-17T03:15:00+11:00
lastmod: 2020-02-17T03:15:54+11:00
tags: ["Algorithm"]
draft: false
---

就从二叉树开始吧。虽然大多数算法学习材料都从数组、链表开始，但是对于我的学习习惯来说，从最基础开始容易让我懈怠，拖慢我接受新知识的进度。二叉树是个有趣的起点，它有广泛的递归使用，需要更多的抽象思考。二叉树的许多变体，是当今工程里普遍使用的热门，比如红黑树，B＋树等。不过这篇博文属于入门级别，那些高级数据结构就留到之后再说吧。

这篇博文主要会涉及：

1.  二叉树和二叉搜索树的实现
2.  遍历、插入、查找、删除

<!--more-->


## 基本术语 {#基本术语}


### 树（[维基百科](https://zh.wikipedia.org/wiki/%E6%A0%91%5F(%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84))） {#树-维基百科}

-   是一种抽象数据类型（ADT）或是实现这种抽象数据类型的数据结构，用来模拟具有树状结构性质的数据集合。它是由 n（n>0）个有限节点组成一个具有层次关系的集合。


#### 树的术语 {#树的术语}

-   节点：构成树的基本单位
-   根节点：所有节点的祖先节点，没有父节点
-   非终端节点（分支节点）：至少有一个子节点
-   叶节点（终端节点）：没有子节点
-   Edge: 两个节点之间的连接
-   深度：对于任意节点 n,n 的深度为从根到 n 的唯一路径长，根的深度为 0
-   高度：对于任意节点 n,n 的高度为从 n 到一片树叶的最长路径长，所有叶节点的高度为 0
-   层：从根开始定义起，根为第 1 层，根的子节点为第 2 层，以此类推。深度＋1；


### 二叉树（[维基百科](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%8F%89%E6%A0%91)） {#二叉树-维基百科}

-   二叉树是每个节点最多只有两个子节点的树结构。通常分支被称作“左子树”或“右子树”。


### 二叉搜索树（[维基百科](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E6%90%9C%E5%B0%8B%E6%A8%B9)） {#二叉搜索树-维基百科}

-   二叉搜索树是指一棵空树或者具有下列性质的二叉树：
    -   若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值
    -   若任意节点的右子树不空，则右子树上所有节点的值均大于它的根节点的值
    -   任意节点的左、右子树也分别为二叉查找树
-   二叉查找树相比于其他数据结构的优势在于查找、插入的时间复杂度较低。


## 二叉树的实现 {#二叉树的实现}

二叉树有数组实现和链式实现两种方式。通常来说，链式实现是二叉树，以及普通二叉搜索树的常见实现方式，从定义的角度看，链式实现更为直观。而堆，一种特殊的完全二叉树，通常使用数组实现。

这里使用链式，定义一个实现二叉树的类 `BinaryTree` 。内嵌类实现节点 `Node` 。节点的域包含指向两个子节点的引用，以及一个泛型类型的域。

```java
public class BinaryTree<E> {
    //only one property
    private Node<E> root;

    //nested Node class
    static class Node<E> {
        E element;
        Node<E> leftNode;
        Node<E> rightNode;
        //constructor, getter, setter
    }

    //more...
}
```


## 二叉树的遍历（[维基百科](https://zh.wikipedia.org/wiki/%E6%A0%91%E7%9A%84%E9%81%8D%E5%8E%86)） {#二叉树的遍历-维基百科}


### 遍历的种类 {#遍历的种类}

-   深度优先遍历
    -   前序遍历
    -   中序遍历
    -   后序遍历
-   广度优先遍历
    -   层序遍历


### 使用递归实现遍历 {#使用递归实现遍历}

深度优先的遍历是最常见的，可以通过递归的方式，直观又简洁地实现出来。广度优先的遍历稍显不用，可以将树视为图，进行遍历，这个可以等到和图的知识一起说。这里就关注于三种深度优先遍历的实现。

递归和传统迭代都可以实现遍历，递归更优雅简洁。学习递归没有捷径，只能不断去尝试使用它，二叉树可以作为一个很好的递归练习对象。因此，本文选择使用递归实现遍历，和其他基础操作。

简单分析一下三种基本遍历，其实它们可以被看做是三个操作的不同顺序组合（ N 表示当前节点）：

-   L：递归遍历左（Left）子树
-   R：递归遍历右（Right）子树
-   N：处理当前节点 N

根据上述定义，三种基本遍历也可以被表示为：

-   前序遍历＝NLR
-   中序遍历＝LNR
-   后序遍历＝LRN

值得注意的是，虽然这三种遍历中，处理 N 的时机不同，但是前 L 后 R 的顺序的不会改变的。由此可以知道，对一个二叉树执行这三种遍历的过程中，递归函数的入栈出栈顺序，一定都是相同的。

掌握三种遍历的方法很简单，拿出纸笔自己画图实现几次就可以了。注意在画图的过程中，记录三个方面：

-   各递归函数入栈的时机
-   各递归函数出栈的时机
-   各节点处理（打印）的时机


#### NLR：前序遍历 {#nlr-前序遍历}

```java
public void preOrder(Node<E> N) {
    if (N == null) {
        return;
    }
    doSomething(N); // N
    preOrder(N.leftNode); // L
    preOrder(N.rightNode); // R
}
```


#### LNR：中序遍历 {#lnr-中序遍历}

```java
public void inOrder(Node<E> N) {
    if (N == null) {
        return;
    }
    preOrder(N.leftNode); // L
    doSomething(N); // N
    preOrder(N.rightNode); // R
}
```


#### LRN：后序遍历 {#lrn-后序遍历}

```java
public void postOrder(Node<E> N) {
    if (N == null) {
        return;
    }
    preOrder(N.leftNode); // L
    preOrder(N.rightNode); // R
    doSomething(N); // N
}
```


## 二叉搜索树的基本操作 {#二叉搜索树的基本操作}

二叉搜索树的基本结构和二叉树相似。对于一个储存整数的二叉搜索树，通过中序遍历，可以得到由这个树的所有节点的值组成的有序数列。由于二叉搜索树的结构和每个节点的数值相关，因此插入、查找、删除也需要遵循一定的规则。


### 定义二叉搜索树 {#定义二叉搜索树}

与二叉树基本相同。值得注意的是， `BinarySearchTree` 类使用了带有限制的通配符，要求泛型类型实现或其父类实现 `Comparable` 接口。因为排序需要比较，所以这是一个很自然的定义。

```java
public class BinarySearchTree<E extends Comparable<? super E>>() {

    // same as Binary Tree
    private Node<E> root;

    // same as Binary Tree
    static class Node<E>() {
        E element;
        Node<E> leftNode;
        Node<E> rightNode;
        // getters, setters, constructors
    }

    public void printSortedEle() {/* in-order traversal */}

    public Node<E> insert(E x) {...}
    public Node<E> find(E x) {...}
    public Node<E> delete(E x) {...}

    public Node<E> findMin() {...}
    public Node<E> findMax() {...}

    /*other methods*/
}
```


### 插入操作：递归方式 {#插入操作-递归方式}

使用递归时，方法过载是个常见的技巧。下列操作都使用了方法过载。

插入操作的逻辑如下：

-   递归步骤：
    1.  如果要插入的数据 `x` 比根节点的值小，那就在左子树中递归插入；否则，在右子树。然后重设子树的根节点。
    2.  如果要插入的数据 `x` 等于根节点的值，什么都不做。
    3.  最后，返回根节点。
-   基准情况：
    1.  根节点为空，用数据 `x` 创建一个新节点并将其返回（新节点就是当前子树的根节点）。

<!--listend-->

```java
public Node<E> insert(E x) {
    return insert(x, root);
}

public Node<E> insert(E x, Node<E> root) {
    /* validate argument */

    if (root == null) {
        return new Node<>(x);
    }

    int cmp = x.compareTo(root.element);
    if (cmp < 0) {
        root.leftNode = insert(x, root.leftNode);
    } else if (cmp > 0) {
        root.rightNode = insert(x, root.rightNode);
    } else { /* cmp == 0, do nothing */ }

    return root;
}
```


### 查找操作：递归方式 {#查找操作-递归方式}

查找的基本逻辑也很简单：

-   递归步骤:
    1.  如果要查找的数据 `x` 比根节点的值小，那就在左子树中递归查找；否则，在右子树。然后返回下一个递归的值。
-   基准情况:
    1.  如果要查找的数据 `x` 等于根节点的值，返回当前根节点（也就是想要查找的节点）。
    2.  根节点为空，返回空值（表示想要的节点不存在）。

<!--listend-->

```java
public Node<E> find(E x) {
    return find(x, root);
}

public Node<E> find(E x, Node<E> root) {
    /* validate agrument */

    if (root == null) {
        return null;
    }
    int cmp = x.compareTo(root.element);
    if (cmp < 0) {
        return find(x, root.leftNode);
    } else if (cmp > 0) {
        return find(x, root.rightNode);
    } else { // cmp == 0
        return root;
    }
}
```


### 删除操作：递归方式 {#删除操作-递归方式}

和很多数据结构一样，删除操作有些麻烦。有三种情况需要考虑：

-   递归步骤:
    1.  如果要删除的数据 `x` 比根节点的值小，那就在左子树中递归删除；否则，在右子树。然后重设子树的根节点。
    2.  如果要删除的数据 `x` 等于根节点的值，那就检查根节点的子节点：
        1.  如果根有
            **两个子节点**, 找到根的后继节点。赋予根节点以后继节点的值，接着删除后继节点。然后重设右子树的根节点。（虽然要‘删除’的是当前根节点，但实际上，这个节点并不会被删除，我们只是更改这个节点储存的数值。而后继节点，是真的被删除了，即没有指针指向它）。
        2.  基准情况 1.
        3.  基准情况 2.
    3.  最后，返回根节点。
-   基准情况:
    1.  如果根有
        **一个子节点**, 使这个子节点成为该子树的根。然后返回这个新的根。
    2.  如果根
        **没有子节点**, 那么使这个根为空（即删除这个节点）然后返回这个根（空）。
    3.  如果根为空，那么返回空。（这个情况发生在，当要删除的数据不存在于树中，因此树没有任何改动）。

<!--listend-->

```java
public Node<E> delete(E x) {
    root = delete(x, root);
    return root;
}

public Node<E> delete(E x, Node<E> root) {
    /* validate argument */

    if (root == null) {
        return null;
    }
    int cmp = x.compareTo(root.element);
    if (cmp < 0) {
        root.leftNode = delete(x, root.leftNode);
    } else if (cmp > 0) {
        root.rightNode = delete(x, root.rightNode);
    } else { // cmp == 0, so going to delete root

        if (root.leftNode != null && root.rightNode != null) { // two children
            // pick root's successor, replace root's element with successor's,
            // then delete successor
            // otherwise, picking root's predecessor also work

            // find the minimal node's element of right subtree
            E newEleOfRoot = findMin(root.rightNode).element;
            root.element = newEleOfRoot;
            root.rightNode = delete(newEleOfRoot, root.rightNode);

        } else if (root.leftNode != null || root.rightNode != null) {
            // one child, just use it to replace root
            root = (root.leftNode != null) ? root.leftNode : root.rightNode;

        } else { // no child, just delete root
            root = null;
        }
    }
    return root;
}
```


### 基本操作的总结 {#基本操作的总结}

-   当单个递归方法不使用于常规调用时，可以使用方法过载，定义一个递归方法的同名常规方法。这个同名方法将被用于外界的调用，而这个同名方法的内部，将调用这个递归方法。
-   `insert(E x)` 和 `delete(E x)` 返回的值是不必要的，因为它们最终返回的就是这个树的根节点。根据不同的情况可以任意选择实现返回或不返回，无伤大雅。
-   在上述删除操作中，当需要被删除的节点有两个子节点的情况下，选择了这个节点的后继节点去进行一系列操作。实际中，也可以选择前驱节点。只要记得后继节点对应右子树的操作，而前驱节点对应左子树的操作，就可以了。
-   除了上述删除操作的实现，
    **懒惰删除** 也是个可行的办法，即并非真的删除节点，而是在相应的节点上标记已删除。在删除操作有限的情况下，懒惰删除更加高效省力，也在一定程度上规避了由动态更新导致的性能退化。二叉搜索树的性能十分依赖于自身的结构，这也是为什么需要更高级更复杂的树结构，详细内容就交给以后的博文介绍吧。


## 写在最后 {#写在最后}

根据本文的知识点，我写了完整的代码实现，放在[Github Repo](https://github.com/muscaestar/algo). 我还准备收集一些与本文相应的 Leet-Code 题目，届时更新在这里。

本篇博文介绍了二叉树知识的初级入门。有关二叉树的进一步学习，会涉及到二叉搜索树的性能分析和动态更新对性能的影响。由此更进一步，就会涉及到一系列平衡二叉搜索树，
AVL 树，红黑树，B＋树等等。这些高级的树型结构，不是那么容易掌握的。但是对于软件工程师，想要真正认识大型数据库的结构和性能，高级树知识是一定
**必不可少的！**


## 参考资料 {#参考资料}

-   Wikipedia
-   Weiss, M. A. (2011). Data structures and algorithm analysis in Java. Pearson.
-   数据结构与算法分析：Java 语言描述（原书第 3 版）/ (美)维斯（ Weiss，M.A.)著；冯舜玺，陈越 译. -- 北京：机械工业出版社，2016.2
-   [极客时间－数据结构与算法之美](https://time.geekbang.org/column/intro/126)
