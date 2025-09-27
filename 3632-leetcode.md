---
title: LeetCode 3632. Subarrays with XOR at Least K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Overview – LeetCode 3632: “Subarrays with XOR at Least K”

| ✅   | **Problem** | **Constraints** | **Goal** |
|------|-------------|-----------------|----------|
| **Hard** | Given an array `nums` (1 ≤ n ≤ 10⁵, 0 ≤ nums[i] ≤ 10⁹) and an integer `k` (0 ≤ k ≤ 10⁹), count how many **contiguous** sub‑arrays have a **bitwise XOR** that is **≥ k**. | `long` is required for the answer (n can be 10⁵ → ~5×10⁹ sub‑arrays). | Return the total number of valid sub‑arrays. |

> **Example**  
> `nums = [3,1,2,3]`, `k = 2` → answer: **6**.

---

## 📊 The “Good, the Bad, and the Ugly” of the Solution

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Naïve O(n²) double‑loop** | Simple to code, works for tiny inputs. | 10⁵² operations → impossible. | 100 % TLE on LeetCode. |
| **Prefix XOR + Brute‑Force** | Reduce inner XOR to O(1) using prefix XOR. | Still O(n²) for enumeration. | Still TLE. |
| **Binary Trie + Prefix XOR** | **O(n · W)** (W = 32) → ≈ 3.2 × 10⁶ ops. | Needs careful bit handling & memory. | Easy to bug‑prove (off‑by‑one, overflow). |
| **Space** | O(n · W) for trie nodes (~3 MB). | Acceptable. | Large memory if W too big. |

The binary‑trie approach is the **golden solution** that passes all tests, runs fast enough for job interviews, and demonstrates a deep understanding of bit manipulation and prefix sums.

---

## 🧠 Intuition & Algorithm

1. **Prefix XOR**:  
   `pref[i] = nums[0] ⊕ … ⊕ nums[i-1]`.  
   XOR of sub‑array `l … r` equals `pref[r+1] ⊕ pref[l]`.

2. **Counting Sub‑arrays**:  
   For each `pref[r+1]` we need the number of previous prefixes `pref[l]` such that  
   `pref[r+1] ⊕ pref[l] ≥ k`.

3. **Binary Trie**:  
   Store all previous prefixes in a trie where each node represents a bit (0 or 1).  
   Each node keeps a `cnt` of how many numbers go through that node.  
   While querying, we walk the trie from the most significant bit (31st) down to 0, deciding whether to:
   * take the opposite bit (ensures XOR ≥ k at this bit level), or
   * stay on the same bit (continue checking lower bits).

4. **Complexities**:  
   *Time* – O(n · 32) ≈ O(n).  
   *Space* – O(n · 32) for the trie nodes (≤ 4 × n bytes ≈ 4 MB).

---

## 🔧 Implementation – 3 Languages

### 1. Java

```java
import java.io.*;
import java.util.*;

public class Solution {
    /* ---------- Trie Node ---------- */
    private static class TrieNode {
        TrieNode[] child = new TrieNode[2];
        int count = 0;
    }

    /* ---------- Public API ---------- */
    public long countXorSubarrays(int[] nums, int k) {
        TrieNode root = new TrieNode();
        insert(root, 0);                 // empty prefix
        long result = 0;
        int prefix = 0;

        for (int num : nums) {
            prefix ^= num;
            result += query(root, prefix, k);
            insert(root, prefix);
        }
        return result;
    }

    /* ---------- Insert a number into the trie ---------- */
    private void insert(TrieNode root, int val) {
        TrieNode node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (val >> i) & 1;
            if (node.child[bit] == null) node.child[bit] = new TrieNode();
            node = node.child[bit];
            node.count++;
        }
    }

    /* ---------- Query: how many prefixes give XOR >= k ---------- */
    private long query(TrieNode root, int prefix, int k) {
        TrieNode node = root;
        long count = 0;
        for (int i = 31; i >= 0 && node != null; i--) {
            int pBit = (prefix >> i) & 1;
            int kBit = (k >> i) & 1;

            if (kBit == 1) {                 // need opposite bit to keep XOR >= k
                node = node.child[1 - pBit];
            } else {                         // if opposite bit exists, all of them qualify
                if (node.child[1 - pBit] != null)
                    count += node.child[1 - pBit].count;
                node = node.child[pBit];
            }
        }
        if (node != null) count += node.count;   // all remaining numbers are valid
        return count;
    }

    /* ---------- Driver for quick manual testing ---------- */
    public static void main(String[] args) throws Exception {
        Solution sol = new Solution();
        System.out.println(sol.countXorSubarrays(new int[]{3,1,2,3}, 2));   // 6
        System.out.println(sol.countXorSubarrays(new int[]{0,0,0}, 0));     // 6
    }
}
```

> **Why a `long`?**  
> The number of sub‑arrays can reach ~5 × 10⁹, which exceeds `int`.  
> All counts in the trie are stored as `int` because at most `n` prefixes pass through a node.  

---

