---
title: LeetCode 1823. Find the Winner of the Circular Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1823 – “Find the Winner of the Circular Game”

> **Difficulty**: Medium  
> **Tag**: Mathematics, Dynamic Programming, Recursion, Simulation  

This post is a *full‑stack* walk‑through:  
- A concise explanation of the problem  
- Three different approaches (simulation, recursion, iterative DP)  
- Java, Python, and C++ implementations for each approach  
- A SEO‑friendly blog article that will help you land that interview call

> **Bonus** – Linear‑time, constant‑space solution (the “follow‑up” you asked for).

---

## 🔍 Problem Recap

You have `n` friends sitting in a circle, numbered `1 … n`.  
Starting at friend `1` you repeatedly:

1. Count `k` people clockwise (including the person you’re standing on).  
2. The person you land on leaves the circle.  
3. If more than one person remains, start counting again **from the person immediately clockwise** of the one that just left.

When only one person is left, that person is the winner.  
Return the 1‑based index of the winner.

> **Constraints** – `1 ≤ k ≤ n ≤ 500` (but the solution works for any `n` up to 10⁷ in O(n) time).

---

## 📚 Approaches

| # | Idea | Time | Space |
|---|------|------|-------|
| **1** | *Simulation* – literally model the circle. | O(n·k) (worst‑case) | O(n) |
| **2** | *Recursion* – bottom‑up Josephus formula. | O(n) | O(n) (call stack) |
| **3** | *Iterative DP* – same formula, no recursion. | O(n) | O(1) |
| **Follow‑up** | Linear‑time, constant‑space (iterative DP). | O(n) | O(1) |

All solutions produce the same result. The difference lies in **readability** and **performance**.

---

## 📦 Code in Three Languages

Below you’ll find *three* variants for each language.  
Choose the one that fits the interview or your coding style.

> **Tip** – When you submit on LeetCode, use the *iterative DP* version; it runs in microseconds for the maximum constraints.

### 1️⃣ Simulation (easy to understand, but slower)

#### Java

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class Solution {
    public int findTheWinner(int n, int k) {
        Queue<Integer> circle = new ArrayDeque<>();
        for (int i = 1; i <= n; i++) circle.offer(i);

        int pos = 0;                     // position we’re currently standing on
        while (circle.size() > 1) {
            // move k‑1 steps forward because the current position is already counted
            for (int i = 0; i < k - 1; i++) {
                circle.offer(circle.poll()); // rotate front to back
            }
            circle.poll();                 // the k‑th person leaves
            // no extra rotation needed: the next poll starts at the next person
        }
        return circle.poll();              // only one element remains
    }
}
```

#### Python

```python
from collections import deque
from typing import Deque

class Solution:
    def findTheWinner(self, n: int, k: int) -> int:
        circle: Deque[int] = deque(range(1, n + 1))
        while len(circle) > 1:
            circle.rotate(-(k - 1))      # bring the k‑th person to the front
            circle.popleft()             # remove him/her
        return circle[0]
```

#### C++

```cpp
#include <queue>
using namespace std;

class Solution {
public:
    int findTheWinner(int n, int k) {
        queue<int> circle;
        for (int i = 1; i <= n; ++i) circle.push(i);

        while (circle.size() > 1) {
            // rotate the queue k‑1 times
            for (int i = 0; i < k - 1; ++i) {
                circle.push(circle.front());
                circle.pop();
            }
            circle.pop(); // the k‑th person leaves
        }
        return circle.front(); // only one remains
    }
};
```

> **Why the simulation works** –  
> `rotate(-(k-1))` (or the loop above) is exactly “count k people, counting the current one”.  
> After `pop()`, the queue’s front becomes the next person clockwise.

---

### 2️⃣ Recursion – The Classical Josephus Formula

The problem is a **Josephus problem**.  
Let `winner(n, k)` be the 0‑based index of the winner among `n` people.  
Base: `winner(1, k) = 0`.  
Recursive relation:

```
winner(n, k) = (winner(n-1, k) + k) % n
```

Explanation: After removing one person, the circle shrinks to `n‑1`.  
Every remaining index shifts `+k` because we “walked” `k` steps to get to the removed person.  
Taking `% n` keeps the index inside the new circle.

#### Java (Recursive)

```java
public class Solution {
    public int findTheWinner(int n, int k) {
        return rec(n, k) + 1;      // +1 to convert to 1‑based
    }

    private int rec(int n, int k) {
        if (n == 1) return 0;
        return (rec(n - 1, k) + k) % n;
    }
}
```

#### Python (Recursive)

```python
class Solution:
    def findTheWinner(self, n: int, k: int) -> int:
        def rec(m: int) -> int:
            if m == 1:
                return 0
            return (rec(m - 1) + k) % m

        return rec(n) + 1
```

#### C++ (Recursive)

```cpp
class Solution {
public:
    int findTheWinner(int n, int k) {
        return rec(n, k) + 1;
    }
private:
    int rec(int m, int k) {
        if (m == 1) return 0;
        return (rec(m - 1, k) + k) % m;
    }
};
```

> **Caution** – For `n > 10⁵` recursion depth will overflow the call stack.  
> Use the iterative version for production or large test cases.

---

### 3️⃣ Bottom‑Up Dynamic Programming – O(1) Space

The recursive relation can be turned into an iterative loop:

```
res = 0
for i = 2 … n:
    res = (res + k) % i
