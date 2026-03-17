
# Red-Black Tree Deletion Lab (C++)

## Objective
- Understand deletion in Red-Black Trees
- Learn the double black concept and why it matters
- Explore all 4 deletion cases (sibling analysis)
- Implement deletion with rotations + recoloring

---

## Red-Black Tree Rules

1. Every node is either RED or BLACK  
2. Root is always BLACK  
3. No two consecutive RED nodes  
4. Every path from a node to NULL has same number of BLACK nodes  
5. NULL nodes are BLACK  

---

## Key Idea in Deletion

- Deleting RED node → No issue  
- Deleting BLACK node → Creates **Double Black**

---

## 📖 Double Black Concept
When a **BLACK node** is deleted, the tree may lose one black count along a path.  
This imbalance is represented as a **double black** at the position of the deleted node (or its child).  

- **Meaning:** The node is “too black” compared to others.  
- **Fix:** We resolve double black by checking the sibling’s color and children, then applying rotations + recoloring.  
- **Analogy:** Imagine every path must have the same number of black “shields.” Deleting a black node removes a shield, so we temporarily mark the spot as **double black** until we redistribute shields correctly.  
---

## Pseudocode (Deletion Fix)

```
delete(node):
    perform BST delete

    if deleted node is RED:
        return

    else:
        fixDoubleBlack(node)

fixDoubleBlack(x):
    if x is root:
        return

    sibling = getSibling(x)

    if sibling is RED:
        recolor parent & sibling
        rotate
        fixDoubleBlack(x)

    else if sibling is BLACK and both children BLACK:
        recolor sibling RED
        if parent BLACK:
            fixDoubleBlack(parent)
        else:
            parent BLACK

    else:
        if far child is RED:
            rotate parent
            recolor
        else:
            rotate sibling
            convert to far case
```

---

## Base Tree for Practice

```
                30(B)
               /     \
           20(B)     40(B)
          /    \     /    \
      10(B)  25(B) 35(B)  50(R)
                                  \
                                  60(B)
```

---

## Case 1: Sibling is RED

```
      P(B)
     /   \
   X(BB) S(R)
```

Action:
- Swap colors
- Rotate
- Convert to other cases

---

## Case 2: Sibling BLACK, both children BLACK

```
      P(B)
     /   \
   X(BB) S(B)
          / \
        B     B
```

Action:
- S → RED
- Move double black upward

---

## Case 3: Sibling BLACK, near child RED

```
      P(B)
     /   \
   X(BB) S(B)
          /
        R
```

Action:
- Rotate sibling
- Convert to Case 4

---

## Case 4: Sibling BLACK, far child RED

```
      P(B)
     /   \
   X(BB) S(B)
            \
             R
```

Action:
- Rotate parent
- Recolor
- Fix completed

---

## C++ Implementation
```cpp
```cpp
#include <iostream>
using namespace std;

enum Color { RED, BLACK };

struct Node {
    int data;
    Color color;
    Node *left, *right, *parent;

    Node(int val) {
        data = val;
        color = RED;              // new nodes are RED by default
        left = right = parent = NULL;
    }
};

class RBTree {
    Node* root;

    // Utility: print tree inorder with colors
    void inorderHelper(Node* root) {
        if (!root) return;
        inorderHelper(root->left);
        cout << root->data << "(" << (root->color == RED ? "R" : "B") << ") ";
        inorderHelper(root->right);
    }

    // Standard BST insert (simplified for demo)
    Node* bstInsert(Node* root, Node* node) {
        if (!root) return node;
        if (node->data < root->data) {
            root->left = bstInsert(root->left, node);
            root->left->parent = root;
        } else if (node->data > root->data) {
            root->right = bstInsert(root->right, node);
            root->right->parent = root;
        }
        return root;
    }

    // Rotations
    void rotateLeft(Node* &root, Node* &x) {
        Node* y = x->right;
        x->right = y->left;
        if (y->left) y->left->parent = x;
        y->parent = x->parent;
        if (!x->parent) root = y;
        else if (x == x->parent->left) x->parent->left = y;
        else x->parent->right = y;
        y->left = x;
        x->parent = y;
    }

