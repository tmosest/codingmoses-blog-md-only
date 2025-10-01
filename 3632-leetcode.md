---
title: LeetCode 3632. Subarrays with XOR at Least K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3632 – Subarrays with XOR at Least K  
> A Deep‑Dive Into the Hard LeetCode Problem: **Good, Bad, & Ugly**  
> Written for job seekers who want to impress recruiters with solid data‑structure skills.

---

### TL;DR  
- **Problem**: Count contiguous sub‑arrays whose XOR ≥ K.  
- **Constraints**: *n* ≤ 10⁵, *nums[i]*, K ≤ 10⁹.  
- **Optimal Solution**: Prefix XOR + Binary Trie → **O(N)** time, **O(N)** space.  
- **Languages**: Java, Python, C++ (full, ready‑to‑copy implementations).  
- **Why it matters**: Demonstrates mastery of bit tricks, tries, and prefix sums – all staples of system‑design and backend interviews.

---

## 1️⃣ Problem Recap (LeetCode 3632)

> **Given** an integer array `nums` (positive values) and an integer `k` (≥ 0).  
> **Return** the number of **contiguous** sub‑arrays whose bitwise XOR of all elements is **greater than or equal to** `k`.

### Example

```text
nums = [3, 1, 2, 3], k = 2
Valid sub‑arrays: [3], [3,1], [3,1,2,3], [1,2], [2], [3]
Answer: 6
```

### Constraints

| Parameter | Bounds |
|-----------|--------|
| `nums.length` | 1 … 10⁵ |
| `nums[i]` | 0 … 10⁹ |
| `k` | 0 … 10⁹ |

---

## 2️⃣ The “Good” – 𝑂(𝑁) Optimal Approach

### 2.1 Intuition

- The XOR of a sub‑array `[l … r]` can be written as  
  `prefixXOR[r] ^ prefixXOR[l-1]`  
  where `prefixXOR[i] = nums[0] ^ … ^ nums[i]`.

- For a fixed `r`, we want the number of earlier indices `l` such that  
  `prefixXOR[r] ^ prefixXOR[l-1] >= k`.

- Thus, the problem reduces to: **How many previously seen prefix XORs `p` satisfy `p ^ x >= k`?**  
  where `x = prefixXOR[r]`.

### 2.2 Binary Trie for “XOR ≥ k”

We can’t check every earlier `p` in *O(1)*; instead we store all prefix XORs in a binary trie (bitwise tree).  
Each node keeps a `count` of how many numbers pass through it.

#### Counting “XOR < k” (and subtracting)

It is easier to count how many `p` satisfy **`p ^ x < k`** and then subtract from the total number of seen prefix XORs.  
The classic trie query for “XOR < k” works as follows:

```
for bit i from 31 down to 0
    bitX = (x >> i) & 1
    bitK = (k >> i) & 1

    if bitK == 1:
        # if we take the same bit (bitX ^ 0), the XOR so far is < k
        add count of child[bitX] to answer
        node = child[1 - bitX]   # continue with opposite bit
    else:
        node = child[bitX]       # must keep XOR bit 0
```

If the traversal stops (node becomes `null`), we return the accumulated count.

**Result for “XOR ≥ k”**  
```
totalSeen - countLessThan(x, k)
```

Where `totalSeen` is the number of prefix XORs inserted so far (including the initial `0`).

### 2.3 Complexity

- **Time**: Each of the *n* array elements triggers one insertion + one query, each O(32) → **O(n)**.  
- **Space**: The trie stores up to *n* numbers, each 32 bits → **O(n)** nodes.

---

## 3️⃣ The “Bad” – Brute Force Quadratic

A naive solution checks every sub‑array:

```java
long ans = 0;
for (int i = 0; i < n; i++) {
    int cur = 0;
    for (int j = i; j < n; j++) {
        cur ^= nums[j];
        if (cur >= k) ans++;
    }
}
```

- **Time**: O(n²) → 10⁵² ≈ 5 × 10¹⁰ operations → impossible for LeetCode’s limits.  
- **Space**: O(1).

While useful for teaching XOR properties, this approach is *unacceptable* for production or interview environments.

---

## 4️⃣ The “Ugly” – Edge Cases & Pitfalls

