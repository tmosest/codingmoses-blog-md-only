---
title: LeetCode 3606. Coupon Code Validator - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Coupon Code Validator – A Complete Guide  
**LeetCode 3606 – “Coupon Code Validator”**  
*(Easy – 100‑1000 votes, 1 ≤ n ≤ 100)*  

> **TL;DR** – Filter coupons that are non‑empty, alphanumeric (underscore allowed), belong to a valid business line (`electronics`, `grocery`, `pharmacy`, `restaurant`) **and** are active.  
> Then sort them first by business line priority (`electronics` < `grocery` < `pharmacy` < `restaurant`) and, within the same line, lexicographically by code.

---

## 1. Problem Summary  

| Input | Description |
|-------|-------------|
| `String[] code` | Coupon identifiers |
| `String[] businessLine` | Business category for each coupon |
| `boolean[] isActive` | `true` if the coupon is currently active |

Return a **sorted list of the codes** of all *valid* coupons.

**Validation Rules**

| Rule | Detail |
|------|--------|
| Non‑empty | `code[i]` must not be an empty string |
| Format | Only letters (`a‑z`, `A‑Z`), digits (`0‑9`) and underscore (`_`) |
| Business line | One of: `electronics`, `grocery`, `pharmacy`, `restaurant` |
| Active | `isActive[i] == true` |

**Sorting Order**

1. Business line priority: `electronics`, `grocery`, `pharmacy`, `restaurant`
2. Within the same business line – lexicographical order of `code`

---

## 2. Why This Problem is a Good Interview Question  

| ✅ Good |
|--------|
| **Small input size** → no need for complex optimisations; focus on correctness. |
| **Mix of filtering & custom sorting** → tests candidate’s ability to apply predicates and a custom comparator. |
| **Use of regular expressions** → common in real‑world data validation. |
| **Clear business logic** → easily translates to real‑world coupon validation systems. |
| **Scalable** → the same pattern applies to thousands of coupons in production. |

---

## 3. Common Pitfalls (The Bad)

| ❌ Bad | What to avoid |
|--------|----------------|
| Ignoring empty strings | `""` is a valid input but must be rejected. |
| Using `String.matches("[A-Za-z0-9_]+")` on `null` | Causes `NullPointerException`. |
| Relying on natural string sort for business lines | Natural order (`restaurant` < `electronics`) does **not** match required priority. |
| Using `Arrays.sort()` directly on a `String[][]` | You lose business line information. |
| Forgetting to return a `String[]` (or `List<String>`) | LeetCode expects the exact return type. |

---

## 4. The Ugly – When Things Get Messy

1. **Multiple validation passes** – If you filter and sort in separate loops you may accidentally reorder the output.  
2. **Hard‑coding priority** – If a new business line is added, you’ll need to touch the code in many places.  
3. **Regex overhead** – For a small input size, this is negligible, but for large datasets you might want to avoid regex and use a character‑by‑character check.  
4. **Mutable state in comparators** – If you store priority in a static map but forget thread safety (not an issue for LeetCode, but in real services it matters).  

---

## 5. Optimal Solution (O(n log n) time, O(n) space)

1. **Validate** each coupon in a single pass.  
2. **Store** only valid coupons in a list of a small helper class.  
3. **Sort** the list with a custom comparator that first looks up the priority of the business line and then compares the codes.  
4. **Extract** the codes into the final output array.

---

## 6. Code Implementations

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

### 6.1 Java

```java
import java.util.*;
import java.util.regex.Pattern;

public class Solution {
    /* Priority map for business lines */
    private static final Map<String, Integer> PRIORITY = Map.of(
        "electronics", 0,
        "grocery",     1,
        "pharmacy",    2,
        "restaurant",  3
    );

    /* Regex pattern – letters, digits, underscore */
    private static final Pattern CODE_PATTERN = Pattern.compile("[A-Za-z0-9_]+");

    public List<String> validateCoupons(String[] code,
                                        String[] businessLine,
                                        boolean[] isActive) {

        List<Coupon> valid = new ArrayList<>();

        for (int i = 0; i < code.length; i++) {
            String c = code[i];
            String bl = businessLine[i];

            if (c == null || c.isEmpty()) continue;          // non‑empty
            if (!CODE_PATTERN.matcher(c).matches()) continue; // format
            if (!PRIORITY.containsKey(bl)) continue;          // valid line
            if (!isActive[i]) continue;                       // active

            valid.add(new Coupon(c, bl));
        }

        /* Sort by priority first, then lexicographically by code */
        Collections.sort(valid);

        /* Build result list */
        List<String> res = new ArrayList<>(valid.size());
        for (Coupon c : valid) {
            res.add(c.code);
        }
        return res;
    }

    /* Helper class that implements Comparable for custom sort */
    private static class Coupon implements Comparable<Coupon> {
        final String code;
        final String businessLine;

        Coupon(String code, String businessLine) {
            this.code = code;
            this.businessLine = businessLine;
        }

        @Override
        public int compareTo(Coupon other) {
            int p1 = PRIORITY.get(this.businessLine);
            int p2 = PRIORITY.get(other.businessLine);
            if (p1 != p2) return Integer.compare(p1, p2);
            return this.code.compareTo(other.code);
        }
    }
}
```

