---
title: LeetCode 3325. Count Substrings With K-Frequency Characters I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Leetcode 3325 – Count Substrings With K‑Frequency Characters I  
**Solution in Java / Python / C++**  
**Time:**  `O(n)` **Space:** `O(1)`  

> **Problem**  
> Given a string `s` (only lowercase English letters) and an integer `k` (1 ≤ k ≤ |s|), count the number of substrings of `s` in which *at least one* character appears **at least `k` times**.  

> **Examples**  
> *Input* `s = "abacb"`, `k = 2` → **Output:** `4`  
> *Input* `s = "abcde"`, `k = 1` → **Output:** `15` (every substring is valid)

> **Constraints**  
> * 1 ≤ `s.length` ≤ 3000  
> * `k` ≤ `s.length`  
> * `s` contains only lowercase letters (`a`‑`z`)

The sliding‑window trick below turns a seemingly “count‑characters” problem into a linear‑time scan.

---

## 2.  Core Idea – Sliding Window on “Too Many” Characters

1. **Total substrings** of a string of length `n` = `(n + 1) * n / 2`.  
2. If a substring has **no** character with frequency ≥ k, it is *invalid*.  
3. We maintain a sliding window `[l, r]` such that **no** character inside the window occurs `k` or more times.  
4. For every right end `r`, the number of *invalid* substrings ending at `r` is exactly `r - l + 1`.  
5. Subtract this from the total to get the answer.

The window shrinks only when the character at `s[r]` reaches a frequency of `k`.  
Because each character is added and removed at most once, the whole algorithm runs in `O(n)`.

---

## 3.  Implementation

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public int numberOfSubstrings(String s, int k) {
        int n = s.length();
        // All substrings minus the "bad" ones
        int result = (n + 1) * n / 2;
        int[] freq = new int[26];          // frequency of each letter
        int left = 0;                      // left pointer of the window

        for (int right = 0; right < n; right++) {
            char c = s.charAt(right);
            freq[c - 'a']++;

            // Shrink window while we have a character with >= k occurrences
            while (freq[c - 'a'] >= k) {
                freq[s.charAt(left) - 'a']--;
                left++;
            }

            // All substrings ending at 'right' that are *bad*
            result -= (right - left + 1);
        }
        return result;
    }

    // For quick manual testing
    public static void main(String[] args) {
        System.out.println(new Solution().numberOfSubstrings("abacb", 2)); // 4
        System.out.println(new Solution().numberOfSubstrings("abcde", 1)); // 15
    }
}
```

### 3.2 Python

```python
from collections import Counter

class Solution:
    def numberOfSubstrings(self, s: str, k: int) -> int:
        n = len(s)
        result = (n + 1) * n // 2
        freq = Counter()
        left = 0

        for right, ch in enumerate(s):
            freq[ch] += 1
            while freq[ch] >= k:
                freq[s[left]] -= 1
                left += 1
            result -= (right - left + 1)

        return result


# Quick tests
if __name__ == "__main__":
    print(Solution().numberOfSubstrings("abacb", 2))  # 4
    print(Solution().numberOfSubstrings("abcde", 1))  # 15
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numberOfSubstrings(string s, int k) {
        int n = s.size();
        long long res = 1LL * (n + 1) * n / 2;          // total substrings
        vector<int> freq(26, 0);
        int left = 0;

        for (int right = 0; right < n; ++right) {
            int idx = s[right] - 'a';
            ++freq[idx];

            while (freq[idx] >= k) {
                --freq[s[left] - 'a'];
                ++left;
            }

            res -= (right - left + 1);                  // bad substrings
        }
        return (int)res;
    }
};

