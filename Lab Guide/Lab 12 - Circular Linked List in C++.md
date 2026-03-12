
# Lab 12 — Circular Linked List in C++

## Lab Title
Circular Linked List Implementation and Operations in C++

## Objectives
After completing this lab, participants will be able to:
- Understand the structure of a circular linked list
- Implement circular linked list using C++ classes
- Perform insertion, deletion, and traversal
- Handle edge cases (empty list, single node, deleting head/tail)
- Understand real-world applications and performance considerations

---

# 1. Introduction

A **Circular Linked List (CLL)** is a variation of a linked list where the last node points back to the first node.

Example:

```
10 → 20 → 30 → 40
 ↑             ↓
 └─────────────┘
```

Key property:

```
tail->next = head
```

This eliminates the NULL pointer found in regular singly linked lists.

---

# 2. Node Structure

```cpp
class Node
{
public:
    int data;
    Node* next;

    Node(int value)
    {
        data = value;
        next = nullptr;
    }
};
```

---

# 3. Implementation (Using Tail Pointer)

Using a **tail pointer** simplifies operations because the head can always be accessed using:

```
head = tail->next
```

---

# 4. Complete Program

```cpp
#include <iostream>
using namespace std;

class Node
{
public:
    int data;
    Node* next;

    Node(int value)
    {
        data = value;
        next = nullptr;
    }
};

class CircularLinkedList
{
private:
    Node* tail;

public:

    CircularLinkedList()
    {
        tail = nullptr;
    }

    void insertAtBeginning(int value)
    {
        Node* newNode = new Node(value);

        if(tail == nullptr)
        {
            tail = newNode;
            newNode->next = newNode;
        }
        else
        {
            newNode->next = tail->next;
            tail->next = newNode;
        }
    }

    void insertAtEnd(int value)
    {
        Node* newNode = new Node(value);

        if(tail == nullptr)
        {
            tail = newNode;
            newNode->next = newNode;
        }
        else
        {
            newNode->next = tail->next;
            tail->next = newNode;
            tail = newNode;
        }
    }

    void deleteNode(int key)
    {
        if(tail == nullptr)
        {
            cout << "List is empty" << endl;
            return;
        }

        Node* current = tail->next;
        Node* prev = tail;

        do
        {
            if(current->data == key)
            {
                if(current == tail && current->next == tail)
                {
                    delete current;
                    tail = nullptr;
                    return;
                }

                prev->next = current->next;

                if(current == tail)
                    tail = prev;

                delete current;
                return;
            }

            prev = current;
            current = current->next;

        } while(current != tail->next);

        cout << "Value not found" << endl;
    }

    void traverse()
    {
        if(tail == nullptr)
        {
            cout << "List is empty" << endl;
            return;
        }

        Node* current = tail->next;

        do
        {
            cout << current->data << " ";
            current = current->next;

        } while(current != tail->next);

        cout << endl;
    }
};

int main()
{
    CircularLinkedList list;

    list.insertAtEnd(10);
    list.insertAtEnd(20);
    list.insertAtEnd(30);

    cout << "Initial list: ";
    list.traverse();

    list.insertAtBeginning(5);

    cout << "After inserting at beginning: ";
    list.traverse();

    list.deleteNode(20);

    cout << "After deleting 20: ";
    list.traverse();

    return 0;
}
```

---

# 5. Expected Output

```
Initial list: 10 20 30
After inserting at beginning: 5 10 20 30
After deleting 20: 5 10 30
```

---

# 6. Traversal Logic

Circular traversal stops when we reach the starting node again.

```
do {
   process node
} while(current != head)
```

---

# 7. Edge Cases

| Scenario | Handling |
|--------|---------|
Empty list | tail == nullptr |
Single node | node->next == node |
Delete tail | update tail pointer |
Delete head | update tail->next |

---

# 8. Time Complexity

| Operation | Complexity |
|----------|-----------|
Insertion (beginning) | O(1) |
Insertion (end) | O(1) |
Traversal | O(n) |
Deletion | O(n) |

---

# 9. Real-World Applications

Circular linked lists appear in:

- Round Robin CPU scheduling
- Multiplayer game turns
- Music playlist looping
- Token ring networks
- Event scheduling systems

---

# 10. Exercises

1. Implement `insertAfter(value, newValue)`.
2. Implement `deleteFirst()` and `deleteLast()`.
3. Implement `countNodes()`.
4. Modify the program to maintain **head pointer instead of tail pointer**.

---

# 11. Summary

Circular linked lists form a loop where:

```
tail->next = head
```

They are useful in systems where elements must be processed repeatedly in cycles.

Understanding circular linked lists strengthens pointer manipulation skills and prepares developers for advanced data structure design.
