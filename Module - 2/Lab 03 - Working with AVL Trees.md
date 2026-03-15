# Lab 03 - Working with AVL Trees in C++

## Table of Contents

-   [1. Introduction](#1-introduction)
-   [2. Definition of AVL Tree](#2-definition-of-avl-tree)
-   [3. Need for AVL Trees](#3-need-for-avl-trees)
-   [4. Height Calculation](#4-height-calculation)
-   [5. Balance Factor](#5-balance-factor)
-   [6. Rotation Detection Rule Chart](#6-rotation-detection-rule-chart)
-   [7. AVL Rotation Cases](#7-avl-rotation-cases)
    -   [LL Rotation](#ll-rotation)
    -   [RR Rotation](#rr-rotation)
    -   [LR Rotation](#lr-rotation)
    -   [RL Rotation](#rl-rotation)
-   [8. AVL Tree Implementation in C++](#8-avl-tree-implementation-in-c)
-   [9. Example Execution](#9-example-execution)
-   [10. Visual Representation](#10-visual-representation)
-   [11. Complexity Analysis](#11-complexity-analysis)

------------------------------------------------------------------------

# 1. Introduction

Binary Search Trees provide efficient operations when balanced.\
However, if keys are inserted in sorted order, the tree becomes
**skewed**.

AVL Trees automatically rebalance themselves after insertion or
deletion.

AVL stands for:

Adelson‑Velsky and Landis (1962)

They introduced the first **self‑balancing Binary Search Tree**.

------------------------------------------------------------------------

# 2. Definition of AVL Tree

An AVL Tree is a Binary Search Tree where the difference between heights
of left and right subtrees is at most **1**.

Balance Factor:

    BF = height(left) − height(right)

Allowed values:

    -1, 0, +1

If the balance factor becomes **+2 or −2**, rotations are performed.

------------------------------------------------------------------------

# 3. Need for AVL Trees

Example BST insertion:

    10, 20, 30, 40

Skewed BST:

``` mermaid
graph TD
10 --> 20
20 --> 30
30 --> 40
```

Height becomes **n**.

Search complexity:

    O(n)

Balanced AVL tree:

``` mermaid
graph TD
20 --> 10
20 --> 30
30 --> 40
```

Search complexity remains:

    O(log n)

------------------------------------------------------------------------

# 4. Height Calculation

Height formula:

    height(node) = 1 + max(height(left), height(right))

Example:

``` mermaid
graph TD
20 --> 10
20 --> 30
```

Heights:

    10 → 1
    30 → 1
    20 → 2

------------------------------------------------------------------------

# 5. Balance Factor

    BF = height(left) − height(right)

  Balance Factor   Meaning
  ---------------- --------------------
  0                Perfectly balanced
  +1               Left heavy
  -1               Right heavy
  +2 or -2         Rotation required

------------------------------------------------------------------------

# 6. Rotation Detection Rule Chart

This table helps identify which rotation is required.

  Condition                       Case   Rotation
  ------------------------------- ------ -----------------------------------
  BF \> 1 and key \< left.key     LL     Right Rotation
  BF \< -1 and key \> right.key   RR     Left Rotation
  BF \> 1 and key \> left.key     LR     Left rotation then Right rotation
  BF \< -1 and key \< right.key   RL     Right rotation then Left rotation

Students can use this rule during **manual tracing of AVL insertion**.

------------------------------------------------------------------------

# 7. AVL Rotation Cases

## LL Rotation

Insertion occurs in **left subtree of left child**.

Before:

``` mermaid
graph TD
30 --> 20
20 --> 10
```

After rotation:

``` mermaid
graph TD
20 --> 10
20 --> 30
```

------------------------------------------------------------------------

## RR Rotation

Insertion occurs in **right subtree of right child**.

Before:

``` mermaid
graph TD
10 --> 20
20 --> 30
```

After rotation:

``` mermaid
graph TD
20 --> 10
20 --> 30
```

------------------------------------------------------------------------

## LR Rotation

Insertion occurs in **right subtree of left child**.

Before:

``` mermaid
graph TD
30 --> 10
10 --> 20
```

Step 1 (Left rotation):

``` mermaid
graph TD
30 --> 20
20 --> 10
```

Step 2 (Right rotation):

``` mermaid
graph TD
20 --> 10
20 --> 30
```

------------------------------------------------------------------------

## RL Rotation

Insertion occurs in **left subtree of right child**.

Before:

``` mermaid
graph TD
10 --> 30
30 --> 20
```

Step 1 (Right rotation):

``` mermaid
graph TD
10 --> 20
20 --> 30
```

Step 2 (Left rotation):

``` mermaid
graph TD
20 --> 10
20 --> 30
```

------------------------------------------------------------------------

# 8. AVL Tree Implementation in C++

``` cpp
#include <iostream>
using namespace std;

class Node{
public:
    int key;
    Node* left;
    Node* right;
    int height;
};

int height(Node* n){
    if(n==NULL)
        return 0;
    return n->height;
}

int max(int a,int b){
    return (a>b)?a:b;
}

Node* newNode(int key){
    Node* node = new Node();
    node->key = key;
    node->left = NULL;
    node->right = NULL;
    node->height = 1;
    return node;
}

Node* rightRotate(Node* y){

    Node* x = y->left;
    Node* T2 = x->right;

    x->right = y;
    y->left = T2;

    y->height = max(height(y->left),height(y->right))+1;
    x->height = max(height(x->left),height(x->right))+1;

    return x;
}

Node* leftRotate(Node* x){

    Node* y = x->right;
    Node* T2 = y->left;

    y->left = x;
    x->right = T2;

    x->height = max(height(x->left),height(x->right))+1;
    y->height = max(height(y->left),height(y->right))+1;

    return y;
}

int getBalance(Node* n){
    if(n==NULL)
        return 0;
    return height(n->left)-height(n->right);
}

Node* insert(Node* node,int key){

    if(node==NULL)
        return newNode(key);

    if(key < node->key)
        node->left = insert(node->left,key);
    else if(key > node->key)
        node->right = insert(node->right,key);
    else
        return node;

    node->height = 1 + max(height(node->left),height(node->right));

    int balance = getBalance(node);

    if(balance>1 && key < node->left->key)
        return rightRotate(node);

    if(balance<-1 && key > node->right->key)
        return leftRotate(node);

    if(balance>1 && key > node->left->key){
        node->left = leftRotate(node->left);
        return rightRotate(node);
    }

    if(balance<-1 && key < node->right->key){
        node->right = rightRotate(node->right);
        return leftRotate(node);
    }

    return node;
}

void preorder(Node* root){

    if(root!=NULL){
        cout<<root->key<<" ";
        preorder(root->left);
        preorder(root->right);
    }
}

int main(){

    Node* root=NULL;

    root=insert(root,10);
    root=insert(root,20);
    root=insert(root,30);
    root=insert(root,40);
    root=insert(root,50);
    root=insert(root,25);

    cout<<"Preorder traversal of AVL tree:\n";
    preorder(root);
}
```

------------------------------------------------------------------------

# 9. Example Execution

Inserted values:

    10 20 30 40 50 25

Program output:

    Preorder traversal of AVL tree
    30 20 10 25 40 50

------------------------------------------------------------------------

# 10. Visual Representation

``` mermaid
graph TD
30 --> 20
30 --> 40
20 --> 10
20 --> 25
40 --> 50
```

------------------------------------------------------------------------

# 11. Complexity Analysis

  Operation   Time Complexity
  ----------- -----------------
  Search      O(log n)
  Insert      O(log n)
  Delete      O(log n)

AVL Trees maintain **strict balance**, ensuring logarithmic performance.
