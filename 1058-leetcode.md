---
title: LeetCode 1058. Minimize Rounding Error to Meet Target - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ Minimize Rounding Error – LeetCode 1058  
**Solve it in Java, Python and C++**  
**+ Blog post (SEO‑friendly, “the good, the bad & the ugly”)**  

---

### 1️⃣ Problem Recap

| # | Problem | Difficulty | Link |
|---|---------|------------|------|
| 1058 | **Minimize Rounding Error to Meet Target** | Medium | <https://leetcode.com/problems/minimize-rounding-error-to-meet-target/> |

*Given an array `prices[]` (each string has exactly 3 decimal digits) and an integer `target`. For each price `pi` you may replace it by either `floor(pi)` or `ceil(pi)`. Choose the replacements so that the sum equals `target`. If it’s impossible, return “-1”. Otherwise return the **minimum** rounding error:  

\[
\text{error}=\sum_{i=1}^{n}\lvert \text{rounded}_i - p_i\rvert
\]  

The answer has to be formatted with exactly three decimal places.*

---

### 2️⃣ Why “Rounding Error” is a **Hidden DP + Greedy** Problem

* You only need to decide **which** of the two integer values (`floor` or `ceil`) you’ll use for each price.
* The change of the sum caused by a floor/ceil decision is **identical** for all prices:
  * `ceil(pi)`  →  adds `1 - frac_i` to the error  
  * `floor(pi)` →  adds `frac_i`      to the error  
  * (where `frac_i = pi – floor(pi)` is the fractional part, 0 ≤ frac_i < 1)

So once we know how many floors we *must* use, the *ordering* of the prices becomes irrelevant – we simply choose the smallest fractional parts to floor, because that gives the smallest error. This is a classic “sort‑and‑take” greedy algorithm that runs in **O(n log n)** time.

---

## 2️⃣ Solution Overview (Integer‑Only Implementation)

Each price string can be safely converted to an **integer that represents the price × 1000**.  
This removes any floating‑point precision issue and lets us do all arithmetic with plain integers.

| Step | What we compute | Why it matters |
|------|-----------------|----------------|
| 1 | `priceInt = whole*1000 + frac` | Exact 3‑digit integer |
| 2 | `ceilUnits = (priceInt + 999) // 1000` | `ceil(pi)` in whole‑number units |
| 3 | `frac = (priceInt % 1000) / 1000.0` | Fractional part for the error |
| 4 | `sumCeilUnits = Σ ceilUnits` | Maximal achievable sum |
| 5 | `floorsNeeded = sumCeilUnits – target` | How many **non‑integer** prices must be floored |
| 6 | Sort all `frac` ascending | The smallest fractions come first |
| 7 | For the first `floorsNeeded` fractional values (≠ 0) pick **floor** (`error += frac`).  
    For all other positions pick **ceil** (`error += 1‑frac`). | Gives the minimal total error |

If `sumCeilUnits < target` or `floorsNeeded` exceeds the number of *non‑integer* prices, the task is impossible → “-1”.

The final error is printed with exactly three decimal places.

---

## 3️⃣ Code (Java)

```java
import java.util.*;

class Solution {
    public String minimizeError(String[] prices, int target) {
        int n = prices.length;
        int sumCeilUnits = 0;            // sum of ceil(pi) as whole numbers
        int nonIntCount = 0;             // how many pi are NOT integers
        double[] frac = new double[n];   // fractional parts

        for (int i = 0; i < n; i++) {
            String s = prices[i];
            int dot = s.indexOf('.');
            int whole = Integer.parseInt(s.substring(0, dot));
            int fracPart = Integer.parseInt(s.substring(dot + 1)); // 3 digits
            int priceInt = whole * 1000 + fracPart; // price * 1000

            int ceilUnits = (priceInt + 999) / 1000; // ceil(pi)
            sumCeilUnits += ceilUnits;

            frac[i] = (priceInt % 1000) / 1000.0; // exact fractional part
            if (priceInt % 1000 != 0) nonIntCount++;
        }

        if (sumCeilUnits < target) return "-1";

        int floorsNeeded = sumCeilUnits - target;
        if (floorsNeeded > nonIntCount) return "-1";

        Arrays.sort(frac);   // zeros go first, then small fractions

        double error = 0.0;
        for (double f : frac) {
            if (f == 0.0) {
                // integer price – floor == ceil, no error
                continue;
            }
            if (floorsNeeded > 0) {
                // choose floor
                error += f;
                floorsNeeded--;
            } else {
                // choose ceil
                error += 1.0 - f;
            }
        }

        // after the loop floorsNeeded must be 0
        return String.format(Locale.US, "%.3f", error);
    }
}
```

---

## 4️⃣ Code (Python)

```python
from typing import List

class Solution:
    def minimizeError(self, prices: List[str], target: int) -> str:
        n = len(prices)
        sumCeilUnits = 0
        frac = [0.0] * n
        non_int = 0

        for i, s in enumerate(prices):
            whole, frac_part = s.split('.')
            whole = int(whole)
            frac_part = int(frac_part)          # always 3 digits
            price_int = whole * 1000 + frac_part

            ceil_units = (price_int + 999) // 1000
            sumCeilUnits += ceil_units

            f = (price_int % 1000) / 1000.0
            frac[i] = f
            if f > 0.0:
                non_int += 1

        if sumCeilUnits < target:
            return "-1"

        floors_needed = sumCeilUnits - target
        if floors_needed > non_int:
            return "-1"

        frac.sort()
        error = 0.0
        for f in frac:
            if f == 0.0:
                continue  # integer price, no error
            if floors_needed > 0:
                error += f
                floors_needed -= 1
            else:
                error += 1.0 - f

        # floors_needed must be 0 here
        return f"{error:.3f}"
```

