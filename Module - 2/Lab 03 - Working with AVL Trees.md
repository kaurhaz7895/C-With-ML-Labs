# AVL Tree Lab (C++)

## Table of Contents

-   [1. Introduction](#1-introduction)
-   [2. Definition of AVL Tree](#2-definition-of-avl-tree)
-   [3. Need for AVL Trees](#3-need-for-avl-trees)
-   [4. Height Calculation](#4-height-calculation)
-   [5. Balance Factor](#5-balance-factor)
-   [6. AVL Rotation Cases](#6-avl-rotation-cases)
    -   [6.1 LL Rotation (Right
        Rotation)](#61-ll-rotation-right-rotation)
    -   [6.2 RR Rotation (Left Rotation)](#62-rr-rotation-left-rotation)
    -   [6.3 LR Rotation (Left‑Right
        Rotation)](#63-lr-rotation-leftright-rotation)
    -   [6.4 RL Rotation (Right‑Left
        Rotation)](#64-rl-rotation-rightleft-rotation)
-   [7. AVL Tree Implementation in C++](#7-avl-tree-implementation-in-c)
-   [8. Example Execution](#8-example-execution)
-   [9. Visual Representation of
    Output](#9-visual-representation-of-output)
-   [10. Complexity Analysis](#10-complexity-analysis)

------------------------------------------------------------------------

# 1. Introduction

Binary Search Trees (BST) allow efficient searching, insertion, and
deletion operations.

However, if nodes are inserted in sorted order, the BST becomes
**skewed**, degrading performance.

AVL Trees automatically **maintain balance after each insertion or
deletion**.

AVL stands for **Adelson‑Velsky and Landis (1962)**.

------------------------------------------------------------------------

# 2. Definition of AVL Tree

An **AVL Tree** is a self‑balancing Binary Search Tree in which the
difference between the heights of left and right subtrees of any node is
at most **1**.

Balance Factor formula:

    BF = height(left subtree) − height(right subtree)

Allowed values:

    -1, 0, +1

If the balance factor becomes **±2**, the tree is rebalanced using
**rotations**.

------------------------------------------------------------------------

# 3. Need for AVL Trees

Consider inserting sorted values into a normal BST.

Insert:

    10, 20, 30, 40

BST becomes:

    10
      \
      20
        \
        30
          \
          40

Height becomes **n**, so searching becomes **O(n)**.

Balanced AVL tree:

          20
         /  \
       10    30
               \
                40

Search complexity remains **O(log n)**.

------------------------------------------------------------------------

# 4. Height Calculation

Height of a node:

    height(node) = 1 + max(height(left), height(right))

Example:

          20
         /  \
       10    30

Heights:

    10 → 1
    30 → 1
    20 → 2

------------------------------------------------------------------------

# 5. Balance Factor

Balance factor determines whether the tree is balanced.

    BF = height(left) − height(right)

  Balance Factor   Meaning
  ---------------- ----------------------
  0                Perfectly balanced
  +1               Slightly left heavy
  -1               Slightly right heavy
  +2 / -2          Rotation required

------------------------------------------------------------------------

# 6. AVL Rotation Cases

Four types of imbalance may occur.

------------------------------------------------------------------------

## 6.1 LL Rotation (Right Rotation)

Occurs when insertion happens in the **left subtree of the left child**.

Insert:

    30, 20, 10

Before:

        30
       /
     20
     /
    10

After rotation:

        20
       /  \
     10   30

------------------------------------------------------------------------

## 6.2 RR Rotation (Left Rotation)

Occurs when insertion happens in the **right subtree of the right
child**.

Insert:

    10, 20, 30

Before:

    10
      \
      20
        \
        30

After rotation:

        20
       /  \
     10   30

------------------------------------------------------------------------

## 6.3 LR Rotation (Left‑Right Rotation)

Occurs when insertion happens in the **right subtree of the left
child**.

Insert:

    30, 10, 20

Before:

         30
        /
      10
        \
         20

Step 1 --- Left rotation on 10

         30
        /
       20
      /
    10

Step 2 --- Right rotation on 30

         20
        /  \
      10    30

------------------------------------------------------------------------

## 6.4 RL Rotation (Right‑Left Rotation)

Occurs when insertion happens in the **left subtree of the right
child**.

Insert:

    10, 30, 20

Before:

    10
      \
       30
      /
    20

Step 1 --- Right rotation on 30

    10
      \
       20
         \
          30

Step 2 --- Left rotation on 10

         20
        /  \
      10    30

------------------------------------------------------------------------

# 7. AVL Tree Implementation in C++

``` cpp
#include <iostream>
using namespace std;

class Node {
public:
    int key;
    Node *left;
    Node *right;
    int height;
};

int height(Node *n) {
    if(n == NULL)
        return 0;
    return n->height;
}

int max(int a,int b) {
    return (a>b)?a:b;
}

Node* newNode(int key) {
    Node* node = new Node();
    node->key = key;
    node->left = NULL;
    node->right = NULL;
    node->height = 1;
    return node;
}

Node* rightRotate(Node *y) {
    Node *x = y->left;
    Node *T2 = x->right;

    x->right = y;
    y->left = T2;

    y->height = max(height(y->left), height(y->right)) + 1;
    x->height = max(height(x->left), height(x->right)) + 1;

    return x;
}

Node* leftRotate(Node *x) {
    Node *y = x->right;
    Node *T2 = y->left;

    y->left = x;
    x->right = T2;

    x->height = max(height(x->left), height(x->right)) + 1;
    y->height = max(height(y->left), height(y->right)) + 1;

    return y;
}

int getBalance(Node *n) {
    if(n == NULL)
        return 0;
    return height(n->left) - height(n->right);
}

Node* insert(Node* node, int key) {

    if(node == NULL)
        return newNode(key);

    if(key < node->key)
        node->left = insert(node->left,key);
    else if(key > node->key)
        node->right = insert(node->right,key);
    else
        return node;

    node->height = 1 + max(height(node->left),height(node->right));

    int balance = getBalance(node);

    if(balance > 1 && key < node->left->key)
        return rightRotate(node);

    if(balance < -1 && key > node->right->key)
        return leftRotate(node);

    if(balance > 1 && key > node->left->key) {
        node->left = leftRotate(node->left);
        return rightRotate(node);
    }

    if(balance < -1 && key < node->right->key) {
        node->right = rightRotate(node->right);
        return leftRotate(node);
    }

    return node;
}

void preOrder(Node *root) {
    if(root != NULL) {
        cout << root->key << " ";
        preOrder(root->left);
        preOrder(root->right);
    }
}

int main() {

    Node *root = NULL;

    root = insert(root,10);
    root = insert(root,20);
    root = insert(root,30);
    root = insert(root,40);
    root = insert(root,50);
    root = insert(root,25);

    cout << "Preorder traversal of AVL tree:\n";
    preOrder(root);

    return 0;
}
```

------------------------------------------------------------------------

# 8. Example Execution

Inserted values:

    10 20 30 40 50 25

Program output:

    Preorder traversal of AVL tree:
    30 20 10 25 40 50

------------------------------------------------------------------------

# 9. Visual Representation of Output

            30
           /  \
         20    40
        /  \     \
       10  25     50

------------------------------------------------------------------------

# 10. Complexity Analysis

  Operation   Time Complexity
  ----------- -----------------
  Search      O(log n)
  Insert      O(log n)
  Delete      O(log n)

AVL trees maintain **strict balance**, ensuring logarithmic time
complexity.
