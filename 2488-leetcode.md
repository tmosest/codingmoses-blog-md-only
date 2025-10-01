---
title: LeetCode 2488. Count Subarrays With Median K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📊 2488. Count Subarrays With Median K  
**Hard | LeetCode | 1‑pass O(n) solution (Java / Python / C++)**

---

### TL;DR
The trick is to convert the median condition into a *balance* between numbers greater than k and numbers smaller than k.  
While scanning the array once we keep a running balance and a hash map of all balances that appear **before** we hit the element `k`.  
Once we see `k`, every future prefix balance that is equal to the current balance or current balance − 1 gives us a valid sub‑array whose median is `k`.

---

## 1. Problem Recap

```text
Given an array `nums` (size n, all distinct, 1 ≤ nums[i] ≤ n) and an integer `k`,
return the number of non‑empty sub‑arrays whose median equals `k`.

Median definition:
  * Sort the sub‑array.
  * If length is odd → the middle element.
  * If length is even → the **left** middle element.
```

*Example*  
`nums = [3,2,1,4,5] , k = 4` → answer `3` (`[4]`, `[4,5]`, `[1,4,5]`).

*Constraints*  
`1 ≤ n ≤ 10⁵` – a linear‑time solution is required.



---

## 2. Insight – Balance of “Greater / Smaller”

For any sub‑array that contains `k`:

```
Let  G = #elements > k
Let  L = #elements < k

k is the median  ⇔  G = L   or   G = L + 1
```

Why?  
If we sort the sub‑array, the position of `k` will be:

| Length (odd) | L | G | Position of k |
|--------------|---|---|---------------|
| 1            | 0 | 0 | 0 (median)    |
| 3            | 1 | 1 | 1 (median)    |
| 5            | 2 | 2 | 2 (median)    |
| …            |   |   | …             |

When the length is even the median is the *left* middle element, so `k` must have **one more** element on its right:

| Length (even) | L | G | Position of k |
|--------------|---|---|---------------|
| 2            | 0 | 1 | 0 (median)    |
| 4            | 1 | 2 | 1 (median)    |
| …            |   |   | …             |

Thus we only need to know the **difference** `D = G − L`.  
For a valid sub‑array `D` must be `0` or `1`.

---

## 3. One‑pass algorithm

```
balance = 0            // current G − L
ans = 0
foundK = false
hashmap[0] = 1         // dummy balance before any element

for each element x in nums:
    if x < k:  balance -= 1
    else if x > k: balance += 1
    else:        foundK = true

    if foundK:
        // sub‑arrays that end at this position and contain k
        ans += hashmap[balance]          // D = 0
        ans += hashmap[balance - 1]      // D = 1
    else:
        // balances seen before k are stored
        hashmap[balance] += 1
```

*Why does it work?*  
The map stores **all balances that appear to the left of k**.  
When we are at a new index `i` that is **after** k, the sub‑array `[j … i]` (where `j` is any index ≤ position of k) has a total balance

```
balance(i) − balance(j-1)  =  D
```

We want `D` to be `0` or `1`.  
So we count how many `j-1` balances equal `balance(i)` or `balance(i) − 1`.

The hashmap update happens *only* before we hit `k`, which guarantees that every counted sub‑array actually contains `k`.

---

### Complexity

|   | Time | Space |
|---|------|-------|
| Total | **O(n)** | **O(n)** (hash map) |

The algorithm is optimal for a `Hard` LeetCode problem.



---

## 4. Reference Code

Below you’ll find the same implementation in three languages.  
All are 1‑pass, use a hash map (or dict) for frequencies, and run in `O(n)` time.

