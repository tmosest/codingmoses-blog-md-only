---
title: LeetCode 2409. Count Days Spent Together - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # How to Solve LeetCode 2409 – Count Days Spent Together  
**Java | Python | C++ – A Complete Guide + SEO‑Optimised Blog Post**

---

## Table of Contents  

| Section | Description |
|---------|-------------|
| 1️⃣ Problem Overview | What the problem asks and why it matters for interview prep |
| 2️⃣ Algorithmic Insight | A concise O(1) solution |
| 3️⃣ Java Implementation | Full, ready‑to‑paste code |
| 4️⃣ Python Implementation | Full, ready‑to‑paste code |
| 5️⃣ C++ Implementation | Full, ready‑to‑paste code |
| 6️⃣ Good, Bad & Ugly | What you should love, what to watch out for, and the messy parts |
| 7️⃣ SEO Tips | How this article can boost your LinkedIn / GitHub traffic |
| 8️⃣ Takeaway | Why this problem is a must‑solve for tech interviews |

---

## 1️⃣ Problem Overview

> **LeetCode 2409 – Count Days Spent Together**  
> *Difficulty: Easy*  

Alice and Bob are traveling to Rome. Each has a **start** and **end** date (`"MM-DD"`). We need to return the number of days **both** are in Rome **simultaneously**.

Key constraints:
- All dates are in a **non‑leap** year.
- Strings are always valid `"MM-DD"`.
- The inclusive range `[arrive, leave]` is guaranteed.

> **Why it matters**  
> - It tests **date arithmetic** without relying on external libraries.  
> - It forces you to think in **integer space** – converting months/days to a single day‑of‑year value.  
> - It’s a classic “interval intersection” problem that appears often in interviews.

---

## 2️⃣ Algorithmic Insight

1. **Pre‑compute month offsets** – an array `monthDays = [0,31,59,90,...]` gives the cumulative days *before* each month.  
2. **Convert a `"MM-DD"` string to a “day of year”**:  
   ```text
   dayOfYear = monthDays[month] + day
   ```  
3. **Find the intersection of the two intervals**:  
   ```text
   start = max(startA, startB)
   end   = min(endA,   endB)
   ```
4. If `end < start` → no overlap → answer `0`.  
   Else answer `end - start + 1`.

All operations are **constant time** – O(1) time & O(1) space.

---

## 3️⃣ Java Implementation

```java
/**
 *  LeetCode 2409 – Count Days Spent Together
 *  Author: ChatGPT
 *  Date: 2025‑09‑24
 */
public class Solution {
    // Day offsets before each month in a non‑leap year
    private static final int[] DAYS_BEFORE_MONTH = {
            0,   // dummy for 1‑based indexing
            0,   // Jan
            31,  // Feb
            59,  // Mar
            90,  // Apr
            120, // May
            151, // Jun
            181, // Jul
            212, // Aug
            243, // Sep
            273, // Oct
            304, // Nov
            334  // Dec
    };

    public int countDaysTogether(String arriveAlice, String leaveAlice,
                                 String arriveBob, String leaveBob) {
        int aStart = toDayOfYear(arriveAlice);
        int aEnd   = toDayOfYear(leaveAlice);
        int bStart = toDayOfYear(arriveBob);
        int bEnd   = toDayOfYear(leaveBob);

        int start = Math.max(aStart, bStart);
        int end   = Math.min(aEnd,   bEnd);

        return Math.max(0, end - start + 1);
    }

    /** Convert "MM-DD" → day‑of‑year (1‑based). */
    private int toDayOfYear(String date) {
        int month = Integer.parseInt(date.substring(0, 2));
        int day   = Integer.parseInt(date.substring(3, 5));
        return DAYS_BEFORE_MONTH[month] + day;
    }

    // --- Test harness (optional) ---
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.countDaysTogether("08-15", "08-18", "08-16", "08-19")); // 3
        System.out.println(s.countDaysTogether("10-01", "10-31", "11-01", "12-31")); // 0
    }
}
```

> **Why this Java code is clean**  
> - No external libraries (`java.time`) to keep it “LeetCode‑friendly”.  
> - `private static final` array guarantees compile‑time constant space.  
> - `Math.max/min` elegantly handles empty intersection.

---

## 4️⃣ Python Implementation

```python
# LeetCode 2409 – Count Days Spent Together
# Author: ChatGPT
# Date: 2025-09-24

class Solution:
    # cumulative days before each month (1‑based)
    DAYS_BEFORE_MONTH = [0, 0, 31, 59, 90, 120, 151,
                         181, 212, 243, 273, 304, 334]

    def countDaysTogether(self, arriveAlice: str, leaveAlice: str,
                          arriveBob: str, leaveBob: str) -> int:
        a_start = self.to_day_of_year(arriveAlice)
        a_end   = self.to_day_of_year(leaveAlice)
        b_start = self.to_day_of_year(arriveBob)
        b_end   = self.to_day_of_year(leaveBob)

        start = max(a_start, b_start)
        end   = min(a_end,   b_end)

        return max(0, end - start + 1)

    @staticmethod
    def to_day_of_year(date: str) -> int:
        month, day = map(int, date.split('-'))
        return Solution.DAYS_BEFORE_MONTH[month] + day


# --- Example usage (not part of LeetCode) ---
if __name__ == "__main__":
    sol = Solution()
    print(sol.countDaysTogether("08-15", "08-18", "08-16", "08-19"))  # 3
    print(sol.countDaysTogether("10-01", "10-31", "11-01", "12-31"))  # 0
```

