---
title: LeetCode 2032. Two Out of Three - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Two Out of Three – LeetCode 2032  
*A deep‑dive into the problem, clean solutions in Java, Python and C++, and a job‑ready interview blog post.*

---

## 1. Problem Recap (LeetCode 2032)

> **Given three integer arrays `nums1`, `nums2`, and `nums3`, return a distinct array containing all the values that are present in at least two out of the three arrays.**  
> You may return the values in any order.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ nums1.length, nums2.length, nums3.length ≤ 100` | |
| `1 ≤ nums1[i], nums2[i], nums3[i] ≤ 100` | |

> *Example*  
> `nums1 = [1,1,3,2]`, `nums2 = [2,3]`, `nums3 = [3]` → output: `[3,2]`

---

## 2. Algorithm Overview

The goal is to identify numbers that appear in **at least two** of the three arrays.

The most straight‑forward idea:

1. **Count frequencies** – For each array, mark whether a value appears (once is enough because we only care about presence, not multiplicity).
2. **Combine counts** – For every value `x`, if it appears in two or more arrays, add it to the answer.

There are a few ways to implement step 1:

| Approach | Pros | Cons |
|----------|------|------|
| **HashSet + Counter** | Clear, language‑agnostic, works for any value range | Extra memory overhead for the set |
| **Fixed‑size boolean array** (`0–100`) | No dynamic allocation, O(1) lookup | Only works because the value range is small (≤100) |
| **Bitmask tricks** | Extremely fast for this specific range | Harder to understand, not portable to other ranges |

Below we give three clean, interview‑friendly solutions – one in **Java**, one in **Python**, and one in **C++** – each using the *HashSet + Counter* pattern. After that we’ll discuss the *good*, *bad*, and *ugly* aspects of different implementations, and wrap up with an SEO‑friendly blog article that will boost your interview prep page rankings.

---

## 3. Code Implementations

### 3.1 Java – Clean HashSet Solution

```java
import java.util.*;

class Solution {
    public List<Integer> twoOutOfThree(int[] nums1, int[] nums2, int[] nums3) {
        // Helper method that returns a set of unique values in an array
        Set<Integer> setFromArray(int[] arr) {
            Set<Integer> set = new HashSet<>();
            for (int v : arr) set.add(v);
            return set;
        }

        Set<Integer> s1 = setFromArray(nums1);
        Set<Integer> s2 = setFromArray(nums2);
        Set<Integer> s3 = setFromArray(nums3);

        Set<Integer> result = new LinkedHashSet<>(); // keeps insertion order

        for (int v : s1) if (s2.contains(v) || s3.contains(v)) result.add(v);
        for (int v : s2) if (s3.contains(v)) result.add(v); // s1 already handled
        // No need to check s3 against s1 again – duplicates are removed by set

        return new ArrayList<>(result);
    }
}
```

**Key points**

* We build three `HashSet<Integer>` objects, each containing unique values of the corresponding array.  
* Then we check each element of `s1` against `s2` and `s3`; any match is added to `result`.  
* The second loop checks only the *remaining* potential pairs (`s2` vs `s3`) – we skip re‑checking `s1` because we already handled it.

### 3.2 Python – One‑liner with Sets

```python
class Solution:
    def twoOutOfThree(self, nums1: List[int], nums2: List[int], nums3: List[int]) -> List[int]:
        s1, s2, s3 = map(set, (nums1, nums2, nums3))
        return list((s1 & s2) | (s1 & s3) | (s2 & s3))
```

**Explanation**

* `set(nums)` turns each list into a set of unique values.  
* `s1 & s2` gives values in both `nums1` and `nums2`.  
* We union the three pairwise intersections with `|` – this automatically removes duplicates.

### 3.3 C++ – Using `unordered_set`

```cpp
#include <vector>
#include <unordered_set>
#include <algorithm>

