---
title: LeetCode 3606. Coupon Code Validator - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3606. **Coupon Code Validator** – Full‑Stack Solution (Java, Python, C++)  
> *“Clean Code | Beginner‑Friendly | Custom Sort”*

---

### TL;DR  
| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| **Java** | **O(n log n)** | **O(n)** |
| **Python** | **O(n log n)** | **O(n)** |
| **C++** | **O(n log n)** | **O(n)** |

The solution is a simple filter‑then‑sort routine.  
- **Filter**: keep only coupons that are active, non‑empty, have a valid alphanum‑underscore code, and belong to one of the four business categories.  
- **Sort**: use a priority map for the four categories and then lexicographical order of the code.

All three implementations are < 40 lines of clean, production‑ready code.



---

## 1. Java Implementation

```java
import java.util.*;

public class Solution {

    // Order of business lines – lower number = higher priority
    private static final Map<String, Integer> ORDER = Map.of(
            "electronics", 0,
            "grocery",     1,
            "pharmacy",    2,
            "restaurant",  3
    );

    public List<String> validateCoupons(String[] code,
                                        String[] businessLine,
                                        boolean[] isActive) {

        List<Coupon> valid = new ArrayList<>();

        for (int i = 0; i < code.length; i++) {
            // --- 1️⃣ Basic checks ------------------------------------------------
            if (code[i] == null || code[i].isEmpty()) continue;
            if (!code[i].matches("[a-zA-Z0-9_]+")) continue;
            if (!ORDER.containsKey(businessLine[i])) continue;
            if (!isActive[i]) continue;

            // --- 2️⃣ Store a lightweight coupon object -------------------------
            valid.add(new Coupon(code[i], businessLine[i]));
        }

        // --- 3️⃣ Sort according to business priority then lexicographic order
        valid.sort(Comparator
                .comparingInt((Coupon c) -> ORDER.get(c.businessLine))
                .thenComparing(c -> c.code));

        // --- 4️⃣ Extract just the codes for the final answer -----------------
        List<String> result = new ArrayList<>(valid.size());
        for (Coupon c : valid) result.add(c.code);
        return result;
    }

    // Lightweight container – no getters / setters for brevity
    private static class Coupon {
        final String code;
        final String businessLine;
        Coupon(String code, String businessLine) {
            this.code = code;
            this.businessLine = businessLine;
        }
    }
}
```

---

## 2. Python Implementation

```python
import re
from typing import List

class Solution:
    # Priority order for the four business categories
    ORDER = {"electronics": 0, "grocery": 1,
             "pharmacy": 2, "restaurant": 3}

    def validateCoupons(self,
                        code: List[str],
                        businessLine: List[str],
                        isActive: List[bool]) -> List[str]:

        # Regex for alphanumeric + underscore
        pattern = re.compile(r'^[A-Za-z0-9_]+$')

        # 1️⃣ Filter out the valid coupons
        valid = [
            (c, bl) for c, bl, act in zip(code, businessLine, isActive)
            if act and c and pattern.match(c) and bl in self.ORDER
        ]

        # 2️⃣ Sort: first by business priority, then lexicographically
        valid.sort(key=lambda x: (self.ORDER[x[1]], x[0]))

        # 3️⃣ Return only the codes
        return [c for c, _ in valid]
```

---

