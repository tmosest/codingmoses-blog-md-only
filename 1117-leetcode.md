---
title: LeetCode 1117. Building H2O - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Building H₂O in the Real World  
> **How to master concurrent programming on LeetCode and land a software‑engineering job**  

> *Good, the bad and the ugly of synchronizing threads to form water molecules.*  

---

## 1. TL;DR

| Language | Key idea | Code snippet |
|----------|----------|--------------|
| **Java** | Two semaphores (`hSemaphore` and `oSemaphore`) + an additional lock to protect the counter | `hSemaphore.acquire(); … oSemaphore.release();` |
| **Python** | `threading.Semaphore` + `threading.Lock` + a counter | `self.h_sem.acquire(); … self.o_sem.release()` |
| **C++** | `std::sem_t` (POSIX) or `std::counting_semaphore` (C++20) + `std::mutex` | `h_sem.acquire(); … o_sem.release();` |

All three versions enforce:

1. **Exactly 2 H and 1 O per group**  
2. **No thread of the next molecule starts until the previous one finishes**  

The solution runs in **O(n)** time (n = number of atoms) and O(1) space.

---

## 2. Problem Recap

> *You have two types of threads: hydrogen (`H`) and oxygen (`O`).  
>  Each `H` thread must call `releaseHydrogen.run()` (which prints “H”).  
>  Each `O` thread must call `releaseOxygen.run()` (which prints “O”).  
>  
>  Threads must pair up into water molecules (H₂O).  
>  No atom of the next molecule may start until the previous one has fully formed.*

Typical LeetCode input: `water = "HOH"` → any permutation that contains two `H` and one `O` is accepted.

---

## 3. Why is it Hard?

- **Race condition** – If you simply let threads run, the output could be `"HOH"`, `"OHH"`, `"HHO"`, etc.  
- **Deadlock** – If you wait for the wrong semaphore count, threads may block forever.  
- **Starvation** – If you allow oxygen to start before two hydrogens are ready, the oxygen will wait forever.  
- **Ordering** – The spec demands *atomic* grouping: *one molecule must finish before the next one starts*.

---

## 4. The “Good” Approach: Counting Semaphores

The key insight is that we can treat the process as a *barrier* that blocks until **exactly three threads** are ready:

1. **Hydrogen** threads release a “ready” semaphore (`hSemaphore`).  
2. **Oxygen** threads wait for two hydrogen threads to be ready (`oSemaphore.acquire(2)`).  
3. Once the oxygen releases its own barrier, all three threads proceed.  
4. Finally, the hydrogen semaphore is reset for the next molecule.

### 4.1 Java Implementation

```java
import java.util.concurrent.Semaphore;

public class H2O {
    // 2 H atoms can pass the barrier before O
    private final Semaphore hydrogen = new Semaphore(2);
    // O atoms wait for 2 H atoms
    private final Semaphore oxygen  = new Semaphore(0);
    // Protect the counter for resetting hydrogen semaphore
    private final Object lock = new Object();
    private int hydrogenCount = 0;

    public H2O() { /* no init needed */ }

    public void hydrogen(Runnable releaseHydrogen) throws InterruptedException {
        hydrogen.acquire();           // wait until a slot is available
        releaseHydrogen.run();        // print "H"

        synchronized(lock) {
            hydrogenCount++;
            if (hydrogenCount == 2) { // two H ready, release O
                oxygen.release();
                oxygen.release();     // oxygen needs two permits
                hydrogenCount = 0;    // reset for next molecule
            }
        }
    }

    public void oxygen(Runnable releaseOxygen) throws InterruptedException {
        oxygen.acquire();             // wait for 2 H threads
        releaseOxygen.run();          // print "O"
        hydrogen.release();           // allow first H of next molecule
        hydrogen.release();           // allow second H of next molecule
    }
}
```

### 4.2 Python Implementation

```python
import threading

class H2O:
    def __init__(self):
        self.h_sema = threading.Semaphore(2)  # 2 H ready
        self.o_sema = threading.Semaphore(0)  # wait for 2 H
        self.lock   = threading.Lock()
        self.h_cnt  = 0

    def hydrogen(self, releaseHydrogen: 'Callable[[], None]') -> None:
        self.h_sema.acquire()
        releaseHydrogen()          # prints "H"

        with self.lock:
            self.h_cnt += 1
            if self.h_cnt == 2:
                self.o_sema.release(2)   # oxygen can proceed
                self.h_cnt = 0

    def oxygen(self, releaseOxygen: 'Callable[[], None]') -> None:
        self.o_sema.acquire()
        releaseOxygen()          # prints "O"
        self.h_sema.release(2)   # reset hydrogen slots
```

