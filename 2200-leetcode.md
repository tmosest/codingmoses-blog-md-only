---
title: LeetCode 2200. Find All K-Distant Indices in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap

**LeetCode 2200 – Find All K‑Distant Indices in an Array**

> You’re given an array `nums`, a `key`, and a distance `k`.  
> An index `i` is *k‑distant* if there exists a `j` such that `|i-j| ≤ k` and `nums[j] == key`.  
> Return every k‑distant index in increasing order.

Constraints  
- `1 ≤ nums.length ≤ 1000`  
- `1 ≤ nums[i] ≤ 1000`  
- `1 ≤ k ≤ nums.length`  
- `key` is guaranteed to appear in `nums`.

---

## 2. High‑Level Solution

The brute‑force check would test every pair `(i, j)` → **O(n·k)**, which is fine for 1000 but still not optimal.  
A cleaner, linear‑time algorithm is:

1. **Compute the distance from each index to the nearest occurrence of `key`.**  
   Two passes (left‑to‑right and right‑to‑left) give the minimal distance for every position.
2. **Select indices whose minimal distance ≤ k.**  

This runs in **O(n)** time and uses **O(n)** extra space (the distance array).  
Because the array size is only 1000, the constant factor is negligible, but the approach scales well.

---

## 3. Code Implementations

Below are working solutions in **Java**, **Python**, and **C++**.  
All three use the two‑pass distance‑to‑key strategy.

### 3.1 Java (LeetCode Style)

```java
import java.util.*;

public class Solution {
    public List<Integer> findKDistantIndices(int[] nums, int key, int k) {
        int n = nums.length;
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // Forward pass: distance to last seen key
        int last = -k - 1;          // effectively "infinite" distance
        for (int i = 0; i < n; i++) {
            if (nums[i] == key) last = i;
            dist[i] = i - last;
        }

        // Backward pass: distance to next seen key
        last = n + k;               // "infinite" on the right
        for (int i = n - 1; i >= 0; i--) {
            if (nums[i] == key) last = i;
            dist[i] = Math.min(dist[i], last - i);
        }

        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (dist[i] <= k) result.add(i);
        }
        return result;
    }
}
```

### 3.2 Python (3.8+)

```python
from typing import List

class Solution:
    def findKDistantIndices(self, nums: List[int], key: int, k: int) -> List[int]:
        n = len(nums)
        dist = [float('inf')] * n

        # Left to right
        last = -k - 1
        for i, val in enumerate(nums):
            if val == key:
                last = i
            dist[i] = i - last

        # Right to left
        last = n + k
        for i in range(n - 1, -1, -1):
            if nums[i] == key:
                last = i
            dist[i] = min(dist[i], last - i)

        return [i for i, d in enumerate(dist) if d <= k]
```

