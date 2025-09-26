---
title: LeetCode 3644. Maximum K to Sort a Permutation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📦 3644. **Maximum K to Sort a Permutation** – Three‑Language Solution + SEO‑Optimized Blog

> **Author:** *Your Name*  
> **Platforms:** LeetCode, Interview Preparation, Coding Interviews  
> **Skills Highlighted:** Bit‑wise Operations, Greedy Algorithms, O(n) Time Complexity, Space‑Efficient Coding

---

## TL;DR

- **Goal** – Find the largest non‑negative integer **k** such that we can sort the given permutation by swapping *any* pair of indices **i, j** *iff* `nums[i] & nums[j] == k`.
- **Answer** – The maximum k is the **bitwise AND of all misplaced numbers**.  
- **Complexity** – `O(n)` time, `O(1)` space.  
- **Why it works** – Every misplaced element shares at least the bits in that common AND, and the permutation property guarantees we can always reach the correct order using only those bits.

---

## Why This Problem Is a Gold‑Mine for Interviews

| **Aspect** | **Why It Matters** | **How It Shines in Interviews** |
|------------|--------------------|---------------------------------|
| **Bitwise Insight** | Uses & to filter common bits – a classic interview pattern. | Show you understand bit manipulation, not just loops. |
| **Greedy Simplicity** | The optimal `k` is obtained by one pass, no backtracking. | Demonstrates you can spot linear greedy solutions. |
| **Edge‑Case Awareness** | Already sorted → answer 0, all numbers misplaced → all bits. | Tests careful handling of special cases. |
| **Language Agnostic** | Implementable in Java, Python, C++. | Proves you can translate logic across ecosystems. |
| **Job‑Relevance** | Companies ask about permutations, sorting, and bitwise ops. | Highlights transferable problem‑solving skills. |

---

## The Good, The Bad, & The Ugly

### The Good  
- **One‑pass solution** – no need for union‑find or graph cycle detection.  
- **Deterministic k** – you never have to “try” multiple k values.  
- **Intuitive proof** – the AND of misplaced elements is the *only* candidate that guarantees every swap is allowed.

### The Bad  
- **Common Misunderstanding** – Many think you can pick different k’s for different swaps.  
- **Assumption of Permutation** – If the array isn’t a true permutation, the AND trick breaks.  

### The Ugly  
- **Implementation Pitfalls** – Starting with `INT_MAX` (or `~0`) and forgetting to reset it when no misplacements exist.  
- **Mis‑reading “Non‑decreasing”** – The permutation property ensures sorted order is `0..n‑1`; otherwise you’d need to handle duplicates.  

---

## Full Code (Three Languages)

> All implementations run in `O(n)` time and `O(1)` extra space.  
> Use the same logic in your favorite language; the core idea never changes.

---

### Java

```java
import java.util.*;

public class Solution {
    public int sortPermutation(int[] nums) {
        // If already sorted, no swaps are needed → k = 0
        int mask = Integer.MAX_VALUE;          // all 1s in binary
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) {                // only consider misplaced elements
                mask &= nums[i];                // keep only common bits
            }
        }
        // If mask unchanged, array was sorted
        return mask == Integer.MAX_VALUE ? 0 : mask;
    }

    // Optional main for quick testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.sortPermutation(new int[]{0,3,2,1})); // 1
        System.out.println(s.sortPermutation(new int[]{0,1,3,2})); // 2
        System.out.println(s.sortPermutation(new int[]{3,2,1,0})); // 0
    }
}
```

---

### Python

```python
class Solution:
    def sortPermutation(self, nums: list[int]) -> int:
        mask = (1 << 31) - 1          # 32‑bit all‑ones mask (works for any n)
        for i, val in enumerate(nums):
            if val != i:
                mask &= val
        return 0 if mask == ((1 << 31) - 1) else mask


# Quick test harness
if __name__ == "__main__":
    s = Solution()
    print(s.sortPermutation([0, 3, 2, 1]))  # 1
    print(s.sortPermutation([0, 1, 3, 2]))  # 2
    print(s.sortPermutation([3, 2, 1, 0]))  # 0
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int sortPermutation(vector<int>& nums) {
        int mask = INT_MAX;              // all bits set
        for (int i = 0; i < (int)nums.size(); ++i) {
            if (nums[i] != i)            // only for misplaced elements
                mask &= nums[i];         // keep only common bits
        }
        return mask == INT_MAX ? 0 : mask;
    }
};

int main() {
    Solution s;
    cout << s.sortPermutation({0,3,2,1}) << endl; // 1
    cout << s.sortPermutation({0,1,3,2}) << endl; // 2
    cout << s.sortPermutation({3,2,1,0}) << endl; // 0
}
```

---

## Step‑by‑Step Proof (Why the AND Works)

1. **All elements are from `0 … n‑1`.**  
   This guarantees that every integer has at most `⌊log₂ n⌋ + 1` bits, and the set of all numbers is *closed* under bitwise AND.

2. **Let `M` be the set of misplaced indices**:  
   `M = { i | nums[i] ≠ i }`.

3. **Define `k* = nums[i₁] & nums[i₂] & … & nums[iₘ]`** (bitwise AND over all misplaced numbers).  
   By construction, `k*` is the **common subset of bits** present in every mis‑placed element.

4. **Any swap between two misplaced elements** `a, b ∈ M` satisfies  
   `a & b` contains all bits of `k*` (since `k*` is the AND of all).  
   Therefore, swapping `a` and `b` is allowed *if we choose* `k = k*`.

