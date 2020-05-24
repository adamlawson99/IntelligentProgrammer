---
layout: single
title:  "Binary Trees (draft)"
date:   2020-05-17 09:40:00 -0400
classes: wide
categories: recursion
---

## A look at binary trees
Binary trees are a fundemental data structure in computer science and their applications stretch beyond computer science into many domains. They are can be found in binary search trees, binary heaps, syntax trees, hash trees and more. The first thing that comes to mind for many of us when we hear the word 'binary tree' is binary search tree. The binary search tree is a very popular implementation of a binary tree that I will be covering later on in this post. I wanted to cover regular binary trees first because it's important to understand their generic properties and aspect before we dive into something more specific. I'll be covering types of binary trees, binary tree traversal, two different types of search operations on binary trees, and a few other pertient subject, so without further ado let's dive in!

Let's start with what exactly a binary search tree is. A tree is a data structure that has a root, which has 0 or more children. Each child of the root is itself a tree and follows the definition we just listed. Essentially, every single node in a tree is a tree itself. A binary search tree is a tree where for each node in this tree, that node can have 0, 1, or 2 children. The name binary comes from the fact that each node can have 2 children, a ternary tree for example has nodes with up to 3 children. These children are called the left child and the right child, and each node has pointers to these children. We sometimes also maintain a parent pointer in a node, where the parent is the node for which I am a child, though this is less common. It's important to note that 2 is a maximum bound on the number of children, certain nodes may have 0 children, these are called leaf nodes. Leaf nodes are nodes without children, which makes sense because like a leaf on a tree we cannot branch out further once we reach a leaf node. The defining mark of a leaf node is that their pointers to the left and right child are null.

**Height**: The height of a binary tree is the longest path from the root to the bottom of the tree.
**Depth**: The depth of a node in a binary tree is the number of nodes from the root to the node, the depth of the root is 0;
**Level**: The level of a binary tree is all the nodes that are found at a certain height. So the root is at level 0, we can have 2 nodes at level 1, 4 nodes at level 2 etc...

![Binary Tree](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze1.png)
The blue and purple triangles represent binary trees. These can be the same size, or they may differ

Let's take a look at some of the binary trees we can make with 1,2, or 3 nodes:

![Binary Tree 1 node](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze1.png)
A binary tree with 1 node has 1 possible configuration, this binary tree has height 1.

![Binary Tree 2 nodes](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze1.png)
A binary tree with 2 nodes has 2 possible configurations, with a height of 2.

![Binary Tree 3 nodes](https://github.com/adamlawson99/IntelligentProgrammer/raw/master/assets/Images/Parens/maze1.png)
A binary tree with 2 nodes has 5 possible configurations, with a maximum height of 3.

## Balanced Binary Trees
A binary tree is considered balanced when the height of the left and right subtrees differ by no more than 1. This must be true for every node in the tree.
Why do we concern ourselves with the balance of the tree? For binary search trees an unbalanced tree can leave us with a worst case time complexity of O(n) for search, which is not good since binary search trees are supposed to give us O(log n) search! I'll talk about binary search trees a little later, just remember that a balanced binary tree is one in which the height of the left and right subtrees differ in height by no more than 1.

## Types of binary trees
There are 3 sub-types of binary trees we will concern ourselves with here, these are : complete binary trees, full binary trees and perfect binary trees.

### Complete binary trees
A complete binary tree is a binary tree in which each level of the binary tree is filled, except for possibly the last level, which if partially filled is filled from left to right. In other words, each node on every level before the last level has 2 children. The last level can be partially filled, but any nodes must be inserted from left to right, here is an example: 

### Full binary trees
A full binary tree is a binary tree where each node has 0 or 2 children, there can be no nodes with 1 child, here's an example:

### Perfect binary trees
A perfect binary tree is a binary tree that is both complete and full. This type of tree is rare and doesn't come up too often, but it's good to know it exists. Below is an example:

## Binary Tree Traversal

## Depth First Search and Breadth First Search

## Time Complexity
