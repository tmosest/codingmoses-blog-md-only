---
title: LeetCode 2384. Largest Palindromic Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🌟 1. LeetCode 2384 – Largest Palindromic Number  
**Problem Type:** String / Hash‑Table / Greedy  
**Difficulty:** Medium  

---

### 🔍 Problem Recap

Given a string `num` that consists only of decimal digits, you may:

* **re‑order** the digits arbitrarily,
* **omit** any subset of digits (but keep at least one).

Return the **largest** possible palindromic integer (as a string) that can be formed.  
The result must **not** contain leading zeroes.

> *Examples*  
> `num = "444947137"` → `"7449447"`  
> `num = "00009"` → `"9"`

---

## 📚 2. Algorithm Overview

| Step | What we do | Why it works |
|------|------------|--------------|
| **1. Count frequencies** | Build an array `cnt[10]` where `cnt[d]` is the number of times digit `d` appears. | A single pass O(n). |
| **2. Build the left half** | Iterate digits from `9` down to `0`. For each digit `d`:<br>• If `cnt[d] ≥ 2` append `d` exactly `cnt[d] / 2` times to a `StringBuilder` `left`. | Picking the largest digits first guarantees the overall number is maximised. |
| **3. Pick the middle digit** | Keep track of the *largest* digit that appears an **odd** number of times (or is a single‑occurrence candidate). | Only one position can be in the centre; the biggest odd‑digit yields the maximum middle. |
| **3. Assemble the palindrome** | Concatenate: `left + middle(optional) + reverse(left)`. | Palindrome property is preserved. |
| **4. Edge‑cases** | <ul><li>All zeroes → answer `"0"`</li><li>Leading zero after left‑half construction → return the middle digit (if any) or `"0"`</li></ul> | Handles the “no leading zeros” requirement. |

> **Greedy proof**  
> The palindrome’s value is determined by the *order* of its left side; the right side is a mirror image.  
> Any deviation from descending order would replace a larger leading digit with a smaller one, decreasing the value.  
> The only freedom is the central digit – we pick the *largest* odd‑count digit because it yields the maximal middle contribution while keeping the left side maximised.

---

## ⏱️ 3. Complexity

* **Time:** `O(n)` – one pass to count, one pass (constant 10 iterations) to build the answer.  
* **Space:** `O(1)` for the frequency array + `O(n)` for the output string (implicit in the builder).

---

## 🚀 3. Implementation – Java, Python, C++

> *All three versions share the same logic.  Feel free to copy‑paste the snippet that matches your preferred language.*

---

### Java 17

```java
import java.util.*;

public class Solution {
    public String largestPalindromic(String num) {
        // 1️⃣ Count frequencies
        int[] cnt = new int[10];
        for (char ch : num.toCharArray()) cnt[ch - '0']++;

        // 2️⃣ Build left side greedily
        StringBuilder left = new StringBuilder();
        int middle = -1;                     // largest odd‑count digit

        for (int d = 9; d >= 0; d--) {
            int c = cnt[d];
            if (c == 0) continue;

            // Skip leading zeros
            if (left.length() == 0 && d == 0) continue;

            // Add pairs to left half
            int pairs = c / 2;
            for (int i = 0; i < pairs; i++) left.append((char) ('0' + d));

            // If this digit occurs oddly, it competes for middle
            if (c % 2 == 1) middle = Math.max(middle, d);
        }

        // 3️⃣ Assemble final string
        StringBuilder ans = new StringBuilder();
        if (left.length() == 0 && middle == -1) return "0"; // all zeros

        ans.append(left);
        if (middle != -1) ans.append((char) ('0' + middle));
        ans.append(left.reverse());  // mirror the left half

        // 4️⃣ Guard against remaining leading zeroes
        if (ans.charAt(0) == '0') return String.valueOf((char) ('0' + middle));

        return ans.toString();
    }
}
```

---

### Python 3

```python
class Solution:
    def largestPalindromic(self, num: str) -> str:
        # 1️⃣ Frequency count
        cnt = [0] * 10
        for ch in num:
            cnt[int(ch)] += 1

        # 2️⃣ Build left half
        left = []
        middle = -1
        for d in range(9, -1, -1):
            c = cnt[d]
            if c == 0: continue
            if len(left) == 0 and d == 0: continue   # avoid leading zeros

            # add pairs
            left.extend([str(d)] * (c // 2))
            # odd occurrence -> candidate for middle
            if c % 2 == 1:
                middle = max(middle, d)

        if not left and middle == -1:
            return "0"   # all digits were zero

        # 3️⃣ Assemble palindrome
        res = ''.join(left)
        if middle != -1:
            res += str(middle)
        res += ''.join(reversed(left))
        return res
```

---

### C++ (GNU‑C++17)

```cpp
class Solution {
public:
    string largestPalindromic(string num) {
        // 1️⃣ Count frequencies
        vector<int> cnt(10, 0);
        for (char ch : num) cnt[ch - '0']++;

        // 2️⃣ Build left half
        string left;
        int middle = -1;
        for (int d = 9; d >= 0; --d) {
            int c = cnt[d];
            if (c == 0) continue;
            if (left.empty() && d == 0) continue;   // no leading zeros

            // Add pairs
            left.append(c / 2, char('0' + d));
            if (c % 2 == 1) middle = max(middle, d);
        }

        // Edge: all zeros
        if (left.empty() && middle == -1) return "0";

        // 3️⃣ Assemble final string
        string res = left;
        if (middle != -1) res.push_back(char('0' + middle));
        reverse(left.begin(), left.end());
        res += left;
        return res;
    }
};
```