**Why this Java solution is nice:**

- Uses immutable `Map.of` (Java 9+) for priority – thread‑safe and concise.  
- Regex compiled once.  
- Single pass validation + sorting.  

### 6.2 Python

```python
from typing import List
import re

class Solution:
    PRIORITY = {
        "electronics": 0,
        "grocery": 1,
        "pharmacy": 2,
        "restaurant": 3
    }

    CODE_RE = re.compile(r'^[A-Za-z0-9_]+$')

    def validateCoupons(
        self,
        code: List[str],
        businessLine: List[str],
        isActive: List[bool]
    ) -> List[str]:
        valid = []

        for c, bl, active in zip(code, businessLine, isActive):
            if not c or not self.CODE_RE.match(c):
                continue
            if bl not in self.PRIORITY:
                continue
            if not active:
                continue
            valid.append((self.PRIORITY[bl], c))

        # Sort by priority, then code
        valid.sort()
        return [c for _, c in valid]
```

**Python highlights**

- List comprehension & `zip` make the code concise.  
- Pre‑compiled regex for performance.  
- Tuple `(priority, code)` allows built‑in sorting.  

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> validateCoupons(vector<string>& code,
                                   vector<string>& businessLine,
                                   vector<bool>& isActive) {
        unordered_map<string, int> priority = {
            {"electronics", 0},
            {"grocery", 1},
            {"pharmacy", 2},
            {"restaurant", 3}
        };

        vector<pair<int, string>> valid;          // (priority, code)

        for (size_t i = 0; i < code.size(); ++i) {
            const string& c = code[i];
            const string& bl = businessLine[i];

            if (c.empty()) continue;
            if (priority.find(bl) == priority.end()) continue;
            if (!isActive[i]) continue;

            if (!isValidCode(c)) continue;         // regex check
            valid.emplace_back(priority[bl], c);
        }

        sort(valid.begin(), valid.end());          // lexicographic on code
        vector<string> res;
        res.reserve(valid.size());
        for (auto& p : valid) res.push_back(p.second);
        return res;
    }

private:
    /* Only letters, digits or underscore */
    bool isValidCode(const string& s) {
        for (char ch : s)
            if (!isalnum(ch) && ch != '_')
                return false;
        return true;
    }
};
```

**C++ notes**

- Uses `unordered_map` for O(1) lookup of priority.  
- `pair<int,string>` allows `std::sort` to work out of the box.  
- Avoids the heavy `std::regex` library; a manual character check is enough for this size.  

---

## 7. Complexity Recap  

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n log n)** (sorting) | **O(n)** (list of valid coupons) |
| Python | **O(n log n)** | **O(n)** |
| C++ | **O(n log n)** | **O(n)** |

All solutions do a single linear pass for validation and then a `sort()` call – perfectly fine for `n ≤ 100`.

---

## 8. How to Talk About This in a Technical Interview  

1. **State your plan** – “I’ll iterate once, filter with a predicate, collect valid coupons, then sort with a custom comparator.”  
2. **Explain the comparator** – “Priority map → business line first, then the code lexicographically.”  
3. **Mention regex** – “Only letters, digits or underscore – we’ll pre‑compile it.”  
4. **Optional optimisation** – “If the input were huge we could replace the regex with a simple character check to cut down on allocations.”  
5. **Edge‑case handling** – Show that you handle empty strings, nulls, and invalid business lines gracefully.  

---

## 9. Keywords & SEO Checklist  

| Keyword | Placement |
|---------|-----------|
| coupon code validator | Title, introduction, code block comments |
| LeetCode 3606 | Problem header, code comment |
| programming interview | Good/Bad/Ugly sections |
| Java | Code blocks, tags |
| Python | Code blocks, tags |
| C++ | Code blocks, tags |
| algorithmic thinking | Blog intro |
| custom sorting | Implementation explanation |
| regular expressions | Validation section |
| job interview | “Why this problem is a good interview question” |
| software engineer | Real‑world application paragraph |
| technical interview | “Talk about this in a technical interview” |

---

## 10. Final Thoughts  

Coupon validation is a **common real‑world scenario**—whether you’re building an e‑commerce site or an ad‑tech platform.  
LeetCode 3606 forces you to think about:

- **Predicate logic** (filtering)  
- **Custom comparators** (sorting by business priority)  
- **Data validation** (regex or manual checks)  

All of this in a handful of lines of code.  

Give it a shot, run it on LeetCode, and keep the snippets handy for your next interview! Happy coding! 🚀