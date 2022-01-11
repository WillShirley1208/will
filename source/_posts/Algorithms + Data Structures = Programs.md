---
title: Algorithms + Data Structures = Programs
date: 2018-12-18 22:00:30
tags: snippet
categories: DSA
---

### What is a Data Structure

> a data structure is a container that stores data in a specific layout. 

### Why do we need Data Structure

> As data structures are used to store data in an organized form, and since data is the most crucial entity in computer science.
> 
> Based on different scenarios, data needs to be stored in a specific format. We have a handful of data structures that cover our need to store data in different formats.

### Commonly used Data Structure

#### 1. Arrays

> An array is the simplest and most widely used data structure. Other data structures like stacks and queues are derived from arrays.

- the two types of arrays:
  - One-dimensional arrays
  - Multi-dimensional arrays
- basic operations
  - Inserts an element at given index
  - Get — Returns the element at given index
  - Delete — Deletes an element at given index
  - Size — Get the total number of elements in array

#### 2. Stacks

> LIFO (Last In First Out)

- basic operations
  - Push — Inserts an element at the top
  - Pop — Returns the top element after removing from the stack
  - isEmpty — Returns true if the stack is empty
  - Top — Returns the top element without removing from the stack

#### 3. Queues

> Similar to Stack, Queue is another linear data structure that stores the element in a sequential manner. The only significant difference between Stack and Queue is that instead of using the LIFO method, Queue implements the FIFO method, which is short for First in First Out.

- basic operations
  - Enqueue() — Inserts element to the end of the queue
  - Dequeue() — Removes an element from the start of the queue
  - isEmpty() — Returns true if queue is empty
  - Top() — Returns the first element of the queue

#### 4. Linked List

> A linked list is like a chain of nodes, where each node contains information like data and a pointer to the succeeding node in the chain.

- the types of linked lists
  - Singly Linked List (Unidirectional)
  - Doubly Linked List (Bi-directional)
- basic operations
  - *InsertAtEnd* — Inserts given element at the end of the linked list
  - *InsertAtHead* — Inserts given element at the start/head of the linked list
  - *Delete* — Deletes given element from the linked list
  - *DeleteAtHead* — Deletes first element of the linked list
  - *Search* — Returns the given element from a linked list
  - *isEmpty* — Returns true if the linked list is empty

#### 5. Graphs

> A graph is a set of nodes that are connected to each other in the form of a network. Nodes are also called vertices. A **pair(x,y)** is called an **edge***,* which indicates that vertex **x** is connected to vertex **y**. An edge may contain weight/cost, showing how much cost is required to traverse from vertex x to y*.*

![](https://i.imgur.com/9sHq0Ek.png)

- types of Graphs
  - Undirected Graph
  - Directed Graph

#### 6. Trees

> A tree is a hierarchical data structure consisting of vertices (nodes) and edges that connect them. Trees are similar to graphs, but the key point that differentiates a tree from the graph is that a cycle cannot exist in a tree.

![](https://i.imgur.com/R6zwf9U.png)

- the types of trees
  
  - N-ary Tree
  - Balanced Tree
  - Binary Tree
  - Binary Search Tree
  - AVL Tree
  - Red Black Tree
  - 2–3 Tree
  
  > Out of the above, Binary Tree and Binary Search Tree are the most commonly used trees.

#### 7. Trie

> Trie, which is also known as “Prefix Trees”, is a tree-like data structure which proves to be quite efficient for solving problems related to strings. It provides fast retrieval, and is mostly used for searching words in a dictionary, providing auto suggestions in a search engine, and even for IP routing.

#### 8. Hash Table

> Hashing is a process used to uniquely identify objects and store each object at some pre-calculated unique index called its “key.”
> 
> Hash tables are generally implemented using arrays.

- The performance of hashing data structure depends upon these three factors:
  - Hash Function
  - Size of the Hash Table
  - Collision Handling Method

Here’s an illustration of how the hash is mapped in an array. The index of this array is calculated through a Hash Function.

![](https://i.imgur.com/fkxWuxr.png)

reference:

- [图解Java常用数据结构](https://www.cnblogs.com/xdecode/p/9321848.html)
