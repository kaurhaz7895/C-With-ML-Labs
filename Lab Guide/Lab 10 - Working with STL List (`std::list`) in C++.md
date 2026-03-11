# Lab 10 --- STL List (`std::list`) Demonstration in C++

## Table of Contents

1.  Introduction
2.  Definition of `std::list`
3.  Internal Structure (Doubly Linked List)
4.  STL List vs Manual Linked List (Class Implementation)
5.  Advantages of `std::list`
6.  Drawbacks of `std::list`
7.  Real‑World Use Cases
8.  Emerging Modern Use Cases
9.  Demo Program Covering List Methods
10. Program Output
11. Step‑by‑Step Explanation
12. Key Observations
13. Conclusion

------------------------------------------------------------------------

# 1. Introduction

In earlier labs we implemented linked lists manually using classes and
pointers.

Modern C++ provides the **Standard Template Library (STL)** which
includes a container called **`std::list`**. This container internally
implements a **doubly linked list**.

Using STL containers simplifies development because programmers do not
need to manually manage memory or pointer logic.

------------------------------------------------------------------------

# 2. Definition of `std::list`

`std::list` is a **sequence container** in C++ STL that stores elements
using a **doubly linked list structure**.

Each node contains: - the data - pointer to the previous node - pointer
to the next node

This allows traversal in **both directions**.

Lists are sequence containers. List allows constant time insert as well as delete operations at any point within the sequence. Moreover, it allows iteration in both directions.
The STL’s (Standard Template Library’s) list container is implemented as doubly linked list. While these lists are able to store every element contained in diverse and distinct storage locations. The elements of the list can be dispersed in multiple chunks of memory. Whereas, the container stores essential information to let the sequential access to the data it contains. Lists can expand as well as shrink dynamically as needed from both ends. Furthermore, the storage requirement is automatically fulfilled by the internal allocator.
The order as well is maintained internally where each element of a link is connected to the element preceding it and to the element following it.

------------------------------------------------------------------------

# 3. Internal Structure

Conceptually the structure looks like:

    NULL <- [10] <-> [20] <-> [30] <-> [40] -> NULL

Node representation:

``` cpp
struct Node
{
    int data;
    Node* prev;
    Node* next;
};
```

------------------------------------------------------------------------

# 4. STL List vs Manual Linked List Implementation

## Manual Linked List

``` cpp
class Node
{
public:
    int data;
    Node* next;

    Node(int val)
    {
        data = val;
        next = nullptr;
    }
};

class LinkedList
{
public:
    Node* head;

    LinkedList()
    {
        head = nullptr;
    }

    void insertFront(int value)
    {
        Node* newNode = new Node(value);
        newNode->next = head;
        head = newNode;
    }
};
```

### Limitations

-   manual memory handling
-   pointer errors possible
-   larger codebase

------------------------------------------------------------------------

## STL Implementation

``` cpp
#include <list>

std::list<int> numbers;
```

One line replaces an entire custom linked list implementation.

------------------------------------------------------------------------

# 5. Advantages of `std::list`

### Dynamic Size

The container grows and shrinks dynamically.

### Efficient Insert/Delete

Insertion or deletion requires only pointer updates.

### Bidirectional Traversal

Doubly linked lists support forward and backward movement.

### Iterator Stability

Iterators remain valid during insertions.

------------------------------------------------------------------------

# 6. Drawbacks of `std::list`

### Extra Memory

Each node stores two pointers.

### No Random Access

Indexing like this is not allowed:

    numbers[2]

### Cache Inefficiency

Elements are not stored in contiguous memory.

------------------------------------------------------------------------

# 7. Real‑World Use Cases

### Browser Navigation

    Page1 <-> Page2 <-> Page3

### Undo / Redo Systems

Applications like editors maintain history using linked structures.

### Music Playlists

    Song1 <-> Song2 <-> Song3

### LRU Cache

Many cache systems combine

    HashMap + Doubly Linked List

------------------------------------------------------------------------

# 8. Emerging Modern Use Cases

Linked list concepts are still used in:

-   distributed storage systems
-   concurrent lock‑free data structures
-   operating system memory managers

------------------------------------------------------------------------

# 9. Demo Program Covering List Methods

``` cpp
#include <iostream>
#include <list>

using namespace std;

int main()
{
    list<int> numbers = {10,20,30};

    numbers.push_front(5);
    numbers.push_back(40);

    cout << "Initial list: ";
    for(int x : numbers)
        cout << x << " ";
    cout << endl;

    cout << "Front: " << numbers.front() << endl;
    cout << "Back: " << numbers.back() << endl;

    auto it = numbers.begin();
    numbers.insert(it, 1);

    numbers.pop_front();
    numbers.pop_back();

    numbers.resize(6,100);

    numbers.sort();

    numbers.reverse();

    numbers.unique();

    cout << "Final list: ";
    for(int x : numbers)
        cout << x << " ";

    cout << endl;

    return 0;
}
```

------------------------------------------------------------------------

# 10. Program Output

    Initial list: 5 10 20 30 40
    Front: 5
    Back: 40
    Final list: 100 100 30 20 10

------------------------------------------------------------------------

# 11. Step‑by‑Step Explanation

Initial list

    10 → 20 → 30

After insertion

    5 → 10 → 20 → 30 → 40

After resize

    5 → 10 → 20 → 30 → 100 → 100

Sorting and reversing reorganizes the elements.

------------------------------------------------------------------------

# 12. Key Observations

  Operation       Complexity
  --------------- ---------------
  Insert          O(1)
  Delete          O(1)
  Traversal       O(n)
  Random Access   Not supported

------------------------------------------------------------------------

# 13. Conclusion

`std::list` provides a ready‑to‑use implementation of a **doubly linked
list** in C++.

It is useful when applications require:

-   frequent insertions
-   frequent deletions
-   bidirectional traversal

However for workloads requiring random access, **`std::vector`** is
usually more efficient.
