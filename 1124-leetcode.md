---
title: LeetCode 1124. Longest Well-Performing Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 1124 – *Longest Well‑Performing Interval*  
*(Java | Python | C++) – Good, the Bad, and the Ugly – Your Path to a Dream Job*  

---

## 1️⃣ Problem Recap (SEO‑ready intro)

> **LeetCode 1124 – Longest Well‑Performing Interval**  
> *Difficulty*: Medium  
> *Tag*: `Array`, `HashMap`, `Prefix Sum`, `Two Pointers`  

You’re given an integer array `hours[]` where each element is the number of hours worked on a particular day.  
- A **tiring day** = `hours[i] > 8`.  
- A **well‑performing interval** = sub‑array where **tiring days > non‑tiring days**.  

**Goal**: Return the length of the longest well‑performing interval.

> **Why this matters for job interviews**  
> 1. Demonstrates mastery of *prefix sums* and *hash maps*.  
> 2. Shows how to transform a problem into a simpler “balance” problem (tiring = +1, non‑tiring = –1).  
> 3. Highlights O(n) time and O(n) space – interviewers love linear solutions.  

---

## 2️⃣ The “Good” – An Elegant O(n) HashMap Solution  

The classic trick is to map each day to **+1** (tiring) or **–1** (not tiring) and keep a running prefix sum.  
If at any point the prefix sum is positive, we’ve already seen a well‑performing interval that starts at index 0.  
If the prefix sum isn’t positive, we look for the earliest index where the sum was **(currentSum‑1)**.  
The distance between those indices gives the longest interval ending at the current day.

| Concept | Code |
|---------|------|
| Prefix Sum (tiring→+1, not tiring→–1) | `sum += hours[i] > 8 ? 1 : -1;` |
| First occurrence map | `Map<Integer,Integer> firstIdx = new HashMap<>();` |
| Update answer | `ans = Math.max(ans, i - firstIdx.get(sum-1));` |

### Java Implementation (HashMap O(n))

```java
import java.util.*;

class Solution {
    public int longestWPI(int[] hours) {
        if (hours.length == 0) return 0;

        int maxLen = 0;
        Map<Integer, Integer> firstIdx = new HashMap<>();
        int sum = 0;   // running balance

        for (int i = 0; i < hours.length; i++) {
            sum += hours[i] > 8 ? 1 : -1;

            // Record the first time we see this sum
            firstIdx.putIfAbsent(sum, i);

            // If the sum > 0, the interval starts from 0
            if (sum > 0) {
                maxLen = i + 1;
            } else {
                // Look for an earlier index where sum was (sum-1)
                Integer j = firstIdx.get(sum - 1);
                if (j != null) {
                    maxLen = Math.max(maxLen, i - j);
                }
            }
        }
        return maxLen;
    }
}
```

**Complexities**  
- **Time**: `O(n)` – one pass, constant‑time hashmap ops.  
- **Space**: `O(n)` – storing first occurrences of each sum.  

---

## 3️⃣ The “Python” – Fast and Readable

```python
class Solution:
    def longestWPI(self, hours: List[int]) -> int:
        first = {}            # sum -> earliest index
        s = 0
        ans = 0

        for i, h in enumerate(hours):
            s += 1 if h > 8 else -1

            if s not in first:
                first[s] = i

            if s > 0:
                ans = i + 1
            else:
                j = first.get(s - 1)
                if j is not None:
                    ans = max(ans, i - j)
        return ans
```

*Why Python is great for interview prep:*  
- Clear syntax (`s += 1 if h > 8 else -1`).  
- Built‑in dictionary makes the hashmap logic trivial.

---

## 4️⃣ The “C++” – Speedy and Idiomatic

```cpp
class Solution {
public:
    int longestWPI(vector<int>& hours) {
        unordered_map<int, int> first;   // sum -> first index
        int sum = 0, ans = 0;

        for (int i = 0; i < hours.size(); ++i) {
            sum += (hours[i] > 8) ? 1 : -1;

            if (!first.count(sum))
                first[sum] = i;

            if (sum > 0) {
                ans = i + 1;
            } else {
                auto it = first.find(sum - 1);
                if (it != first.end())
                    ans = max(ans, i - it->second);
            }
        }
        return ans;
    }
};
```

*Why C++ matters:*  
- `unordered_map` gives average‑case O(1) lookups.  
- Compile‑time safety and fast execution – useful for performance‑critical interviews.

---

## 5️⃣ The “Bad” – Common Pitfalls & How to Avoid Them  

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **O(n²) two‑pointer scan** | Quadratic time, TLE on 10⁴ array | Replace with prefix‑sum + hashmap |
| **Using `HashMap.put(sum, i)` every time** | Overwrites first occurrence, loses earliest index | Use `putIfAbsent` / `count` guard |
| **Mis‑interpreting “tiring”** | Off‑by‑one errors (>=8 instead of >8) | Double‑check condition (`h > 8`) |
| **Ignoring zero‑length answer** | Return negative when no interval found | Return `0` if `ans == 0` |
| **Using `sum-1` incorrectly** | Fails when `sum` never reached `-1` | Check map existence before subtracting |

> **Tip**: Always run the sample cases plus edge cases (all tiring, all non‑tiring, alternating pattern) before submitting.

---

## 6️⃣ The “Ugly” – Advanced Tweaks & Edge Cases  

1. **Space‑Optimized Without HashMap**  
   - Use a monotonic decreasing stack of indices to track prefix sums.  
   - Still `O(n)` time but `O(n)` stack space; works if you want to avoid hashing overhead.

2. **Handling Large Input Constraints**  
   - Prefix sums can be negative up to `-n`.  
   - In Java, an `int` is fine (`n ≤ 10⁴`).  
   - For `n` up to `10⁶`, consider using `long` for safety.

3. **Parallelization**  
   - Not necessary for LeetCode but can be useful in real‑world data pipelines.

4. **Testing Framework**  
   - Build a quick JUnit or PyTest harness to validate thousands of random test cases automatically.

---

## 7️⃣ SEO‑Optimized Blog Summary  

### Title  
> **Crack LeetCode 1124: Longest Well‑Performing Interval – Java, Python, C++ Solutions + Interview Tips**

### Meta Description  
> Master LeetCode 1124 with our complete guide: Java HashMap O(n) solution, Python & C++ implementations, pitfalls, and job‑interview strategies.

### Headings & Keywords  

| Heading | Keywords |
|---------|----------|
| ✅ Problem Recap | “LeetCode 1124”, “Longest Well‑Performing Interval” |
| 🛠️ The Good | “HashMap solution”, “prefix sum”, “O(n) time” |
| 🐍 Python Version | “Python solution”, “dictionary”, “LeetCode 1124” |
| ⚙️ C++ Version | “C++ solution”, “unordered_map”, “competitive programming” |
| ❌ Common Mistakes | “LeetCode time limit”, “hash map pitfalls” |
| 🎯 Job Interview Edge | “coding interview”, “algorithm interview questions” |

### Closing Call‑to‑Action  
> Ready to ace your next coding interview? Drop a comment below with your own solution or ask any questions. Don’t forget to hit **Subscribe** for more LeetCode walkthroughs that land you the job you want!

---

## 8️⃣ Final Takeaway

- **Good**: O(n) hashmap + prefix sum solution – simple, fast, interview‑friendly.  
- **Bad**: Overcomplicating with quadratic scans or misusing hash maps – avoid!  
- **Ugly**: Rare edge cases, but with a clear plan you’ll never trip over them.  

With these implementations and tips, you’re now equipped to impress recruiters on LeetCode, GitHub, or in your next interview. Happy coding! 🚀