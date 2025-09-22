---
title: LeetCode 1507. Reformat Date - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Reformat Date – A Complete Interview‑Ready Guide  
*(Java | Python | C++)*  

---

### Table of Contents  

| # | Section |
|---|---------|
| 1 | 📖 Problem Statement |
| 2 | 🧠 Core Idea & Edge Cases |
| 3 | 📦 Solution Approaches |
| 4 | ⚙️ Java Implementation |
| 5 | 🐍 Python Implementation |
| 6 | 🧱 C++ Implementation |
| 7 | ⏱️ Time & Space Complexity |
| 8 | 🔍 Common Pitfalls & “The Ugly” |
| 9 | 💡 Interview Tips & Soft‑Skill Boost |
| 10 | 🎯 SEO & Job‑Hunting Checklist |

---

## 1. 📖 Problem Statement

> **LeetCode 1507 – Reformat Date**  
> **Difficulty:** Easy  
> Given a date string in the form **`Day Month Year`** (e.g., `"20th Oct 2052"`), convert it to **`YYYY-MM-DD`**.  
> 
> - **Day** is a string in the set `{"1st", "2nd", ..., "31st"}`.  
> - **Month** is an abbreviation: `{"Jan", "Feb", ..., "Dec"}`.  
> - **Year** is an integer in `[1900, 2100]`.  
> 
> The input dates are always valid.

---

## 2. 🧠 Core Idea & Edge Cases

1. **Split** the input on spaces → `[day, month, year]`.  
2. **Strip** the ordinal suffix (`st`, `nd`, `rd`, `th`) from the day.  
3. **Pad** a single‑digit day with a leading zero.  
4. **Map** month abbreviations to numeric values (`Jan → 01`, …).  
5. **Concatenate** into `"YYYY-MM-DD"`.

*Edge cases are minimal*:  
- Day “1st” → “01”.  
- Month “Jan” → “01”.  
- Years stay untouched.  
The problem guarantees validity, so we don’t need error handling.

---

## 3. 📦 Solution Approaches

| Approach | Why it works | Typical time | Typical space |
|----------|--------------|--------------|---------------|
| **Split + HashMap** | Direct mapping, O(1) lookup | O(1) | O(1) |
| **Switch / Enum** | Compile‑time mapping, no hashmap | O(1) | O(1) |
| **Regex** | Extract parts in one pass | O(n) | O(1) |

For an *interview* you usually pick the **Split + HashMap** approach – it’s concise and clearly demonstrates string manipulation skills.

---

## 4. ⚙️ Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public String reformatDate(String date) {
        // 1. Split into [day, month, year]
        String[] parts = date.split(" ");

        // 2. Month mapping
        Map<String, String> monthMap = new HashMap<>();
        monthMap.put("Jan", "01");
        monthMap.put("Feb", "02");
        monthMap.put("Mar", "03");
        monthMap.put("Apr", "04");
        monthMap.put("May", "05");
        monthMap.put("Jun", "06");
        monthMap.put("Jul", "07");
        monthMap.put("Aug", "08");
        monthMap.put("Sep", "09");
        monthMap.put("Oct", "10");
        monthMap.put("Nov", "11");
        monthMap.put("Dec", "12");

        // 3. Extract year
        String year = parts[2];

        // 4. Convert month
        String month = monthMap.get(parts[1]);

        // 5. Clean day (remove last 2 chars)
        String day = parts[0].substring(0, parts[0].length() - 2);
        if (day.length() == 1) day = "0" + day;

        return String.format("%s-%s-%s", year, month, day);
    }
}
```

**Key takeaways for recruiters:**  
- Uses **HashMap** for O(1) lookups.  
- Handles zero‑padding succinctly.  
- No external libraries – pure Java SE.

---

## 5. 🐍 Python Implementation

```python
def reformatDate(date: str) -> str:
    # Split: ['20th', 'Oct', '2052']
    day_str, month_str, year = date.split()

    # Strip ordinal suffix
    day = int(day_str[:-2])          # e.g., '20th' -> 20

    # Month map (list index 0 -> Jan)
    month_map = ["Jan", "Feb", "Mar", "Apr", "May", "Jun",
                 "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"]
    month = month_map.index(month_str) + 1  # 1‑based

    # Format with zero padding
    return f"{year}-{month:02d}-{day:02d}"
