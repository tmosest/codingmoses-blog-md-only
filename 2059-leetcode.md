---
title: LeetCode 2059. Minimum Operations to Convert Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2059 – Minimum Operations to Convert Number  
*BFS, DP, Java | Python | C++ – Your “Good, the Bad, and the Ugly” Guide*  

---

### 📖 Problem Summary  
Given a distinct integer array `nums`, a starting value `start` (0 ≤ start ≤ 1000) and a target `goal`, we can apply the following operations repeatedly while `x` stays inside the range `[0, 1000]`:

| operation | description |
|-----------|-------------|
| `x + nums[i]` | Add a value from the array |
| `x - nums[i]` | Subtract a value from the array |
| `x ^ nums[i]` | Bitwise XOR with a value from the array |

If an operation would take `x` outside the range, we are allowed to perform that operation **once** – after that we cannot continue.  
Return the minimum number of operations needed to reach `goal` from `start`, or `-1` if impossible.

> **Constraints**  
> - `1 ≤ nums.length ≤ 1000`  
> - `-10⁹ ≤ nums[i], goal ≤ 10⁹`  
> - `0 ≤ start ≤ 1000`  
> - `start ≠ goal`  

---

## 🔎 Key Observations

1. **Only values 0 – 1000 can be expanded**.  
   Once we step out of this window, the path ends – we just need to check if that step equals `goal`.

2. **The state space is tiny (1001 states)**.  
   This allows an exhaustive search like BFS or a bottom‑up DP without memory worries.

3. **Operations are reversible**.  
   The graph is undirected: from `x` we can reach `y`, and from `y` we can come back to `x` with the same element.  
   Therefore we can run the BFS **from the goal** or **from the start** – both are optimal.

---

## 🧠 Algorithm – Breadth‑First Search (BFS)

1. **Initialize**  
   - `queue` with the start value (or goal, see note below).  
   - `visited[0…1000]` to keep track of already explored states.  
   - `steps = 0`.

2. **Loop**  
   While the queue isn’t empty:  
   - Process all nodes of the current level (`size = queue.size()`).
   - For each `x` in this level, try all three operations with every `nums[i]`.
   - If the resulting `y` equals `goal`, return `steps + 1`.
   - If `0 ≤ y ≤ 1000` **and** not visited, mark visited and enqueue.

3. **Increment** `steps` after finishing a level.

4. If the queue empties without finding `goal`, return `-1`.

> **Why BFS works** – BFS explores nodes by increasing distance from the start, guaranteeing the first time we hit `goal` we used the fewest operations.

---

## 📦 Code (Java, Python, C++)

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public int minimumOperations(int[] nums, int start, int goal) {
        boolean[] visited = new boolean[1001];
        Queue<Integer> q = new ArrayDeque<>();
        q.offer(start);
        int steps = 0;

        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int x = q.poll();
                for (int n : nums) {
                    int[] next = {x + n, x - n, x ^ n};
                    for (int y : next) {
                        if (y == goal) return steps + 1;
                        if (y >= 0 && y <= 1000 && !visited[y]) {
                            visited[y] = true;
                            q.offer(y);
                        }
                    }
                }
            }
            steps++;
        }
        return -1;
    }
}
```

---

### 2️⃣ Python

```python
from collections import deque
from typing import List

class Solution:
    def minimumOperations(self, nums: List[int], start: int, goal: int) -> int:
        visited = [False] * 1001
        q = deque([start])
        steps = 0

        while q:
            for _ in range(len(q)):
                x = q.popleft()
                for n in nums:
                    for y in (x + n, x - n, x ^ n):
                        if y == goal:
                            return steps + 1
                        if 0 <= y <= 1000 and not visited[y]:
                            visited[y] = True
                            q.append(y)
            steps += 1
        return -1
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumOperations(vector<int>& nums, int start, int goal) {
        vector<bool> visited(1001, false);
        queue<int> q;
        q.push(start);
        int steps = 0;

        while (!q.empty()) {
            int sz = q.size();
            for (int i = 0; i < sz; ++i) {
                int x = q.front(); q.pop();
                for (int n : nums) {
                    int ops[3] = {x + n, x - n, x ^ n};
                    for (int y : ops) {
                        if (y == goal) return steps + 1;
                        if (0 <= y && y <= 1000 && !visited[y]) {
                            visited[y] = true;
                            q.push(y);
                        }
                    }
                }
            }
            ++steps;
        }
        return -1;
    }
};
```

---

## 🔍 Complexity Analysis

| **Metric** | **Value** |
|------------|-----------|
| Time | `O(1001 * |nums|)` (worst‑case ~ 1 million operations) |
| Memory | `O(1001)` for the visited array + queue |

The tiny state space makes the algorithm essentially constant‑time for interview constraints.

---

## ✅ Good, the Bad, and the Ugly

| Aspect | Good | The Bad | The Ugly |
|--------|------|---------|----------|
| **Algorithm choice** | BFS guarantees optimality, very clear for candidates | None – BFS is always fine here | None – the problem is designed for BFS, no hidden pitfalls |
| **State space** | Only 1001 states → easy to reason | The `goal` may lie outside `[0,1000]` – remember to check it before discarding | Enqueuing too many nodes if `visited` is omitted → exponential blow‑up |
| **Implementation** | Boolean array of size 1001 instead of a `HashSet` → O(1) lookup | If you forget to check `y == goal` before the `[0,1000]` guard, you’ll miss solutions that jump out of range | Using recursion or DFS without level tracking can lead to stack overflow or wrong answer |

---

## 🎯 Edge Cases to Cover in Your Tests

1. **Goal inside the range** – normal BFS path.  
2. **Goal outside the range** – last operation must be the one that lands exactly on `goal`.  
3. **All operations keep you in `[0,1000]`** – path never exits.  
4. **Negative `nums[i]` values** – subtraction may become positive, XOR is unaffected.  
5. **Large `nums[i]` values** (close to `10⁹`) – ensure int doesn’t overflow (in Java/C++ the max sum stays < 2³¹).  

---

## 🔁 Bottom‑Up DP Variant (Optional)

If you prefer a *dynamic programming* flavor, run the BFS **from the goal** and build a table of distances. The code below is a more compact version of the Java solution; it works identically.

```java
boolean[] dist = new boolean[1001];
int[] queue = new int[1001];
int head = 0, tail = 0;
queue[tail++] = goal;
dist[goal] = true;
...
```

---

## 📋 How to Use These Solutions in Your Interview

1. **Explain the graph model** – “states are numbers, edges are the three operations.”  
2. **Justify BFS** – it guarantees minimal steps.  
3. **Show the code** – highlight the level‑by‑level loop and the out‑of‑range check.  
4. **Discuss complexity** – it runs in constant time for the 0–1000 window.  

---

## 🎯 Summary

- The problem is a **short‑path search on a 1001‑node graph**.  
- A simple **BFS** from `start` (or `goal`) finds the minimum operations instantly.  
- The code works cleanly in Java, Python, and C++, making it a great interview staple.

Ready to nail this LeetCode challenge and impress your hiring managers? 🚀

---

## 📣 Call to Action

- **Try the BFS solution yourself** – tweak `nums` to see how the path changes.  
- **Post your own version** on GitHub or your favorite blog platform.  
- **Share this article** with a friend who’s prepping for algorithm interviews.  

> **Keywords**: LeetCode 2059, Minimum Operations to Convert Number, BFS, interview algorithm, Java solution, Python solution, C++ solution, job interview, coding interview, data structure, job interview prep.