---
title: LeetCode 1409. Queries on a Permutation With Key - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 1409 – “Queries on a Permutation With Key”
> **Target audience:** Front‑end / Back‑end developers, data‑structure enthusiasts, interview prep lovers  
> **Goal:** Deliver clean, well‑commented code in **Java, Python, and C++** + a **SEO‑friendly blog article** that showcases your problem‑solving chops and helps you land your next software‑engineering role.

---

## Table of Contents
| Section | What you’ll learn |
|---------|--------------------|
| 🧩 Problem Statement | Understand the exact requirements |
| 📊 Constraints & Edge Cases | Why the solution matters |
| ⚙️ Brute‑Force Approach | Intuitive but still efficient for LeetCode |
| 🚀 Optimal (Fenwick Tree) | O(n log m) solution – perfect for interviews |
| 💻 Code Snippets | Java, Python, C++ – ready to copy & paste |
| 📝 Blog Article | “The Good, The Bad, and The Ugly” – an SEO‑optimized write‑up |
| 🎯 Take‑aways | What recruiters want to see |

---

## 1️⃣ Problem Recap

Given:

* An array `queries` – each element ∈ [1, m].
* A starting permutation `P = [1, 2, …, m]`.

For every query `q = queries[i]`:

1. Find the 0‑based index of `q` in `P`.
2. Move `q` to the front of `P`.
3. Record the index before the move.

Return the array of recorded indices.

> **Constraints**  
> • `1 ≤ m ≤ 10³`  
> • `1 ≤ queries.length ≤ m`  
> • `1 ≤ queries[i] ≤ m`

---

## 2️⃣ Why This Problem Is a “Gold‑Mine” for Interviews

| What recruiters care about | How this problem demonstrates it |
|-----------------------------|---------------------------------|
| **Data‑structure knowledge** | You use permutations, indexing, and optionally Fenwick trees. |
| **Algorithmic thinking** | You identify a naive O(n²) vs an O(n log n) solution. |
| **Coding style & comments** | Clean, readable code with edge‑case handling. |
| **Time‑space trade‑offs** | You discuss when a simple approach is acceptable. |
| **Problem‑solving under constraints** | m ≤ 1000 → brute‑force OK; but you still show a scalable method. |

---

## 3️⃣ Brute‑Force (Intuitive) Solution

### ✅ Complexity
* **Time:** O(n × m) (worst‑case 1 000 × 1 000 = 10⁶ operations – perfectly fine for LeetCode).  
* **Space:** O(1) extra (besides output).

### 📌 Java Implementation

```java
public class Solution {
    public int[] processQueries(int[] queries, int m) {
        int[] perm = new int[m];
        for (int i = 0; i < m; i++) perm[i] = i + 1;  // 1‑based values

        int[] result = new int[queries.length];

        for (int idx = 0; idx < queries.length; idx++) {
            int q = queries[idx];
            int pos = 0;
            // find index
            for (int i = 0; i < m; i++) {
                if (perm[i] == q) {
                    pos = i;
                    break;
                }
            }
            result[idx] = pos;

            // move to front (shifting elements right)
            int temp = perm[pos];
            for (int i = pos; i > 0; i--) perm[i] = perm[i - 1];
            perm[0] = temp;
        }
        return result;
    }
}
```

### 📌 Python Implementation

```python
class Solution:
    def processQueries(self, queries: List[int], m: int) -> List[int]:
        perm = list(range(1, m + 1))
        res = []

        for q in queries:
            pos = perm.index(q)          # O(m) search
            res.append(pos)
            perm.pop(pos)                # O(m) shift
            perm.insert(0, q)            # O(m) shift
        return res
```

### 📌 C++ Implementation

```cpp
class Solution {
public:
    vector<int> processQueries(vector<int>& queries, int m) {
        vector<int> perm(m);
        iota(perm.begin(), perm.end(), 1);          // 1‑based values

        vector<int> res;
        for (int q : queries) {
            int pos = find(perm.begin(), perm.end(), q) - perm.begin();
            res.push_back(pos);
            int val = perm[pos];
            perm.erase(perm.begin() + pos);        // shift left
            perm.insert(perm.begin(), val);        // shift right
        }
        return res;
    }
};
```

> **Why is this acceptable?**  
> The constraints are tiny (`m ≤ 1000`). Even with a nested loop, the runtime stays well below 100 ms on LeetCode.

---

## 4️⃣ Optimal Solution – Fenwick Tree (Binary Indexed Tree)

When `m` grows (e.g., 10⁵ or 10⁶), the naive solution becomes too slow.  
A **Fenwick Tree** lets us maintain the current permutation’s positions and update them in **O(log m)** time.

### How it works

1. **Mapping**  
   * Place every number at a “virtual” position `m + i` (so that indices never become negative).  
   * Maintain a Fenwick tree over size `2m` to track how many elements are currently in front of each index.

2. **Query**  
   * The current index of a number `x` is the prefix sum up to its current position – that’s the number of elements before it.

3. **Move to front**  
   * Set the tree value at the current position to `0`.  
   * Place the element at the next free “front” slot (starting from `m`) and set its tree value to `1`.  
   * Update `pos[x]` to the new slot.

### ✅ Complexity
* **Time:** O((n + m) log m)  
* **Space:** O(m)

### 📌 Java Implementation (Fenwick)