```

**Why Python works well:**  
- `int(day_str[:-2])` automatically removes the suffix.  
- List `index()` gives month number in O(12).  
- `:02d` keeps code clean.

---

## 6. 🧱 C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string reformatDate(string date) {
        // Split string manually to avoid regex
        stringstream ss(date);
        string dayStr, monthStr, year;
        ss >> dayStr >> monthStr >> year;

        // Remove last two characters (ordinal suffix)
        string day = dayStr.substr(0, dayStr.size() - 2);

        // Month mapping via array
        vector<string> months = {
            "Jan","Feb","Mar","Apr","May","Jun",
            "Jul","Aug","Sep","Oct","Nov","Dec"
        };
        string month;
        for (int i = 0; i < 12; ++i) {
            if (months[i] == monthStr) {
                month = (i < 9 ? "0" : "") + to_string(i + 1);
                break;
            }
        }

        // Ensure day is two digits
        if (day.size() == 1) day = "0" + day;

        return year + "-" + month + "-" + day;
    }
};
```

**Highlights for C++ recruiters:**  
- Uses `stringstream` for robust splitting.  
- `vector<string>` for month map – O(1) lookups via simple loop.  
- `to_string` & string concatenation keep code readable.

---

## 7. ⏱️ Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | **O(1)** (fixed‑size string ops, map lookup) | **O(1)** (12‑entry map) |
| Python | **O(1)** | **O(1)** |
| C++ | **O(1)** | **O(1)** |

All solutions run in constant time because the input length is bounded (≤ 20 characters).

---

## 8. 🔍 Common Pitfalls & “The Ugly”

| Pitfall | Why it’s bad | Fix |
|---------|--------------|-----|
| **Missing leading zeros** | `"3rd Jan 1999"` → `"1999-01-3"` (wrong format) | Pad day and month with `:02d` (Python) or `String.format("%02d")` (Java) |
| **Hard‑coding suffix lengths** | Assuming all days end with 2 chars; “11th” vs “1st” | Use `int(dayStr[:-2])` or `substr(0, len-2)` |
| **Case‑sensitive month map** | Input “Oct” vs “oct” | Convert to proper case or use a case‑insensitive map |
| **Using regex with backtracking** | Overkill for a fixed format | Prefer simple split + map |
| **Ignoring year range** | No need – input guaranteed valid | Still, sanity checks can be added for defensive coding |

### The Ugly: Over‑engineering  

> “Can I use a full date parser library?”  
>  
> **Answer:** For this LeetCode problem, a **tiny** custom parser is preferable. Using heavy libraries (e.g., `java.time`, `datetime` with `%b`) may confuse interviewers and clutter your solution. Keep it **lean**.

---

## 9. 💡 Interview Tips & Soft‑Skill Boost

1. **Clarify the problem**: Confirm input format, ask about edge cases.  
2. **Explain your plan**: Outline split → map → format.  
3. **Show trade‑offs**: Why you chose a hashmap vs enum vs regex.  
4. **Write clean code**: Use meaningful variable names (`dayStr`, `monthAbbrev`).  
5. **Test quickly**: Run a few examples on a REPL to validate.  
6. **Talk about time/space**: Always bring up O(1) for interviewers.  
7. **Mention extensibility**: If you had to support full ISO dates, how would you modify?  

---

## 10. 🎯 SEO & Job‑Hunting Checklist

| ✅ Item | Why it matters |
|---------|----------------|
| **Keyword‑rich title** | “LeetCode 1507 – Reformat Date” attracts recruiters searching the exact problem. |
| **Meta description** | “Solve LeetCode Reformat Date in Java, Python, and C++. Learn clean code, complexity analysis, and interview tips.” |
| **Headings with keywords** | `## 📦 Solution Approaches`, `## ⚙️ Java Implementation` etc. |
| **Code blocks** | Highlighted snippets make the post skimmable for hiring managers. |
| **Link to LeetCode** | `https://leetcode.com/problems/reformat-date/` – shows authenticity. |
| **Call‑to‑action** | “Want to master algorithmic challenges? Follow for more solutions.” |
| **Share on LinkedIn / Twitter** | Recruiters often browse tech blogs. |

---

### TL;DR

- **Problem**: Convert `"Day Month Year"` → `"YYYY-MM-DD"`.  
- **Solution**: Split → strip suffix → map month → pad → join.  
- **Languages**: Java, Python, C++.  
- **Complexity**: O(1) time, O(1) space.  
- **Interview**: Keep it clean, explain trade‑offs, test quickly.  

Happy coding and good luck landing that software‑engineering role! 🚀