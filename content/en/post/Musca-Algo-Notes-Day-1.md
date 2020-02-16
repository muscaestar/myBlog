---
title: "Algo Day 1: Start with Binary Tree"
author: ["Muscaestar"]
date: 2020-02-17T03:15:00+11:00
lastmod: 2020-02-17T03:15:58+11:00
tags: ["Algorithm"]
draft: false
---

Just start from the interesting one. I don't really want to go back and start
from array, linked list and so on... My past experience tells me if I try to be
perfect in my safe zone first, then I may never get to the next level.

Binary Tree is a good spot to start from. You can really learn recursion with
it. You do lots of abstract thinking even if you know it just shapes like a tree.
Some of its variants are very important in current industry, like Red-black Tree
and B+ Tree. This post aims to cover only the beginner level, so I'll leave
those advanced structures for other posts.

This post mainly includes:

1.  Implementation of Binary Tree and BST
2.  Operations like traverse, insert, delete and find

<!--more-->


## Basic Terminology {#basic-terminology}


### Tree (From [WIKIPEDIA](https://en.wikipedia.org/wiki/Tree%5F(data%5Fstructure))) {#tree--from-wikipedia}

-   A tree is a widely used abstract data type (ADT) that simulates a hierarchical
    tree structure, represented as a set of linked nodes.
-   Recursively defining, a tree is a collection of nodes, where each node
    consists of a list of references of other nodes.
-   Constraints: no reference is duplicated, and none points to the root.


#### Terminology used in trees {#terminology-used-in-trees}

