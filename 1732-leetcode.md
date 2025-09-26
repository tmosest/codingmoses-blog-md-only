---
title: LeetCode 1732. Find the Highest Altitude - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔍 Mastering LeetCode 1732 – *Find the Highest Altitude*  
**Java, Python & C++ Solutions | SEO‑Optimized Interview Guide**  

---

### 📌 Problem Statement (LeetCode 1732)

> A biker starts at point 0 with altitude 0.  
> For an array `gain` of length `n`, `gain[i]` is the net altitude change between point `i` and `i+1`.  
> **Return the highest altitude the biker reaches.**

| Example | Input | Output | Altitudes |
|---------|-------|--------|-----------|
| 1 | `[-5,1,5,0,-7]` | `1` | `[0,-5,-4,1,1,-6]` |
| 2 | `[-4,-3,-2,-1,4,3,2]` | `0` | `[0,-4,-7,-9,-10,-6,-3,-1]` |

**Constraints**

| Property | Value |
|----------|-------|
| `n == gain.length` | 1 ≤ n ≤ 100 |
| `-100 ≤ gain[i] ≤ 100` | |  

---

### 🎯 Why This Problem Rocks for Interview Prep

| Strength | Reason |
|----------|--------|
| **Easy** | Ideal for quick confidence‑building |
| **Linear Scan** | Reinforces O(n) time & O(1) space patterns |
| **Cumulative Sum** | Classic prefix‑sum trick – a common interview theme |
| **Negative Numbers** | Shows that max can stay at zero (important edge case) |

---

## 🧠 Intuition & Approach

1. **Track current altitude** while iterating through `gain`.
2. **Keep a running maximum** of the altitude seen so far.
3. Return the maximum after the loop ends.

> This is a pure *running sum* problem.  
> No extra data structures, just two integers.

---

## ✅ Implementation (Java, Python, C++)

Below are clean, production‑ready solutions in the three most popular interview languages. Each solution runs in **O(n)** time and **O(1)** additional space.

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public int largestAltitude(int[] gain) {
        int maxAltitude = 0;   // start at point 0
        int current = 0;

        for (int delta : gain) {
            current += delta;           // update current altitude
            maxAltitude = Math.max(maxAltitude, current);
        }
        return maxAltitude;
    }

    // Quick test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.largestAltitude(new int[]{-5,1,5,0,-7})); // 1
        System.out.println(s.largestAltitude(new int[]{-4,-3,-2,-1,4,3,2})); // 0
    }
}
```

### 2️⃣ Python

```python
class Solution:
    def largestAltitude(self, gain: List[int]) -> int:
        max_alt = 0
        current = 0
        for delta in gain:
            current += delta
            max_alt = max(max_alt, current)
        return max_alt

# Demo
if __name__ == "__main__":
    s = Solution()
    print(s.largestAltitude([-5, 1, 5, 0, -7]))  # 1
    print(s.largestAltitude([-4, -3, -2, -1, 4, 3, 2]))  # 0
```

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int largestAltitude(vector<int>& gain) {
        int maxAlt = 0, cur = 0;
        for (int d : gain) {
            cur += d;
            maxAlt = max(maxAlt, cur);
        }
        return maxAlt;
    }
};

// Simple main for quick testing
int main() {
    Solution s;
    vector<int> g1 = {-5, 1, 5, 0, -7};
    vector<int> g2 = {-4, -3, -2, -1, 4, 3, 2};
    cout << s.largestAltitude(g1) << endl; // 1
    cout << s.largestAltitude(g2) << endl; // 0
}
```

---

## 🔍 The Good, The Bad, & The Ugly

| Category | What It Means | How to Handle in Interviews |
|----------|---------------|-----------------------------|
| **Good** | • **Time Complexity**: O(n) (linear). <br>• **Space Complexity**: O(1). <br>• Straight‑forward logic. | Emphasise that it’s a classic *running sum* pattern. Mention that it’s often used for “prefix sum” interview questions. |
| **Bad** | • All numbers can be negative → max altitude may stay at 0. <br>• The name “gain” can mislead beginners into thinking you need to sort or use a priority queue. | Clarify the initial altitude is 0, and you never *add* the starting point to the array. |
| **Ugly** | • Off‑by‑one errors: forgetting that the array length is `n`, not `n+1`. <br>• Over‑engineering: using a prefix‑sum array or list. | Show a concise loop; talk about why you can keep a single integer for the current altitude. |

---

## 🚀 Time & Space Complexity

| Complexity | Explanation |
|------------|-------------|
| **Time** | `O(n)` – single pass through `gain`. |
| **Space** | `O(1)` – only two integer variables (`current`, `maxAltitude`). |

---

## 🔧 Edge Cases to Test

| Edge | Reason | Expected Result |
|------|--------|-----------------|
| All positives | Highest altitude grows monotonically | Sum of all elements |
| All negatives | Highest altitude remains 0 | 0 |
| Mixed with zeros | No effect on altitude | Correct max |
| Single element | Minimal input size | `max(0, gain[0])` |
| Large positive/negative range | Ensures no overflow in 32‑bit int | Handle with `long` if needed (LeetCode’s constraints keep it safe) |

---

## 📚 Interview Tips

1. **Explain the intuition first** – talk about running sum and tracking max.  
2. **Mention time & space** before diving into code.  
3. **Ask clarifying questions**: “Is the initial altitude always 0?” “Can the array contain negative values?”  
4. **Walk through an example** in the interview to show the state changes.  
5. **Talk about edge cases** – especially all negatives or a single element.  

---

## 🎯 Takeaway

- **LeetCode 1732** is a *quick win* that demonstrates mastery of prefix sums and linear scans.  
- Mastering this problem showcases your ability to solve *clean, O(n) time, O(1) space* challenges—an attractive skill set for software engineering interviews.  
- By delivering concise, readable Java, Python, and C++ implementations, you’ll impress recruiters who value language versatility.

Good luck on your next coding interview! 🚀

--- 

**Keywords**: LeetCode 1732, Find the Highest Altitude, Java solution, Python solution, C++ solution, running sum, prefix sum, interview coding question, algorithm interview, software engineer interview, coding interview prep.