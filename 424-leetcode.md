---
title: LeetCode 424. Longest Repeating Character Replacement - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Longest Repeating Character Replacement – LeetCode 424  
*Java • Python • C++ Solutions + In‑Depth Blog Post*  

---

## 🎯 Problem Statement

> **Given a string `s` consisting of only uppercase English letters and an integer `k`, you may change at most `k` characters in `s` to any other uppercase letter. Return the length of the longest substring that can contain the same letter after the replacements.**

> **Example**  
> `s = "ABAB", k = 2` → **4** (change both `A`’s to `B`’s or vice‑versa)  
> `s = "AABABBA", k = 1` → **4** (change the middle `A` to `B` → `"AABBBBA"` → `"BBBB"`)

**Constraints**

| | |
|---|---|
| `1 <= s.length <= 10^5` | `0 <= k <= s.length` |
| `s` contains only uppercase letters |

---

## 📌 Why This Problem Is a “Must‑Know” for Interviews

* **Classic Sliding Window** – demonstrates mastery of two‑pointer technique.  
* **Frequency Counting** – shows comfort with hash maps / fixed‑size arrays.  
* **Optimal Time vs. Space** – perfect for discussing asymptotic trade‑offs.  
* **Real‑World Analogy** – data cleaning / normalization problems.  

---

## 🧠 Core Idea – Sliding Window + “Keep the Most Frequent Char”

When we look at any substring, the only thing that matters is:

1. The length of the window  
2. The count of the **most frequent** character inside that window  

If the window length minus the count of the most frequent char is **≤ k**, we can change the remaining characters to that frequent char, making the whole window uniform.  
Otherwise the window is invalid and we must shrink it from the left.

The trick is that the “most frequent” value is **monotonic** – it never decreases as the window grows, so we can keep a single variable `maxFreq` and update it only when a new character’s count becomes larger.

---

## 📈 Time & Space Complexity

| | |
|---|---|
| **Time** | `O(n)` – each index is added and removed at most once. |
| **Space** | `O(1)` – fixed array of size 26 (or dictionary of size ≤ 26). |

---

## 🤓 Edge Cases & Common Pitfalls

| # | Pitfall | Fix |
|---|----------|-----|
| 1 | Forgetting to subtract the outgoing char’s frequency when shrinking the window. | Always decrement `freq[s[left] - 'A']` before moving `left`. |
| 2 | Using `int` for `maxFreq` but not updating it when it decreases. | **Do not** recompute `maxFreq` on shrink – keep it monotonic; it only *increases*. |
| 3 | Using `<= k` vs `< k` in the validity check. | Correct condition is `windowLen - maxFreq <= k`. |
| 4 | Off‑by‑one in window length calculation (`end - left + 1`). | Use `end - left + 1` or `end + 1 - left`. |

---

## 🛠️ Code – Java

```java
public class Solution {
    /**
     * Longest Repeating Character Replacement (LeetCode 424)
     *
     * @param s The input string of uppercase letters.
     * @param k The maximum number of replacements allowed.
     * @return The length of the longest substring that can be made all the same.
     */
    public int characterReplacement(String s, int k) {
        int left = 0;                 // window start
        int maxFreq = 0;              // highest frequency in current window
        int[] freq = new int[26];     // frequency of each letter

        int best = 0;                 // best window length seen

        for (int right = 0; right < s.length(); right++) {
            int idx = s.charAt(right) - 'A';
            freq[idx]++;                      // include new char

            maxFreq = Math.max(maxFreq, freq[idx]);

            // If we need more than k changes, shrink from left
            if (right - left + 1 - maxFreq > k) {
                int leftIdx = s.charAt(left) - 'A';
                freq[leftIdx]--;             // remove char leaving window
                left++;                       // move window start
            }

            best = Math.max(best, right - left + 1);
        }

        return best;
    }
}
```

---

## 🐍 Code – Python

```python
class Solution:
    def characterReplacement(self, s: str, k: int) -> int:
        """
        Sliding window with fixed frequency array (size 26).
        """
        left = 0
        max_freq = 0
        freq = [0] * 26
        best = 0

        for right, ch in enumerate(s):
            idx = ord(ch) - ord('A')
            freq[idx] += 1
            max_freq = max(max_freq, freq[idx])

            if right - left + 1 - max_freq > k:
                freq[ord(s[left]) - ord('A')] -= 1
                left += 1

            best = max(best, right - left + 1)

        return best
```

---

