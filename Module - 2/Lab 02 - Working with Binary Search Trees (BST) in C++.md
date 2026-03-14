# Lab 02 -- Working with Binary Search Trees (BST) in C++

## Table of Contents

1. [Objective](#1-objective)
2. [Binary Search Tree Overview](#2-binary-search-tree-overview)
3. [BST Properties](#3-bst-properties)
4. [Traversal Concepts](#4-traversal-concepts)
5. [Traversal Types](#5-traversal-types)
6. [Industry Use Cases](#6-industry-use-cases)
7. [BST Demonstration Program](#7-bst-demonstration-program)
8. [Expected Output](#8-expected-output)
9. [Visual Understanding of Traversals](#9-visual-understanding-of-traversals)
10. [Exercises](#10-exercises)
11. [Challenge Problems](#11-challenge-problems)
12. [Key Takeaways](#12-key-takeaways)


------------------------------------------------------------------------

# 1. Objective

In this lab, you will explore the **Binary Search Tree (BST)** data
structure and learn how different traversal techniques are used in real
systems.

By completing this lab, you will:

-   Understand the BST ordering rule
-   Implement insertion, search, and deletion
-   Implement four traversal techniques
-   Understand practical use cases for each traversal
-   Observe traversal behavior through program output

------------------------------------------------------------------------

# 2. Binary Search Tree Overview

A **Binary Search Tree (BST)** is a hierarchical data structure where
each node follows the rule:

Left Subtree \< Root \< Right Subtree

Example BST:

        50
       /  \
     30    70
    / \   / \

20 40 60 80

Advantages:

-   Faster search operations
-   Sorted data retrieval using inorder traversal
-   Efficient insert and delete operations

------------------------------------------------------------------------

# 3. BST Properties

  Property              Description
  --------------------- ---------------------------------------
  Ordering Rule         Left subtree values \< root
  Recursive Structure   Each subtree is itself a BST
  Sorted Traversal      Inorder traversal gives sorted values
  Search Efficiency     Average complexity O(log n)

Worst-case scenario occurs when the tree becomes skewed:

O(n)

------------------------------------------------------------------------

# 4. Traversal Concepts

Traversal means **visiting every node in a tree exactly once**.

Four common traversal methods exist:

  Traversal     Order
  ------------- --------------------------
  Preorder      Root → Left → Right
  Inorder       Left → Root → Right
  Postorder     Left → Right → Root
  Level Order   Level-by-level traversal

The first three belong to **Depth-First Search (DFS)** and the last is
**Breadth-First Search (BFS)**.

------------------------------------------------------------------------

# 5. Traversal Types

## Inorder Traversal

Left → Root → Right

Important property:

When applied to a BST, it produces **sorted output**.

Example:

Tree:

        4
       / \
      2   6
     / \ / \
    1  3 5  7

Output:

1 2 3 4 5 6 7

------------------------------------------------------------------------

## Preorder Traversal

Root → Left → Right

Used when parent nodes must be processed before children.

Applications:

-   Tree serialization
-   Copying tree structures

------------------------------------------------------------------------

## Postorder Traversal

Left → Right → Root

Used when children must be processed before the parent.

Applications:

-   Deleting tree structures
-   Expression tree evaluation

------------------------------------------------------------------------

## Level Order Traversal

Level-by-level traversal of the tree.

Also called **Breadth First Search (BFS)**.

Uses a **queue**.

Example traversal:

1 2 3 4 5 6

------------------------------------------------------------------------

# 6. Industry Use Cases

Traversal methods appear in many systems.

Inorder:

-   Database indexing
-   Sorted data reports
-   Ordered containers

Preorder:

-   Configuration tree serialization
-   Exporting hierarchical data

Postorder:

-   Safe memory cleanup
-   Dependency resolution

Level-order:

-   Network routing algorithms
-   Shortest path problems
-   Job scheduling systems

------------------------------------------------------------------------

# 7. BST Demonstration Program

``` cpp
#include <iostream>
#include <queue>
using namespace std;

struct Node
{
    int data;
    Node* left;
    Node* right;

    Node(int value)
    {
        data = value;
        left = nullptr;
        right = nullptr;
    }
};

class BST
{
private:

    Node* root;

    Node* insertNode(Node* node, int value)
    {
        if(node == nullptr)
            return new Node(value);

        if(value < node->data)
            node->left = insertNode(node->left, value);
        else
            node->right = insertNode(node->right, value);

        return node;
    }

    bool searchNode(Node* node, int key)
    {
        if(node == nullptr)
            return false;

        if(node->data == key)
            return true;

        if(key < node->data)
            return searchNode(node->left, key);
        else
            return searchNode(node->right, key);
    }

    void inorder(Node* node)
    {
        if(node == nullptr) return;

        inorder(node->left);
        cout << node->data << " ";
        inorder(node->right);
    }

    void preorder(Node* node)
    {
        if(node == nullptr) return;

        cout << node->data << " ";
        preorder(node->left);
        preorder(node->right);
    }

    void postorder(Node* node)
    {
        if(node == nullptr) return;

        postorder(node->left);
        postorder(node->right);
        cout << node->data << " ";
    }

public:

    BST()
    {
        root = nullptr;
    }

    void insert(int value)
    {
        root = insertNode(root, value);
    }

    bool search(int key)
    {
        return searchNode(root, key);
    }

    void inorderTraversal()
    {
        inorder(root);
        cout << endl;
    }

    void preorderTraversal()
    {
        preorder(root);
        cout << endl;
    }

    void postorderTraversal()
    {
        postorder(root);
        cout << endl;
    }

    void levelOrderTraversal()
    {
        if(root == nullptr) return;

        queue<Node*> q;
        q.push(root);

        while(!q.empty())
        {
            Node* temp = q.front();
            q.pop();

            cout << temp->data << " ";

            if(temp->left)
                q.push(temp->left);

            if(temp->right)
                q.push(temp->right);
        }

        cout << endl;
    }
};

int main()
{
    BST tree;

    tree.insert(50);
    tree.insert(30);
    tree.insert(70);
    tree.insert(20);
    tree.insert(40);
    tree.insert(60);
    tree.insert(80);

    cout << "Inorder Traversal: ";
    tree.inorderTraversal();

    cout << "Preorder Traversal: ";
    tree.preorderTraversal();

    cout << "Postorder Traversal: ";
    tree.postorderTraversal();

    cout << "Level Order Traversal: ";
    tree.levelOrderTraversal();

    cout << "\nSearching 40: ";
    if(tree.search(40))
        cout << "Found\n";
    else
        cout << "Not Found\n";

    return 0;
}
```

------------------------------------------------------------------------

# 8. Expected Output

Inorder Traversal: 20 30 40 50 60 70 80\
Preorder Traversal: 50 30 20 40 70 60 80\
Postorder Traversal: 20 40 30 60 80 70 50\
Level Order Traversal: 50 30 70 20 40 60 80

Searching 40: Found

------------------------------------------------------------------------

# 9. Visual Understanding of Traversals

Example Tree:

        1
       / \
      2   3
     / \   \
    4   5   6

Traversal Results:

Inorder:\
4 2 5 1 3 6

Preorder:\
1 2 4 5 3 6

Postorder:\
4 5 2 6 3 1

Level Order:\
1 2 3 4 5 6

------------------------------------------------------------------------

# 10. Exercises

1.  Modify the program to **delete a node from the BST**.
2.  Insert additional nodes and observe traversal outputs.
3.  Print the **height of the tree**.
4.  Implement **iterative inorder traversal using a stack**.

------------------------------------------------------------------------

# 11. Challenge Problems

1.  Write a function to find the **kth smallest element** in a BST.
2.  Write a function to **validate if a binary tree is a BST**.
3.  Implement **level-by-level printing of the tree**.

------------------------------------------------------------------------

# 12. Key Takeaways

After completing this lab, you should understand:

-   BST structure and ordering property
-   Differences between DFS and BFS traversals
-   Practical applications of traversal algorithms
-   Implementation of recursive tree algorithms

Binary Search Trees are foundational structures used in:

-   Database indexing
-   Search engines
-   Memory management systems
-   Graph algorithms

------------------------------------------------------------------------

End of Lab 02