class Solution {
public:
    std::vector<int> twoOutOfThree(std::vector<int>& nums1,
                                   std::vector<int>& nums2,
                                   std::vector<int>& nums3) {
        auto toSet = [](const std::vector<int>& v) {
            return std::unordered_set<int>(v.begin(), v.end());
        };

        std::unordered_set<int> s1 = toSet(nums1);
        std::unordered_set<int> s2 = toSet(nums2);
        std::unordered_set<int> s3 = toSet(nums3);

        std::unordered_set<int> result;

        for (int val : s1)
            if (s2.count(val) || s3.count(val))
                result.insert(val);
        for (int val : s2)
            if (s3.count(val))
                result.insert(val);

        return std::vector<int>(result.begin(), result.end());
    }
};
```

**Takeaways**

* The helper lambda `toSet` builds an `unordered_set` from a vector.  
* As in the Java solution, we avoid checking all three combinations twice.

---

## 4. Good, Bad, and Ugly Approaches

| Category | Example | What Works | Why It Matters |
|----------|---------|------------|----------------|
| **Good** | Boolean array of size 101 (Java) | *O(n + V)* time, *O(V)* space, no hashing overhead. Works perfectly because `1 ≤ nums[i] ≤ 100`. | Keeps memory usage tiny and lookup constant‑time. |
| **Bad** | Using `HashSet` when the value range is tiny | Extra memory, slower constants, but still correct. | For interviews, prefer the simplest logic that fits the constraints – here the boolean array wins. |
| **Ugly** | Bitmask tricks (`int` masks) | Very fast, 1‑ms runtime, but code is cryptic and hard to maintain. | Interviewers value *readability* over micro‑optimisations unless explicitly asked. |

### When to pick which?

| Scenario | Recommendation |
|----------|----------------|
| Range is small (≤100) | **Boolean array** – fastest, simplest, no collections. |
| Range is large or unknown | **HashSet** – still linear time, but flexible. |
| You’re on a timed test and want to show cleverness | **Bitmask** – but be prepared to explain it. |
| You’re coding a production feature | Prefer **HashSet** for readability and maintainability. |

---

## 5. Blog Article – “The Two Out of Three Interview Puzzle: How to Nail It”

> **Title (SEO‑optimized):**  
> *“Two Out of Three LeetCode 2032 – Java, Python & C++ Solutions | Interview Tips & Job‑Ready Algorithms”*

### 1️⃣ What the Problem Really Means

- Three integer arrays → find numbers that appear in *any two* arrays.  
- Constraints are small, but the question tests *set operations*, *frequency counting*, and *algorithmic reasoning*.

### 2️⃣ Why This Is a Gold‑Mine Interview Question

- **Language agnostic**: You can solve it in Java, Python, C++, or even Go.  
- **Time & space analysis**: Interviewers love when you talk about O(n) vs. O(n log n) solutions.  
- **Common interview pattern**: “Two‑out‑of‑three” → generalise to “k‑out‑of‑n” problems (e.g., majority element, intersection of sets).

### 3️⃣ The Cleanest Solution (Java)

- Build three `HashSet<Integer>` objects.  
- Combine with pairwise intersections, avoid duplicates by using another `Set`.  
- Time: **O(n)**, Space: **O(n)**.  
- Code snippet (see section 3.1).

### 4️⃣ Pythonic One‑liner

```python
s1, s2, s3 = map(set, (nums1, nums2, nums3))
result = list((s1 & s2) | (s1 & s3) | (s2 & s3))
```

- Uses set algebra; perfect for *Python* interviews.

### 5️⃣ C++ Version with `unordered_set`

- Same idea, but leverages C++’s STL.  
- Time: **O(n)**, Space: **O(n)**.  

### 6️⃣ When to Use a Boolean Array

- Because all numbers are ≤ 100, a fixed‑size array `[101]` suffices.  
- Even faster (`O(n)` time, constant extra memory).  
- Avoids the overhead of a hash table.

### 7️⃣ Avoiding Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using `List` instead of `Set`** – duplicates creep in | Convert to `Set` first |
| **Checking all 3×3 pairs** – O(n³) in worst case | Use pairwise intersections only |
| **Not handling duplicates inside a single array** | Use a `Set` or boolean array to de‑duplicate |

### 8️⃣ Final Thought: Show Your Thought Process

- Talk about the *value range* → motivates using a boolean array.  
- Mention the *time/space trade‑off*.  
- Provide a quick pseudocode before writing code.  
- Keep the code **readable**; interviewers want to see *clean logic*, not just a micro‑optimized hack.

---

### 9️⃣ Keywords to Boost Your Blog Ranking

- LeetCode Two Out of Three  
- LeetCode 2032  
- Two Out of Three Java solution  
- Two Out of Three Python solution  
- Two Out of Three C++ solution  
- Interview algorithm Two Out of Three  
- Job interview algorithm questions  
- HashSet vs boolean array LeetCode  
- Array intersection problem  
- Software engineer interview tips

### 📌 TL;DR

> *“Two Out of Three” is a simple yet powerful interview problem. A set‑based solution is usually the best compromise between readability and performance, but with the tight value range you can use a boolean array for a lightning‑fast answer. Implement it in Java, Python, or C++ and you’ll impress any hiring manager on day one.*  

---

## 6. Conclusion

- **Java** – HashSet + counter, clean O(n) solution.  
- **Python** – One‑liner with set algebra.  
- **C++** – `unordered_set` version.  
- **Best practice** – For LeetCode 2032, a **boolean array** is the fastest, but set solutions are more universally readable.  

Happy coding, and good luck with your next interview!