// For quick manual testing
int main() {
    Solution sol;
    cout << sol.numberOfSubstrings("abacb", 2) << endl; // 4
    cout << sol.numberOfSubstrings("abcde", 1) << endl; // 15
}
```

All three implementations share the same logic:  
*Count all substrings → subtract the “too‑many” ones with a shrinking sliding window.*  

---

## 4.  Blog Article – “Leetcode 3325: Mastering the Sliding Window to Crack the K‑Frequency Substrings Interview”

> **Meta‑Description** – Want to land that software‑engineering job? Learn the slick O(n) solution to Leetcode 3325 “Count Substrings With K‑Frequency Characters I” in Java, Python, and C++. Get the interview‑ready breakdown of the sliding‑window technique, pitfalls, and how to explain it on a whiteboard.

### 4.1 Why This Problem Rocks Your Interview Portfolio

- **String + Sliding Window** – A classic combo that many interviewers love.  
- **Time‑Space Trade‑off** – O(n) time, O(1) space shows you can reason about asymptotic limits.  
- **Scalable Constraints** – 3,000 characters → brute force O(n²) will TLE.  
- **Cross‑Language Mastery** – You’ll show you can implement the same algorithm in Java, Python, or C++.

### 4.2 The “Good” – What Makes the O(n) Sliding Window Great

1. **Linear Scan** – Each character enters and leaves the window once.  
2. **Constant Extra Space** – Only 26 counters for lowercase letters.  
3. **Intuitive Explanation** – “Keep the window as long as *no* character hits `k`”.  
4. **Easily Extendable** – The same pattern works for “at least k”, “exactly k”, or “at most k”.

### 4.3 The “Bad” – Common Pitfalls You Must Avoid

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| Off‑by‑one on substring count | Forget that `(n+1)*n/2` counts *all* substrings. | Pre‑compute total and subtract invalid ones. |
| Using a map instead of fixed array | Slower look‑ups; still fine but unnecessary. | Use `int[26]` for lowercase letters. |
| Shrinking the window incorrectly | Shrink only when the *current* character reaches `k`, not any character. | While `freq[cur] >= k`, decrement left and move `left++`. |
| Not resetting the counter | Left index moves but the freq of the char removed is not updated. | Decrement the counter of `s[left]` each time we shift left. |

### 4.4 The “Ugly” – Edge Cases That Make You Sweat

- `k = 1`: Every substring is valid → answer is `(n+1)*n/2`.  
- `k > n`: No substring can have a character ≥ k → answer is 0.  
- All identical characters: The window will shrink aggressively.  
- Large `n`: Make sure you use `long long` or `int64_t` for the total count to avoid overflow.

### 4.5 Step‑by‑Step Walk‑through (Whiteboard Friendly)

1. **Count All Substrings**  
   ```text
   total = (n + 1) * n / 2
   ```
2. **Initialize Window**  
   `l = 0` (left), `freq[26] = 0`
3. **Scan Right**  
   For each `r` from `0` to `n-1`:
   - `freq[s[r]]++`
   - While `freq[s[r]] >= k`:  
     `freq[s[l]]--`, `l++`
   - Subtract bad substrings: `total -= (r - l + 1)`
4. **Result**  
   Return `total`.

This sequence is perfect for a 5‑minute explanation during an interview.

### 4.6 SEO‑Optimized Keywords

- Leetcode 3325  
- Count Substrings With K‑Frequency Characters I  
- Sliding Window algorithm  
- Java string problem  
- Python interview question  
- C++ interview coding  
- Software engineer interview tips  
- String manipulation in interviews  
- O(n) string counting  

Including these phrases in your LinkedIn post, résumé, or portfolio article will let hiring managers spot your expertise in exactly the topics they care about.

---

## 5.  Final Takeaway

> *“Keep the window until a character reaches `k`, then slide left, and subtract the bad substrings.”*  
> That’s the core line of reasoning that lets you solve Leetcode 3325 in **O(n)** with **O(1)** memory, and explains to any interviewer that you can turn a seemingly heavy counting problem into a clean linear scan.  

Happy coding – and good luck on that next interview!