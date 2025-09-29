---
title: LeetCode 2200. Find All K-Distant Indices in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Find All K‑Distant Indices in an Array  
**(LeetCode 2200 – Easy)**

> *“The good, the bad, and the ugly” – a deep dive into a seemingly simple problem, how to solve it fast, and how you can showcase it in an interview.*  

---

## 🚀 Why This Problem Matters

* **Interview‑ready** – It’s a frequent “medium‑easy” question on LeetCode and is a staple in many data‑structure interviews.  
* **Showcases understanding of two‑pointer & prefix‑sum tricks** – A clean solution demonstrates a solid grasp of algorithmic thinking.  
* **Easy to optimize** – The naive solution is \(O(n^2)\); the optimal one is \(O(n)\) and constant space.  

If you can explain the trade‑offs and give a clean implementation in Java, Python, or C++, you’ll impress any hiring manager.

---

## 📌 Problem Statement (Restated)

> Given an integer array `nums`, a `key` value that appears in `nums`, and an integer `k`,  
> return a **sorted** list of all indices `i` such that there exists at least one index `j` with  
> \[
> |i - j| \le k \quad\text{and}\quad nums[j] == key
> \]
> 
> 1 ≤ `nums.length` ≤ 1 000  
> 1 ≤ `nums[i]` ≤ 1 000  
> 1 ≤ `k` ≤ `nums.length`

---

## 📖 Two Solutions

| Approach | Time | Space | Why it matters |
|----------|------|-------|----------------|
| **Brute‑Force** – check every pair | \(O(n^2)\) | \(O(1)\) | Easy to implement, but slow for n ≈ 1 000. |
| **One‑pass with marking** – **Optimal** | **\(O(n)\)** | \(O(1)\) | Scans once, no nested loops, only integer math. |

We’ll focus on the optimal solution and provide the code in three popular languages.

---

## 🔍 Optimal Strategy – One‑pass Marking

1. **Traverse** the array once.  
2. When `nums[j] == key`, the indices that become **k‑distant** are the range  
   \[
   \text{left} = \max(0, j - k) \quad\text{to}\quad \text{right} = \min(n-1, j + k)
   \]
3. Instead of adding every index in that range **naively**, keep a running pointer `nextFree`.  
   * `nextFree` is the smallest index **not** yet added.  
   * For each key, we only add the interval `[max(nextFree, left), right]`.  
4. After processing a key, set `nextFree = right + 1`.  
5. Finally return the collected indices.

The idea is similar to “sweep line” or “interval merging” – we never revisit an index that’s already been added.

---

## 🧪 Test Cases

| Input | Expected Output |
|-------|-----------------|
| `nums=[3,4,9,1,3,9,5]`, `key=9`, `k=1` | `[1,2,3,4,5,6]` |
| `nums=[2,2,2,2,2]`, `key=2`, `k=2` | `[0,1,2,3,4]` |
| `nums=[1,2,3,4,5]`, `key=3`, `k=0` | `[2]` |
| `nums=[1,3,1,3,1]`, `key=1`, `k=1` | `[0,1,2,3,4]` |

---

## 🖥️ Code

### Java

```java
import java.util.*;

public class Solution {
    public List<Integer> findKDistantIndices(int[] nums, int key, int k) {
        List<Integer> res = new ArrayList<>();
        int n = nums.length;
        int nextFree = 0;                // smallest index not yet added

        for (int j = 0; j < n; j++) {
            if (nums[j] == key) {
                int left  = Math.max(0, j - k);
                int right = Math.min(n - 1, j + k);

                int start = Math.max(left, nextFree);
                for (int i = start; i <= right; i++) {
                    res.add(i);
                }
                nextFree = right + 1;   // avoid duplicates
            }
        }
        return res;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.findKDistantIndices(
                new int[]{3,4,9,1,3,9,5}, 9, 1)); // [1,2,3,4,5,6]
    }
}
```

### Python

```python
from typing import List

class Solution:
    def findKDistantIndices(self, nums: List[int], key: int, k: int) -> List[int]:
        n = len(nums)
        res = []
        next_free = 0                     # first index that hasn't been added

        for j, val in enumerate(nums):
            if val == key:
                left  = max(0, j - k)
                right = min(n - 1, j + k)

                start = max(left, next_free)
                res.extend(range(start, right + 1))
                next_free = right + 1

        return res

if __name__ == "__main__":
    sol = Solution()
    print(sol.findKDistantIndices([3,4,9,1,3,9,5], 9, 1))  # [1,2,3,4,5,6]
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findKDistantIndices(vector<int>& nums, int key, int k) {
        vector<int> res;
        int n = nums.size();
        int nextFree = 0;                 // smallest index not yet appended

        for (int j = 0; j < n; ++j) {
            if (nums[j] == key) {
                int left  = max(0, j - k);
                int right = min(n - 1, j + k);

                int start = max(left, nextFree);
                for (int i = start; i <= right; ++i)
                    res.push_back(i);
                nextFree = right + 1;    // skip already-added indices
            }
        }
        return res;
    }
};

int main() {
    Solution s;
    vector<int> ans = s.findKDistantIndices({3,4,9,1,3,9,5}, 9, 1);
    for (int x : ans) cout << x << ' ';
    // Output: 1 2 3 4 5 6
}
```