## 3. C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> validateCoupons(vector<string> code,
                                   vector<string> businessLine,
                                   vector<bool> isActive) {
        // 1️⃣ Business priority map
        unordered_map<string, int> order = {
            {"electronics", 0},
            {"grocery",     1},
            {"pharmacy",    2},
            {"restaurant",  3}
        };

        // 2️⃣ Helper regex: alphanumeric + underscore
        regex pattern(R"([A-Za-z0-9_]+)");

        // 3️⃣ Store valid coupons as pairs (code, businessLine)
        vector<pair<string, string>> valid;
        for (size_t i = 0; i < code.size(); ++i) {
            if (!isActive[i]) continue;
            if (code[i].empty() || !regex_match(code[i], pattern)) continue;
            if (!order.count(businessLine[i])) continue;
            valid.emplace_back(code[i], businessLine[i]);
        }

        // 4️⃣ Custom sort: priority then lexicographic
        sort(valid.begin(), valid.end(),
             [&](const auto &a, const auto &b) {
                 int p = order[a.second] - order[b.second];
                 if (p != 0) return p < 0;
                 return a.first < b.first;
             });

        // 5️⃣ Extract the sorted codes
        vector<string> result;
        result.reserve(valid.size());
        for (const auto &p : valid) result.push_back(p.first);
        return result;
    }
};
```

---

## 4. Blog Article: “Coupon Code Validator – The Good, The Bad, and The Ugly”

> **SEO Focus:** Coupon code validator, LeetCode 3606, Java/Python/C++ solution, interview question, clean code, custom sort, job interview

---

### 4.1  Introduction

When I first saw *Coupon Code Validator* (LeetCode 3606), the problem seemed almost too easy for a 2‑minute interview: “Filter and sort coupons.”  
But there are subtle traps—empty strings, weird characters, invalid business categories—that can trip even seasoned engineers.  

In this article I’ll walk through the **good** (what the problem teaches), the **bad** (common pitfalls), and the **ugly** (hidden edge cases). I’ll finish with a clean, production‑ready solution in **Java, Python, and C++** that you can drop into your résumé or interview notes.

---

### 4.2  Problem Recap

> **Input**  
> Three arrays of length *n* (`code`, `businessLine`, `isActive`).

> **Rules for a valid coupon**  
> 1. `code` is non‑empty and contains only letters, digits, or underscore (`_`).  
> 2. `businessLine` ∈ {“electronics”, “grocery”, “pharmacy”, “restaurant”}.  
> 3. `isActive` == `true`.

> **Output**  
> Sorted list of valid coupon codes, first by business priority  
> (`electronics → grocery → pharmacy → restaurant`), then lexicographically.

> **Constraints**  
> 1 ≤ n ≤ 100, each string length ≤ 100.  
> All inputs are printable ASCII.

---

### 4.3  The Good – Why This Problem is Valuable

1. **Clear Specifications** – The rules are unambiguous, so the problem tests *reading comprehension*.
2. **Simple Data Structures** – Lists/arrays and a small lookup map.  
   Great for practicing *collection manipulation*.
3. **Custom Sort** – Introduces the idea of a *priority map* plus a *secondary key*.
4. **Regex Basics** – Validating a coupon code with a single regex (`[A-Za-z0-9_]+`) is a quick way to show knowledge of pattern matching.
5. **Time‑Space Trade‑Off** – The naive O(n²) solution would be slower; the optimal O(n log n) solution is straightforward.

---

### 4.4  The Bad – Common Pitfalls

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Using `==` for strings** | In Java or C++, string equality is reference‑based. | Use `.equals()` or `==` after storing in a `unordered_map`. |
| **Ignoring `null` values** | `code[i]` or `businessLine[i]` can be `null` in Java tests. | Add `if (code[i] == null || code[i].isEmpty()) continue;` |
| **Omitting regex boundaries** | `matches("[A-Za-z0-9_]+")` can match substrings. | Prepend `^` and append `$` → `^[A-Za-z0-9_]+$`. |
| **Sorting with wrong comparator** | Priority map value is used incorrectly (e.g., `-ORDER.get()` vs `ORDER.get()`). | Keep comparator **ascending**: `Comparator.comparingInt(c -> ORDER.get(c.businessLine))`. |
| **Duplicate business lines** | If the same code appears twice, the sort might still order them correctly, but some people forget to preserve the order of duplicates. | Store a lightweight object (`Coupon`) instead of a `String[]` pair. |
| **Over‑engineering** | Writing a full class with getters/setters for such a tiny function. | Use a minimal inner class or tuple; keep the code readable. |

---

### 4.5  The Ugly – Hidden Edge Cases

| Edge Case | Impact | How to Guard |
|-----------|--------|--------------|
| `code` = “_” | Valid by regex but semantically strange. | The regex still accepts it – the spec says only alphanum or underscore, so it’s fine. |
| Duplicate valid codes | Sorting still works; no duplicates are removed. | If you want uniqueness, you can add a `Set` before the final extraction. |
| Very long `code` (100 chars) | Might hit recursion depth or buffer limits in languages with small stacks. | No effect here because n ≤ 100; but still use `StringBuilder`/`vector` pre‑allocation in Java/C++. |
| Mixing `True`/`true` in input | Some interviewers give a boolean array of strings (“True”). | Convert `isActive[i]` to a Boolean (`isActive[i] == "true"`). |
| Leading/trailing whitespace | Regex `[A-Za-z0-9_]+` will reject spaces, but people sometimes trim first. | No trimming required by spec. |

---

### 4.6  Clean‑Code Solution – Why It Looks Good

1. **Single Responsibility** – `validateCoupons` does *exactly* what its name says.  
2. **Immutable Order Map** – `Map.of(...)` or `static constexpr` keeps the priority constant and thread‑safe.  
3. **Regular Expression** – A single line (`^[A-Za-z0-9_]+$`) encapsulates the entire code‑validation logic.  
4. **Modern Comparator** – Java’s lambda + `thenComparing` reads like a business requirement.  
5. **Minimal Container** – The `Coupon` inner class holds only the data needed for sorting.  
6. **Pre‑allocation** – `result.reserve(valid.size())` avoids needless reallocations in C++/Java.  

> **Interview Tip** – When asked about this problem, say: *“I used a small lookup map for business priority and a regex for the code. That lets me filter in linear time and then sort in O(n log n), which is optimal for the constraints.”*

---

### 4.7  Full‑Stack Code – Copy & Paste

> Each snippet below compiles on the latest JDK 17 / CPython 3.10 / GCC 12.

| Language | Code |
|----------|------|
| **Java** | <details><summary>Show</summary>```java\nimport java.util.*;\n\npublic class Solution {\n    private static final Map<String, Integer> ORDER = Map.of(\n        \"electronics\", 0,\n        \"grocery\",     1,\n        \"pharmacy\",    2,\n        \"restaurant\",  3\n    );\n\n    public List<String> validateCoupons(String[] code, String[] businessLine, boolean[] isActive) {\n        List<Coupon> valid = new ArrayList<>();\n        for (int i = 0; i < code.length; i++) {\n            if (code[i] == null || code[i].isEmpty()) continue;\n            if (!code[i].matches(\"^[A-Za-z0-9_]+$\")) continue;\n            if (!ORDER.containsKey(businessLine[i])) continue;\n            if (!isActive[i]) continue;\n            valid.add(new Coupon(code[i], businessLine[i]));\n        }\n        valid.sort(Comparator\n                .comparingInt((Coupon c) -> ORDER.get(c.businessLine))\n                .thenComparing(c -> c.code));\n        List<String> result = new ArrayList<>(valid.size());\n        for (Coupon c : valid) result.add(c.code);\n        return result;\n    }\n\n    private static class Coupon {\n        final String code;\n        final String businessLine;\n        Coupon(String code, String businessLine) {\n            this.code = code;\n            this.businessLine = businessLine;\n        }\n    }\n}\n```\n</details> |
| **Python** | <details><summary>Show</summary>```python\nimport re\nfrom typing import List\n\nclass Solution:\n    ORDER = {\"electronics\": 0, \"grocery\": 1, \"pharmacy\": 2, \"restaurant\": 3}\n\n    def validateCoupons(self, code: List[str], businessLine: List[str], isActive: List[bool]) -> List[str]:\n        pattern = re.compile(r'^[A-Za-z0-9_]+$')\n        valid = [\n            (c, bl) for c, bl, act in zip(code, businessLine, isActive)\n            if act and c and pattern.match(c) and bl in self.ORDER\n        ]\n        valid.sort(key=lambda x: (self.ORDER[x[1]], x[0]))\n        return [c for c, _ in valid]\n```\n</details> |
| **C++** | <details><summary>Show</summary>```cpp\n#include <bits/stdc++.h>\nusing namespace std;\n\nclass Solution {\npublic:\n    vector<string> validateCoupons(vector<string> code, vector<string> businessLine, vector<bool> isActive) {\n        unordered_map<string, int> order = {\n            {\"electronics\", 0},\n            {\"grocery\",     1},\n            {\"pharmacy\",    2},\n            {\"restaurant\",  3}\n        };\n        regex pattern(R\"([A-Za-z0-9_]+)\");\n        vector<pair<string, string>> valid;\n        for (size_t i = 0; i < code.size(); ++i) {\n            if (!isActive[i]) continue;\n            if (code[i].empty() || !regex_match(code[i], pattern)) continue;\n            if (!order.count(businessLine[i])) continue;\n            valid.emplace_back(code[i], businessLine[i]);\n        }\n        sort(valid.begin(), valid.end(), [&](const auto &a, const auto &b) {\n            int p = order[a.second] - order[b.second];\n            if (p != 0) return p < 0;\n            return a.first < b.first;\n        });\n        vector<string> res;\n        res.reserve(valid.size());\n        for (auto &p : valid) res.push_back(p.first);\n        return res;\n    }\n};\n```\n</details> |

---

### 4.8  Take‑Away Checklist for Interviewers

- ✅ **Read the spec carefully** – every rule matters.  
- ✅ **Use a priority map** – it keeps the sorting logic readable.  
- ✅ **Validate with a single regex** – avoid over‑engineering the pattern.  
- ✅ **Sort with a stable comparator** – first key first, then second.  
- ✅ **Return only the data you’re asked for** – don’t leak internal objects.  

Feel free to paste the language‑specific snippets into your résumé or into the “My Solutions” section of your GitHub profile. When the interviewer asks “How would you do it in another language?” you’ll already have a ready‑to‑copy answer.

---

### 4.9  Closing Thoughts

*Coupon Code Validator* may feel trivial on paper, but it’s an excellent **micro‑test** of clean code practices:

- **Parsing & Validation** – shows you can write and reason about regex.  
- **Mapping & Sorting** – demonstrates familiarity with lookup tables and custom comparators.  
- **Edge‑Case Awareness** – proves you’re careful about `null`, empty strings, and invalid data.

So next time you’re in a coding interview, give this problem a thumbs‑up, and when you code it, keep it as clean as the snippet above. It’ll impress hiring managers, score points in the interview, and look great on your résumé. Happy coding!