| Pitfall | How it shows up | Fix |
|---------|-----------------|-----|
| **Sign bits / 32 vs 31** | Using 32 bits on Java signed ints may introduce negative values when shifting right. | Use `>>>` (unsigned right shift) or limit to 31 bits (`int MAX_BIT = 31`). |
| **Overflow in count** | Number of sub‑arrays can exceed 2³¹, requiring `long`. | Use `long` for answer, counts, and intermediate totals. |
| **Trie node count** | Forgetting to increment `node.count` after moving to child → incorrect counts. | Increment after assigning `node = node.children[bit]`. |
| **Initial prefix 0** | Without inserting 0, sub‑arrays starting at index 0 are missed. | Insert `0` before the loop. |
| **Large K** | If `k` has more bits than numbers, traversal may early‑exit incorrectly. | Always iterate 31 bits (or 30 for 1e9). |
| **Python recursion depth** | Recursion in query (if implemented recursively) can hit recursion limit for 10⁵ nodes. | Use iterative loops. |

---

## 5️⃣ Full Implementations

Below you’ll find ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

> **Tip**: In your resume or portfolio, showcase the same algorithm in multiple languages to demonstrate versatility.

---

### 5.1 Java

```java
import java.util.*;

public class Solution {
    private static final int MAX_BIT = 31; // 0‑based, 31 for 32‑bit ints

    private static class TrieNode {
        TrieNode[] child = new TrieNode[2];
        int count = 0;
    }

    public long countXorSubarrays(int[] nums, int k) {
        TrieNode root = new TrieNode();
        insert(root, 0); // prefix XOR 0 before starting
        long result = 0;
        int prefix = 0;
        long seen = 1; // number of prefixes inserted (including 0)

        for (int num : nums) {
            prefix ^= num;
            long less = queryLessThan(root, prefix, k);
            result += seen - less; // XOR >= k
            insert(root, prefix);
            seen++;
        }
        return result;
    }

    // Insert number into trie
    private void insert(TrieNode root, int num) {
        TrieNode node = root;
        for (int i = MAX_BIT; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (node.child[bit] == null) node.child[bit] = new TrieNode();
            node = node.child[bit];
            node.count++;
        }
    }

    // Count numbers y in trie such that x ^ y < k
    private long queryLessThan(TrieNode root, int x, int k) {
        TrieNode node = root;
        long cnt = 0;
        for (int i = MAX_BIT; i >= 0 && node != null; i--) {
            int bitX = (x >> i) & 1;
            int bitK = (k >> i) & 1;

            if (bitK == 1) {
                if (node.child[bitX] != null) cnt += node.child[bitX].count;
                node = node.child[1 - bitX];
            } else {
                node = node.child[bitX];
            }
        }
        return cnt;
    }

    // --- Driver for quick sanity test ---
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.countXorSubarrays(new int[]{3,1,2,3}, 2)); // 6
    }
}
```

**Why it’s safe**  
- Uses `long` for all counters.  
- Shifts use signed `>>` but we always mask with `& 1`, so no sign‑propagation problems.  
- Iterates **exactly** 32 bits, guaranteeing coverage for `k ≤ 10⁹`.

---

### 5.2 Python

```python
class Solution:
    MAX_BIT = 31          # 0‑based, covers 32‑bit signed ints

    def countXorSubarrays(self, nums, k):
        root = {}
        insert(root, 0)      # initial prefix 0
        seen = 1
        prefix = 0
        ans = 0

        for num in nums:
            prefix ^= num
            less = query_less_than(root, prefix, k)
            ans += seen - less     # XOR >= k
            insert(root, prefix)
            seen += 1
        return ans

def insert(root, num):
    node = root
    for i in range(Solution.MAX_BIT, -1, -1):
        bit = (num >> i) & 1
        if bit not in node:
            node[bit] = {}
            node[bit]['cnt'] = 0
        node = node[bit]
        node['cnt'] += 1

def query_less_than(root, x, k):
    node = root
    cnt = 0
    for i in range(Solution.MAX_BIT, -1, -1):
        if node is None:
            break
        bitX = (x >> i) & 1
        bitK = (k >> i) & 1
        if bitK == 1:
            child_same = node.get(bitX)
            if child_same:
                cnt += child_same['cnt']
            node = node.get(1 - bitX)
        else:
            node = node.get(bitX)
    return cnt
```