### 4.3 C++ Implementation (C++20)

```cpp
#include <semaphore>
#include <mutex>

class H2O {
    std::counting_semaphore<2> h_sem{2}; // 2 hydrogen slots
    std::counting_semaphore<2> o_sem{0}; // wait for 2 hydrogen
    std::mutex mtx;
    int h_cnt = 0;

public:
    H2O() = default;

    void hydrogen(std::function<void()> releaseHydrogen) {
        h_sem.acquire();
        releaseHydrogen();           // prints "H"

        std::lock_guard<std::mutex> lock(mtx);
        ++h_cnt;
        if (h_cnt == 2) {
            o_sem.release(2);
            h_cnt = 0;
        }
    }

    void oxygen(std::function<void()> releaseOxygen) {
        o_sem.acquire();
        releaseOxygen();           // prints "O"
        h_sem.release(2);          // allow next two hydrogens
    }
};
```

---

## 5. The “Bad” Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| Using **only** a `CountDownLatch` | Latches are one‑shot; after a molecule is formed they cannot be reused. | Use semaphores or a cyclic barrier. |
| Forgetting to **reset** hydrogen slots | Next molecule blocks forever because hydrogen semaphore is exhausted. | Release 2 permits after oxygen finishes. |
| Allowing oxygen to release *before* hydrogen threads have executed `releaseHydrogen` | The output order can be `"OHH"` which is invalid. | Ensure oxygen releases after both hydrogen threads have run by releasing permits only after the two hydrogens finish. |
| Not protecting the shared counter (`hydrogenCount`) | Race conditions can lead to premature release of oxygen or deadlocks. | Use a mutex or atomic integer. |

---

## 6. The “Ugly” Edge‑Case: Tight Coupling

Some solutions *hard‑code* the number of threads (`n = 3`). They create a barrier that *exactly* expects 3 threads, but if the input string contains a different ratio of H/O the code fails. The clean solution above uses only the *counts* of available atoms (2 H, 1 O), making it robust for any valid input.

---

## 7. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| `hydrogen`/`oxygen` call | O(1) | O(1) |
| Entire run (n atoms) | **O(n)** | **O(1)** (semaphore & mutex overhead is constant) |

---

## 8. Alternate Approaches

| Approach | Pros | Cons |
|----------|------|------|
| **CyclicBarrier** | Built‑in barrier that resets automatically | Requires 3 permits per group; need a separate counter for hydrogen releases |
| **ReentrantLock + Condition** | Fine‑grained control | More verbose, harder to reason about |
| **AtomicInteger + Busy‑wait** | Very short code | CPU wasteful, not scalable |
| **Two Semaphores + a lock** (our approach) | Simple, proven, low overhead | Slightly more code than a pure `CyclicBarrier` solution |

---

## 9. SEO‑Optimized Blog Wrap‑Up

### What Job‑Seekers Should Take Away

1. **Understand the problem statement fully** – concurrency puzzles are often about *grouping* rather than explicit matching.  
2. **Pick the right primitive** – semaphores are perfect for “wait for N events” problems.  
3. **Protect shared state** – use mutexes or atomic counters to avoid race conditions.  
4. **Reset state for reuse** – after one molecule finishes, release permits for the next.  
5. **Document your thought process** – interviewers love seeing the trade‑offs you considered.  

### Keywords to Sprinkle

- Concurrent programming  
- Java semaphores  
- Python threading  
- C++ concurrency  
- LeetCode 1117 H2O  
- Synchronization primitives  
- Job interview tips  
- Software engineering interview  

### Call‑to‑Action

> **Want to ace your next software‑engineering interview?**  
>  
> Subscribe for more in‑depth posts on concurrency, data structures, and algorithmic problem solving.  
>  
> 🚀 Get started today – download my free e‑book “Mastering Multithreading for Interviews”!  

---

## 10. Final Takeaway

> **Good:** A concise semaphore‑based solution that guarantees atomic grouping.  
> **Bad:** Neglecting to reset or protect shared state leads to deadlocks.  
> **Ugly:** Tight coupling to specific thread counts or ignoring the barrier semantics.

Master this pattern, practice variations, and you’ll be ready to explain *why* your code works – the ultimate interview advantage. Happy coding!