5. **Bridging via correctly placed elements**  
   Even if some misplaced numbers share no direct bitwise AND equal to `k*`, we can route through a *correctly placed* element `c` that already contains the required bits.  
   Because `c` is `c = i` (its own index), it always shares at least the bits of `i` with any element `x` that has `i & x` containing those bits.  
   This guarantees we can move any misplaced element to its correct position using a sequence of swaps all respecting `k = k*`.

6. **Maximality** – Suppose any larger `k` (`k' > k*`) could sort the array.  
   Then `k'` would have to be a common bitwise AND of **every** misplaced number (since each swap must satisfy `x & y == k'`).  
   But `k*` is already the *maximum* such common bitset: adding any extra bit would turn it off in at least one misplaced element, breaking the equality.  
   Therefore, `k*` is the maximum feasible `k`.

7. **Edge Cases** – If no element is misplaced, the loop never changes `mask`, so we return `0`.  
   If all elements are misplaced, `mask` becomes the AND of all numbers in `0 … n‑1`, which is `0` for `n ≥ 2` (since some number is missing each bit). Thus, the answer is `0`, matching intuition.

---

## How to Turn This into a Job‑Winning Narrative

1. **Start with the Problem Statement** – Summarize in one sentence: “Find the largest `k` that allows sorting a permutation by swapping only pairs with `nums[i] & nums[j] == k`.”

2. **Show Your Thought Process** – Explain why you first considered a greedy AND approach, why cycles were unnecessary, and how the permutation property simplifies things.

3. **Present the Code** – Highlight the linear pass and the subtle use of `INT_MAX`/`~0` as the initial mask. Mention that the solution works in any language.

4. **Explain the Proof** – Share the reasoning (as above) to demonstrate deep understanding. Interviewers love candidates who can articulate why a solution works.

5. **Mention Edge Cases & Testing** – Show test harnesses in each language and verify correctness for typical examples.

6. **Wrap Up with Takeaways** – “Bitwise AND can often collapse constraints into a single integer. In this problem, the common AND of misplaced elements is the magic key to sorting.”  

Adding this story to your résumé or portfolio will give you a *standout* talking point in technical interviews.

---

## SEO‑Optimized Blog Post Draft

> **Title**: *Master LeetCode 3644 – “Maximum K to Sort a Permutation” – One‑Pass, O(n) Solution (Java, Python, C++)*  
> **Meta Description**: Learn the fastest way to solve LeetCode 3644. A step‑by‑step guide, proof, and Java/Python/C++ code snippets for “Maximum K to Sort a Permutation”.  

---

### Introduction (≈200 words)

> In coding interviews, LeetCode’s “Maximum K to Sort a Permutation” (ID 3644) is a favorite for testing your mastery of bitwise operations and greedy algorithms. The challenge asks: *Given a permutation of 0…n‑1, find the largest integer k such that we can sort the array by swapping only pairs where the AND equals k.*  
> Many solutions waste time exploring graph cycles or using union‑find. The real key is a simple, elegant one‑pass trick. This article walks you through the intuition, the formal proof, and ready‑to‑copy code in Java, Python, and C++.  

> By the end of this post you’ll not only know how to implement the solution but also why it’s optimal—a crucial skill to impress interviewers at Google, Amazon, and Microsoft.  

---

### 1. Problem Recap (≈150 words)

- Definition of permutation  
- Swap constraint `nums[i] & nums[j] == k`  
- Goal: maximum `k` to sort array

---

### 2. Brute Force vs. Optimized (≈200 words)

- Discuss naive approach (try every k) → O(n · 2^bits)  
- Why cycles/unions are unnecessary due to permutation  
- Lead into greedy AND

---

### 3. Core Idea: AND of Misplaced Elements (≈300 words)

- Introduce `mask = all bits`  
- One loop: if `nums[i] != i` → `mask &= nums[i]`  
- Return `0` if mask unchanged  

Include the proof in a “Why It Works” sidebar.  

---

### 4. Code Galleries (≈250 words each)

- Java snippet  
- Python snippet  
- C++ snippet  

Show minimal boilerplate, highlight any language quirks (e.g., `~0` in Python).  

---

### 5. Edge Cases & Test Cases (≈200 words)

- Already sorted → 0  
- All misplaced → 0 for n≥2  
- Provide quick harness and sample outputs.  

---

### 6. Proof of Optimality (≈400 words)

- Formal explanation as earlier.  
- Use diagrams or a simple table of bits to illustrate.

---

### 7. Takeaways for Interviews (≈200 words)

- Bitwise AND as a collapsing constraint.  
- Permutation property ensures closed set.  
- No cycles or graph needed – a pure greedy linear scan.

---

### Closing (≈150 words)

> Whether you’re preparing for a senior software engineer role or just brushing up on bitwise tricks, LeetCode 3644 is a must‑know problem. The one‑pass, `O(n)` solution in Java, Python, and C++ is as elegant as it is efficient. Drop the code into your interview prep, walk your interviewer through the proof, and you’ll show you can solve non‑trivial constraints with minimal complexity. Happy coding!

---

### Call‑to‑Action

> *Want more fast LeetCode solutions? Subscribe for weekly posts, or contact me to review your interview prep.*  

---

## Final Thought

*The “Maximum K to Sort a Permutation” problem is not just a test of coding—it’s a test of insight. The AND trick turns a seemingly complex constraint into a single integer, allowing a clean, one‑pass solution. Master it, share it, and watch interviewers recognize the depth of your algorithmic thinking.*

--- 

Happy coding—and good luck landing that dream job! 🚀