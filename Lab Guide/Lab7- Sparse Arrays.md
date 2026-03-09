# Lab Guide: Sparse Arrays in C++

## Learning Objectives

By the end of this lab, students will be able to:

-   Explain what a **sparse array** is
-   Understand why sparse arrays are memory efficient
-   Compare **dense vs sparse representations**
-   Implement a **1‑D sparse array using C++ STL**
-   Represent **2‑D sparse matrices**
-   Analyze time and space complexity of sparse structures

------------------------------------------------------------------------

# 1. Prerequisites

Students should be familiar with:

-   Basic C++ syntax
-   Arrays
-   Structures (`struct`)
-   STL containers such as `vector`
-   Loops and functions

------------------------------------------------------------------------

# 2. What is a Sparse Array?

A **sparse array** is an array in which **most elements are zero**.

Instead of storing all elements, we store only the **non‑zero values and
their positions**.

This dramatically reduces memory usage when the dataset contains mostly
zeros.

------------------------------------------------------------------------

# 3. Dense vs Sparse Representation

## Dense Array

    Index : 0 1 2 3 4 5 6 7 8 9
    Value : 0 0 0 5 0 0 0 0 9 0

Memory usage:

    10 integers stored

------------------------------------------------------------------------

## Sparse Representation

Instead of storing every value:

    (3,5)
    (8,9)

Meaning:

    Index 3 → Value 5
    Index 8 → Value 9

Memory usage:

    2 elements stored

------------------------------------------------------------------------

# 4. Applications of Sparse Arrays

Sparse arrays appear frequently in real systems:

-   Machine learning feature vectors
-   Graph adjacency matrices
-   Recommendation engines
-   Search engines
-   Scientific computing
-   Image processing

------------------------------------------------------------------------

# 5. Conceptual Storage Model

Logical array:

    Index : 0 1 2 3 4 5 6 7 8 9
    Value : 0 0 0 5 0 0 0 0 9 0

Sparse storage using a vector of structures:

    +--------+-------+
    | index  | value |
    +--------+-------+
    |   3    |   5   |
    |   8    |   9   |
    +--------+-------+

Only non‑zero elements are stored.

------------------------------------------------------------------------

# 6. 1‑D Sparse Array Implementation

The following program demonstrates how to simulate a sparse array using
a `vector`.

``` cpp
/* ***********************************1-D Sparse Arrays *********************************/
#include <iostream>
#include <vector>

using namespace std;

// Structure representing one non-zero element of the sparse array
struct SparseElement {
    size_t index;   // Position in the logical array
    int value;      // Value stored at that position
};

int main() {

    // Logical size of the array we want to simulate
    const size_t SIZE = 10;

    // Vector that stores only non-zero elements
    vector<SparseElement> sparse;

    /*
        Function: set_value
        Purpose: Insert or update a value at a given index
    */
    auto set_value = [&](size_t idx, int val)
    {
        // Check array boundary
        if (idx >= SIZE)
        {
            cout << "Index out of range\n";
            return;
        }

        // Search for existing index
        for (auto it = sparse.begin(); it != sparse.end(); ++it)
        {
            if (it->index == idx)
            {
                if (val == 0)
                    sparse.erase(it);     // remove element
                else
                    it->value = val;      // update value

                return;
            }
        }

        // Insert new element if non-zero
        if (val != 0)
            sparse.push_back({ idx, val });
    };

    /*
        Function: get_value
        Returns value stored at an index
    */
    auto get_value = [&](size_t idx)
    {
        for (const auto& e : sparse)
        {
            if (e.index == idx)
                return e.value;
        }

        return 0;   // default value
    };

    // Insert sparse elements
    set_value(3, 5);
    set_value(8, 9);

    // Reconstruct array
    cout << "Array:\n";

    for (size_t i = 0;i < SIZE;i++)
        cout << get_value(i) << " ";

    cout << endl;

    // Display sparse storage
    cout << "\nSparse Representation:\n";

    for (const auto& e : sparse)
        cout << "Index: " << e.index
             << " Value: " << e.value << endl;
}
```

------------------------------------------------------------------------

# 7. Code Walkthrough

## Structure Definition

    struct SparseElement

Stores two pieces of information:

  Field   Description
  ------- -----------------------
  index   location in the array
  value   non‑zero value

------------------------------------------------------------------------

## Sparse Storage Container

    vector<SparseElement> sparse;

Stores only non‑zero elements.

Example internal representation:

    [(3,5),(8,9)]