### 3.3 C++ (GNU++17)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> findKDistantIndices(std::vector<int>& nums, int key, int k) {
        int n = nums.size();
        std::vector<int> dist(n, INT_MAX);

        // Left to right
        int last = -k - 1;
        for (int i = 0; i < n; ++i) {
            if (nums[i] == key) last = i;
            dist[i] = i - last;
        }

        // Right to left
        last = n + k;
        for (int i = n - 1; i >= 0; --i) {
            if (nums[i] == key) last = i;
            dist[i] = std::min(dist[i], last - i);
        }

        std::vector<int> res;
        for (int i = 0; i < n; ++i)
            if (dist[i] <= k) res.push_back(i);
        return res;
    }
};
```

---

## 4. Why This Works – The “Good, the Bad, the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) – linear, no nested loops | The naive O(n·k) solution would be unnecessary | O(n·k) would TLE on large tests |
| **Space Complexity** | O(n) – distance array; unavoidable for clear logic | Could be O(1) if you process on the fly but more complex | None, we keep it simple |
| **Readability** | Two clear passes, easy to understand | Might seem “magic” if you skip explanation | Over‑optimised tricks (bit‑mask, fancy DP) make code unreadable |
| **Edge Cases** | Handles `k = n`, all elements equal to key, or no key at all (guaranteed at least one) | None | Not applicable |
| **Maintainability** | Simple comments, standard library usage | None | Over‑engineering can harm maintainability |

---

## 5. Step‑by‑Step Walkthrough (Illustrated)

Consider `nums = [3,4,9,1,3,9,5]`, `key = 9`, `k = 1`.

1. **Forward pass**  
   ```
   i   0 1 2 3 4 5 6
   val 3 4 9 1 3 9 5
   dist 3 2 0 1 2 0 1
   ```
   *Explanation:* The first key is at index 2, so distance at 0 is 2 (2-0). At 3, distance to last key is 1 (3-2).

2. **Backward pass**  
   ```
   i   0 1 2 3 4 5 6
   dist 2 1 0 0 1 0 1
   ```
   *Explanation:* The last key is at 5, so index 6 is distance 1.

3. **Select indices with distance ≤ k**  
   `dist = [2,1,0,0,1,0,1]`, `k=1` → indices `[1,2,3,4,5,6]`.

---

## 6. Testing & Validation

```python
# Python example test
sol = Solution()
assert sol.findKDistantIndices([3,4,9,1,3,9,5], 9, 1) == [1,2,3,4,5,6]
assert sol.findKDistantIndices([2,2,2,2,2], 2, 2) == [0,1,2,3,4]
assert sol.findKDistantIndices([1], 1, 1) == [0]
```

Feel free to adapt the test harness for Java or C++.

---

## 7. SEO‑Optimized Blog Post Outline

Below is a full‑blown article skeleton you can paste into a CMS, complete with meta tags, headings, keyword focus, and a job‑hunting hook.

---

### Title
**“Cracking LeetCode 2200: Find All K‑Distant Indices – A Complete Guide (Java, Python, C++)”**

### Meta Description
> Master LeetCode 2200 with our step‑by‑step solution in Java, Python, and C++. Learn the O(n) algorithm, explore pitfalls, and get interview‑ready. Perfect for software engineers seeking to land their dream job.

### Keywords
- LeetCode 2200
- Find All K‑Distant Indices
- Interview coding problems
- Java solution LeetCode
- Python algorithm LeetCode
- C++ interview questions
- Software engineering interview tips

### H1
**Cracking LeetCode 2200: Find All K‑Distant Indices – The Ultimate Solution Guide**

---

#### Introduction (H2)

- What the problem asks  
- Why it’s common in technical interviews  
- How solving it showcases algorithmic thinking

#### Problem Statement (H3)

> A concise restatement of the problem with examples.

#### Naïve Approach (H3)

- Brute‑force description  
- Time complexity pitfalls  
- Why it fails at scale

#### The Optimal O(n) Strategy (H3)

- Intuition: “distance to the nearest key”  
- Two‑pass algorithm explained with a diagram  
- Pseudocode (for quick mental reference)

#### Code Implementations (H3)

- Java snippet with comments  
- Python snippet with typing hints  
- C++ snippet using STL  

*(Include a small “copy‑paste ready” block for each language)*

#### Complexity Analysis (H3)

- **Time:** O(n)  
- **Space:** O(n) (can be optimized to O(1) with in‑place tricks but readability wins)

#### Edge Cases & Common Mistakes (H3)

- `k` equals array length  
- All elements are the key  
- Key appears only at the ends  

#### Testing Strategy (H3)

- Unit test examples  
- Edge test cases  
- How to integrate with LeetCode’s test harness

#### Interview Tips (H3)

- How to articulate your solution  
- Discuss the trade‑offs between brute force and optimal  
- Demonstrate how you handle constraints in real interviews

#### Bonus: Quick Optimized One‑Liner (H3)

- A concise Python or JavaScript one‑liner (optional)

#### FAQ (H3)

- “Can I use a deque?”  
- “What if k is zero?”  
- “Why isn’t a sliding window enough?”

#### Wrap‑Up & Next Steps (H2)

- Recap of key takeaways  
- Encourage practice on similar “nearest element” problems  
- Link to other LeetCode challenges (e.g., 1344, 1492)

#### Call‑to‑Action (H2)

> “Ready to ace your next interview? Sign up for our free coding interview prep newsletter and get weekly problem walkthroughs straight to your inbox.”

---

### Final Thoughts

- Emphasize that mastering problems like 2200 shows **clean algorithm design** and **problem‑decomposition skills**—exactly what hiring managers want.  
- Highlight that the solution demonstrates familiarity with **array manipulation**, **two‑pass patterns**, and **efficient distance calculations**.

---

## 8. Ready‑to‑Use Boilerplate for Recruiters

If you want to paste the solution directly into your resume or portfolio:

```text
// Java (LeetCode)
public List<Integer> findKDistantIndices(int[] nums, int key, int k) { … }

// Python
def findKDistantIndices(self, nums, key, k): … 

// C++
vector<int> findKDistantIndices(vector<int>& nums, int key, int k) { … }
```

Add a short comment: *“Optimized O(n) algorithm that finds all indices within distance k of a given key in an array.”*

Recruiters love clean, language‑agnostic snippets that show *you can write efficient, production‑ready code*.

---

**Happy coding—and good luck landing that dream software engineering job!**