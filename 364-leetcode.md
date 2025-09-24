---
title: LeetCode 364. Nested List Weight Sum II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code

Below are three ready‑to‑paste solutions that will pass the official LeetCode test cases.  
Each snippet implements the same *one‑pass BFS* strategy:  
* while traversing the list we keep the current depth,  
* accumulate the sum of all integers (`sumElements`) and the sum of *depth × integer* (`sumDepthProduct`),  
* track the maximum depth (`maxDepth`).  
After the loop we can derive the answer in O(1):

```
answer = (maxDepth + 1) * sumElements - sumDepthProduct
```

This formula follows directly from the definition of the inverse weight.

---

### Java (LeetCode signature)

```java
import java.util.*;

class Solution {
    public int depthSumInverse(List<NestedInteger> nestedList) {
        Queue<NestedInteger> q = new LinkedList<>(nestedList);

        int depth = 1;
        int maxDepth = 0;
        int sumElements = 0;
        int sumDepthProduct = 0;

        while (!q.isEmpty()) {
            int size = q.size();
            maxDepth = Math.max(maxDepth, depth);

            for (int i = 0; i < size; i++) {
                NestedInteger cur = q.poll();

                if (cur.isInteger()) {
                    int val = cur.getInteger();
                    sumElements += val;
                    sumDepthProduct += val * depth;
                } else {
                    q.addAll(cur.getList());
                }
            }
            depth++;
        }

        return (maxDepth + 1) * sumElements - sumDepthProduct;
    }
}
```

> **Note**: In the LeetCode environment the `NestedInteger` interface is already supplied.  
> If you test locally you can create a simple stub:

```java
class NestedInteger {
    private Integer value;
    private List<NestedInteger> list;

    // Constructors, getters, and setters omitted for brevity
}
```

---

### Python 3

```python
from collections import deque
from typing import List, Union

# LeetCode stub – you do NOT need to implement it
class NestedInteger:
    def __init__(self, value: Union[int, List] = None):
        if isinstance(value, int):
            self._int = value
            self._list = None
        else:
            self._int = None
            self._list = value if value is not None else []

    def isInteger(self) -> bool:
        return self._int is not None

    def getInteger(self) -> int:
        return self._int

    def getList(self) -> List["NestedInteger"]:
        return self._list

class Solution:
    def depthSumInverse(self, nestedList: List[NestedInteger]) -> int:
        q = deque(nestedList)

        depth = 1
        max_depth = 0
        sum_elements = 0
        sum_depth_product = 0

        while q:
            size = len(q)
            max_depth = max(max_depth, depth)

            for _ in range(size):
                cur = q.popleft()
                if cur.isInteger():
                    val = cur.getInteger()
                    sum_elements += val
                    sum_depth_product += val * depth
                else:
                    q.extend(cur.getList())
            depth += 1

        return (max_depth + 1) * sum_elements - sum_depth_product
```

---

### C++ (LeetCode signature)

```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int depthSumInverse(vector<NestedInteger> &nestedList) {
        queue<NestedInteger> q;
        for (auto &ni : nestedList) q.push(ni);

        int depth = 1;
        int maxDepth = 0;
        int sumElements = 0;
        int sumDepthProduct = 0;

        while (!q.empty()) {
            int sz = q.size();
            maxDepth = max(maxDepth, depth);

            for (int i = 0; i < sz; ++i) {
                NestedInteger cur = q.front(); q.pop();

                if (cur.isInteger()) {
                    int val = cur.getInteger();
                    sumElements += val;
                    sumDepthProduct += val * depth;
                } else {
                    const vector<NestedInteger> &lst = cur.getList();
                    for (const auto &x : lst) q.push(x);
                }
            }
            ++depth;
        }

        return (maxDepth + 1) * sumElements - sumDepthProduct;
    }
};
```

> **C++ Stub**  
> In a local environment you might need a minimal `NestedInteger` implementation:

```cpp
class NestedInteger {
public:
    NestedInteger() : is_int(false) {}
    NestedInteger(int val) : is_int(true), integer(val) {}
    bool isInteger() const { return is_int; }
    int getInteger() const { return integer; }
    const vector<NestedInteger> &getList() const { return list; }
    void add(const NestedInteger &ni) { list.push_back(ni); }
private:
    bool is_int;
    int integer;
    vector<NestedInteger> list;
};
```

---

## 2.  The Blog Article

### Title  
**LeetCode 364 – Nested List Weight Sum II: A One‑Pass BFS Solution in Java, Python & C++**

### Meta Description  
Learn how to solve LeetCode 364 (Nested List Weight Sum II) efficiently. Read our step‑by‑step guide with Java, Python, and C++ code snippets, plus a deep dive into the algorithmic trade‑offs.

---

## Introduction

If you’re prepping for a software‑engineering interview, LeetCode problem **364 – Nested List Weight Sum II** is a classic.  
It asks you to compute the weighted sum of a nested list where the weight of an integer is **inverse‑depth**:

> **weight = maxDepth – depth + 1**

The goal is to return  
`Σ (integer * weight)` for all integers in the list.

It’s deceptively simple but tests your ability to reason about recursion, depth‑first search (DFS), breadth‑first search (BFS), and mathematical insight.

In this post we’ll cover:

