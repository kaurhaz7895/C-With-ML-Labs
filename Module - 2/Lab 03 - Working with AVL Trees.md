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

/*
Node structure of AVL Tree
Each node stores:
1. data      → value of the node
2. left     → pointer to left child
3. right    → pointer to right child
4. height   → height of the node
*/

class Node {
public:
    int data, height;
    Node* left, * right;
};

/*
Function to get height of a node
If node is NULL → height = 0
*/
int height(Node* n) {
    if (n == NULL)
        return -1;
    return n->height;
}
void printHeights(Node* root)
{
    if (root == NULL)
        return;

    cout << "Node " << root->data
        << " height = " << root->height << endl;

    printHeights(root->left);
    printHeights(root->right);
}
/*
Utility function to get maximum of two numbers
Used for height calculation
*/
int max(int a, int b) {
    return (a > b) ? a : b;
}

/*
Create a new AVL node
Initial height is set to 1
because leaf node height = 1
*/
Node* newNode(int data) {

    Node* node = new Node();

    node->data = data;
    node->left = NULL;
    node->right = NULL;
    node->height = 0;

    return node;
}

/*
Right Rotation (used in LL imbalance)

        y                              x
       / \                           /   \
      x   T3     Right Rotate      T1    y
     / \        ------------->          / \
    T1  T2                           T2   T3
*/

Node* rightRotate(Node* y) {

    Node* x = y->left;
    Node* T2 = x->right;

    // Perform rotation
    x->right = y;
    y->left = T2;

    // Update heights
    y->height = max(height(y->left), height(y->right)) + 1;
    x->height = max(height(x->left), height(x->right)) + 1;

    // Return new root
    return x;
}

/*
Left Rotation (used in RR imbalance)

      x                               y
     / \                            /   \
   T1   y        Left Rotate       x     T3
       / \      ------------->    / \
      T2 T3                      T1 T2
*/

Node* leftRotate(Node* x) {

    Node* y = x->right;
    Node* T2 = y->left;

    // Perform rotation
    y->left = x;
    x->right = T2;

    // Update heights
    x->height = max(height(x->left), height(x->right)) + 1;
    y->height = max(height(y->left), height(y->right)) + 1;

    // Return new root
    return y;
}

/*
Get Balance Factor

BF = height(left subtree) - height(right subtree)
*/

int getBalance(Node* n) {

    if (n == NULL)
        return 0;

    return height(n->left) - height(n->right);
}

/*
Insert a data into AVL tree
This works like BST insertion first
Then checks balance factor
If imbalance occurs → apply rotations
*/

Node* insert(Node* node, int data) {

    // Step 1: Perform normal BST insertion
    if (node == NULL)
        return newNode(data);

    if (data < node->data)
        node->left = insert(node->left, data);

    else if (data > node->data)
        node->right = insert(node->right, data);

    else
        return node; // duplicate datas not allowed


    // Step 2: Update height of current node
    node->height = 1 + max(height(node->left), height(node->right));


    // Step 3: Get balance factor
    int balance = getBalance(node);


    /*
    Step 4: Check imbalance cases
    */


    // Case 1: LL Case
    if (balance > 1 && data < node->left->data)
        return rightRotate(node);


    // Case 2: RR Case
    if (balance < -1 && data > node->right->data)
        return leftRotate(node);


    // Case 3: LR Case
    if (balance > 1 && data > node->left->data) {

        node->left = leftRotate(node->left);

        return rightRotate(node);
    }


    // Case 4: RL Case
    if (balance < -1 && data < node->right->data) {

        node->right = rightRotate(node->right);

        return leftRotate(node);
    }


    // return node pointer
    return node;
}

/*
Preorder traversal of AVL tree
Used to print tree structure
*/

void preorder(Node* root) {

    if (root != NULL) {

        cout << root->data << " ";

        preorder(root->left);

        preorder(root->right);
    }
}

/*
Driver program
*/

int main() {

    Node* root = NULL;

    /*
    Insert elements into AVL tree
    */

    root = insert(root, 10);
    root = insert(root, 20);
    root = insert(root, 30);
    root = insert(root, 40);
    root = insert(root, 50);
    root = insert(root, 25);

	// After inserting above elements, the AVL tree will be balanced
	// The structure of the AVL tree will be:
	// 	   30
	//   /  \
    // 20   40
	// / \    \
    //10 25   50

	// The height of the root node (30) will be 2, as it has two levels of children below it.
	// The preorder traversal will print the nodes in the order: 30, 20, 10, 25, 40, 50

    /*
    Print preorder traversal
    */

    cout << "Preorder traversal of AVL tree:\n";
    preorder(root);

	cout << "\nHeight of root node: " << root->height << endl;

	cout << "Print heights of all nodes:\n";
	printHeights(root);
    

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