```java
class Fenwick {
    int[] bit;
    int n;
    Fenwick(int n) { this.n = n; bit = new int[n + 2]; }

    void add(int idx, int val) {
        for (int i = idx; i <= n; i += i & -i) bit[i] += val;
    }
    int sum(int idx) {
        int s = 0;
        for (int i = idx; i > 0; i -= i & -i) s += bit[i];
        return s;
    }
}

class Solution {
    public int[] processQueries(int[] queries, int m) {
        int[] res = new int[queries.length];
        int[] pos = new int[m + 1];      // current position of each value
        int size = 2 * m;                // to avoid negative indices
        Fenwick ft = new Fenwick(size + 2);

        // initialise: all elements in the right half [m+1 .. 2m]
        for (int i = 1; i <= m; i++) {
            pos[i] = m + i;
            ft.add(pos[i], 1);
        }

        int front = m;                   // next free front slot
        for (int i = 0; i < queries.length; i++) {
            int x = queries[i];
            int curPos = pos[x];
            int idx = ft.sum(curPos - 1); // elements before x
            res[i] = idx;

            // move to front
            ft.add(curPos, -1);           // remove from old slot
            ft.add(front, 1);             // add to new front slot
            pos[x] = front;
            front--;                      // next query will go even earlier
        }
        return res;
    }
}
```

### 📌 Python Implementation (Fenwick)

```python
class Fenwick:
    def __init__(self, n):
        self.n = n
        self.bit = [0]*(n+2)
    def add(self, i, delta):
        while i <= self.n:
            self.bit[i] += delta
            i += i & -i
    def sum(self, i):
        s = 0
        while i:
            s += self.bit[i]
            i -= i & -i
        return s

class Solution:
    def processQueries(self, queries: List[int], m: int) -> List[int]:
        n = len(queries)
        size = 2*m
        pos = [0]*(m+1)
        ft = Fenwick(size+2)

        for i in range(1, m+1):
            pos[i] = m + i
            ft.add(pos[i], 1)

        front = m
        res = []
        for x in queries:
            cur = pos[x]
            idx = ft.sum(cur-1)
            res.append(idx)
            ft.add(cur, -1)
            ft.add(front, 1)
            pos[x] = front
            front -= 1
        return res
```

### 📌 C++ Implementation (Fenwick)

```cpp
struct Fenwick {
    vector<int> bit;
    int n;
    Fenwick(int n=0){init(n);}
    void init(int n){this->n=n; bit.assign(n+2,0);}
    void add(int idx,int val){
        for(; idx<=n; idx+=idx&-idx) bit[idx]+=val;
    }
    int sum(int idx){
        int s=0;
        for(; idx>0; idx-=idx&-idx) s+=bit[idx];
        return s;
    }
};

class Solution {
public:
    vector<int> processQueries(vector<int>& queries, int m) {
        int sz = 2*m;
        Fenwick ft(sz+2);
        vector<int> pos(m+1), res;
        for(int i=1;i<=m;i++){
            pos[i] = m + i;
            ft.add(pos[i],1);
        }
        int front = m;
        for(int x: queries){
            int cur = pos[x];
            int idx = ft.sum(cur-1);
            res.push_back(idx);
            ft.add(cur,-1);
            ft.add(front,1);
            pos[x] = front;
            front--;
        }
        return res;
    }
};
```

> **When should you pick this?**  
> • `m` > 10⁵  
> • You want a guaranteed O(log m) per query (e.g., for streaming systems).  
> • Demonstrates deep knowledge of BIT.

---

## 5️⃣ The “Good‑Enough” Trade‑off

| Scenario | Preferred Solution | Reason |
|----------|-------------------|--------|
| `m ≤ 10³` | Brute‑force | Simpler code, fewer lines, easier to explain. |
| `m` grows or you want to showcase scalability | Fenwick | Shows algorithmic depth. |
| You’re interviewing for a *coding‑heavy* role | Brute‑force | The interviewer focuses on correctness & code clarity. |
| You’re in a *systems* interview | Fenwick | Real‑world constraints. |

> **Tip:**  
> Start with the brute‑force, explain its complexity, then “ask” the interviewer if a more efficient method is needed. This shows humility and adaptability.

---

## 6️⃣ Edge‑Case & Robustness Checks

| Edge case | What we handle in code |
|-----------|------------------------|
| Duplicate queries | No issue – we simply move the same number again. |
| Querying the front element | Index = 0, tree operations remove and re‑insert the same slot – still O(log m). |
| Smallest `m` (1) | `perm.index(1)` returns 0; moving it to front does nothing – still works. |

---

## 7️⃣ Final Thoughts & Takeaway

* **Show a simple solution** – it proves you can translate a problem statement into code immediately.
* **Then, “raise the bar”** – present an O(n log n) alternative, discussing when it matters.
* **Explain the trade‑offs** clearly: why the constraints allow a brute‑force, but a scalable method shows deeper insight.
* **Keep your code clean** – use meaningful variable names, comment the Fenwick logic, and format consistently.

> **Pro tip for your interview**:  
> *Ask the interviewer, “If `m` were 10⁵, would you still want the same approach?”*  
> This initiates a conversation about time complexity and showcases your ability to think about scaling.

---

## 8️⃣ Ready to Write the Next Winning Solution?

Feel free to copy/paste the code snippets above into LeetCode.  
If you’d like to practice more, try modifying the problem:  
* **Variant** – instead of moving to front, move to the middle.  
* **Follow‑up** – compute the permutation after all queries.

Happy coding! 🚀

---  

*Prepared by **[Your Name]** – Data‑Structure Enthusiast, Competitive Programmer, and Interview Coach.*  
---  
<blockquote>
  **“Your best code interview is not about the answer, but about the thought process.”**  
</blockquote>  

---  

### 📚 Further Reading

1. Fenwick Tree – <https://cp-algorithms.com/data_structures/fenwick.html>  
2. Binary Indexed Tree on permutations – <https://codeforces.com/blog/entry/79354>  
3. `iota` usage in C++ – <https://en.cppreference.com/w/cpp/algorithm/iota>  

Happy learning! 🎓
--- 

**End of Solution**.