> **Tip** – In all three languages the core logic is identical:  
> *find the interval around each key, merge it with the already‑visited interval, and keep a “next free” pointer.*

---

## 📈 Complexity Analysis

| Metric | Brute‑Force | Optimal |
|--------|-------------|---------|
| **Time** | \(O(n^2)\) | **\(O(n)\)** |
| **Auxiliary Space** | \(O(1)\) | **\(O(1)\)** (apart from the result list) |
| **Result Size** | \(O(n)\) (the array itself) | \(O(n)\) |

With `n ≤ 1 000`, the optimal solution will finish in milliseconds, while the brute‑force one can take several hundred milliseconds—often enough to cause a warning in a timed interview.

---

## ⚠️ Common Pitfalls (“The Ugly”)

| Pitfall | Fix |
|---------|-----|
| **Adding indices inside the full `[left, right]` interval for every key** – leads to duplicates. | Use the `nextFree` pointer or a `boolean[] seen`. |
| **Off‑by‑one errors when updating `nextFree`** | Remember: `nextFree = right + 1`. |
| **Ignoring array bounds** (`j-k` < 0 or `j+k` ≥ n) | Clamp with `Math.max(0, …)` and `Math.min(n-1, …)`. |
| **Using a `Set` to dedupe indices** – works but uses \(O(n)\) extra space and may ruin the \(O(1)\) space claim. | Prefer the pointer trick. |

---

## 📚 Take‑aways for Interviews

1. **Explain the intuition** first – talk about “every key influences a contiguous interval”.
2. **Discuss the complexity trade‑offs** – why you’ll avoid \(O(n^2)\) when you can do \(O(n)\).
3. **Show the one‑pass marking** – it’s a classic pattern that appears in many interval problems (e.g., “max consecutive ones II”, “minimum number of taps to water a garden”).
4. **Mention testability** – provide a few corner cases (k = 0, k ≥ n, all elements equal to key).

---

## 📣 SEO Checklist (If You’re Publishing This Post)

| ✅ | How to optimize for search engines |
|---|-------------------------------------|
| **Title** – “Find All K‑Distant Indices in an Array: Optimal One‑Pass Solution” | Contains keyword “K‑Distant Indices” + “Optimal One‑pass” |
| **Meta Description** | “Learn how to solve LeetCode 2200, Find All K‑Distant Indices, with an \(O(n)\) algorithm and clean Java, Python, and C++ code. Perfect interview prep.” |
| **Headers** | Use H1 for title, H2 for sections (Problem, Brute‑Force, Optimal, Code, Complexity), H3 for language‑specific code. |
| **Internal Links** | Reference other interview‑prep posts on “Two‑Pointer Tricks” or “Prefix Sum for Intervals”. |
| **External Links** | Link to LeetCode problem page, to your GitHub repo, to job‑search resources. |
| **Content Length** | 800–1,200 words; enough to cover the problem, code, and analysis without being too verbose. |
| **Images** | Include a small diagram of the sweep line concept (optional). |
| **Keywords** | “k-distant indices”, “LeetCode 2200”, “two‑pointer algorithm”, “data structure interview”, “job interview coding”. |

---

## 🎯 How to Use This Post in Your Job Hunt

1. **GitHub Gist** – Publish the Java/Python/C++ code in a public repo and include the repo link in your résumé.  
2. **Portfolio** – Embed the code snippet and explanation in your personal website.  
3. **Interview Prep** – Practice explaining the “good” (optimal), “bad” (brute‑force), and “ugly” (boundary bugs) parts.  
4. **Networking** – Share the blog post on LinkedIn with a caption:  
   > “Just cracked a LeetCode problem that many interviewers love. #Java #Python #C++ #DataStructures #InterviewPrep”

---

## ✅ Final Verdict

*The “Find All K‑Distant Indices” problem is deceptively simple, but it hides a neat algorithmic pattern. By presenting a clear \(O(n)\) one‑pass solution, you prove you’re not just a coder—you’re an efficient problem‑solver who can reduce time complexity without sacrificing clarity.*

Happy coding, and best of luck on those interviews!