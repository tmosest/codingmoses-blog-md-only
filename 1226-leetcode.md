---
title: LeetCode 1226. The Dining Philosophers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# The Dining Philosophers – A LeetCode Classic (1226)  
> **Problem**: Five philosophers sit around a circular table, each with a bowl of spaghetti.  
> Between each pair of philosophers sits a fork.  
> A philosopher can only eat when he holds **both** his left and right fork.  
> Implement `wantsToEat()` so that no philosopher starves, even when the method is called repeatedly and from multiple threads.

> **Why it matters**  
> The Dining Philosophers problem is the canonical example of a dead‑lock‑prone concurrent algorithm.  
> Interviewers love it because it tests your understanding of **mutexes**, **semaphores**, **resource ordering**, and **fairness** in a real‑world setting.

> **Languages**: Java, Python, C++  

---

## 1.  The Classic Pitfall – “All Forks First”

A naïve solution looks like this:

```text
lock left fork
lock right fork
eat
unlock right fork
unlock left fork
```

When every philosopher grabs the left fork first, a circular wait arises:

```
P0 -> left0
P1 -> left1
P2 -> left2
P3 -> left3
P4 -> left4
```

All philosophers then block on the right fork, never progressing → **deadlock**.

---

## 2.  The “Good” – Acquire the Lower‑ID Fork First

The simplest way to avoid deadlock is to impose a global order on fork acquisition.

* **Rule**: Always lock the **smaller index** first, then the larger.
* For philosopher `i`  
  * left fork index = `i`  
  * right fork index = `(i+1) % 5`

If a philosopher’s left index < right index, pick left first; otherwise pick right first.

**Why it works**  
The order breaks the circular wait: each fork can only be requested in a unique direction.  

**Fairness** – It is still possible for one philosopher to starve if the scheduler is biased, but LeetCode’s tests rarely exhibit that extreme behavior.  

**Complexity** – O(1) per call, no busy‑waiting, no special lock types.

---

## 3.  The “Bad” – Busy‑Wait and Unnecessary Spinning

A common mistake is to keep trying to acquire a fork in a tight loop (`tryLock()` repeatedly).  
This wastes CPU cycles, degrades throughput, and can still lead to starvation.

---

## 4.  The “Ugly” – Using `synchronized` with No Fairness

```
synchronized (fork[i]) {
   synchronized (fork[(i+1)%5]) { … }
}
```

`java.lang.Object` locks are *non‑fair*.  If you have a lot of concurrent attempts, some threads can be starved for long periods.  

---

## 5.  The Final Solution – Code for Java, Python, C++

Below is a clean, production‑ready implementation for each language, following the **lower‑ID‑first** rule.

### 5.1 Java

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class DiningPhilosophers {

    // 5 fork locks
    private final Lock[] forks = new ReentrantLock[5];

    public DiningPhilosophers() {
        for (int i = 0; i < 5; i++) {
            forks[i] = new ReentrantLock();          // non‑fair lock is fine here
        }
    }

    // run() will execute the supplied Runnable
    public void wantsToEat(int philosopher,
                           Runnable pickLeftFork,
                           Runnable pickRightFork,
                           Runnable eat,
                           Runnable putLeftFork,
                           Runnable putRightFork) throws InterruptedException {

        int left  = philosopher;                // index of left fork
        int right = (philosopher + 1) % 5;      // index of right fork

        // pick the lower index first to avoid circular wait
        int first  = Math.min(left, right);
        int second = Math.max(left, right);

        // acquire locks
        forks[first].lock();          // will block until lock is available
        try {
            forks[second].lock();
            try {
                // pick up forks
                if (first == left) pickLeftFork.run(); else pickRightFork.run();
                if (second == right) pickRightFork.run(); else pickLeftFork.run();

                // eat
                eat.run();

                // put down forks
                if (first == left) putLeftFork.run(); else putRightFork.run();
                if (second == right) putRightFork.run(); else putLeftFork.run();
            } finally {
                forks[second].unlock();      // release second fork
            }
        } finally {
            forks[first].unlock();           // release first fork
        }
    }
}
```

**Test harness (minimal)**

```java
public static void main(String[] args) throws InterruptedException {
    DiningPhilosophers dp = new DiningPhilosophers();
    Runnable pickL = () -> System.out.println(Thread.currentThread().getName()+" picks left");
    Runnable pickR = () -> System.out.println(Thread.currentThread().getName()+" picks right");
    Runnable eat   = () -> System.out.println(Thread.currentThread().getName()+" eats");
    Runnable putL  = () -> System.out.println(Thread.currentThread().getName()+" puts left");
    Runnable putR  = () -> System.out.println(Thread.currentThread().getName()+" puts right");

    int n = 3;   // how many times each philosopher will try to eat

    for (int i = 0; i < 5; i++) {
        int p = i;
        new Thread(() -> {
            try { for (int j = 0; j < n; j++) dp.wantsToEat(p, pickL, pickR, eat, putL, putR); }
            catch (InterruptedException e) { e.printStackTrace(); }
        }, "Philosopher-"+i).start();
    }
}
```

---

### 5.2 Python

```python
import threading
from typing import Callable

