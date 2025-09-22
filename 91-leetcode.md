---
title: LeetCode 91. Decode Ways - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Decode Ways – 91 – Full‑Stack Solution (Java | Python | C++)  
> **Leetcode Problem #91 – Medium**  
> https://leetcode.com/problems/decode-ways/  

> **Goal** – Count the number of ways to decode a numeric string (1 → A, 2 → B, …, 26 → Z).  
> **Constraint** – 1 ≤ s.length ≤ 100, answer fits in a 32‑bit signed int.  

Below you’ll find three production‑ready implementations that you can paste straight into your IDE or CI pipeline.  After that, a short yet SEO‑friendly blog article that explains the “good”, the “bad” and the “ugly” of this classic DP puzzle – perfect for LinkedIn, Medium or a personal blog to showcase your problem‑solving chops to recruiters.  

---

## 📌 1. Java Implementation (Iterative DP)

```java
import java.util.*;

public class Solution {
    /**
     * Returns the number of ways to decode the string s.
     * Uses O(n) time and O(1) extra space.
     */
    public int numDecodings(String s) {
        int n = s.length();
        if (n == 0 || s.charAt(0) == '0') return 0;

        int prev = 1;          // dp[i-2]
        int curr = 1;          // dp[i-1]
        for (int i = 1; i < n; i++) {
            int temp = 0;

            // one‑digit decode (current char alone)
            if (s.charAt(i) != '0') {
                temp += curr;
            }

            // two‑digit decode (previous + current)
            int two = (s.charAt(i-1) - '0') * 10 + (s.charAt(i) - '0');
            if (two >= 10 && two <= 26) {
                temp += prev;
            }

            // slide window
            prev = curr;
            curr = temp;
        }
        return curr;
    }
}
```

### Why O(1) space?

The recurrence only depends on the previous two DP values (`dp[i-1]` and `dp[i-2]`).  
Keeping two integer variables (`prev`, `curr`) eliminates the O(n) array.

---

## 📌 2. Python Implementation (Bottom‑Up DP)

```python
class Solution:
    def numDecodings(self, s: str) -> int:
        n = len(s)
        if n == 0 or s[0] == '0':
            return 0

        # dp[i] = ways to decode s[:i]
        dp = [0] * (n + 1)
        dp[0], dp[1] = 1, 1

        for i in range(2, n + 1):
            # one‑digit (s[i-1])
            if s[i-1] != '0':
                dp[i] += dp[i-1]

            # two‑digit (s[i-2:i])
            two = int(s[i-2:i])
            if 10 <= two <= 26:
                dp[i] += dp[i-2]

        return dp[n]
```

> **Tip** – Python’s slice `s[i-2:i]` is already an integer when passed to `int()`, which keeps the code concise.

---

## 📌 3. C++ Implementation (Iterative DP with O(1) Space)

```cpp
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size();
        if (n == 0 || s[0] == '0') return 0;

        long long prev = 1;   // dp[i-2]
        long long curr = 1;   // dp[i-1]

        for (int i = 1; i < n; ++i) {
            long long temp = 0;

            // One‑digit
            if (s[i] != '0')
                temp += curr;

            // Two‑digit
            int two = (s[i-1] - '0') * 10 + (s[i] - '0');
            if (two >= 10 && two <= 26)
                temp += prev;

            prev = curr;
            curr = temp;
        }
        return static_cast<int>(curr);
    }
};
```

> **Why `long long`?**  
> Although the answer fits in `int`, intermediate values can temporarily exceed 32‑bit when `s` is long and all combinations are valid. Using `long long` is a safety net.

---

## 📚 4. Blog Article – “The Good, the Bad, and the Ugly of Decode Ways”

> **Title:** *Decode Ways: The Good, The Bad, and The Ugly – A Classic DP Challenge for Your Interview Toolkit*  
> **Meta‑description:** Master Leetcode #91 “Decode Ways” with Java, Python, and C++ solutions. Learn the DP recurrence, edge‑case pitfalls, and how to talk to recruiters about it.  

---

### 🎯 Introduction  

When I first stumbled upon Leetcode 91 “Decode Ways,” I thought it was a simple puzzle.  Turns out it’s a **golden opportunity** to showcase your dynamic‑programming chops, handling edge cases, and even your ability to talk to recruiters about the *why* behind your code.  

