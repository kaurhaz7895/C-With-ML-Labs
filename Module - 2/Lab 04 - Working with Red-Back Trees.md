# Red-Black Tree Lab (C++)

## Table of Contents

-   [1. Introduction](#1-introduction)
-   [2. Definition of Red-Black Tree](#2-definition-of-red-black-tree)
-   [3. Properties of Red-Black Trees](#3-properties-of-red-black-trees)
-   [4. Why Red-Black Trees are
    Needed](#4-why-red-black-trees-are-needed)
-   [5. Height Guarantee](#5-height-guarantee)
-   [6. Insertion Cases](#6-insertion-cases)
-   [7. Rotation Concepts](#7-rotation-concepts)
-   [8. Red-Black Tree Implementation in
    C++](#8-red-black-tree-implementation-in-c)
-   [9. Example Execution](#9-example-execution)
-   [10. Visual Representation](#10-visual-representation)
-   [11. Complexity Analysis](#11-complexity-analysis)

------------------------------------------------------------------------

# 1. Introduction

A **Red-Black Tree** is a self-balancing Binary Search Tree.

It maintains balance by enforcing **color rules on nodes**.

Each node is either:

-   Red
-   Black

These rules ensure the tree height remains **logarithmic**, guaranteeing
efficient operations.

------------------------------------------------------------------------

# 2. Definition of Red-Black Tree

A **Red-Black Tree** is a Binary Search Tree where each node contains an
extra attribute: **color**.

The tree uses coloring and rotations to maintain balance after insertion
and deletion.

------------------------------------------------------------------------

# 3. Properties of Red-Black Trees

A valid Red-Black Tree satisfies these five properties:

1.  Every node is either **Red or Black**
2.  The **root is always Black**
3.  All **NULL leaves are Black**
4.  A **Red node cannot have a Red child**
5.  Every path from a node to its descendant NULL nodes contains the
    **same number of Black nodes**

Property 4 prevents long chains of red nodes.

Property 5 guarantees balanced height.

------------------------------------------------------------------------

# 4. Why Red-Black Trees are Needed

Normal BST can become skewed:

    10
      \
      20
        \
        30
          \
          40

Search becomes:

    O(n)

Red-Black Trees maintain approximate balance so height remains small.

Operations stay:

    O(log n)

------------------------------------------------------------------------

# 5. Height Guarantee

For a Red-Black Tree with **n nodes**:

    height <= 2 log(n+1)

This ensures efficient search, insertion, and deletion.

------------------------------------------------------------------------

# 6. Insertion Cases

When inserting into a Red-Black Tree:

1.  Insert node like a normal BST.
2.  Color the new node **Red**.
3.  Fix violations of Red-Black properties.

Common cases:

  Case     Situation        Fix
  -------- ---------------- ------------------
  Case 1   Uncle is Red     Recolor
  Case 2   Triangle shape   Rotate + recolor
  Case 3   Line shape       Rotate + recolor

------------------------------------------------------------------------

# 7. Rotation Concepts

Rotations restructure the tree while preserving BST order.

### Left Rotation

          x                y
           \              / \
            y    →       x   T3
           / \            \
         T2  T3           T2

### Right Rotation

            y              x
           /              / \
          x      →       T1  y
         / \                /
       T1  T2              T2

Rotations are also used in **AVL trees**, but Red-Black Trees combine
them with recoloring.

------------------------------------------------------------------------

# 8. Red-Black Tree Implementation in C++

``` cpp
#include <iostream>
using namespace std;

enum Color {RED, BLACK};

/*
Node structure for Red-Black Tree
Each node stores:
1. key value
2. color (RED or BLACK)
3. left child
4. right child
5. parent pointer
*/

class Node{
public:
    int data;
    Color color;
    Node *left, *right, *parent;
};

class RBTree{

private:
    Node *root;
    Node *TNULL;

    /*
    Initialize NULL node
    All leaves point to TNULL
    */

    void initializeNULLNode(Node *node, Node *parent){
        node->data = 0;
        node->parent = parent;
        node->left = nullptr;
        node->right = nullptr;
        node->color = BLACK;
    }

    /*
    Left Rotation
    */

    void leftRotate(Node *x){

        Node *y = x->right;

        x->right = y->left;

        if(y->left != TNULL)
            y->left->parent = x;

        y->parent = x->parent;

        if(x->parent == nullptr)
            root = y;

        else if(x == x->parent->left)
            x->parent->left = y;

        else
            x->parent->right = y;

        y->left = x;
        x->parent = y;
    }

    /*
    Right Rotation
    */

    void rightRotate(Node *x){

        Node *y = x->left;

        x->left = y->right;

        if(y->right != TNULL)
            y->right->parent = x;

        y->parent = x->parent;

        if(x->parent == nullptr)
            root = y;

        else if(x == x->parent->right)
            x->parent->right = y;

        else
            x->parent->left = y;

        y->right = x;
        x->parent = y;
    }

    /*
    Fix Red-Black Tree after insertion
    */

    void fixInsert(Node *k){

        Node *u;

        while(k->parent->color == RED){

            if(k->parent == k->parent->parent->right){

                u = k->parent->parent->left;

                if(u->color == RED){

                    u->color = BLACK;
                    k->parent->color = BLACK;
                    k->parent->parent->color = RED;
                    k = k->parent->parent;
                }

                else{

                    if(k == k->parent->left){

                        k = k->parent;
                        rightRotate(k);
                    }

                    k->parent->color = BLACK;
                    k->parent->parent->color = RED;
                    leftRotate(k->parent->parent);
                }
            }

            else{

                u = k->parent->parent->right;

                if(u->color == RED){

                    u->color = BLACK;
                    k->parent->color = BLACK;
                    k->parent->parent->color = RED;
                    k = k->parent->parent;
                }

                else{

                    if(k == k->parent->right){

                        k = k->parent;
                        leftRotate(k);
                    }

                    k->parent->color = BLACK;
                    k->parent->parent->color = RED;
                    rightRotate(k->parent->parent);
                }
            }

            if(k == root)
                break;
        }

        root->color = BLACK;
    }

public:

    RBTree(){

        TNULL = new Node;
        TNULL->color = BLACK;
        TNULL->left = nullptr;
        TNULL->right = nullptr;

        root = TNULL;
    }

    /*
    Insert node
    */

    void insert(int key){

        Node *node = new Node;

        node->parent = nullptr;
        node->data = key;
        node->left = TNULL;
        node->right = TNULL;
        node->color = RED;

        Node *y = nullptr;
        Node *x = this->root;

        while(x != TNULL){

            y = x;

            if(node->data < x->data)
                x = x->left;

            else
                x = x->right;
        }

        node->parent = y;

        if(y == nullptr)
            root = node;

        else if(node->data < y->data)
            y->left = node;

        else
            y->right = node;

        if(node->parent == nullptr){

            node->color = BLACK;
            return;
        }

        if(node->parent->parent == nullptr)
            return;

        fixInsert(node);
    }

    /*
    Preorder traversal
    */

    void preorder(Node *node){

        if(node != TNULL){

            cout << node->data << " ";
            preorder(node->left);
            preorder(node->right);
        }
    }

    void printTree(){
        preorder(this->root);
    }
};

int main(){

    RBTree tree;

    tree.insert(10);
    tree.insert(20);
    tree.insert(30);
    tree.insert(15);
    tree.insert(25);

    cout << "Preorder traversal of Red-Black Tree:\n";

    tree.printTree();
}
```

------------------------------------------------------------------------

# 9. Example Execution

Inserted values:

    10 20 30 15 25

Program Output:

    Preorder traversal of Red-Black Tree:
    20 10 15 30 25

------------------------------------------------------------------------

# 10. Visual Representation

Example balanced tree:

            20(B)
           /    \
       10(R)    30(B)
          \      /
         15(B) 25(R)

B = Black node\
R = Red node

------------------------------------------------------------------------

# 11. Complexity Analysis

  Operation   Time Complexity
  ----------- -----------------
  Search      O(log n)
  Insert      O(log n)
  Delete      O(log n)

Red-Black Trees maintain **near-perfect balance**, ensuring logarithmic
performance.