-   **Node**: A basic unit of data structure.
-   **Root**: The top node in a tree, the prime ancestor.
-   **Internal node**: A node with at least one child.
-   **Leaf**: A node with no children.
-   **Edge**: The connection between one node and another.
-   **Depth**: The number of edges on the shortest path between a node and the root.
    (Root's depth is 0.)
-   **Height**: The number of edges on the longest path between a node and a
    descendant leaf. (Root is the highest.)
-   **Level**: Depth + 1.(Root's level is 1.)
-   [More in Wiki](https://en.wikipedia.org/wiki/Tree%5F(data%5Fstructure)#Terminology%5Fused%5Fin%5Ftrees)


### Binary Tree (From [WIKIPEDIA](https://en.wikipedia.org/wiki/Binary%5Ftree)) {#binary-tree--from-wikipedia}

-   A binary tree is a tree data structure in which each node
    has at most two children, left child and right child.
-   A recursive definition: a (non-empty) binary tree is a tuple (L, S, R), where L and R are binary trees
    or the empty set and S is a singleton set.


### Binary Search Tree (BST) {#binary-search-tree--bst}

-   A binary tree, each key of whose nodes must be greater than any
    key stored in the left sub-tree, and less than any key stored in
    the right sub-tree.
-   It allows fast lookup, addition and removal of items.


## Implementation of a Binary Tree {#implementation-of-a-binary-tree}

Generally, You can implement a binary tree with an array or a linked list. The
most common and easy way is using linked list, which is more obvious to the
definition. Further, a BST is also commonly implemented with linked list and
recursion. As for array, it is used with Heap(a special complete binary tree).

Here's a simple implementation of a binary tree, which has a nested class `Node`.
Each node has two references pointing to other nodes, and a property
holding the element. For the class `BinaryTree`, it only holds one node, which is
the root node.

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


## Binary Tree Traversals ([From WIKIPEDIA](https://en.wikipedia.org/wiki/Tree%5Ftraversal)) {#binary-tree-traversals--from-wikipedia}


### Types of tree traversals {#types-of-tree-traversals}

1.  Pre-order traversal (DFS)
2.  In-order traversal (DFS)
3.  Post-order traversal (DFS)
4.  Level-order traversal (BFS)


### Taste of recursive traversal {#taste-of-recursive-traversal}

Using DFS to traverse a binary tree is the most common way, and it can be
easily done with recursion. But for BFS, the level-order traversal is a bit more
complex than the former three. Since the DFS/BFS is a core concept in Graph,
this post focuses more on the three common traversals, pre-order, in-order, and
post-order.

Each of these three traversals can be implemented in both recursion way and
iterative way. By comparing the two implementations, you can see the
recursion is such a beauty. [Checkout the pseudocode.](https://en.wikipedia.org/wiki/Tree%5Ftraversal#Implementations)

Basically, these traversals are the combination of following operations in
various order (N stands for the current node):

-   (L) Recursively traverse N's left subtree. This step is finished at the node N again.
-   (R) Recursively traverse N's right subtree. This step is finished at the node N again.
-   (N) Process N itself.

According to this, the traversals can also be represented as:

-   Pre-order = NLR
-   In-order = LNR
-   Post-order = LRN

One thing to notify, for these 3 types of traversals, even though the order of
"print each node itself" is different, the order of "stack-in-out of each node's
recursive method" is the same.

For wholly understand the traversals, highly recommend you drawing pictures by
yourself. Remember to record the order of:

-   when is each recursive method pushed into call stack
-   when is each recursive method popped out of call stack
-   when is each node itself printed


#### NLR: pre-order traversal {#nlr-pre-order-traversal}

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


#### LNR: in-order traversal {#lnr-in-order-traversal}

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


#### LRN: post-order traversal {#lrn-post-order-traversal}

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


## BST with basic operations {#bst-with-basic-operations}

Binary Search Tree is a binary tree, so it extends the Binary Tree's structure.
With an in-order traversal, you can get a sorted list of nodes. Since the nodes
of BST are structured according to their keys(values), methods of insertion, lookup, and
deletion should be carefully defined.


### Define a BST {#define-a-bst}

A basic structure of BST. Note that element of node should implement
`Comparable`, as one of the usages of BST is to sort.

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


### Insertion: recursive way {#insertion-recursive-way}

A way of using recursion is to utilize the method overloading. The logic is
simple:

-   **Recursive step**:
    1.  If `x` is less than `root.element`, do this with the left subtrees.
        Otherwise, the right subtrees. Then re-set the root of the subtree with the
        coming back value.
    2.  If `x` equals to `root.element`, do nothing.
    3.  Finally, return root.
-   **Base case**:
    1.  `root` is null, then return a new Node with `x` (it becomes the new root).

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


### Lookup: recursive way {#lookup-recursive-way}

The basic logic is simple:

-   **Recursive step**:
    1.  If `x` is less than `root.element`, do this with the left subtrees.
        Otherwise, the right subtrees. Then return the coming back value.
-   **Base cases**:
    1.  `x` equals to `root.element`, then return the root (which is the node wanted).
    2.  `root` is null, then return null (the node wanted not exists).

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


### Deletion: recursive way {#deletion-recursive-way}

Like many data structures, the deletion is kinda tricky. There're three
circumstances needed to be considered. Here's the logic:

-   **Recursive step**:
    1.  If `x` is less than `root.element`, do this with the left subtree.
        Otherwise, the right subtrees. Then re-set the root of the subtree with
        the coming back value.
    2.  If `x` equals to `root.element`, then check root's children:
        1.  If root has _two children_, find the successor of the root first. Replace
            root's element with successor's. Then delete successor, and re-set the
            root of right subtree with the coming back value. (So, the 'root node' is
            actually not changed. What is replaced is the root's element. And the
            'successor node' is truely deleted.)
        2.  Base case 1.
        3.  Base case 2.
    3.  Finally, return root.
-   **Base cases**:
    1.  If root has _one child_, make the child be the new root. Then return the new root.
    2.  If root has _no child_, then make the root be null (delete it). Then return the root(null).
    3.  If root is null, then return null. (In this case, node is not found, so no node is deleted.)

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


### Summary of basic operations {#summary-of-basic-operations}

-   When one recursive method cannot fit in the normal use case, utilize method
    overloading, creating a non-recursive method (with fewer parameters) to assist
    it. The non-recursive method is exposed for oursiders to use, while the recursive
    method is called in the non-recursive one.
-   `insert(E x)` and `delete(E x)` doesn't have to return any value. Because what
    they eventually return, is the root of the BST. I choose to return
    the root, just personal choice. Doesn't really matter.
-   In deletion operation, when the node to be deleted has two children, my
    implementation pick successor. Actually choose predecessor instead is also
    fine. Just bear in mind that, the successor deals with right subtree, while the
    predecessor deals with the left subtree.
-   Lazy deletion is also a solution, as an alternative to the deletion above.
    When there requires limited deletion, just mark node as 'deleted' instead of
    really delete it. Lazy deletion saves some extra effort, and avoid the risk of
    constantly changing the tree structure. The performance of BST is highly
    sensitive to its structure, I'll go deep in some future posts.


## In the End {#in-the-end}

With all the knowledge mentioned above, I write the complete implementation
code. Checkout my [Github Repo](https://github.com/muscaestar/algo). I also want to append some related leet-code
puzzles here. I'll update this post at a proper time.

This post gives an entry level to the tree structure. Further, I'm planning to analyze
the performance of the BST and discover more about its degeneration. With those
findings, I can write more about the balanced tree, AVL tree, red-black tree, B+
Tree... These advanced trees are gonna be so hard. But they are also must-know, if
you want to understand the performance of modern databases.


## Reference {#reference}

-   Wikipedia
-   Weiss, M. A. (2011). Data structures and algorithm analysis in Java. Pearson.
-   数据结构与算法分析：Java 语言描述（原书第 3 版）/ (美)维斯（ Weiss，M.A.)著；冯舜玺，
    陈越 译. -- 北京：机械工业出版社，2016.2
-   [极客时间－数据结构与算法之美](https://time.geekbang.org/column/intro/126)
