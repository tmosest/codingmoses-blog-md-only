---
title: LeetCode 2575. Find the Divisibility Array of a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2575. Find the Divisibility Array of a String  
**Difficulty:** Medium | **Time:** O(n) | **Space:** O(n)  

---  

### 📖 Problem Recap  

Given a decimal string `word` (length `n` up to `10⁵`) and an integer `m` (`1 ≤ m ≤ 10⁹`), return an array `div` of length `n` where  

```
div[i] = 1  if the integer represented by word[0 … i] is divisible by m
div[i] = 0  otherwise
```

Directly parsing the prefix into a 64‑bit integer can overflow, so we must rely on modular arithmetic.

---

## Solution Idea – “Keep the Remainder”  

* Process the string from left to right.  
* Maintain a running remainder `rem` of the current prefix modulo `m`.  
  * `rem = (rem * 10 + digit) % m`  
* If `rem == 0` the current prefix is divisible by `m`.  
* Store `1` or `0` in the answer array.

Because `rem < m` at all times, we never overflow, even if the actual number is astronomically large.

---

## Code

Below are clean, production‑ready solutions for **Java**, **Python**, and **C++**.

---

### Java

```java
public class Solution {
    public int[] divisibilityArray(String word, int m) {
        char[] ch = word.toCharArray();
        int[] ans = new int[ch.length];
        long rem = 0;                     // long to avoid accidental overflow
        for (int i = 0; i < ch.length; i++) {
            int digit = ch[i] - '0';       // faster than Character.getNumericValue()
            rem = (rem * 10 + digit) % m;  // keep only the remainder
            ans[i] = (rem == 0) ? 1 : 0;   // prefix divisible?
        }
        return ans;
    }
}
```

---

### Python 3

```python
class Solution:
    def divisibilityArray(self, word: str, m: int) -> list[int]:
        ans = []
        rem = 0
        for ch in word:
            rem = (rem * 10 + int(ch)) % m
            ans.append(1 if rem == 0 else 0)
        return ans
```

---

### C++ (C++17)

```cpp
class Solution {
public:
    vector<int> divisibilityArray(string word, int m) {
        vector<int> ans;
        ans.reserve(word.size());
        long long rem = 0;
        for (char ch : word) {
            int digit = ch - '0';
            rem = (rem * 10 + digit) % m;
            ans.push_back(rem == 0 ? 1 : 0);
        }
        return ans;
    }
};
```

---

## Performance

| Language | Time | Space |
|----------|------|-------|
| Java     | O(n) | O(n)  |
| Python   | O(n) | O(n)  |
| C++      | O(n) | O(n)  |

The only heavy operation per iteration is a modular multiplication/addition, which is constant time.

---

## The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Overflow** | Uses only remainders → safe | None | None |
| **Complexity** | Linear time & memory | `n` can be 100 000, still fine | None |
| **Readability** | Short, idiomatic code | Requires understanding of modular arithmetic | Some people may think `long long` is overkill |
| **Edge Cases** | Handles leading zeros, `m = 1`, `m = 10⁹` | None | None |
| **Memory Footprint** | O(n) result array | `ans` size `n` unavoidable | None |
| **Testing** | Easy to unit‑test prefixes | None | None |

### Why It’s “Great” for Interviews  

1. **Shows you understand modular arithmetic** – a common interview pitfall.  
2. **Demonstrates O(n) efficiency** – meets the LeetCode constraints.  
3. **Simple code** – less room for bugs, easier to explain.  

### Potential Pitfalls  

- Forgetting to mod after each multiplication can cause overflow.  
- Converting characters incorrectly (e.g., `int digit = ch - 48;` is fine, but `int digit = Character.getNumericValue(ch);` is slower).  
- Returning a wrong type (`List<Integer>` vs `int[]` in Java).  

---

## Tips for Turning This Into a Job‑Winning Portfolio Piece

1. **Add a Test Suite**  
   ```python
   def test():
       sol = Solution()
       assert sol.divisibilityArray("998244353", 3) == [1,1,0,0,0,1,1,0,0]
       assert sol.divisibilityArray("1010", 10) == [0,1,0,1]
   ```
2. **Document Your Code** – add brief comments explaining modular steps.  
3. **Explain the Algorithm in Your Resume** – “Used modular arithmetic to solve large‑number divisibility in O(n) time.”  
4. **Mention Interview Context** – “Adapted this solution for LeetCode 2575; ready to discuss edge cases and optimization.”  

---

## SEO‑Optimized Blog Title & Meta Description  

**Title:**  
*LeetCode 2575: Divisibility Array – The Good, The Bad, and The Ugly (Java, Python, C++)*

**Meta Description:**  
"Master LeetCode 2575 with clean Java, Python, and C++ solutions. Learn the modular arithmetic trick, time & space analysis, and interview tips to land your next job."

---

## Final Thoughts  

The “Keep the Remainder” pattern is a staple for string‑to‑integer problems where the number can exceed built‑in types.  
- **Good**: O(n) time, no overflow, easy to reason.  
- **Bad**: None if you remember to mod each step.  
- **Ugly**: None – the solution is elegant.

Show this in your portfolio, and you’ll have a strong talking point for technical interviews. Good luck! 🚀