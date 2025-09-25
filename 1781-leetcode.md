---
title: LeetCode 1781. Sum of Beauty of All Substrings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 1781. Sum of Beauty of All Substrings – Full‑Stack Solution

> **Keywords:** LeetCode 1781, Sum of Beauty, String Substrings, Java, Python, C++, Algorithm Interview, Time Complexity, Space Complexity

---

### 1. Problem Recap

> **Beauty of a string** = (frequency of most common character) – (frequency of least common character that actually appears).  
> **Task:** For a given string `s` (1 ≤ |s| ≤ 500, lowercase letters only), return the sum of beauty values for **all** possible substrings.

---

### 2. Why This Problem Is a Great Interview Exercise

| Good | Bad | Ugly |
|------|-----|------|
| **Clarity** – well‑defined input & output | **Nested loops** can be confusing for beginners | **Off‑by‑one pitfalls** when computing frequencies |
| **Scalability** – 500 length → ~125 000 substrings → < 1 M operations | **O(n³)** naïve solutions are unnecessary | **Large constant factors** if we forget to reuse the frequency array |
| **Multi‑language** – Java, Python, C++ → shows cross‑platform thinking | **Misunderstanding of “least frequent”** (ignore zero counts) | **Integer overflow** (unlikely with 500, but worth noting) |

---

### 3. Brute‑Force Intuition

The most straightforward way is to enumerate every substring, count the frequency of each letter, then compute `max - min`.

```text
for i in 0 .. n-1:            // start index
  for j in i .. n-1:          // end index
    compute freq[26] of s[i..j]
    beauty = max(freq) - min(freq>0)
    total += beauty
```

*Complexity:* `O(n³)` – because we re‑count the same characters many times.  
*Space:* `O(1)` – only a fixed 26‑int array.

This is *acceptable* for `n ≤ 20`, but for `n = 500` it becomes too slow.

---

### 4. Optimal O(n² · 26) Solution

We can **reuse the frequency array** while expanding the substring one character at a time.

```text
for start in 0 .. n-1:
  freq[26] = {0}
  for end in start .. n-1:
    freq[s[end]]++                // O(1) update
    maxFreq = 0
    minFreq = INF
    for k in 0 .. 25:             // scan 26 letters
      if freq[k] > 0:
        maxFreq = max(maxFreq, freq[k])
        minFreq = min(minFreq, freq[k])
    total += maxFreq - minFreq
```

*Why it works:*  
- By resetting `freq` at each new start index, we ensure we only consider the current substring.  
- The inner scan of 26 letters is constant‑time, so the overall complexity becomes `O(n²)`.

*Complexity:*  
- **Time:** `O(n²)` (≈ 125 000 × 26 ≈ 3.2 M ops for `n = 500`) – comfortably fast.  
- **Space:** `O(1)` – fixed 26‑int array.

---

### 5. Code Implementations

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**.  
All three follow the same logic and achieve the same optimal complexity.

---

#### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int beautySum(String s) {
        int n = s.length();
        int total = 0;

        for (int start = 0; start < n; start++) {
            int[] freq = new int[26];          // reset for each start
            for (int end = start; end < n; end++) {
                freq[s.charAt(end) - 'a']++;  // update frequency

                int maxFreq = 0;
                int minFreq = Integer.MAX_VALUE;
                for (int f : freq) {
                    if (f > 0) {
                        if (f > maxFreq) maxFreq = f;
                        if (f < minFreq) minFreq = f;
                    }
                }
                total += maxFreq - minFreq;
            }
        }
        return total;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.beautySum("aabcb"));   // 5
        System.out.println(sol.beautySum("aabcbaa")); // 17
    }
}
```

---

#### 5.2 Python

```python
class Solution:
    def beautySum(self, s: str) -> int:
        n = len(s)
        total = 0

        for start in range(n):
            freq = [0] * 26                 # reset for each start
            for end in range(start, n):
                freq[ord(s[end]) - 97] += 1

                max_f = 0
                min_f = float('inf')
                for f in freq:
                    if f > 0:
                        if f > max_f:
                            max_f = f
                        if f < min_f:
                            min_f = f
                total += max_f - min_f

        return total


# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.beautySum("aabcb"))   # 5
    print(sol.beautySum("aabcbaa")) # 17
```

---

#### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int beautySum(string s) {
        int n = s.size();
        long long total = 0;                    // use long long for safety

        for (int start = 0; start < n; ++start) {
            int freq[26] = {0};                 // reset
            for (int end = start; end < n; ++end) {
                freq[s[end] - 'a']++;

                int maxF = 0, minF = INT_MAX;
                for (int f : freq) {
                    if (f > 0) {
                        if (f > maxF) maxF = f;
                        if (f < minF) minF = f;
                    }
                }
                total += maxF - minF;
            }
        }
        return (int)total;
    }
};

// Demo
int main() {
    Solution sol;
    cout << sol.beautySum("aabcb") << endl;   // 5
    cout << sol.beautySum("aabcbaa") << endl; // 17
}
```

---

### 6. Complexity Breakdown

| Step | Operation | Complexity |
|------|-----------|------------|
| Outer loop (`start`) | n times | O(n) |
| Inner loop (`end`) | n − start times | O(n) |
| Frequency update | O(1) | |
| Scan 26 letters | 26 × (n²) | O(n²) |
| **Total** | | **O(n²)** |
| Extra space | freq[26] | **O(1)** |

---

### 7. Common Pitfalls & How to Avoid Them

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Counting zero frequencies as min** | Some people mistakenly use `minFreq = 0` initially | Skip `freq == 0` when updating min/max |
| **Off‑by‑one in loops** | Forgetting that substring ends at `end` inclusive | Use `end < n` and `s[end]` |
| **Large integer overflow** | Unlikely with 500, but `int` could overflow for larger constraints | Use `long long` (C++) or `long` (Java) if needed |
| **Re‑allocating freq each time** | Minor slowdown | Re‑use a single array and reset values to 0 |

---

### 8. Interview Tips

1. **Explain your reasoning** – Talk through why scanning 26 letters is cheap and how the algorithm stays `O(n²)`.  
2. **Mention the edge case** – When all characters are the same, beauty is always 0; our algorithm naturally handles it.  
3. **Discuss alternatives** – Mention prefix sums or sliding windows, but explain why they are unnecessary here.  
4. **Show your code quickly** – Even if you write pseudocode on paper, be ready to translate it into the language requested.  

---

### 9. Conclusion

> **Sum of Beauty of All Substrings** is a deceptively simple problem that showcases a neat trick: **reuse a small frequency table while expanding substrings**.  
> The optimal solution runs in `O(n²)` time and `O(1)` space, making it perfect for both online judges (LeetCode, HackerRank) and live coding interviews.

Good luck – and remember: the *beauty* of your solution lies in its **simplicity, efficiency, and cross‑language clarity**! 🚀