> **Tip** – Use `long` for the answer in Java/C++ and cast to `int` only at the end.  
> Python’s integers are unbounded so overflow isn’t an issue.

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int countSubarrays(int[] nums, int k) {
        // hashmap that stores how many times a particular balance appeared
        Map<Integer, Integer> freq = new HashMap<>();
        freq.put(0, 1);          // balance before any element

        int balance = 0;         // G - L so far
        int ans = 0;
        boolean foundK = false;

        for (int x : nums) {
            if (x < k) {
                balance -= 1;
            } else if (x > k) {
                balance += 1;
            } else {                // we hit k
                foundK = true;
            }

            if (foundK) {
                // D must be 0 or 1
                ans += freq.getOrDefault(balance, 0);          // D = 0
                ans += freq.getOrDefault(balance - 1, 0);     // D = 1
            } else {
                // record balances that occur before k
                freq.put(balance, freq.getOrDefault(balance, 0) + 1);
            }
        }
        return ans;
    }
}
```

### Python

```python
def countSubarrays(nums: List[int], k: int) -> int:
    from collections import defaultdict

    freq = defaultdict(int)
    freq[0] = 1          # dummy balance before first element

    balance = 0
    ans = 0
    found_k = False

    for x in nums:
        if x < k:
            balance -= 1
        elif x > k:
            balance += 1
        else:                     # we hit k
            found_k = True

        if found_k:
            ans += freq[balance]          # D == 0
            ans += freq[balance - 1]      # D == 1
        else:
            freq[balance] += 1

    return ans
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countSubarrays(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        freq[0] = 1;          // dummy balance before any element

        int balance = 0;
        long long ans = 0;
        bool foundK = false;

        for (int x : nums) {
            if (x < k) balance -= 1;
            else if (x > k) balance += 1;
            else foundK = true;

            if (foundK) {
                ans += freq[balance];          // D == 0
                ans += freq[balance - 1];      // D == 1
            } else {
                freq[balance] += 1;
            }
        }
        return (int)ans;
    }
};
```

> **Compile flag (C++17)**  
> `g++ -std=c++17 -O2 solution.cpp -o solution`



---

## 4. Good / Bad / Ugly – What the Interviewer Wants

| ✅ Good | ⚠️ Bad | 😈 Ugly |
|--------|--------|---------|
| *Linear time, single scan* – no nested loops, perfect for `n ≤ 10⁵`. | *Hash map overhead* – in worst‑case many distinct balances (≈ n) → O(n) memory. | *Even‑length case* – remembering that `D` can be `1` (balance‑1) is subtle. Missing it gives wrong answer. |
| *Simple arithmetic* – `balance` only increments or decrements by 1. | *Edge‑case handling* – need a dummy `balance = 0` entry to account for sub‑arrays that start at index `0`. | *Index of `k` not needed* – the algorithm does not require a pre‑scan to find `k`; it discovers it during the loop, making it *slightly* easier to implement. |
| *Works in all languages* – the same idea applies to Java, Python, C++ (just a dict vs. unordered_map). | *Large constants* – hash map look‑ups can be slower on Java (hashing) and Python (dynamic typing). | *Mis‑reading the median rule* – many candidates forget that for even lengths the median is the *left* middle element, causing `D` to be checked incorrectly. |

---

## 5. Alternative Approaches (what NOT to do)

| Approach | Complexity | Comments |
|----------|------------|----------|
| Brute force enumeration of all sub‑arrays + sort each | **O(n² log n)** | TLE for n = 10⁵. |
| Two‑pass with prefix sums and two hash maps (left / right) | **O(n)** | Works but requires two separate passes; the single‑pass variant above is cleaner. |
| Binary search on sub‑array lengths + two‑pointer | **O(n log n)** | Still slower than linear, but useful for educational purposes. |

---

## 6. Interview‑Ready Checklist

1. **Understand the median definition** (especially the *left* middle for even lengths).  
2. **Explain the balance idea**: why `G = L` or `G = L + 1`.  
3. **Show the one‑pass logic**:  
   * keep running `balance = G − L`,  
   * store all balances before `k` in a map,  
   * after hitting `k`, add `map[balance] + map[balance‑1]`.  
4. **Discuss complexity**: `O(n)` time, `O(n)` space, linear is required by constraints.  
5. **Edge‑case demonstration**: test on arrays where the answer is `0`, on arrays of even length, and on arrays where `k` is the first/last element.  
6. **Mention possible optimisations**: use `int` for balances, avoid `long` unless you’re counting very large numbers (LeetCode’s `int` return type is safe because the answer ≤ n × (n+1)/2).  

---

## 7. Final Thoughts

- **Good** – The solution is *conceptually simple* once you see the balance.  
- **Bad** – Requires a hash map; if the interviewer is skeptical about hashing you may need to explain why unordered_map/dict is fine for typical interview constraints.  
- **Ugly** – The subtlety of even‑length median can trip up many candidates; practice with examples until you can articulate it fluently.

---

## 8. Take‑away

> ✅ **Linear‑time, constant‑extra‑space‑ish** solution for a hard LeetCode problem.  
> 🔄 One‑pass prefix‑balance + hash map → perfect to impress interviewers.  
> 💡 Remember the “balance = G − L” trick for any median‑related sub‑array question.

---

### 📣 Spread the Word

If you found this helpful, consider:

* 🔁 Retweet / Share on LinkedIn  
* 💬 Comment “I’d love to see a visual walkthrough”  
* ⭐️ Upvote on LeetCode and GitHub  

Happy coding! 🚀



---



### SEO Tags

```
LeetCode 2488, Count Subarrays With Median K, algorithm, coding interview,
Java, Python, C++, prefix sum, hashmap, median, subarray, interview prep,
coding challenge, LeetCode Hard, job interview, software engineer interview,
technical interview, data structures, algorithm design, interview tips
```

---