------------------------------------------------------------------------

## set_value()

Responsible for:

-   inserting values
-   updating values
-   removing zeros

Process:

    Check index validity
    ↓
    Search existing element
    ↓
    Update or delete
    ↓
    Insert new element

------------------------------------------------------------------------

## get_value()

Returns the value at a given index.

Logic:

    Search sparse vector
    ↓
    If found → return value
    ↓
    Else → return 0

------------------------------------------------------------------------

# 8. Program Output

    Array:
    0 0 0 5 0 0 0 0 9 0

    Sparse Representation:
    Index: 3 Value: 5
    Index: 8 Value: 9

------------------------------------------------------------------------

# 9. Time Complexity

  Operation   Complexity
  ----------- ------------
  Insert      O(n)
  Lookup      O(n)
  Memory      O(k)

Where:

    k = number of non‑zero elements

------------------------------------------------------------------------

# 10. 2‑D Sparse Arrays

Sparse matrices store only:

    (row, column, value)

Example matrix:

    0 5 0
    0 0 0
    0 0 9

Sparse representation:

    (0,1,5)
    (2,2,9)

------------------------------------------------------------------------

# 11. Modern Sparse Matrix Example

``` cpp
#include <iostream>
#include <vector>
#include <cstddef>

struct SparseElement {
	std::size_t row;
	std::size_t col;
	int value;
};

int main() {
	constexpr std::size_t SIZE = 5;

	// Store only non-zero elements in a vector (array-of-structs).
	std::vector<SparseElement> sparse;

	// Helper: set a value at (r,c). Zero entries are removed from the sparse vector.
	auto set_value = [&](std::size_t r, std::size_t c, int v) {
		if (r >= SIZE || c >= SIZE) {
			std::cerr << "Index out of range: (" << r << ',' << c << ")\n";
			return;
		}
		for (auto it = sparse.begin(); it != sparse.end(); ++it) {
			if (it->row == r && it->col == c) {
				if (v == 0)
					sparse.erase(it);
				else
					it->value = v;
				return;
			}
		}
		if (v != 0) sparse.push_back({r, c, v});
	};

	// Helper: get value at (r,c). Returns 0 if not present.
	auto get_value = [&](std::size_t r, std::size_t c) -> int {
		for (const auto &e : sparse) {
			if (e.row == r && e.col == c) return e.value;
		}
		return 0;
	};

	// Populate sparse structure directly (no dense allocation)
	set_value(0, 1, 5);
	set_value(2, 3, 10);
	set_value(4, 0, 15);

	// Display the original array (reconstructed from sparse representation)
	std::cout << "Original Array:\n";
	for (std::size_t i = 0; i < SIZE; ++i) {
		for (std::size_t j = 0; j < SIZE; ++j) {
			std::cout << get_value(i, j) << ' ';
		}
		std::cout << '\n';
	}

	// Display the sparse array representation
	std::cout << "\nSparse Array Representation:\n";
	for (const auto &e : sparse) {
		std::cout << "Row: " << e.row
				  << ", Col: " << e.col
				  << ", Value: " << e.value << '\n';
	}

	return 0;
}
```

------------------------------------------------------------------------

# 12. Lab Exercises

### Exercise 1

Modify the program to insert:

    set_value(5,20)

Observe how the sparse vector changes.

------------------------------------------------------------------------

### Exercise 2

Remove an element:

    set_value(3,0)

Explain why the element disappears from the sparse storage.

------------------------------------------------------------------------

### Exercise 3

Implement a sparse array using:

    unordered_map<int,int>

Compare lookup performance.

------------------------------------------------------------------------

# 13. Challenge Task

Create a class:

    class SparseArray

with functions:

    set(index,value)
    get(index)
    remove(index)
    print_sparse()

------------------------------------------------------------------------

# 14. FAQs

### What is a sparse array?

A sparse array stores **only non‑zero values with their indices**.

### Why are zeros not stored?

Zeros waste memory and provide no useful information.

### Why use `vector`?

Vectors provide dynamic memory management and easy insertion.

### What is `size_t`?

`size_t` is an unsigned integer type used for **array sizes and
indexing**.

### Where are sparse arrays used?

They are widely used in machine learning, graph processing, and
scientific computing.

------------------------------------------------------------------------

# 15. Summary

Key points from this lab:

-   Sparse arrays store **only meaningful values**
-   They significantly **reduce memory usage**
-   Vector‑of‑struct representation is a common sparse format
-   The same concept extends naturally to **sparse matrices**