## 🥶 Code – C++

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        vector<int> freq(26, 0);
        int left = 0, maxFreq = 0, best = 0;

        for (int right = 0; right < (int)s.size(); ++right) {
            int idx = s[right] - 'A';
            freq[idx]++;
            maxFreq = max(maxFreq, freq[idx]);

            if (right - left + 1 - maxFreq > k) {
                freq[s[left] - 'A']--;
                ++left;
            }

            best = max(best, right - left + 1);
        }
        return best;
    }
};
```

---

## 📚 In‑Depth Blog Post – “The Good, The Bad, and the Ugly”

### Introduction

> **Longest Repeating Character Replacement** (LeetCode 424) is a seemingly simple problem that hides a deep insight into *windowed optimization* and *frequency bookkeeping*. It’s a staple in interview portfolios for good reason: it tests a candidate’s ability to:
> 
> 1. Formulate a sliding‑window invariant  
> 2. Maintain auxiliary state efficiently  
> 3. Reason about worst‑case bounds  
> 4. Communicate their approach clearly  
> 
> In this article we’ll walk through the “good” (why the algorithm is elegant), the “bad” (common pitfalls and misconceptions), and the “ugly” (less‑optimal approaches that still work but will get you rejected in a timed interview).

### The Good – An Elegant Sliding‑Window Solution

1. **Invariant**  
   *The window is valid if `windowLen - maxFreq <= k`.*  
   This single check guarantees that we never need to scan the entire window for changes.  

2. **Monotonic `maxFreq`**  
   *Why we can skip recomputing*  
   `maxFreq` only increases as we expand the window. Even if we drop the most frequent char, the previous `maxFreq` is still a *lower bound* on the true maximum for the reduced window. This subtlety allows us to keep the algorithm linear without extra passes.  

3. **O(1) Space**  
   A fixed array of 26 ints is trivial compared to the potential 10^5 length of the string.  

### The Bad – What Usually Breaks Candidates

| Problem | Symptom | Fix |
|---|---|---|
| **Recomputing frequency on shrink** | O(n²) time | Keep `maxFreq` monotonic, don't recompute |
| **Wrong window length formula** | Off‑by‑one bugs | Use `right - left + 1` consistently |
| **Using `<= k` incorrectly** | Failing test cases | Validate condition with simple examples (`"AB", k=1` → 2) |
| **Mutable state after loop** | Unclear final answer | Track `best` separately or compute at loop end |

### The Ugly – Alternatives That Still Pass but Don’t Scale

| Approach | Complexity | Why It’s Ugly |
|---|---|---|
| **Binary Search over answer length + check** | `O(n log n)` | Extra logarithmic factor; more code |
| **Brute‑Force with nested loops** | `O(n²)` | Exceeds time limits for 10⁵ |
| **Using a `Map<Character, Deque<Integer>>`** | `O(n)` but heavier constants | Unnecessary overhead |

While these approaches might win on small test cases, interviewers look for *clean, optimal* solutions.

### How to Talk About It in an Interview

1. **State the problem in your own words** – ensures you understand the goal.  
2. **Explain the sliding window invariant** – show you’re thinking in terms of *valid/invalid* windows.  
3. **Discuss `maxFreq` monotonicity** – that’s the crux that keeps it linear.  
4. **Mention edge cases** – e.g., `k = 0`, `s` of length 1, all identical characters.  
5. **Talk about complexity** – interviewers love seeing a clear `O(n)`/`O(1)` justification.

### Summary

- **Good**: Linear time, constant space, elegant invariant.  
- **Bad**: Common off‑by‑one, recomputation, wrong condition.  
- **Ugly**: Quadratic or binary‑search variants that waste time.

Mastering this problem signals that you can handle *dynamic constraints* and *frequency‑based optimizations*—skills highly prized by top tech companies.

---

## 🎯 SEO‑Optimized Closing

> Looking to land a software engineering role? Mastering **LeetCode 424 – Longest Repeating Character Replacement** showcases your proficiency in **sliding window**, **string manipulation**, and **optimal algorithm design**.  
> Dive into our Java, Python, and C++ solutions, read the detailed blog for the **good, bad, and ugly** of this classic interview question, and boost your interview confidence today!

---

## 📎 Bonus – Quick Test Harness (Optional)

```java
public static void main(String[] args) {
    Solution sol = new Solution();
    System.out.println(sol.characterReplacement("ABAB", 2));   // 4
    System.out.println(sol.characterReplacement("AABABBA", 1)); // 4
}
```

```python
if __name__ == "__main__":
    sol = Solution()
    print(sol.characterReplacement("ABAB", 2))          # 4
    print(sol.characterReplacement("AABABBA", 1))      # 4
```

```cpp
int main() {
    Solution s;
    cout << s.characterReplacement("ABAB", 2) << endl;          // 4
    cout << s.characterReplacement("AABABBA", 1) << endl;      // 4
}
```

Happy coding and best of luck on your next interview!