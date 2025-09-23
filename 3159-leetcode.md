---
title: LeetCode 3159. Find Occurrences of an Element in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 3159 – Find Occurrences of an Element in an Array  
**Problem Type:** Medium | **Tag:** Hash Map | **Languages:** Java | Python | C++

---

### 1. Problem Summary

You are given

| Input | Description |
|-------|-------------|
| `nums` | an array of integers (size ≤ 10⁵) |
| `queries` | an array of integers (size ≤ 10⁵) |
| `x` | a single integer |

For each `queries[i]` you must return the **index** of the *k‑th* occurrence of `x` in `nums`, where `k = queries[i]`.  
If `x` occurs fewer than `k` times, return `-1` for that query.

> **Example**  
> `nums = [1,3,1,7]`, `queries = [1,3,2,4]`, `x = 1` → `[0,-1,2,-1]`

---

### 2. Straight‑Forward Approach

The core observation: we only care about the positions of **x**.  
If we can quickly map “k‑th occurrence of x” → “index in nums”, every query becomes an O(1) lookup.

The typical solution is:

1. **First Pass – Build the map**  
   Scan `nums`.  
   Every time we see `x`, increment a counter `cnt`.  
   Store `cnt → current index` in a hash map.

2. **Second Pass – Answer queries**  
   For each `k` in `queries`, fetch `map[k]`.  
   If it does not exist → `-1`.

Both passes are linear, giving O(n + m) time and O(n) auxiliary space (worst‑case when `x` occurs at every position).

---

### 3. Code (Three Languages)

#### Java

```java
import java.util.*;

public class Solution {
    public int[] occurrencesOfElement(int[] nums, int[] queries, int x) {
        // Map: occurrence count -> index
        Map<Integer, Integer> map = new HashMap<>();
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == x) {
                count++;
                map.put(count, i);
            }
        }

        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            ans[i] = map.getOrDefault(queries[i], -1);
        }
        return ans;
    }
}
```

#### Python

```python
def occurrencesOfElement(nums, queries, x):
    occ = {}
    cnt = 0
    for idx, val in enumerate(nums):
        if val == x:
            cnt += 1
            occ[cnt] = idx

    return [occ.get(q, -1) for q in queries]
```

#### C++

```cpp
#include <vector>
#include <unordered_map>

using namespace std;

class Solution {
public:
    vector<int> occurrencesOfElement(vector<int>& nums,
                                     vector<int>& queries,
                                     int x) {
        unordered_map<int, int> mp;          // occurrence -> index
        int cnt = 0;
        for (int i = 0; i < (int)nums.size(); ++i) {
            if (nums[i] == x) {
                ++cnt;
                mp[cnt] = i;
            }
        }

        vector<int> ans;
        ans.reserve(queries.size());
        for (int k : queries) {
            auto it = mp.find(k);
            ans.push_back(it == mp.end() ? -1 : it->second);
        }
        return ans;
    }
};
```

---

### 4. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Build map | `O(n)` | `O(n)` (worst‑case) |
| Answer queries | `O(m)` | `O(1)` (output array not counted) |
| **Total** | `O(n + m)` | `O(n)` |

- `n` – size of `nums`
- `m` – size of `queries`

Both passes are single scans; no nested loops.

---

### 5. Edge Cases & Pitfalls

| Situation | Why it matters | How to avoid |
|-----------|----------------|--------------|
| `x` never appears | All queries return `-1` | Map will stay empty – lookup works fine |
| Queries contain values > number of occurrences | Must return `-1` | `getOrDefault` / `unordered_map::find` handles it |
| Large input (10⁵) | Risk of TLE or MLE if using recursion or O(n²) | Linear passes, constant‑time lookup |
| `queries` unsorted | Doesn’t affect correctness | No special handling needed |

---

### 6. “Good, Bad, Ugly” of This Solution

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Clear separation of concerns (build map, answer queries) | None | – |
| **Performance** | O(n+m) time, optimal for 10⁵ | Uses a hash map → O(n) space | If `x` occurs in every position, memory = O(n) but still acceptable |
| **Scalability** | Works for large arrays | None | – |
| **Maintainability** | Easy to extend (e.g., return indices for multiple values) | None | – |
| **Potential bug** | None | None | – |
| **Hard part** | None | Mapping indices correctly | – |