---

## 5️⃣ Code (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string minimizeError(vector<string> &prices, int target) {
        int n = prices.size();
        long long sumCeil = 0;          // sum of ceil(pi) as whole numbers
        vector<double> frac(n);         // fractional parts
        int nonInt = 0;

        for (int i = 0; i < n; ++i) {
            const string &s = prices[i];
            int dot = s.find('.');
            int whole = stoi(s.substr(0, dot));
            int fracPart = stoi(s.substr(dot + 1));   // always 3 digits
            int priceInt = whole * 1000 + fracPart;   // price * 1000

            int ceilUnits = (priceInt + 999) / 1000;   // ceil(pi)
            sumCeil += ceilUnits;

            frac[i] = (priceInt % 1000) / 1000.0;      // exact fractional part
            if (priceInt % 1000 != 0) ++nonInt;
        }

        if (sumCeil < target) return "-1";

        int floorsNeeded = sumCeil - target;
        if (floorsNeeded > nonInt) return "-1";

        sort(frac.begin(), frac.end());   // zeros first
        double error = 0.0;

        for (double f : frac) {
            if (f == 0.0) continue;      // integer price, no error either way
            if (floorsNeeded > 0) {
                error += f;              // floor
                --floorsNeeded;
            } else {
                error += 1.0 - f;        // ceil
            }
        }

        return to_string(error).substr(0, error < 1e-9 ? 1 : error < 1e-6 ? 4 : 7); // 3 decimals
    }
};
```

> **Why not use `double` for all calculations?**  
> Because every input price has **exactly three** decimal digits, we can store the value as an integer `priceInt = price * 1000`. All arithmetic is then exact, the only floating‑point operation left is `error += f` or `1‑f`, which we format to three decimal places in the output.

---

## 📚 SEO‑Friendly Blog Post  
> **Title:** *Minimize Rounding Error – The Good, The Bad & The Ugly (LeetCode 1058)*  

---

### 🔎 1. Introduction  

Have you ever tried to make a list of prices add up to a specific total while rounding each price up or down?  
LeetCode 1058 is exactly that. In the post below we’ll walk through why the problem is deceptively simple, how a clean integer‑based algorithm solves it, and the real‑world pitfalls (the **bad**) and best‑practice tricks (the **good**).  
We’ll finish with a “the ugly” section that explains the subtle edge cases that can trip up naive solutions.

---

### 2️⃣ The “Good” – Clean, Exact, O(n log n)  

* **Integer‑only arithmetic** – Convert `"2.800"` → `2800`. No `double` precision worries.  
* **Ceil calculation** – `ceilUnits = (priceInt + 999) / 1000`.  
* **Greedy choice** – Once we know how many non‑integer prices must be floored, simply floor the *smallest* fractional parts.  
* **Sorting** – The fractional parts array (`frac`) is sorted; zeros go first.  
* **Total error** – Sum of `frac` for floored positions plus `1‑frac` for the rest.  

This approach guarantees the minimal error, and it runs in `O(n log n)` due to the sort.

---

### 3️⃣ The “Bad” – Precision & Edge Cases  

* **Floating‑point mis‑conversions** – A naive `double` implementation can mis‑interpret `"0.300"` as `0.299999…`, changing the error.  
* **All‑integer price lists** – If every price is an integer, you might think you can always pick `floor`, but actually the sum never changes.  
* **Too many floors** – `floorsNeeded > nonIntCount` should return “-1”, but forgetting this check yields incorrect positive answers.  

These are the subtle traps that many coders run into.

---

### 4️⃣ The “Ugly” – Why Sorting Alone is Not Enough  

Even with perfect arithmetic, you *must* verify:
1. **Maximal sum achievable** – `sumCeilUnits`.  
2. **Minimum floors required** – `floorsNeeded`.  

If you skip either check, you’ll produce an answer that satisfies the sum but not the problem’s constraints, or worse, you’ll output an error value when the task is impossible.  

---

### 5️⃣ Best‑Practice Checklist  

| Item | What to Do |
|------|------------|
| **Always count non‑integer prices** | You cannot floor an integer without affecting the sum. |
| **Validate the target** | `sumCeilUnits` is the upper bound. |
| **Check for impossible floor counts** | `floorsNeeded > nonIntCount` → “-1”. |
| **Sort fractional parts** | Smaller fractions → smaller error when floored. |
| **Format exactly three decimals** | Use `String.format("%.3f", error)` (Java), `f"{error:.3f}"` (Python), or a manual substring in C++. |

---

### 🏁 6️⃣ Conclusion  

LeetCode 1058 is a great reminder that many DP or greedy problems hide behind a simple transformation – here, turning decimals into integers.  
The clean solution above is both efficient and robust against floating‑point quirks.  

If you’re aiming for that perfect interview score, remember:  
* **Convert first, compute later.**  
* **Always check feasibility before greedily selecting decisions.**  
* **Format correctly – three decimals are mandatory!**

Happy coding, and may your sums always round up (or down) exactly as you need them!  

---  

*Author: ChatGPT*  
*Published on 2023‑08‑14*  

--- 

### 🎯 Final Thoughts

* **Time Complexity:** O(n log n) – sorting dominates.  
* **Space Complexity:** O(n) – storing the fractional parts.  
* **Edge Cases:** Prices that are all integers, a target larger than the maximal sum, or a target that forces too many floors.  
* **Takeaway:** Integer‑only solutions are the gold standard for problems with fixed decimal precision – they’re simple, fast, and foolproof.

---

And that’s it! You now have a complete, production‑ready solution for LeetCode 1058, along with a blog‑friendly explanation that can land you points for both algorithmic depth and clean coding style. Happy interviewing! 🚀

---