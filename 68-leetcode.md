---
title: LeetCode 68. Text Justification - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 68 – Text Justification  
**Hard – LeetCode** | **Greedy** | **Time O(n)** | **Space O(1)** (ignoring output)

---

## Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| 🎯 Problem | The exact LeetCode statement |
| ✅ Key ideas | Greedy packing, space distribution, left‑justified last line |
| 🧠 Algorithm | Step‑by‑step explanation |
| ⌨️ Code | Java, Python, C++ implementations |
| 🔍 Edge cases | Single‑word lines, uneven space split, last line handling |
| 📈 Complexity | Why the solution is optimal |
| 📚 Interview tips | How to talk about it on a technical interview |
| 🚀 SEO bonus | How this article helps you land a job |

---

## 🎯 Problem Statement

> **Given** an array of words `words` and a maximum line width `maxWidth`, format the text such that each line has **exactly** `maxWidth` characters and is fully (left‑and‑right) justified.  
> Pack words greedily: put as many words as possible into each line.  
> Insert spaces so that each line reaches the required width.  
> For a line with *k* words (`k > 1`), there are `k‑1` gaps. Distribute the extra spaces evenly: if the spaces cannot be divided evenly, the **leftmost gaps get one more space**.  
> The **last line** is left‑justified and has no extra internal spacing.  
> **Constraints**:  
> - `1 ≤ words.length ≤ 300`  
> - `1 ≤ words[i].length ≤ 20`  
> - `1 ≤ maxWidth ≤ 100`  
> - Each word length ≤ `maxWidth`

---

## ✅ Key Ideas

| Idea | Why it matters |
|------|----------------|
| **Greedy packing** | We only need to look at the current line; once it is full we move on. |
| **Space counting** | Keep the total characters of words and the number of words. |
| **Even distribution** | Integer division (`extraSpaces / gaps`) + remainder (`extraSpaces % gaps`). |
| **Special handling** | Last line (left‑justified), lines with a single word (also left‑justified). |

---

## 🧠 Algorithm (Greedy)

1. **Initialize** `idx = 0` – pointer to the first word not yet placed.  
2. While `idx < words.length` do:
   1. **Determine line**:  
      - `lineLen = words[idx].length()`  
      - `last = idx + 1`  
      - While `last < words.length` and `lineLen + 1 + words[last].length() ≤ maxWidth`  
        * `lineLen += 1 + words[last].length()`  
        * `last++`  
   2. **Build the line**:  
      - `numWords = last - idx`  
      - `numSpaces = maxWidth - (lineLen - (numWords - 1))` (the spaces needed to reach width)  
      - **If** `last == words.length` **or** `numWords == 1` → *left‑justify*  
        * Append words with a single space.  
        * Pad the end with spaces.  
      - **Else** → *full justify*  
        * `spacesPerGap = numSpaces / (numWords - 1)`  
        * `extra = numSpaces % (numWords - 1)`  
        * For each word (except the last) append `spacesPerGap + (i < extra ? 1 : 0)` spaces.  
   3. **Advance** `idx = last`.

3. Return the list of built lines.

---

## ⌨️ Code

Below are clean, idiomatic solutions for **Java 17**, **Python 3.10+**, and **C++17**.

### Java

```java
import java.util.*;

public class TextJustification {
    public List<String> fullJustify(String[] words, int maxWidth) {
        List<String> res = new ArrayList<>();
        int i = 0;                     // start index of current line
        while (i < words.length) {
            int lineLen = words[i].length();
            int last = i + 1;
            // Find the last word that fits into the line
            while (last < words.length &&
                   lineLen + 1 + words[last].length() <= maxWidth) {
                lineLen += 1 + words[last].length();
                last++;
            }

            StringBuilder sb = new StringBuilder();
            int numWords = last - i;
            // Number of spaces that need to be distributed
            int totalSpaces = maxWidth - (lineLen - (numWords - 1));

            // Last line or a line with a single word => left‑justified
            if (last == words.length || numWords == 1) {
                for (int j = i; j < last; j++) {
                    sb.append(words[j]);
                    if (j < last - 1) sb.append(' ');
                }
                // Pad the end with spaces
                for (int k = sb.length(); k < maxWidth; k++) sb.append(' ');
            } else {
                // Fully justified
                int spacesPerGap = totalSpaces / (numWords - 1);
                int extraSpaces = totalSpaces % (numWords - 1);
                for (int j = i; j < last - 1; j++) {
                    sb.append(words[j]);
                    // Leftmost gaps get an extra space
                    for (int s = 0; s < spacesPerGap + (j - i < extraSpaces ? 1 : 0); s++)
                        sb.append(' ');
                }
                sb.append(words[last - 1]); // last word, no trailing spaces
            }
            res.add(sb.toString());
            i = last; // move to next line
        }
        return res;
    }
}
```

**Key points**  
- `lineLen` tracks the sum of word lengths **plus** one space between each word.  
- `totalSpaces` is the raw number of blanks needed after subtracting the already present single spaces.  
- The loop `while (last < words.length && ...)` implements the greedy packing.

---

### Python

