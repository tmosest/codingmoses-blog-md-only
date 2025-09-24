---
title: LeetCode 816. Ambiguous Coordinates - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 816. Ambiguous Coordinates – A Complete, SEO‑Ready Guide  

> **TL;DR**  
> • Parse the string **s** after stripping the outer parentheses.  
> • Enumerate all possible “comma” positions.  
> • For each side generate every legal “integer” or “decimal” representation.  
> • Combine the two sides and return the final list.  
> • Complexity: **O(n²)** time, **O(n)** auxiliary space.  

---

## 1. Problem Recap

You are given a string `s` that looks like a 2‑dimensional point, e.g. `"(1, 3)"`.  
All commas, decimal points and spaces were removed, producing a string such as  

```
"(1,3)"   ->  "(13)"
"(2,0.5)" ->  "(205)"
```

Your task is to return **every** possible original coordinate pair that could
produce `s` when the punctuation was stripped.  
The rules for valid numbers are:

* No leading zeros unless the number is exactly `"0"`.  
* A decimal point may only appear **after** at least one digit.  
* Numbers that can be represented with fewer digits (e.g. `"001"` → `"1"`, `"0.0"` → `"0"`) are not allowed.  

The final list may be in any order, but each pair must have exactly one space after the comma.

---

## 2. Why This Problem Is Interesting

* It tests **string manipulation** skills, especially handling decimal positions.  
* The “leading/trailing‑zero” rules make it a **corner‑case** problem.  
* The solution is a good illustration of the **“generate and filter”** pattern:  
  – generate all ways to split the digits and insert a decimal point,  
  – then filter out the invalid ones.  

Because of its moderate difficulty (LeetCode “Medium”) and the elegance of the
solution, this problem is a favourite on interview prep lists.

---

## 3. Solution Overview

1. **Strip the parentheses** → obtain the pure digit string `digits`.  
2. **Enumerate comma positions** – split `digits` into a left part `x` and a right part `y`.  
3. **Generate all valid representations** of a digit string  
   * no decimal point (only if the string is a single digit or does not start with `'0'`)  
   * one decimal point after the first digit (only if the string ends with a non‑zero)  
   * one decimal point somewhere in the middle (only if the string does not start with `'0'` and does not end with `'0'`)  
4. **Combine** every left option with every right option and wrap them in `"(" + left + ", " + right + ")"`.  
5. **Return** the final list.

The “generate all valid representations” step is the heart of the algorithm.

---

## 4. Detailed Algorithm

```text
digits = s[1:-1]                 // strip '(' and ')'
for each i from 1 to len(digits)-1:
    x_str = digits[0:i]
    y_str = digits[i:]
    left_opts  = generateOptions(x_str)
    right_opts = generateOptions(y_str)
    for each a in left_opts:
        for each b in right_opts:
            result.add("(" + a + ", " + b + ")")
return result
```

`generateOptions(str)` works like this:

```
options = []

// 1) plain integer
if len(str) == 1 or str[0] != '0':
    options.append(str)

// 2) decimal after the first digit
if len(str) > 1 and str[-1] != '0':
    options.append(str[0] + "." + str[1:])

// 3) decimal at any other position
if len(str) > 2 and str[0] != '0' and str[-1] != '0':
    for k in range(2, len(str)):
        options.append(str[:k] + "." + str[k:])

return options
```

All constraints are checked by the conditions above; the function returns
only strings that satisfy the “no leading/trailing zero” rule.

---

## 4.1 Edge Cases

| Input | Explanation | Result |
|-------|-------------|--------|
| `"(10)"` | Impossible – there are not enough digits for a comma | `[]` |
| `"(000)"` | Split as `00` / `0` – all options would contain leading zeros → `[]` |
| `"(010)"` | Split `0` / `10` → `0` is fine, but `10` is fine → `["(0, 10)"]` |
| `"(0000)"` | All splits contain more than one zero → `[]` |

---

## 4.2 Correctness Proof (Sketch)

1. **Comma positions**:  
   Every legal pair must have a comma somewhere between the first and last digit of `digits`.  
   The loop `for i in 1..len(digits)-1` visits **all** such positions, so no possible split is missed.

2. **`generateOptions` validity**:  
   * If the string has one digit → it is always valid (no leading zeros problem).  
   * If it starts with `'0'` → the only valid decimal is `"0.xxx"`; any other form would either have a leading zero or a trailing zero.  
   * If it ends with `'0'` → only the integer form is allowed because a decimal ending with zero would have a trailing zero.  
   * For strings that satisfy the above “leading/trailing” rules, every possible decimal position is considered, but only if the right part of the decimal (the fractional part) is non‑zero – otherwise it would end with a zero.

Thus `generateOptions` enumerates **all** and **only** legal representations.  
Combining left and right parts therefore produces every feasible coordinate pair,
and no invalid pair can slip through.

---

## 5. Complexity Analysis

Let `n` be the number of digits inside the parentheses (`3 ≤ n ≤ 8` for the problem constraints).

* Splitting the string: `O(n)` per split.  
* For each side, `generateOptions` produces at most `n` strings (no decimal) + `n-1` (decimal at any of the `n-1` positions).  
  So `O(n)` per side.  
* We have `n-1` comma positions → overall time `O(n * n) = O(n²)` (≤ 64 operations for the max input).  
* The result list can contain at most `O(n²)` elements; we keep a few auxiliary vectors of size `O(n)` → overall auxiliary space `O(n)`.

