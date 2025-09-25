---
title: LeetCode 1764. Form Array by Concatenating Subarrays of Another Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap (LeetCode 1764)

> **Form Array by Concatenating Subarrays of Another Array**  
> Given a 2‑D array `groups` and an array `nums`, can you pick `n` *disjoint* sub‑arrays from `nums` so that the *i‑th* sub‑array equals `groups[i]` and the order is preserved?  
> Return **true** if it is possible, otherwise **false**.

* `1 ≤ groups.length ≤ 10³`  
* `1 ≤ groups[i].length, sum(groups[i].length) ≤ 10³`  
* `1 ≤ nums.length ≤ 10³`  
* Elements fit in a 32‑bit signed integer.

---

## 2.  Core Idea

We need to scan `nums` once and try to locate each group in order.  
A simple *two‑pointer sliding window* suffices:

1. Keep an index `pos` pointing to the next unchecked element in `nums`.
2. For the current `group` try to match it character‑by‑character:
   * If the current element matches, advance both pointers.
   * If a mismatch occurs, roll back `pos` to the next element after the original start of this window and restart matching the current group.
3. If we successfully match a group, `pos` becomes the index just **after** the matched sub‑array.  
   Continue with the next group.

If at any point a group can’t be found, we return `false`.  
Otherwise, if all groups are matched, return `true`.

The algorithm runs in `O(|nums| * |groups|)` time (worst‑case when every group is long and matches almost everywhere) but with the given limits it is fast enough.  
Memory usage is `O(1)` beyond the input arrays.

---

## 3.  Reference Implementations  

### 3.1 Java – Sliding Window

```java
import java.util.*;

public class Solution {

    // Return the index in nums right after the matched group.
    // If not found, return -1.
    private int findGroup(int[] group, int[] nums, int start) {
        int n = nums.length;
        int m = group.length;
        int i = start, j = 0;

        while (i < n) {
            if (nums[i] == group[j]) {
                i++; j++;
                if (j == m) return i;          // group fully matched
            } else {
                // roll back i to the element after the original start
                i = i - j + 1;
                j = 0;
            }
        }
        return -1;   // group not found
    }

    public boolean canChoose(int[][] groups, int[] nums) {
        int pos = 0;
        for (int[] group : groups) {
            pos = findGroup(group, nums, pos);
            if (pos == -1) return false;
        }
        return true;
    }

    // --- main for quick sanity check ---
    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] groups1 = {{1,-1,-1},{3,-2,0}};
        int[] nums1 = {1,-1,0,1,-1,-1,3,-2,0};
        System.out.println(s.canChoose(groups1, nums1)); // true
    }
}
```

### 3.2 Python – Sliding Window

```python
class Solution:
    def canChoose(self, groups: List[List[int]], nums: List[int]) -> bool:
        pos = 0  # next free index in nums
        for group in groups:
            pos = self._find_group(group, nums, pos)
            if pos == -1:
                return False
        return True

    def _find_group(self, group: List[int], nums: List[int], start: int) -> int:
        n, m = len(nums), len(group)
        i, j = start, 0
        while i < n:
            if nums[i] == group[j]:
                i += 1
                j += 1
                if j == m:            # whole group matched
                    return i
            else:
                i = i - j + 1          # roll back
                j = 0
        return -1
```

### 3.3 C++ – Sliding Window

```cpp
class Solution {
public:
    int findGroup(const vector<int>& group, const vector<int>& nums, int start) {
        int n = nums.size(), m = group.size();
        int i = start, j = 0;
        while (i < n) {
            if (nums[i] == group[j]) {
                ++i; ++j;
                if (j == m) return i;           // matched completely
            } else {
                i = i - j + 1;   // rollback
                j = 0;
            }
        }
        return -1; // not found
    }

    bool canChoose(vector<vector<int>>& groups, vector<int>& nums) {
        int pos = 0;
        for (auto &g : groups) {
            pos = findGroup(g, nums, pos);
            if (pos == -1) return false;
        }
        return true;
    }
};
```

---

## 4.  Alternative: KMP (O(n + m) per group)

> **When you need absolute speed** (e.g. `nums.length` up to `10⁵`), replace the inner linear scan with the Knuth–Morris–Pratt algorithm:
> 1. Build the *Longest Proper Prefix which is also Suffix* (LPS) array for `group`.
> 2. Scan `nums` from `pos` using the standard KMP search.
> 3. Return the index after the match or `-1`.

KMP guarantees that each element of `nums` is examined at most twice per group, making the total complexity `O(|nums| * #groups)` still, but with a very small constant.  
The sliding‑window solution is often simpler to read and already fast enough for the constraints of this LeetCode problem.

---

## 5.  Blog Article – “Mastering LeetCode 1764: The Good, The Bad, and The Ugly”

> **Keywords**: LeetCode 1764, form array by concatenating subarrays, two‑pointer technique, KMP algorithm, interview prep, coding interview, algorithm explanation, Python solution, Java solution, C++ solution, disjoint subarrays, time complexity, space complexity

---

### 5.1  Introduction

> In the world of coding interviews, problems that test your *array manipulation* and *sub‑array matching* skills are a common sight. One such challenge is **LeetCode 1764 – “Form Array by Concatenating Subarrays of Another Array.”**  
> Today, we’ll dissect this problem, walk through an elegant sliding‑window solution, touch on a more optimal KMP variant, and uncover the hidden pitfalls that can trip up even seasoned developers.

---

### 5.2  Problem Statement (in Plain English)

You’re given:

* `groups` – a list of *n* small arrays.  
* `nums`   – a longer array.

You must decide whether you can **pick `n` non‑overlapping segments of `nums`** such that:

1. The *i‑th* segment is *exactly* `groups[i]`.  
2. The segments appear in the same order as the groups.  

If you can do that, return `true`; otherwise, `false`.

---

### 5.3  Constraints & Why They Matter

| Parameter | Limits |
|-----------|--------|
| `groups.length` | 1 … 1 000 |
| `groups[i].length` | 1 … 1 000 |
| `sum(groups[i].length)` | ≤ 1 000 |
| `nums.length` | 1 … 1 000 |

These numbers tell us:

* The whole problem is **small enough** for an \(O(n^2)\) solution to pass comfortably.  
* The input arrays fit in memory with **constant auxiliary space**.  
* However, an interview may ask you to *scale* the algorithm, so it’s good to know the more efficient KMP option.

---

### 5.4  The “Good” – A Clean Sliding‑Window Solution

The core idea: **scan once, greedily match each group, then move on.**

#### 5.4.1  Step‑by‑Step

1. Keep a pointer `pos` into `nums` – the first unchecked element.
2. For each `group`:
   * Attempt to match it starting at `pos`.  
     * If a character matches, move both pointers.  
     * If a mismatch, *rollback* `pos` to the element just after the original start of this group and restart.
   * If you reach the end of the group, `pos` now points just *after* the matched segment.
3. If any group can’t be matched, immediately return `false`.

Because we **never backtrack** across already matched groups, the algorithm is linear in `nums` for each group. With at most 1 000 groups, the total work stays tiny.

#### 5.4.2  Code (Java)

*(see the Java implementation above)*

#### 5.4.3  Time & Space

* **Time**: \(O(|nums| \times |groups|)\) in the worst case, but bounded by `1 000 * 1 000` ≈ 1 million operations – well under the 1‑second limit.  
* **Space**: \(O(1)\) extra (only a few indices).

---

### 5.5  The “Bad” – Common Mistakes

| Mistake | Why it Fails | Fix |
|---------|--------------|-----|
| Using *nested loops* that restart from the beginning of `nums` for every group | O(n²) with large `nums`, but more importantly it ignores the *order* requirement. | Maintain a moving pointer (`pos`) and never re‑start at `0`. |
| Assuming groups are unique or that the whole array can be partitioned | Groups may overlap in values, and you can select a sub‑array that contains multiple groups (as in the example). | Treat each group independently; the algorithm automatically ensures disjointness. |
| Not handling the **edge** when a group is at the very end of `nums` | You may index out of bounds. | The while loop checks `i < n`; on mismatch you rollback, never exceeding array bounds. |

---

### 5.6  The “Ugly” – Edge Cases That Survive Even the Good Solution

| Ugly Scenario | What Happens | What to Watch |
|---------------|--------------|---------------|
| `groups` contains a *sub‑array that is itself a concatenation of two smaller groups* | The greedy approach may grab the whole thing for the first group, leaving nothing for the second. | Since the algorithm matches groups **exactly**, it won’t inadvertently merge them. |
| `nums` is filled with a repeating pattern (e.g. all 1’s) | The inner matching will keep rolling back a lot, potentially leading to the \(O(n \times m)\) worst case. | Accept that this is still fast for the constraints, but if you’re pushed to bigger data, switch to KMP. |
| Using `String` concatenation or converting arrays to strings | This introduces huge overhead and can lead to wrong comparisons because of number formatting (leading zeros, negative signs). | Operate on integer arrays directly. |

---

### 5.7  When Speed Is the Question – KMP

If you’re interviewing for a role that demands **linear‑time string matching** (e.g. *searching for a DNA motif in a genome*), the **Knuth–Morris–Pratt** algorithm is a must‑know.

* Build an LPS array for each group once – \(O(|group|)\).  
* Run the classic KMP search over `nums` starting from `pos`.  
* You’ll still only touch each element of `nums` a handful of times.

**Why still linear?** Because the *order* constraint lets you treat each group as a separate pattern; the overall complexity remains \(O(|nums| \times #groups)\) but with a far smaller constant than the naive scan.

*(Pseudo‑code for KMP is omitted for brevity but is readily available on LeetCode discussion pages.)*

---

### 5.7  Bottom‑Line Takeaway

* **Sliding‑window** is the *go‑to* solution for LeetCode 1764.  
* **KMP** is the *scalable* upgrade for interviewers who love big input sizes.  
* Avoid restarting scans from the beginning, always track the current position, and remember that *order* + *disjointness* are the two pillars of the problem.

---

### 5.8  Practice Problems

1. **LeetCode 1448 – “Count Good Ways to Choose a Line of Students”** – similar array partitioning with constraints.  
2. **LeetCode 438 – “Find All Anagrams in a String”** – a classic KMP/Kadane hybrid.  
3. **LeetCode 1048 – “Shortest Path with Alternating Colors”** – practice maintaining multiple pointers across graph traversals.

---

### 5.9  Conclusion

> Mastering LeetCode 1764 is more than solving a single problem; it’s about **understanding how array pointers behave**, recognizing pitfalls, and appreciating the power of classic string‑matching algorithms.  
> By combining a clean sliding‑window solution with the optional KMP twist, you’ll be ready to tackle the problem in any interview setting – from a 1 000‑element dataset to a massive real‑world dataset.  

Happy coding, and keep solving!

---

## 6.  Closing Notes

* Feel free to adapt the reference code to your preferred language.  
* For real interview prep, practice tracing through the algorithm on paper – this helps catch the rolling‑back logic.  
* Keep the discussion of KMP handy – it demonstrates your knowledge of *time‑optimal pattern matching* and can earn you extra points in a technical interview.

--- 

> **End of Blog**.