In this post, I’ll dissect the solution into three parts that every software‑engineering candidate should master:

1. **The Good** – What makes this problem a perfect interview staple.  
2. **The Bad** – Common pitfalls that kill a candidate’s score.  
3. **The Ugly** – Real‑world complications you’ll hit when you move from a toy test to production.  

---

### 🔵 The Good – Why “Decode Ways” Rocks  

| Aspect | Why it’s Good |
|--------|---------------|
| **Clear Problem Statement** | One numeric string → many alphabetic interpretations – easy to reason about. |
| **Strong DP Foundation** | The recurrence `dp[i] = dp[i-1] + dp[i-2]` is a textbook example of overlapping sub‑problems. |
| **O(n) Solution** | Both time and space can be linear, fitting comfortably into Leetcode’s constraints. |
| **Reusable Pattern** | The “look‑back‑one‑and‑two” trick appears in many string‑based DP problems (e.g., “Climbing Stairs”, “Unique Paths”). |
| **Great for Interviews** | Recruiters love seeing a clean, iterative DP solution that runs in `O(n)` time with `O(1)` space. |

> *Pro Tip:* During a live interview, start by describing the base cases (`dp[0] = 1`, `dp[1] = 1` unless first char is '0'). This shows you understand the problem’s edge conditions right away.

---

### 🔴 The Bad – Common Mistakes that Plague Candidates  

| Mistake | Why it hurts | How to avoid it |
|---------|--------------|-----------------|
| **Ignoring '0'** | A '0' cannot map to any letter, but can be part of a valid two‑digit number (`10`–`26`). Forgetting to handle this returns 0 or over‑counts. | Add a guard: `if (s[0] == '0') return 0;` |
| **Off‑by‑One Indexing** | Mixing up `dp[i]` vs `dp[i-1]` leads to mis‑counted combinations. | Use 1‑based DP (`dp[0] = 1`) or explicitly document that `dp[i]` refers to the first *i* characters. |
| **Not Checking Two‑Digit Bounds** | `int two = ...` must be between 10 and 26 *inclusive*. Some solutions mistakenly use `< 27` without the `>=10` check, counting `05` as a valid two‑digit. | Explicitly test `if (two >= 10 && two <= 26)` |
| **Array vs. Rolling Variables** | Implementing DP with an array is fine for small inputs, but a 100‑character string can still be done with only two integers. Using the array makes the solution look over‑engineering. | Keep two variables (`prev`, `curr`) and slide the window. |
| **Edge‑Case “empty string”** | Many people return 0 for `s=""`, but the LeetCode spec guarantees `length >= 1`. Adding a guard is still safe for reusable libraries. | `if (n == 0) return 0;` |

---

### 🕸️ The Ugly – Real‑World “Production” Complications  

1. **Large Inputs in Different Environments**  
   * Leetcode limits `s.length <= 100`, but internal services may feed longer strings.  
   * Intermediate values can overflow `int`. Use `long long` or `long` for safety.

2. **Mixed‑Language Codebases**  
   * You may need a Java library, a Python service, and a C++ micro‑service all solving the same puzzle.  
   * Keep the interface identical: `int numDecodings(String)` / `int numDecodings(String s)` / `int numDecodings(string s)`.  

3. **Thread‑Safety & Immutability**  
   * In a multithreaded service, you cannot use class‑level static DP arrays.  
   * The iterative solutions above are naturally thread‑safe because they use only local variables.

4. **Unit‑Testing & Mocking**  
   * Avoid hard‑coding edge cases in tests; instead generate random strings and cross‑check with a naive brute‑force validator for `n <= 10`.  
   * Example Python test harness:

   ```python
   def brute(s):
       if not s: return 1
       if s[0] == '0': return 0
       ways = 0
       if s[0] != '0': ways += brute(s[1:])
       if len(s) >= 2 and 10 <= int(s[:2]) <= 26:
           ways += brute(s[2:])
       return ways
   ```

5. **Performance in a High‑Load Service**  
   * Even though `O(n)` is trivial for `n <= 100`, a micro‑service might call this method 10⁶ times per second.  
   * Cache the results for repeated inputs or pre‑compute for all possible strings of length ≤ 100 if your traffic is mostly repeated.

---

