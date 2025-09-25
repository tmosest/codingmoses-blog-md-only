---
title: LeetCode 3329. Count Substrings With K-Frequency Characters II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview – Count Substrings With K‑Frequency Characters II**

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** | **O(26)** |
| Python | **O(n)** | **O(26)** |
| C++ | **O(n)** | **O(26)** |

*`n = s.length()` – up to 3 × 10⁵*  

The key idea is a **two‑pointer sliding window**.  
While the window `[l … r]` contains **at least one character that appears ≥ k times**, every longer window that starts at `l` and ends at any index `>= r` is also valid.  
So when the window becomes valid, we can add `n – r` to the answer and then shrink the window from the left until it becomes invalid again.

Below are clean, production‑ready implementations in Java, Python and C++.

---

## Java

```java
import java.util.*;

public class Solution {
    // helper: does the window contain a char with count >= k?
    private static boolean hasKFrequency(int[] freq, int k) {
        for (int c : freq) {
            if (c >= k) return true;
        }
        return false;
    }

    public long numberOfSubstrings(String s, int k) {
        int n = s.length();
        long ans = 0;
        int[] freq = new int[26];

        int l = 0;
        for (int r = 0; r < n; r++) {
            freq[s.charAt(r) - 'a']++;

            // shrink from the left while window is valid
            while (hasKFrequency(freq, k)) {
                ans += n - r;          // all windows [l, r..n-1] are valid
                freq[s.charAt(l) - 'a']--;
                l++;
            }
        }
        return ans;
    }

    public static void main(String[] args) {
        System.out.println(new Solution().numberOfSubstrings("abacb", 2)); // 4
        System.out.println(new Solution().numberOfSubstrings("abcde", 1)); // 15
    }
}
```

---

## Python

```python
class Solution:
    def numberOfSubstrings(self, s: str, k: int) -> int:
        n = len(s)
        freq = [0] * 26
        l = 0
        ans = 0

        def valid() -> bool:
            return any(c >= k for c in freq)

        for r, ch in enumerate(s):
            freq[ord(ch) - 97] += 1

            while valid():
                ans += n - r          # all longer windows starting at l
                freq[ord(s[l]) - 97] -= 1
                l += 1

        return ans


# quick tests
if __name__ == "__main__":
    print(Solution().numberOfSubstrings("abacb", 2))  # 4
    print(Solution().numberOfSubstrings("abcde", 1))  # 15
```

---

## C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long numberOfSubstrings(string s, int k) {
        int n = s.size();
        vector<int> freq(26, 0);
        long long ans = 0;
        int l = 0;

        auto valid = [&]() -> bool {
            for (int c : freq) if (c >= k) return true;
            return false;
        };

        for (int r = 0; r < n; ++r) {
            freq[s[r] - 'a']++;

            while (valid()) {
                ans += n - r;                 // all longer windows are valid
                freq[s[l] - 'a']--;
                ++l;
            }
        }
        return ans;
    }
};

int main() {
    Solution sol;
    cout << sol.numberOfSubstrings("abacb", 2) << '\n'; // 4
    cout << sol.numberOfSubstrings("abcde", 1) << '\n'; // 15
}
```

---

## Blog Article – *The Good, The Bad, The Ugly of LeetCode 3329*

### 📚 Why LeetCode 3329 Matters in Technical Interviews

- **Keyword‑Rich**: “Count Substrings With K‑Frequency Characters II”, “LeetCode Hard”, “sliding window”, “Java Python C++”.
- **Recruiter‑Friendly**: Demonstrates mastery of two‑pointer technique, linear‑time complexity, and handling of large inputs.
- **Career Boost**: A concise, well‑commented solution is a great talking‑point in a behavioural interview.

---

### The Good – What Makes This Problem a Win

| Aspect | Why it’s Great |
|--------|----------------|
| **Linear Time** | `O(n)` is the best you can do; satisfies the 3 × 10⁵ constraint. |
| **Simple Data Structure** | 26‑element array (`int[26]`), no hash maps → constant‑space overhead. |
| **Two‑Pointer Intuition** | Sliding window is a staple interview pattern; this problem tests it at a higher difficulty level. |
| **Scalable to Multiple Languages** | Implementation is nearly identical in Java, Python, C++ – perfect for multilingual portfolios. |

**Takeaway**: The problem reinforces a core CS concept—maintaining a *valid* window and counting all extensions in one shot.

---

### The Bad – Common Pitfalls You Should Avoid

1. **Using a HashMap**  
   *Problem*: `O(n log σ)` per operation, and extra overhead.  
   *Fix*: Use a fixed‑size array for the 26 lowercase letters.

2. **Forgetting Long Long**  
   *Problem*: Substring count can reach ~ (3 × 10⁵)² / 2 ≈ 4.5 × 10¹⁰, overflow 32‑bit ints.  
   *Fix*: In Java `long`, in C++ `long long`, in Python `int` (unbounded).

3. **Incorrect Valid‑Check**  
   *Problem*: Checking only the character that just entered leads to missed windows when other characters satisfy the `k` condition.  
   *Fix*: Re‑evaluate the window after each shrink: scan the 26 frequencies.

4. **Off‑By‑One Errors in Counting**  
   *Problem*: Adding `n - r` is the key trick; adding `n - r + 1` over‑counts by one.  
   *Fix*: Remember that `r` is zero‑based and inclusive.

---

### The Ugly – The Edge Cases that Can Trip You Up

| Edge | Why It Breaks naïve solutions | How to Handle It |
|------|------------------------------|------------------|
| `k == 1` | Every substring is valid; the window never becomes “invalid”, causing an infinite loop if you shrink incorrectly. | The algorithm naturally counts `n - r` for each `r`; no special case needed. |
| Long repeated patterns (`aaaaa…`) | Frequent shrink‑shifts may appear expensive, but the constant‑size freq array keeps it fast. | Ensure your shrink loop uses the updated `l` and `r` correctly. |
| Empty string (not allowed by constraints) | Some templates check `if (!s.length()) return 0;`. | Constraints guarantee `n >= 1`; safe to skip. |

---

### Final Checklist Before the Interview

1. **Read the Constraints** – use `long` / `long long` for the answer.  
2. **Use an Array for Frequencies** – 26 elements, `freq[s[i]-'a']`.  
3. **Validate the Window** – `any(freq[c] >= k for c in freq)`.  
4. **Add `n - r` When Valid** – counts all longer substrings at once.  
5. **Shrink From the Left** – decrease frequency, move `l`.  
6. **Test Edge Cases** – `k=1`, `k=n`, all same character, random string.  

---

### 🚀 SEO‑Optimized Takeaway

> *"Solve LeetCode 3329 – Count Substrings With K‑Frequency Characters II – with a sliding window in Java, Python, or C++. Learn how to handle large strings, use constant‑space frequency arrays, and avoid common pitfalls. Master this Hard problem to impress recruiters and boost your interview score."*

Share this article, add the code snippets to your GitHub, and you’ll have a solid LeetCode showcase that highlights:

- **Problem‑solving skills** (hard‑level algorithm).
- **Cross‑language proficiency** (Java/Python/C++).
- **Interview readiness** (sliding window, time/space optimization).

Good luck landing that dream tech role!