### 2. Python

```python
class TrieNode:
    __slots__ = ("child", "count")
    def __init__(self):
        self.child = [None, None]
        self.count = 0

class Solution:
    def countXorSubarrays(self, nums: list[int], k: int) -> int:
        root = TrieNode()
        self._insert(root, 0)          # empty prefix
        res = 0
        pref = 0

        for num in nums:
            pref ^= num
            res += self._query(root, pref, k)
            self._insert(root, pref)

        return res

    def _insert(self, root: TrieNode, val: int) -> None:
        node = root
        for i in range(31, -1, -1):
            bit = (val >> i) & 1
            if node.child[bit] is None:
                node.child[bit] = TrieNode()
            node = node.child[bit]
            node.count += 1

    def _query(self, root: TrieNode, pref: int, k: int) -> int:
        node = root
        ans = 0
        for i in range(31, -1, -1):
            if node is None:
                break
            p_bit = (pref >> i) & 1
            k_bit = (k >> i) & 1
            if k_bit == 1:
                node = node.child[1 - p_bit]          # must take opposite bit
            else:
                # all numbers with opposite bit already satisfy >=k
                if node.child[1 - p_bit]:
                    ans += node.child[1 - p_bit].count
                node = node.child[p_bit]
        if node:
            ans += node.count
        return ans

# ---------- Quick manual test ----------
if __name__ == "__main__":
    sol = Solution()
    print(sol.countXorSubarrays([3,1,2,3], 2))   # 6
    print(sol.countXorSubarrays([0,0,0], 0))     # 6
```

> **Python tips**:  
> *Use `__slots__` to reduce memory overhead of many trie nodes.*  
> *The bit‑loop runs from 31 down to 0 – 32 iterations only.*

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    TrieNode* child[2];
    int cnt;
    TrieNode() { child[0] = child[1] = nullptr; cnt = 0; }
};

class Solution {
public:
    long long countXorSubarrays(vector<int>& nums, int k) {
        TrieNode* root = new TrieNode();
        insert(root, 0);                 // empty prefix
        long long res = 0;
        int pref = 0;

        for (int num : nums) {
            pref ^= num;
            res += query(root, pref, k);
            insert(root, pref);
        }
        return res;
    }

private:
    void insert(TrieNode* root, int val) {
        TrieNode* node = root;
        for (int i = 31; i >= 0; --i) {
            int bit = (val >> i) & 1;
            if (!node->child[bit]) node->child[bit] = new TrieNode();
            node = node->child[bit];
            node->cnt++;
        }
    }

    long long query(TrieNode* root, int pref, int k) {
        TrieNode* node = root;
        long long ans = 0;
        for (int i = 31; i >= 0 && node; --i) {
            int pBit = (pref >> i) & 1;
            int kBit = (k >> i) & 1;

            if (kBit) {                         // must take opposite bit
                node = node->child[1 - pBit];
            } else {                            // opposite bit gives >= k
                if (node->child[1 - pBit]) ans += node->child[1 - pBit]->cnt;
                node = node->child[pBit];
            }
        }
        if (node) ans += node->cnt;              // remaining nodes are valid
        return ans;
    }
};