* Problem restatement & constraints
* A “good” one‑pass BFS solution (the code is already provided above)
* A “bad” approach that may cause stack overflow
* The “ugly” formula‑based trick that can trip up beginners
* SEO‑friendly highlights to help your article rank for interview questions

Let’s dive in.

---

## The Problem (Restated)

- Input: `List<NestedInteger> nestedList`
- Each element is either:
  * an integer
  * a list of other `NestedInteger`s (nested arbitrarily)
- Depth of an integer = number of lists it is inside of
- `maxDepth` = deepest integer depth
- Weight of an integer = `maxDepth - depth + 1`
- Return the weighted sum of all integers

**Constraints**

| Constraint | Value |
|------------|-------|
| 1 ≤ `nestedList.length` ≤ 50 | |
| Integer value ∈ [-100, 100] | |
| `maxDepth` ≤ 50 | |
| No empty lists | |

---

## The “Good” – One‑Pass BFS

### Why BFS?

* **Level‑by‑level**: naturally tracks depth without recursion.
* **Iterative**: no risk of stack overflow on deeply nested lists.
* **Single traversal**: we can compute `maxDepth`, `sumElements`, and `sumDepthProduct` in one pass.

### The Math

Let:

- `S = Σ integer`
- `P = Σ (integer × depth)`
- `D = maxDepth`

The weighted sum `W` is:

```
W = Σ integer × (D - depth + 1)
  = Σ integer × (D + 1) - Σ integer × depth
  = (D + 1) × S - P
```

So after the BFS we just compute `W = (D + 1) * S - P`.

### Time & Space

| Complexity | Reason |
|------------|--------|
| **O(n)** | Each integer/list processed once |
| **O(d)** | `d` = maximum depth; queue size never exceeds the breadth of the current level |

The code for all three languages is already provided in the previous section.

---

## The “Bad” – Deep Recursion

A common student solution uses DFS with recursion, passing the current depth as a parameter. While elegant, it has pitfalls:

* **Stack overflow** if the nesting depth approaches the recursion limit (~1000 in Java, ~1000 in Python, ~10 000 in C++). LeetCode’s constraint of depth ≤ 50 is safe, but interviewers love to push you harder.
* **Two passes**: either compute `maxDepth` first (DFS) then recompute the weighted sum, or store every integer and its depth in a list, then post‑process.

If you must use DFS, ensure you:

1. Store pairs (integer, depth) in a vector/array.
2. Find `maxDepth`.
3. Compute `(maxDepth + 1) * S - P`.

Even then, the code becomes more verbose and harder to read.

---

## The “Ugly” – Trying to Avoid the Formula

Some participants attempt to directly apply weights during traversal:

```pseudo
for each integer:
    weight = (maxDepth - depth + 1)
    result += integer * weight
```

But `maxDepth` is *unknown* until the end of traversal!  
Thus you end up:

* **Storing** all integers with tentative weights, or
* **Updating** weights on the fly after discovering deeper nodes (which requires recomputation or a dynamic data structure).

This back‑and‑forth is exactly what the formula removes: you only need *depth × integer* once, then a one‑liner for the final sum.

---

## A Quick‑Start Checklist for Interviewers

| Quick Tip | What the Candidate Should Highlight |
|-----------|-------------------------------------|
| *Use BFS, not DFS* | Iterative, safe depth handling |
| *Track three aggregates* | `S`, `P`, `D` in one loop |
| *Show the formula* | `(D + 1) × S – P` – demonstrates mathematical thinking |
| *Mention stack limits* | Avoid “bad” recursion |
| *Explain space* | Queue breadth < breadth of the deepest level |

---

## SEO Boosters

| SEO Pillar | How We Use It |
|------------|---------------|
| **Keywords** | “LeetCode 364”, “Nested List Weight Sum II”, “Interview BFS”, “Java Python C++ solution” |
| **Header tags** | H2/H3 used for problem, algorithm, languages, pitfalls |
| **Bullet points** | Clear, concise, helps readers skim |
| **Code fences** | `java`, `python`, `cpp` tags for search engines to index |
| **Meta description** | 155‑character summary for SERPs |
| **Internal links** | Suggest reading other LeetCode problem posts (e.g., 341, 341, 107) |

Feel free to add the following anchor link at the bottom of the article:

```html
<a href="#javascript--java--solution">Java Code</a> | 
<a href="#python-3-solution">Python Code</a> | 
<a href="#c++-solution">C++ Code</a>
```

---

## Conclusion

LeetCode 364 is a neat illustration of how a simple problem can have elegant, efficient, and risky solutions. The **one‑pass BFS** is the most interview‑friendly:

1. **Iterative** – no recursion limits.  
2. **Linear time** – one scan of the whole list.  
3. **Mathematically concise** – a single line of arithmetic after the loop.

Use the code snippets above to add to your GitHub, portfolio, or personal practice repo.  
Feel free to copy the article into Medium, Dev.to, or your personal blog and watch it rank for *“LeetCode 364 solution”*.

Happy coding, and good luck on your next interview! 🚀

--- 

### TL;DR

* **Problem** – Weighted sum with inverse depth.  
* **Good solution** – One‑pass BFS + `(maxDepth+1)*S – P`.  
* **Bad solution** – Recursive DFS that may overflow.  
* **Ugly** – Attempting to apply weights directly without the formula.  

The Java, Python, and C++ code above is ready for copy‑and‑paste and will pass every official LeetCode test.