class DiningPhilosophers:
    def __init__(self):
        self.forks = [threading.Lock() for _ in range(5)]

    def wantsToEat(self,
                   philosopher: int,
                   pickLeftFork: Callable[[], None],
                   pickRightFork: Callable[[], None],
                   eat: Callable[[], None],
                   putLeftFork: Callable[[], None],
                   putRightFork: Callable[[], None]) -> None:

        left  = philosopher
        right = (philosopher + 1) % 5

        # order locks by id
        first  = min(left, right)
        second = max(left, right)

        with self.forks[first]:
            with self.forks[second]:
                # pick forks
                if first == left:
                    pickLeftFork()
                else:
                    pickRightFork()

                if second == right:
                    pickRightFork()
                else:
                    pickLeftFork()

                # eat
                eat()

                # put down forks
                if first == left:
                    putLeftFork()
                else:
                    putRightFork()

                if second == right:
                    putRightFork()
                else:
                    putLeftFork()
```

**Simple test (use 5 threads)**

```python
import time

def main():
    dp = DiningPhilosophers()

    def pickL(): print(f"{threading.current_thread().name} picks left")
    def pickR(): print(f"{threading.current_thread().name} picks right")
    def eat():   print(f"{threading.current_thread().name} eats")
    def putL():  print(f"{threading.current_thread().name} puts left")
    def putR():  print(f"{threading.current_thread().name} puts right")

    def philosopher(id):
        for _ in range(3):
            dp.wantsToEat(id, pickL, pickR, eat, putL, putR)
            time.sleep(0.01)      # give other threads a chance

    threads = [threading.Thread(target=philosopher, args=(i,), name=f"P{i}") for i in range(5)]
    for t in threads: t.start()
    for t in threads: t.join()

if __name__ == "__main__":
    main()
```

> **Note**: Python’s GIL still protects the locks, but the algorithm is the same as in Java.

---

### 5.3 C++

```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <mutex>

class DiningPhilosophers {
public:
    DiningPhilosophers() {
        for (int i = 0; i < 5; ++i) {
            forks[i] = std::make_unique<std::mutex>();
        }
    }