return res + 1
```

Here `res` is always the **0‑based** winner index for the current circle size.  
Adding `1` converts the answer to 1‑based numbering.

#### Java (Iterative DP)

```java
public class Solution {
    public int findTheWinner(int n, int k) {
        int res = 0;                     // 0‑based
        for (int i = 2; i <= n; i++) {
            res = (res + k) % i;
        }
        return res + 1;                  // 1‑based
    }
}
```

#### Python (Iterative DP)

```python
class Solution:
    def findTheWinner(self, n: int, k: int) -> int:
        res = 0
        for i in range(2, n + 1):
            res = (res + k) % i
        return res + 1
```

#### C++ (Iterative DP)

```cpp
class Solution {
public:
    int findTheWinner(int n, int k) {
        int res = 0;                     // 0‑based
        for (int i = 2; i <= n; ++i) {
            res = (res + k) % i;
        }
        return res + 1;                  // 1‑based
    }
};
```

> **Why this is linear‑time, constant‑space** –  
> We only loop `n-1` times and keep a single integer.  
> No recursion stack, no extra containers.  
> The algorithm runs in `O(n)` time, `O(1)` memory – perfect for the follow‑up question.

---

## 📈 Complexity Table

| Approach | Time   | Space  | When to Use |
|----------|--------|--------|-------------|
| Simulation (queue/rotate) | **O(n · k)** | **O(n)** | Small `n`, want to see the process |
| Recursion (Josephus) | **O(n)** | **O(n)** (call stack) | Easy to reason, small `n` |
| Iterative DP (O(1)) | **O(n)** | **O(1)** | Production‑ready, interview favourite |
| **Follow‑up** | **O(n)** | **O(1)** | Best choice for large `n` |

> For the official LeetCode limits (`n ≤ 500`) *any* approach passes quickly, but the iterative DP is the only one that scales gracefully to millions of people.

---

## 🚦 “Good, Bad, Ugly” of the Three Approaches

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simulation** | *Very intuitive* – you literally see who leaves. | *Time‑inefficient* – `O(n·k)`. | *Memory waste* – needs a queue or array of size `n`. |
| **Recursion** | *Mathematically elegant* – captures the essence of Josephus. | *Stack overflow* risk for huge `n`. | *Hard to optimize* if you want O(1) memory. |
| **Iterative DP** | *Fastest & cleanest* – one loop, one variable. | *Off‑by‑one confusion* when switching between 0‑based and 1‑based. | *Can be mis‑typed* (`%` vs `+`), leading to wrong results. |

> **Bottom line:** For interviews, always propose the *iterative DP* first. It shows you know the math and you can write efficient, bug‑free code.

---

## 🎓 Take‑Away: How to Talk About This in an Interview

1. **Name the Problem** – “Josephus” (classic interview staple).  
2. **Explain the recurrence** – `winner(n) = (winner(n‑1)+k) % n`.  
3. **Show the iterative form** – “We unroll the recursion into a single for‑loop.”  
4. **Mention Complexity** – `O(n)` time, `O(1)` space.  
5. **Edge Cases** – “When `k = 1` the answer is `n` (last person).”  

> *Tip:* Keep the math concise: “After removing one person, the next round starts `k` steps later, so we add `k` and wrap around with modulo.”

---

## 🛠️ How to Use This Solution in Real Projects

- **Backend services**: Use the iterative DP if you need to compute safe positions for a gaming lobby or a raffle.  
- **Educational tools**: Keep the simulation to demonstrate algorithm behaviour.  
- **Competitive programming**: Choose the fastest variant – iterative DP or even an even more specialized formula if `k` is fixed.

---

## 📌 Quick Checklist for LeetCode Submission

- **Class name**: `Solution`.  
- **Method signature**:

```java
public int findTheWinner(int n, int k)
```

- **Language**: Java, Python, or C++.  
- **Submit the iterative DP** (shown above).  

You’ll get “Accepted” in <1 ms – no surprises!

---

## 📢 Final Thought

You’ve just seen four ways to solve a problem that has been asked in **Google**, **Facebook**, **Amazon**, and countless *coding bootcamps*.  
Master the iterative DP and you’ll be ready for any interview that throws a Josephus‑style question your way.

Happy coding! 🚀

---



---

### 📝 How to Turn This into a Blog Post

1. **Headline** – “Master the Josephus Problem in 4 Minutes (Java, Python, C++)”.  
2. **Sub‑headings** – “Simulation”, “Recursion”, “Iterative DP”.  
3. **Code blocks** – Use syntax highlighting for each language.  
4. **Complexity graph** – Visual bar chart comparing approaches.  
5. **Interview Tips** – Bulleted points for the “talking points” section.  
6. **“Good, Bad, Ugly”** – Add a quick table.  

> Use the markdown snippet above as your skeleton and flesh it out with your own voice. Add a real‑world example or a short video to showcase the simulation if you’re a visual learner.

---



---



*Prepared by: [Your Name], Senior Software Engineer*  
*Follow me on Twitter @YourHandle for more algorithm insights!*  
---



---



*(End of article.)*