### 📢 5. How to Share This on Your Blog

> *Include the following tags:*  
> `#DynamicProgramming`, `#Leetcode91`, `#DecodeWays`, `#CodingInterview`, `#Java`, `#Python`, `#C++`, `#InterviewPrep`, `#Recruitment`

```markdown
# Decode Ways: The Good, The Bad, and The Ugly

When you land an interview at a tech company, your recruiter will often ask you to solve **Decode Ways** (#91 on LeetCode).  
Here’s why it’s a *must‑know* problem and how you can answer it in any language.

## The Good  
*Linear time & constant space* – perfect for OJ constraints.  
*Clear recurrence*: `dp[i] = dp[i-1]` (one digit) + `dp[i-2]` (two digits).  
*Reusable pattern* for many string‑DP questions.

## The Bad  
*Zero handling* – a single ‘0’ or a ‘0’ that isn’t part of a valid two‑digit number immediately kills the result.  
*Off‑by‑one mistakes* are common; remember that `dp[0] = 1` (empty string) and `dp[1] = 1` unless the first char is ‘0’.

## The Ugly  
*Large‑scale deployments* may accidentally use an `int` array where the sum temporarily overflows 32‑bit.  
*Thread‑safety*: shared mutable DP arrays are a nightmare in concurrent environments.

## Code (Java | Python | C++)

*Java:*

```java
public int numDecodings(String s) { … }
```

*Python:*

```python
class Solution:
    def numDecodings(self, s: str) -> int: …
```

*C++:*

```cpp
class Solution {
public:
    int numDecodings(string s) { … }
};
```

## Final Thoughts

The Decode Ways problem is a *dynamic‑programming micro‑test* that showcases:

- Understanding of base cases and edge‑case handling.  
- Ability to compress memory from `O(n)` to `O(1)`.  
- Clear communication of your reasoning (good for interview notes).

Good luck in your next coding interview – you’ll now have a ready‑to‑copy solution and a polished blog post that recruiters can find with a quick Google search for “decode ways solution Java” or “decode ways Python”.  

*Happy coding!*
```

> **SEO Tip** – The post includes the keyword “Decode Ways” multiple times, and it ends with a call‑to‑action for recruiters to view the solution on LeetCode. Search engines love these signals.

---

## 🏁 6. Running the Code Locally

| Language | Command |
|----------|---------|
| Java | `javac Solution.java`<br>`java Solution` (with a `main` that instantiates `Solution`) |
| Python | `python -m unittest` (add a `unittest` script that calls `Solution().numDecodings()`) |
| C++ | `g++ -std=c++17 solution.cpp -o decode`<br>`./decode` |

---

### 🎓 Bonus: Unit‑Test Skeletons

```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class SolutionTest {
    private final Solution s = new Solution();

    @Test
    void testSimple() {
        assertEquals(2, s.numDecodings("12"));   // AB, L
    }

    @Test
    void testZero() {
        assertEquals(0, s.numDecodings("0"));
        assertEquals(0, s.numDecodings("10"));
    }

    @Test
    void testLarge() {
        assertEquals(1, s.numDecodings("1111111111")); // all A's
    }
}
```

```python
import unittest
from solution import Solution

class TestDecodeWays(unittest.TestCase):
    def setUp(self):
        self.s = Solution()

    def test_basic(self):
        self.assertEqual(2, self.s.numDecodings("12"))

    def test_with_zero(self):
        self.assertEqual(0, self.s.numDecodings("0"))
        self.assertEqual(0, self.s.numDecodings("101"))

if __name__ == '__main__':
    unittest.main()
```

```cpp
#include <gtest/gtest.h>
#include "solution.cpp"

TEST(DecodeWaysTest, Simple) {
    Solution sol;
    EXPECT_EQ(2, sol.numDecodings("12"));
}
```

---

## 🚀 Final Words

- **Copy & paste** the Java, Python, or C++ snippet into your IDE.  
- **Add** the test cases above to validate your solution.  
- **Write** the Markdown blog post and publish on your personal site or Medium.  

Now you’re ready for that LeetCode 91 interview question – code, test, and explain like a pro!  

Happy interviewing! 🎉
```

**End of README**

--- 

The README now provides a thorough explanation, includes the three code snippets, and contains an entire Markdown blog post with a clear structure for interview preparation.