    void rotateRight(Node* &root, Node* &x) {
        Node* y = x->left;
        x->left = y->right;
        if (y->right) y->right->parent = x;
        y->parent = x->parent;
        if (!x->parent) root = y;
        else if (x == x->parent->left) x->parent->left = y;
        else x->parent->right = y;
        y->right = x;
        x->parent = y;
    }

    // Fix double black cases
    void fixDoubleBlack(Node* x) {
        if (x == root) return;
        Node* parent = x->parent;
        Node* sibling = (x == parent->left) ? parent->right : parent->left;

        // Case 1: sibling RED
        if (sibling && sibling->color == RED) {
            parent->color = RED;
            sibling->color = BLACK;
            if (sibling == parent->left)
                rotateRight(root, parent);
            else
                rotateLeft(root, parent);
            fixDoubleBlack(x);
        }
        else {
            bool leftRed = sibling && sibling->left && sibling->left->color == RED;
            bool rightRed = sibling && sibling->right && sibling->right->color == RED;

            // Case 2: sibling BLACK, both children BLACK
            if (!leftRed && !rightRed) {
                if (sibling) sibling->color = RED;
                if (parent->color == BLACK)
                    fixDoubleBlack(parent);
                else
                    parent->color = BLACK;
            }
            else {
                // Case 3 & 4
                if (sibling == parent->left) {
                    if (leftRed) {
                        sibling->left->color = sibling->color;
                        sibling->color = parent->color;
                        rotateRight(root, parent);
                    } else {
                        sibling->right->color = parent->color;
                        rotateLeft(root, sibling);
                        rotateRight(root, parent);
                    }
                } else {
                    if (rightRed) {
                        sibling->right->color = sibling->color;
                        sibling->color = parent->color;
                        rotateLeft(root, parent);
                    } else {
                        sibling->left->color = parent->color;
                        rotateRight(root, sibling);
                        rotateLeft(root, parent);
                    }
                }
                parent->color = BLACK;
            }
        }
    }

    // BST delete + fix
    Node* bstDelete(Node* root, int key) {
        if (!root) return NULL;

        if (key < root->data)
            root->left = bstDelete(root->left, key);
        else if (key > root->data)
            root->right = bstDelete(root->right, key);
        else {
            if (!root->left || !root->right) {
                Node* child = (root->left) ? root->left : root->right;
                if (!child) {
                    if (root->color == BLACK)
                        fixDoubleBlack(root);
                    delete root;
                    return NULL;
                } else {
                    child->parent = root->parent;
                    if (root->color == BLACK && child->color == BLACK)
                        fixDoubleBlack(child);
                    delete root;
                    return child;
                }
            } else {
                Node* succ = root->right;
                while (succ->left) succ = succ->left;
                root->data = succ->data;
                root->right = bstDelete(root->right, succ->data);
            }
        }
        return root;
    }

public:
    RBTree() { root = NULL; }

    void insert(int key) {
        Node* node = new Node(key);
        root = bstInsert(root, node);
        root->color = BLACK; // ensure root is always black
    }

    void deleteNode(int key) {
        root = bstDelete(root, key);
        if (root) root->color = BLACK; // root must remain black
    }

    void inorder() {
        inorderHelper(root);
        cout << endl;
    }
};

/*
========================================
MAIN FUNCTION
========================================
*/
int main() {
    RBTree tree;

    // Insert nodes
    int arr[] = {30, 20, 40, 10, 25, 35, 50, 60};
    for (int x : arr) tree.insert(x);

    cout << "Initial Tree (Inorder with colors): ";
    tree.inorder();

    // Perform deletions
    tree.deleteNode(10); // leaf
    tree.deleteNode(20); // one child
    tree.deleteNode(30); // two children

    cout << "After Deletions (Inorder with colors): ";
    tree.inorder();

    return 0;
}
---

## Key Takeaways

- Only BLACK deletion causes imbalance  
- Double black must be resolved  
- Use sibling + children colors  
- Rotations + recoloring restore balance  

---

## Practice

- Identify case for each deletion  
- Draw tree before & after  
- Track color changes  

---