> **Bottom line:** The hash‑map approach is *the* cleanest, fastest way to solve this problem. Avoiding extra data structures (e.g., linked lists) keeps the code lean and interview‑friendly.

---

### 7. SEO‑Optimized Blog Post

---

#### Title  
**Crack LeetCode 3159 – Find Occurrences of an Element in an Array (Java, Python, C++)**

#### Meta Description  
Master LeetCode 3159 in seconds. Learn the hash‑map solution, see Java/Python/C++ code, understand time‑space trade‑offs, and get interview‑ready. Perfect for software engineers prepping for coding interviews.

---

#### Introduction

If you’re hunting for a *medium‑level* LeetCode problem that’s perfect for sharpening your hash‑map skills, look no further than **3159. Find Occurrences of an Element in an Array**. This problem tests your ability to map occurrences to indices and answer queries in linear time – a classic interview scenario.

In this article we’ll:
- Break down the problem statement in plain English.
- Present a clean, O(n+m) solution with Java, Python, and C++ code.
- Discuss the “good, bad, ugly” of this approach.
- Highlight why this solution shines in real‑world coding interviews.

---

#### Problem Recap

Given:
- `nums` – an array of integers (up to 100 000 elements).
- `queries` – an array of integers where each value is a rank (k‑th occurrence).
- `x` – the target value.

Return an array where each element is the index of the k‑th occurrence of `x` in `nums`, or `-1` if it doesn’t exist.

---

#### Why a Hash Map is the Key

The straightforward idea is to walk through `nums` once and remember every position where `x` appears.  
Once we have that “occurrence → index” mapping, each query is a constant‑time dictionary lookup.

This strategy gives:
- **Linear time** – only two scans (`O(n+m)`).
- **Constant‑time queries** – no nested loops, no binary search over a list of positions.
- **Scalable space usage** – at most one entry per occurrence of `x`.

---

#### Step‑by‑Step Walkthrough

1. **Build the map**  
   ```text
   count = 0
   for i in 0 … nums.length-1:
       if nums[i] == x:
           count += 1
           map[count] = i
   ```

2. **Answer each query**  
   ```text
   ans[i] = map.getOrDefault(queries[i], -1)
   ```

That’s all. No extra arrays, no complex data structures.

---

#### Code Samples

> See the *Java*, *Python*, and *C++* implementations in the “Code” section above. Each snippet is only a few lines long, making it interview‑friendly and easy to remember.

---

#### Good, Bad, Ugly

| Feature | Good | Bad | Ugly |
|---------|------|-----|------|
| **Readability** | Clear, concise logic | None | – |
| **Speed** | O(n+m) – optimal for 10⁵ | None | – |
| **Space** | O(n) worst‑case – still fine for 10⁵ | Hash map overhead | – |
| **Maintainability** | Easy to tweak for variations | None | – |
| **Interview‑fit** | No boilerplate, clean comments | None | – |

> **Interview Tip:** Talk through the mapping idea before writing code. It shows the interviewer you understand the underlying data structure, not just the “code‑it‑up” trick.

---

#### Common Gotchas to Avoid

- **Missing `x` in `nums`** – the map remains empty, but lookups still return `-1`.  
- **Queries larger than actual occurrence count** – use `getOrDefault` / `unordered_map::find` to guard.  
- **Memory blow‑up** – if `x` appears in every element, the map has 100 000 entries; still well within typical interview constraints.

---

#### Takeaway

The hash‑map solution is the *canonical* answer to LeetCode 3159. It’s concise, fast, and demonstrates mastery of dictionary lookups – exactly what interviewers want to see.  
Practice it until you can write it in one line (Java/Python) or two loops (C++), then you’ll be ready to ace this and many other medium‑level hash‑map questions.

---

#### Want to Boost Your Interview Skills?

- Build a **LeetCode playground** and practice 5–10 problems a day.  
- Pair‑program with a friend or use a platform like **InterviewBit** for mock interviews.  
- Share this solution on LinkedIn – the community loves clean code!

Happy coding, and may your indices always point to success! 🚀

--- 

*Feel free to drop a comment if you have questions or want to discuss other LeetCode strategies!*