> **Python Highlights**  
> - `split('-')` is concise and readable.  
> - `@staticmethod` keeps helper logic self‑contained.  
> - No need for `datetime` – keeps runtime minimal.

---

## 5️⃣ C++ Implementation

```cpp
// LeetCode 2409 – Count Days Spent Together
// Author: ChatGPT
// Date: 2025-09-24

#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    static constexpr int daysBeforeMonth[13] = {
        0,   // dummy (1‑based index)
        0,   // Jan
        31,  // Feb
        59,  // Mar
        90,  // Apr
        120, // May
        151, // Jun
        181, // Jul
        212, // Aug
        243, // Sep
        273, // Oct
        304, // Nov
        334  // Dec
    };

    int toDayOfYear(const string& date) {
        int month = stoi(date.substr(0, 2));
        int day   = stoi(date.substr(3, 2));
        return daysBeforeMonth[month] + day;
    }

public:
    int countDaysTogether(string arriveAlice, string leaveAlice,
                          string arriveBob, string leaveBob) {
        int aStart = toDayOfYear(arriveAlice);
        int aEnd   = toDayOfYear(leaveAlice);
        int bStart = toDayOfYear(arriveBob);
        int bEnd   = toDayOfYear(leaveBob);

        int start = max(aStart, bStart);
        int end   = min(aEnd,   bEnd);

        return max(0, end - start + 1);
    }
};

// --- Optional test harness ---
int main() {
    Solution s;
    cout << s.countDaysTogether("08-15", "08-18", "08-16", "08-19") << endl; // 3
    cout << s.countDaysTogether("10-01", "10-31", "11-01", "12-31") << endl; // 0
}
```

> **C++ Notes**  
> - Uses `constexpr` for compile‑time constants.  
> - `substr` is safe because input format is fixed.  
> - No STL containers beyond `<string>` and `<bits/stdc++.h>` for brevity.

---

## 6️⃣ Good, Bad & Ugly

| Category | ✅ What’s Great | ⚠️ Things to Watch | 💀 The Ugly (possible pitfalls) |
|----------|----------------|--------------------|---------------------------------|
| **Good** | • O(1) time and space<br>• No heavy libraries<br>• Works with all valid dates | • Handles same‑day intervals<br>• Works even if ranges touch at endpoints | — |
| **Bad** | • None for a simple problem | • If you accidentally use a leap‑year offset, you'll get wrong answers<br>• Forgetting the inclusive `+1` yields off‑by‑one errors | — |
| **Ugly** | — | — | • Hard‑coding month offsets can be error‑prone (mis‑typed values)<br>• Re‑implementing date parsing instead of using `datetime` in Python may feel redundant<br>• In C++ you might forget `constexpr` and end up with runtime array lookups |

**Bottom line:** The problem is intentionally trivial but checks your ability to handle intervals cleanly. Keep the date‑conversion logic compact, avoid heavy libraries, and test edge cases (same day, no overlap, full overlap).

---

## 7️⃣ SEO Tips – How This Blog Can Boost Your Job Search

| Strategy | Implementation in Article |
|----------|---------------------------|
| **Keyword‑rich title** | “LeetCode 2409 – Count Days Spent Together (Java, Python, C++)” |
| **Headings with keywords** | H2: “Java Solution for LeetCode 2409”, H2: “Python Implementation”, H2: “C++ Code” |
| **Meta description** | “Learn how to solve LeetCode 2409 – Count Days Spent Together in Java, Python, and C++. Fast, O(1) solution & interview‑ready code.” |
| **Internal links** | Link to your GitHub repo, your LeetCode profile, or other algorithm posts. |
| **Use of “Interview‑ready” tags** | Emphasize “easy interview question”, “interval intersection”, “date manipulation”. |
| **Code snippets** | Use Markdown code blocks to improve readability for both humans and search engines. |
| **Engagement** | Encourage comments: “Did you solve this before? Share your tricks in the comments.” |

> **Result:** The article will rank for queries like *“LeetCode 2409 solution Java”* or *“count days spent together code”*, driving traffic to your profile and making recruiters spot your problem‑solving skills.

---

## 8️⃣ Takeaway

- **Problem essence**: Find the intersection of two date ranges in a non‑leap year.  
- **Optimal approach**: Convert `"MM-DD"` to a single day‑of‑year integer → O(1) intersection.  
- **Multi‑language implementation**: Java, Python, C++ all share the same logic; just adapt syntax.  
- **Interview value**: Shows mastery of interval arithmetic, constant‑time solutions, and clean coding practices.  

> *“Be prepared to explain your logic in under 5 minutes – that’s how interviewers test your communication skills.”*

Happy coding, and may those interview doors open for you! 🚀