With `n ≤ 8`, this is easily fast enough for all test cases.

---

## 6. Code Implementations

Below are clean, well‑commented solutions in **Python 3**, **Java 17** and **C++17**.
Feel free to copy‑paste into your favourite IDE or online judge.

### 6.1 Python 3

```python
from typing import List

class Solution:
    # Helper that turns a pure digit string into all legal numbers
    def _options(self, num: str) -> List[str]:
        opts = []

        # 1) Integer form
        if len(num) == 1 or num[0] != '0':
            opts.append(num)

        # 2) Decimal after the first digit
        if len(num) > 1 and num[-1] != '0':
            opts.append(num[0] + '.' + num[1:])

        # 3) Decimal in the middle (no leading zero and no trailing zero)
        if len(num) > 2 and num[0] != '0' and num[-1] != '0':
            for i in range(1, len(num)):
                opts.append(num[:i] + '.' + num[i:])

        return opts

    def ambiguousCoordinates(self, s: str) -> List[str]:
        digits = s[1:-1]                # strip '(' and ')'
        res = []

        # split between 1..len(digits)-1
        for i in range(1, len(digits)):
            left  = digits[:i]
            right = digits[i:]

            left_opts  = self._options(left)
            right_opts = self._options(right)

            for a in left_opts:
                for b in right_opts:
                    res.append(f'({a}, {b})')

        return res
```

---

### 6.2 Java 17

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    // Generates all legal representations of a numeric string
    private List<String> options(String num) {
        List<String> res = new ArrayList<>();

        // 1) Integer form
        if (num.length() == 1 || num.charAt(0) != '0')
            res.add(num);

        // 2) Decimal after the first digit
        if (num.length() > 1 && num.charAt(num.length() - 1) != '0')
            res.add(num.charAt(0) + "." + num.substring(1));

        // 3) Decimal in the middle
        if (num.length() > 2 && num.charAt(0) != '0' && num.charAt(num.length() - 1) != '0') {
            for (int i = 1; i < num.length(); ++i) {
                res.add(num.substring(0, i) + "." + num.substring(i));
            }
        }
        return res;
    }

    public List<String> ambiguousCoordinates(String s) {
        String digits = s.substring(1, s.length() - 1);   // strip parentheses
        List<String> result = new ArrayList<>();

        for (int i = 1; i < digits.length(); ++i) {      // possible comma positions
            String left  = digits.substring(0, i);
            String right = digits.substring(i);

            List<String> leftOpts  = options(left);
            List<String> rightOpts = options(right);

            for (String a : leftOpts) {
                for (String b : rightOpts) {
                    result.add("(" + a + ", " + b + ")");
                }
            }
        }
        return result;
    }
}
```

---

### 6.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> ambiguousCoordinates(string s) {
        string digits = s.substr(1, s.size() - 2);   // strip parentheses
        vector<string> ans;

        for (size_t i = 1; i < digits.size(); ++i) {
            string left  = digits.substr(0, i);
            string right = digits.substr(i);

            vector<string> leftOpts  = options(left);
            vector<string> rightOpts = options(right);

            for (const string &a : leftOpts)
                for (const string &b : rightOpts)
                    ans.emplace_back("(" + a + ", " + b + ")");
        }
        return ans;
    }

private:
    // All valid representations of a numeric string
    vector<string> options(const string &num) {
        vector<string> res;

        // Integer form
        if (num.size() == 1 || num[0] != '0')
            res.push_back(num);

        // Decimal after first digit
        if (num.size() > 1 && num.back() != '0')
            res.push_back(string(1, num[0]) + "." + num.substr(1));

        // Decimal at any middle position
        if (num.size() > 2 && num[0] != '0' && num.back() != '0') {
            for (size_t i = 1; i < num.size(); ++i)
                res.push_back(num.substr(0, i) + "." + num.substr(i));
        }
        return res;
    }
};
```

---

## 7. Summary

* **Problem** – reconstruct all possible coordinates from a mangled string.  
* **Key Insight** – enumerate all legal splits and for each part generate only the valid integer/decimal formats.  
* **Time** – `O(n²)` (`n ≤ 8`), **Space** – `O(n)`.  
* **Result** – Clean solutions in Python, Java, C++ ready for interview or competitive programming.

---

## 7.1 Bonus: More Efficient Alternative

A small optimization you can drop into any of the solutions is to skip splits that
would definitely produce leading zeros on both sides.  
For example, if `num[0] == '0' && num[1] == '0'` we can break early, saving a handful of needless calls – not strictly required for correctness, but nice for performance in languages where function calls are expensive.

---

## 7.2 Testing

```python
if __name__ == "__main__":
    sol = Solution()
    print(sol.ambiguousCoordinates("(123)"))   # Example from the statement
```

---

## 7.3 Final Thoughts

The “ambiguous coordinates” problem is a great example of a **combinatorial enumeration** with a few subtle string constraints.
The key takeaway: **break the problem into small, testable pieces** (splitting + option generation).  
Once you can isolate a sub‑problem, the proof of correctness is usually straightforward, and the code stays tidy.

Happy coding! 🚀

---

### 8. Further Reading

* [LeetCode 797. All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/) – another combinatorial enumeration.  
* [Top‑Down vs Bottom‑Up DP] – understanding complexity trade‑offs.  
* [C++17 String Manipulation] – learn to work efficiently with `substr`, `emplace_back`.  

Good luck with your next interview or coding challenge!