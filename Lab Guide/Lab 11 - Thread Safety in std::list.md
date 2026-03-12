
# Lab 11 - Thread Safety Handling in Lists (C++)

## Overview
In multi-threaded applications, multiple threads may attempt to access or modify the same data structure at the same time. 
Standard containers like `std::list` are **not inherently thread-safe for concurrent modification**. Without proper synchronization, 
this can lead to race conditions, data corruption, or program crashes.

This guide demonstrates:

1. Why lists are not thread-safe
2. A demo illustrating the problem
3. Using `std::mutex` to protect shared data
4. Using `std::lock_guard` with RAII for safer locking
5. Comparison between manual locking and RAII-based locking
6. Summary of best practices

---

# 1. Why `std::list` Is Not Thread-Safe

A C++ container like `std::list` does **not internally synchronize access**.  
If two threads attempt to modify the list simultaneously, internal pointers may be updated inconsistently.

Example of unsafe operations:

- `push_back()`
- `pop_front()`
- `insert()`
- `erase()`

These operations involve multiple steps that must execute atomically to preserve the list structure.

---

# 2. Demonstration of Thread-Unsafe Behavior

The following program launches multiple threads that insert and remove elements from the same list.

Because there is **no synchronization**, the result may vary between executions.

```cpp
#include <iostream>
#include <list>
#include <thread>
#include <vector>

using namespace std;

list<int> numbers;

void addElements(int start)
{
    for(int i = 0; i < 10000; i++)
    {
        numbers.push_back(start + i);
    }
}

void removeElements()
{
    for(int i = 0; i < 5000; i++)
    {
        if(!numbers.empty())
        {
            numbers.pop_front();
        }
    }
}

int main()
{
    vector<thread> threads;

    for(int i = 0; i < 4; i++)
        threads.push_back(thread(addElements, i * 10000));

    for(int i = 0; i < 2; i++)
        threads.push_back(thread(removeElements));

    for(auto &t : threads)
        t.join();

    cout << "Final list size: " << numbers.size() << endl;
}
```

### Expected Result

```
4 threads inserting 10000 elements = 40000
2 threads removing 5000 elements = 10000 removed

Expected final size = 30000
```

### Actual Result (Example)

```
Final list size: 29541
Final list size: 30110
Final list size: 28793
```

Sometimes the program may even crash.

This happens because the list structure is modified by multiple threads at the same time.

---

# 3. Race Condition Explanation

Consider two threads modifying the same value:

```cpp
counter++;
```

This operation internally performs multiple steps:

1. Read counter
2. Increment value
3. Write result back

Possible execution:

```
Thread A reads counter = 0
Thread B reads counter = 0
Thread A writes 1
Thread B writes 1
```

Expected result:

```
counter = 2
```

Actual result:

```
counter = 1
```

This problem is called a **race condition**.

---

# 4. Solution 1 — Using `std::mutex`

The simplest solution is to use **mutual exclusion locks**.

A mutex ensures that only **one thread executes the critical section at a time**.

## Example Using Mutex

```cpp
#include <iostream>
#include <list>
#include <thread>
#include <vector>
#include <mutex>

using namespace std;

list<int> numbers;
mutex listMutex;

void addElements(int start)
{
    for(int i = 0; i < 10000; i++)
    {
        listMutex.lock();
        numbers.push_back(start + i);
        listMutex.unlock();
    }
}

void removeElements()
{
    for(int i = 0; i < 5000; i++)
    {
        listMutex.lock();

        if(!numbers.empty())
            numbers.pop_front();

        listMutex.unlock();
    }
}

int main()
{
    vector<thread> threads;

    for(int i = 0; i < 4; i++)
        threads.push_back(thread(addElements, i * 10000));

    for(int i = 0; i < 2; i++)
        threads.push_back(thread(removeElements));

    for(auto &t : threads)
        t.join();

    cout << "Final list size: " << numbers.size() << endl;
}
```

Now the result becomes consistent because only one thread modifies the list at a time.

---

# 5. Problem With Manual Locking

Manual locking introduces a risk:

```
mutex.lock()
critical section
mutex.unlock()
```

If an exception or early return occurs before `unlock()` executes, the mutex remains locked.

This causes a **deadlock**, where other threads wait forever.

---

# 6. Solution 2 — Using `std::lock_guard` (RAII)

C++ provides a safer alternative using **RAII (Resource Acquisition Is Initialization)**.

RAII ties the lifetime of a resource to an object.

`std::lock_guard` automatically locks the mutex when created and unlocks it when the object goes out of scope.

## Example

```cpp
#include <mutex>

mutex listMutex;

void addElements(int start)
{
    for(int i = 0; i < 10000; i++)
    {
        lock_guard<mutex> lock(listMutex);
        numbers.push_back(start + i);
    }
}
```

What happens internally:

1. `lock_guard` constructor locks the mutex
2. Critical section executes
3. When the scope ends, the destructor unlocks the mutex automatically

This makes the code **exception-safe**.

---

# 7. Comparison: `std::mutex` vs `std::lock_guard`

| Feature | `std::mutex` (manual) | `std::lock_guard` |
|-------|----------------|----------------|
| Lock control | Manual `lock()` | Automatic |
| Unlock control | Manual `unlock()` | Automatic |
| Exception safety | Risky | Safe |
| Code complexity | Higher | Lower |
| Recommended | Sometimes | Preferred |

Best practice in modern C++:

```
Prefer lock_guard for most critical sections.
```

---

# 8. Critical Section

A **critical section** is the part of the program that accesses shared data.

Example:

```cpp
lock_guard<mutex> lock(listMutex);
numbers.push_back(10);
```

Only one thread can execute this block at a time.

---

# 9. Performance Considerations

Locks guarantee correctness but reduce concurrency.

If many threads compete for the same mutex:

- threads wait frequently
- throughput decreases
- lock contention occurs

Example timeline:

```
Thread 1 acquires lock
Thread 2 waits
Thread 3 waits

Thread 1 releases lock

Thread 2 acquires lock
```

---

# 10. Summary

### Key Points

- `std::list` is **not thread-safe for concurrent modification**
- Race conditions occur when multiple threads update shared data simultaneously
- `std::mutex` ensures **mutual exclusion**
- `std::lock_guard` uses **RAII** to automatically release locks
- `lock_guard` is the **preferred modern C++ approach**

### Recommended Strategy

```
Shared container → protect with mutex
Use lock_guard → automatic and exception-safe
```

---

# Final Insight

Thread safety is critical in concurrent systems. While locks ensure correctness, they may reduce scalability. 
In high-performance systems, developers sometimes move to **lock-free data structures** or specialized concurrent containers.