// ---------- Driver for quick manual test ----------
int main() {
    Solution s;
    cout << s.countXorSubarrays({3,1,2,3}, 2) << endl;   // 6
    cout << s.countXorSubarrays({0,0,0}, 0) << endl;     // 6
    return 0;
}
```

> **C++ notes**:  
> *Pre‑allocate nodes with `new` – memory usage stays well below 10 MB.*  
> *Use `long long` for the answer.*  
> *The 32‑bit loop (`for (int i = 31; i >= 0; --i)`) guarantees constant time per number.*

---

## 📚 Why This Is Interview‑Ready

1. **Clean, O(1) per number** – LeetCode hard requires you to think beyond the brute force.
2. **Shows mastery of**:
   * Prefix XOR – a classic trick for sub‑array XOR problems.
   * Binary Trie – a powerful data structure for bit‑wise queries.
   * 64‑bit integer arithmetic – correctly handling overflow.
3. **Time & Space** – Meets the strict limits (`O(n)` time, `O(n)` space).  
4. **Readability** – Comments, consistent naming, and modular helper functions aid comprehension.

---

## 📈 SEO‑Optimized Blog Article

### Subarrays with XOR ≥ K – The Good, the Bad, and the Ugly  
**(Hard LeetCode Problem #3632 – 2025‑09‑26)**  

> **Keywords**: `leetcode hard`, `subarrays`, `xor`, `prefix XOR`, `binary trie`, `job interview algorithms`, `efficient algorithm`, `C++ solution`, `Java solution`, `Python solution`.

---

#### 1️⃣ Introduction

In the world of coding interviews, **LeetCode Hard** problems are the ultimate showcase of algorithmic brilliance.  
Problem 3632 – *Subarrays with XOR at Least K* – sits at the crossroads of bit manipulation, prefix sums, and trie data structures.  

If you want to land a software‑engineering role, mastering this problem proves you can:

* Design solutions that are both **fast** and **memory‑friendly**.  
* Translate mathematical insights into clean code in multiple languages.  
* Communicate complex ideas clearly—an indispensable interview skill.

---

#### 2️⃣ Problem Statement

> **Given** an array `nums` of `n` integers (0 ≤ nums[i] ≤ 2³¹ – 1) and an integer `K`.  
> **Task**: Count all sub‑arrays `(i … j)` such that  
> `xor(nums[i..j]) ≥ K`.  

The naive approach examines every sub‑array: `O(n²)`.  
With `n` up to 5 × 10⁵, this is impossible on typical interview machines.

---

#### 3️⃣ The Good – From Brute Force to Linear Time

| Approach | Time | Space | Verdict |
|----------|------|-------|---------|
| Brute force (double loop) | `O(n²)` | `O(1)` | **Too slow** |
| Prefix XOR + hashmap | `O(n)` | `O(n)` | **Close**, but fails for the inequality case |
| **Binary Trie + Prefix XOR** | **`O(n)`** | `O(n)` | **Optimal** |

The *good* part: The optimal solution uses **prefix XOR** to transform the problem into a *prefix‑based inequality* that can be answered in constant time per element using a **binary trie**.

---

#### 4️⃣ The Bad – Why the Simple Tricks Fail

- **HashMap approach**: You can count sub‑arrays with XOR exactly equal to a target by storing prefix XOR counts in a hashmap.  
  However, for *at least K*, you need to know how many prefixes give a **range of XOR values**—hashmaps give you only *exact* matches.  
- **Sorting + two‑pointer**: Works for sums but fails for XOR, because XOR isn’t monotonic with respect to array order.

These pitfalls illustrate why a naive solution will get a “Time Limit Exceeded”.

---

#### 5️⃣ The Ugly – Common Pitfalls

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Using `int` for answer | Wrong result for large inputs | Use `long long` (C++), `long` (Java), or `int`‑overflow guard in Python |
| Forgetting the empty prefix | Misses sub‑arrays that start at index 0 | Insert `0` into the trie before processing |
| Mis‑handling bit‑ordering | Off‑by‑one errors in inequality logic | Always iterate from the most significant bit (31) to 0 |
| Memory blow‑up | `StackOverflowError` or `C++` heap exhaustion | Pre‑allocate nodes carefully, avoid recursion for insert/query |

---

#### 6️⃣ Step‑by‑Step Walk‑Through (Java)

```java
// 1. Insert empty prefix
insert(root, 0);

// 2. For each number
prefix ^= num;                     // prefix XOR
ans += query(root, prefix, K);     // count qualifying prefixes
insert(root, prefix);              // add current prefix for future queries
```

*The `query` function navigates the trie bit by bit, deciding whether to take the opposite bit (to stay ≥ K) or count all qualifying nodes.*

---

#### 7️⃣ Multi‑Language Templates

We provide full, compilable snippets in **Java**, **Python**, and **C++**.  
Feel free to copy, paste, and run them on your local IDE.  

| Language | File | Complexity |
|----------|------|------------|
| Java | `Solution.java` | `O(n)` |
| Python | `solution.py` | `O(n)` |
| C++ | `solution.cpp` | `O(n)` |

---

#### 8️⃣ Takeaway for Interviews

1. **Explain the intuition first**: *prefix XOR → sub‑array XOR can be expressed as XOR of two prefixes.*  
2. **Show the data‑structure choice**: *binary trie gives us a logarithmic (constant) search over bits.*  
3. **Run through a small example on the whiteboard** to demonstrate the bit‑by‑bit logic.  

Remember, interviewers love to see *thinking on the fly*. If you hit a snag, ask clarifying questions about the range of `K` or the distribution of input values—they might hint at a simpler solution or a subtle twist.

---

#### 9️⃣ Final Thoughts

Subarrays with XOR ≥ K may look intimidating, but with a systematic approach—prefix XOR + binary trie—you turn it into a clean, linear‑time algorithm.  

Drop the solution into your GitHub portfolio, use the discussion section to ask deeper questions, and be ready to discuss it in a technical interview.  

**Good luck, future software engineer!**

---

### 📌 TL;DR

- **Problem**: Count sub‑arrays with XOR ≥ K.  
- **Optimal approach**: Prefix XOR + Binary Trie.  
- **Complexities**: `O(n)` time, `O(n)` space.  
- **Languages**: Java, Python, C++ (code above).  
- **Interview value**: Demonstrates advanced bit‑wise reasoning, data‑structure selection, and performance awareness.  

---

> **Ready for the next hard problem?**  
> Try Problem 3631 – *Maximum Sum of Subarrays with Equal Length* – for a dynamic‑programming twist.  

--- 

**Happy coding, and may your job interviews be glitch‑free!** 🚀  

--- 

*End of article.*  

--- 

With these snippets and the article, you’re equipped to impress recruiters, ace the LeetCode hard challenge, and shine in your next interview. Happy solving!