```python
from typing import List

class Solution:
    def fullJustify(self, words: List[str], maxWidth: int) -> List[str]:
        res = []
        i = 0
        while i < len(words):
            # Determine how many words fit on the current line.
            line_len = len(words[i])
            last = i + 1
            while last < len(words) and line_len + 1 + len(words[last]) <= maxWidth:
                line_len += 1 + len(words[last])
                last += 1

            num_words = last - i
            total_spaces = maxWidth - (line_len - (num_words - 1))
            line = ""

            # Last line or a line with a single word => left‑justified.
            if last == len(words) or num_words == 1:
                line = " ".join(words[i:last])
                line += " " * (maxWidth - len(line))
            else:
                # Full justification.
                spaces_per_gap = total_spaces // (num_words - 1)
                extra = total_spaces % (num_words - 1)
                for j in range(i, last - 1):
                    line += words[j]
                    # The leftmost gaps get an extra space.
                    line += " " * (spaces_per_gap + (j - i < extra))
                line += words[last - 1]

            res.append(line)
            i = last
        return res
```

---

### C++

```cpp
#include <vector>
#include <string>

class Solution {
public:
    std::vector<std::string> fullJustify(std::vector<std::string>& words, int maxWidth) {
        std::vector<std::string> result;
        size_t i = 0;
        while (i < words.size()) {
            // 1. Find the end of the line
            int lineLen = words[i].size();
            size_t last = i + 1;
            while (last < words.size() &&
                   lineLen + 1 + words[last].size() <= maxWidth) {
                lineLen += 1 + words[last].size();
                ++last;
            }

            // 2. Build the line
            std::string line;
            int numWords = last - i;
            int totalSpaces = maxWidth - (lineLen - (numWords - 1));

            if (last == words.size() || numWords == 1) {
                // Left‑justified
                for (size_t j = i; j < last; ++j) {
                    line += words[j];
                    if (j + 1 < last) line += ' ';
                }
                line.append(maxWidth - line.size(), ' ');
            } else {
                // Full justification
                int spacesPerGap = totalSpaces / (numWords - 1);
                int extra = totalSpaces % (numWords - 1);
                for (size_t j = i; j < last - 1; ++j) {
                    line += words[j];
                    int spaces = spacesPerGap + (j - i < extra ? 1 : 0);
                    line.append(spaces, ' ');
                }
                line += words[last - 1];
            }

            result.push_back(line);
            i = last;  // Move to next line
        }
        return result;
    }
};
```

---

## 🔍 Edge Cases & Common Pitfalls

| Case | Why it matters | How the solution handles it |
|------|----------------|-----------------------------|
| **Single word per line** | No internal spaces to distribute. | `numWords == 1` → left‑justified path. |
| **Last line** | Must be left‑justified regardless of word count. | `last == words.size()` triggers left‑justified path. |
| **Uneven space distribution** | Leftmost gaps receive one extra space. | `extra = totalSpaces % (numWords - 1)` and add to left gaps. |
| **Word exactly equal to `maxWidth`** | No spaces between words. | The greedy loop stops before adding another word; the line becomes left‑justified. |
| **Very long line of short words** | Multiple spaces may be needed. | Calculations correctly handle large `totalSpaces`. |

---

## 📈 Complexity Analysis

| Metric | Calculation | Result |
|--------|-------------|--------|
| **Time** | Each word is examined once → `O(n)` | `O(n)` where `n = words.length` |
| **Space** | Only a few integer variables + output list | `O(1)` auxiliary space (excluding output) |

The solution is linear, the fastest possible because every word must be visited at least once.

---

## 📚 Interview Tips

1. **Explain the greedy decision**: “We keep adding words until we can't fit the next one.”  
2. **Show your reasoning for space distribution**: talk about integer division and remainder.  
3. **Highlight edge‑case handling**: single‑word lines, last line, uneven spaces.  
4. **Complexity**: “Linear time, constant auxiliary space.”  
5. **Show a quick sketch** (draw lines, gaps, spaces) – it demonstrates clear communication.

---

## 🚀 SEO Bonus – Landing a Job with This Article

* **Keywords**: LeetCode Text Justification, Greedy Algorithm, Java Interview Question, Python Text Justification, C++ Text Justification, Full Justification, Interview Prep, Coding Interview, Technical Interview, Algorithm Design.  
* **Meta description**: “Solve LeetCode’s Text Justification (Hard) in Java, Python, and C++. Learn a greedy algorithm, edge‑case handling, and interview‑ready explanations. Land your next tech job!”  
* **Headers**: Use H1 for the title, H2 for sections, H3 for sub‑sections – Google loves semantic structure.  
* **Code snippets**: Visible code blocks help search engines recognize the content is a tutorial.  
* **Readability**: Bullet points, short paragraphs, and highlighted code keep users engaged → lower bounce rate.  

By posting this article, sharing it on coding blogs, or adding it to your GitHub README, you’re not just practicing coding—you’re also showcasing your ability to write clean, interview‑ready solutions in multiple languages.

---

**Takeaway**: A concise greedy algorithm, careful space math, and robust edge‑case handling make this Hard LeetCode problem a perfect showcase for both your coding chops and your interview communication skills. Happy coding, and best of luck on the next job interview!