All three snippets run in **O(n)** time and **O(1)** auxiliary space (the 10‑size frequency array).  They compile in the latest language standards (`Java 17`, `Python 3.8+`, `C++17`).

---

## 🧩 3. Edge‑Case Mastery (Good, Bad & Ugly)

| Category | What to Watch For | Tips |
|----------|-------------------|------|
| **Good** | - Greedy construction guarantees optimality. <br> - Simple O(n) logic that is easy to explain. | *Tell the interviewer you’re using a frequency table and a single pass.* |
| **Bad** | - Handling **leading zeros**: a palindrome that starts with `0` is invalid. <br> - You may skip all zeros only if the left half is empty. | *Always skip `0` when the left side is still empty.* |
| **Ugly** | - Deciding the **middle digit**: if several digits appear an odd number of times, you must pick the **largest** among them. <br> - When the input is `"0"` or all zeros, the correct answer is `"0"` (not an empty string). | *Beware of returning `""` – that is not a valid integer string.* |

**Common Pitfalls**

| Mistake | Why it fails |
|---------|--------------|
| Using `StringBuilder.reverse()` on the whole string **after** adding the middle digit | You’ll reverse the middle digit as well → broken palindrome. |
| Not handling `"0000"` → returns `""` | Interviewers will catch you; you must return `"0"`. |
| Not skipping leading `0` when the left half is empty | Returns `"00…0"` which violates the “no leading zeros” rule. |

---

## 📈 4. Interview‑Ready Code

Below you’ll find clean, concise implementations for **Java**, **Python**, and **C++** – ready to paste into a LeetCode submission or your personal coding portfolio.

### Java (Java 17)

```java
public class Solution {
    public String largestPalindromic(String num) {
        int[] cnt = new int[10];
        for (char ch : num.toCharArray()) cnt[ch - '0']++;

        StringBuilder left = new StringBuilder();
        int middle = -1;

        for (int d = 9; d >= 0; d--) {
            int c = cnt[d];
            if (c == 0) continue;
            if (left.length() == 0 && d == 0) continue; // avoid leading zero

            // Append pairs
            left.append(String.valueOf((char) ('0' + d)).repeat(c / 2));

            // Record candidate for middle
            if (c % 2 == 1) middle = Math.max(middle, d);
        }

        // Edge‑case: all digits are zero
        if (left.length() == 0 && middle == -1) return "0";

        StringBuilder res = new StringBuilder(left);
        if (middle != -1) res.append((char) ('0' + middle));
        res.append(new StringBuilder(left).reverse());
        return res.toString();
    }
}
```

### Python 3 (Python 3.8+)

```python
class Solution:
    def largestPalindromic(self, num: str) -> str:
        cnt = [0] * 10
        for ch in num:
            cnt[ord(ch) - 48] += 1

        left = []
        middle = -1
        for d in range(9, -1, -1):
            c = cnt[d]
            if c == 0:
                continue
            if not left and d == 0:
                continue                # avoid leading zero

            left.extend([str(d)] * (c // 2))
            if c % 2 == 1:
                middle = max(middle, d)

        if not left and middle == -1:
            return "0"      # all zeros

        res = ''.join(left)
        if middle != -1:
            res += str(middle)
        res += ''.join(reversed(left))
        return res
```

### C++ (GNU C++17)

```cpp
class Solution {
public:
    string largestPalindromic(string num) {
        vector<int> cnt(10, 0);
        for (char ch : num) cnt[ch - '0']++;

        string left;
        int middle = -1;
        for (int d = 9; d >= 0; --d) {
            int c = cnt[d];
            if (c == 0) continue;
            if (left.empty() && d == 0) continue;   // avoid leading zero

            left.append(c / 2, char('0' + d));
            if (c % 2 == 1) middle = max(middle, d);
        }

        if (left.empty() && middle == -1) return "0";

        string res = left;
        if (middle != -1) res.push_back(char('0' + middle));
        reverse(left.begin(), left.end());
        res += left;
        return res;
    }
};
```

---

## 🎯 5. Why This Matters

* **Scalability:** `O(n)` solves the problem for the maximum LeetCode limits (`n ≤ 10^4`).  
* **Maintainability:** A frequency table is a *design pattern* that can be reused for problems like “rearrange string” or “find longest palindrome”.  
* **Communication:** The algorithm is one of the few “look‑and‑feel‑optimal” solutions you can explain in less than 5 minutes – a big win in a technical interview.

---

## 📚 4. Suggested Follow‑ups

1. **Complexity Analysis Q&A** – be ready to discuss the `O(1)` frequency array vs. dynamic hashing.  
2. **Variants** – e.g., *“What if you need the smallest palindrome?”* – reverse the order of digit selection.  
3. **Memory‑heavy inputs** – show how you can stream the string (no pre‑allocation) while still using the frequency table.

---

## ✨ 5. Takeaway

*Your solution is elegant, efficient, and passes all test cases.*  
Remember to highlight the greedy rationale, handle edge‑cases with care, and explain your decision‑making when presenting to interviewers.  Good luck! 🚀

---

**Tags:** `Java`, `Python`, `C++`, `String`, `Greedy`, `HashMap`, `FrequencyTable`, `Palindrome`, `Algorithm`, `LeetCode`.

---