    void wantsToEat(int philosopher,
                    const std::function<void()>& pickLeftFork,
                    const std::function<void()>& pickRightFork,
                    const std::function<void()>& eat,
                    const std::function<void()>& putLeftFork,
                    const std::function<void()>& putRightFork) {

        int left  = philosopher;
        int right = (philosopher + 1) % 5;

        int first  = std::min(left, right);
        int second = std::max(left, right);

        // lock in global order
        std::unique_lock<std::mutex> lk1(*forks[first]);
        std::unique_lock<std::mutex> lk2(*forks[second]);

        // pick up
        if (first == left)  pickLeftFork();
        else                pickRightFork();
        if (second == right)pickRightFork();
        else                pickLeftFork();

        // eat
        eat();

        // put down
        if (first == left)  putLeftFork();
        else                putRightFork();
        if (second == right)putRightFork();
        else                putLeftFork();
    }

private:
    std::vector<std::unique_ptr<std::mutex>> forks{5};
};
```

**Quick test**

```cpp
int main() {
    DiningPhilosophers dp;
    auto pickL = [](){ std::cout << std::this_thread::get_id() << " picks left\n"; };
    auto pickR = [](){ std::cout << std::this_thread::get_id() << " picks right\n"; };
    auto eat   = [](){ std::cout << std::this_thread::get_id() << " eats\n"; };
    auto putL  = [](){ std::cout << std::this_thread::get_id() << " puts left\n"; };
    auto putR  = [](){ std::cout << std::this_thread::get_id() << " puts right\n"; };

    std::vector<std::thread> ths;
    for (int i = 0; i < 5; ++i) {
        ths.emplace_back([&dp,i,pickL,pickR,eat,putL,putR]{
            for (int j=0;j<3;++j)
                dp.wantsToEat(i,pickL,pickR,eat,putL,putR);
        });
    }
    for (auto& t: ths) t.join();
}
```

---

## 6.  How the Solution Meets LeetCode’s Requirements

* The **API** is exactly the one LeetCode supplies – no main function needed for the judge.  
* Each method acquires **exactly two** locks and releases them immediately after eating.  
* No busy‑waiting, no thread starvation (for the purpose of the tests).  
* Works under a highly concurrent workload – every call blocks only on the two necessary forks.

---

## 7.  What Recruiters Want to See

1. **Clear dead‑lock reasoning** – “All philosophers grabbing left first → circular wait”  
2. **Resource ordering** – “Acquire the lower‑ID fork first.”  
3. **Thread‑safe code** – Use `Lock`/`Mutex`/`Semaphore` correctly.  
4. **Test‑driven mindset** – Show a tiny test harness or unit tests.  
5. **Explanation** – Why the algorithm works, not just code.

---

## 8.  SEO‑Friendly Summary (for the job‑searching developer)

- **Title**: *Mastering LeetCode 1226 – The Dining Philosophers Problem in Java, Python & C++*  
- **Meta description**: “Learn how to solve LeetCode’s Dining Philosophers problem (1226) with clean, dead‑lock‑free code in Java, Python, and C++. Boost your interview prep and land your next job.”  
- **Target keywords**:  
  - `Dining Philosophers`  
  - `LeetCode 1226`  
  - `concurrency interview question`  
  - `deadlock avoidance`  
  - `Java threading`  
  - `Python multiprocessing`  
  - `C++ mutex`  
  - `job interview concurrency`  

---

## 9.  Final Takeaway

| Aspect      | Good                                    | Bad                            | Ugly                                      |
|-------------|-----------------------------------------|--------------------------------|-------------------------------------------|
| **Algorithm** | Resource ordering (lower‑ID first)      | Busy‑wait `tryLock` looping    | Non‑fair `synchronized` locks             |
| **CPU usage** | O(1) blocking locks                   | High CPU spin                  | Unnecessary context switching             |
| **Fairness**  | Mostly fair (but can still starve)     | Potential starvation           | Real‑world starvation due to scheduler bias |
| **Readability** | Clear comments, single lock per fork | Hard to read loops             | Nested `synchronized` blocks              |

---

### Want to nail your next interview?

- **Explain the dead‑lock**: Show the circular wait diagram.  
- **Show the fix**: Resource ordering or a single semaphore for all forks.  
- **Mention fairness**: “Using a `fair` semaphore if you want strict round‑robin behaviour.”  
- **Talk about scalability**: The algorithm runs in constant time, independent of the number of philosophers.

Drop this article into your personal blog or LinkedIn, and watch recruiters notice that you can **think critically about concurrency**, **write clean code** in multiple languages, and **solve classic interview puzzles**. Good luck!