> **Note**: In Python, dictionaries act as trie nodes (`{}`), and the `'cnt'` key stores the number of values passing through that node.

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    static const int MAX_BIT = 31;   // 0‑based

    struct TrieNode {
        TrieNode *ch[2] = {nullptr, nullptr};
        int cnt = 0;
    };

    void insert(TrieNode *root, int num) {
        TrieNode *node = root;
        for (int i = MAX_BIT; i >= 0; --i) {
            int bit = (num >> i) & 1;
            if (!node->ch[bit]) node->ch[bit] = new TrieNode();
            node = node->ch[bit];
            ++node->cnt;
        }
    }

    long long queryLessThan(TrieNode *root, int x, int k) {
        TrieNode *node = root;
        long long res = 0;
        for (int i = MAX_BIT; i >= 0 && node; --i) {
            int bitX = (x >> i) & 1;
            int bitK = (k >> i) & 1;
            if (bitK == 1) {
                if (node->ch[bitX]) res += node->ch[bitX]->cnt;
                node = node->ch[1 - bitX];
            } else {
                node = node->ch[bitX];
            }
        }
        return res;
    }

public:
    long long countXorSubarrays(vector<int>& nums, int k) {
        TrieNode *root = new TrieNode();
        insert(root, 0);          // initial prefix
        long long ans = 0;
        int prefix = 0;
        long long seen = 1;       // we already inserted 0

        for (int num : nums) {
            prefix ^= num;
            long long less = queryLessThan(root, prefix, k);
            ans += seen - less;   // XOR >= k
            insert(root, prefix);
            ++seen;
        }
        return ans;
    }
};
```

---

## 6️⃣ Interview‑Ready Checklist

| ✔️ | Item |
|---|------|
| **1** | **Explain prefix XOR** – it’s a *must‑know* for sub‑array XOR problems. |
| **2** | **Show the binary trie** – talk about bit‑wise children, node `count`, and how we query “< k”. |
| **3** | **Complexity** – emphasise *O(n)* time, *O(n)* space, and compare to the quadratic brute force. |
| **4** | **Edge cases** – initial `0`, use of `long`, and bit‑shifting nuances. |
| **5** | **Why it’s interview‑worthy** – bit manipulation + tries are core data‑structure themes that frequently appear in large‑scale backend & system design questions. |
| **6** | **Show the code in multiple languages** – recruiters love candidates who can write clean, idiomatic code in Java, Python, C++. |

---

## 7️⃣ Closing Thoughts – “Why This Problem Beats the Rest”

- **Showcases depth**: You’re not just solving a puzzle; you’re applying *prefix XOR* (a classic trick) and *trie* (advanced DS) together.  
- **Demonstrates thinking**: The “countLessThan” subtraction trick shows you can think in reverse to simplify a problem.  
- **Handles large input**: 𝑂(𝑁) solutions are essential for 10⁵‑sized arrays, a common interview requirement.  
- **Ready for production**: The algorithm is memory‑friendly, runs in linear time, and uses primitives – all qualities sought after in backend engineers and data scientists.  

> **Make this solution the centerpiece of your technical portfolio.** Add a short blog post (like this one) to your LinkedIn profile; recruiters love readable, self‑contained explanations.

---

## 📈 SEO Tags for Recruiter‑Friendly Content

- *Subarray XOR at least K*  
- *LeetCode 3632*  
- *Count subarrays with XOR ≥ K*  
- *Binary trie algorithm*  
- *Prefix XOR*  
- *O(N) solution*  
- *Bitwise data structure*  
- *System design interview*  
- *Backend engineer*  

Add these tags to your blog, résumé, and GitHub README to capture traffic from recruiters searching for exactly this problem.

---

### 🎯 Final Take‑away for the Job‑Seeker

> Master the prefix‑XOR + binary‑trie pattern, implement it in Java, Python, and C++, and write a concise yet thorough explanation.  
> In a technical interview, you’ll be able to **impress the panel** with a perfect blend of theory, code, and interview‑ready commentary.

Good luck, and may your next interview be *less buggy* and *more elegant*!