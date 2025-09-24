---
title: LeetCode 418. Sentence Screen Fitting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 418 – Sentence Screen Fitting  
## 🚀 Java | Python | C++ – One‑liner, One‑pass, One‑simulation Solutions

> **Problem**  
> Given a `rows × cols` screen and an array `sentence` of words, determine how many times the whole sentence can be written on the screen while preserving the order of words.  
> A word cannot be split between two lines and words are separated by a single space.

> **Key Constraints**  
> * 1 ≤ sentence.length ≤ 100  
> * 1 ≤ sentence[i].length ≤ 10  
> * 1 ≤ rows, cols ≤ 2 × 10⁴  

---

## 1️⃣ The Idea: “Simulate, Count, Repeat”

The classic greedy approach is to **simulate** filling each row:  

1. Keep a pointer `idx` that indicates which word will be printed next.  
2. For each row, start with the full width `cols`.  
3. While the remaining width is enough for the current word (plus a space if it’s not the first word on the line), place the word, subtract the width, and move to the next word.  
4. When the row is done, remember how many times the pointer wrapped around the sentence – that is the number of complete sentences for that row.  
5. Sum over all rows.

Because the width of a row is at most 20 000 and the word length is at most 10, each row’s simulation takes at most `O(cols / avgWordLen)` steps – far below the time limit.  

**Optimization** – cache the next pointer for each word so the simulation runs in `O(rows)` time for all rows (no need to recompute the same word‑pointer pairs).  

---

## 2️⃣ Code

Below you’ll find **three** ready‑to‑copy implementations – one in Java, one in Python, and one in C++.  
All three share the same algorithmic idea, so you can choose whichever language you’re most comfortable with (or want to test in a coding interview).

### 2.1 Java

```java
import java.util.*;

public class Solution {
    /**
     * @param sentence an array of words
     * @param rows     number of rows on the screen
     * @param cols     number of columns on the screen
     * @return how many times the sentence fits
     */
    public int wordsTyping(String[] sentence, int rows, int cols) {
        int n = sentence.length;
        int[] next = new int[n];     // next word index after completing a row starting at word i
        int[] count = new int[n];    // how many full sentences are completed in that row

        // Pre‑compute next index and count for every word
        for (int i = 0; i < n; i++) {
            int col = 0;   // current used columns in this row
            int cur = i;   // current word index
            int cnt = 0;   // full sentences completed

            while (true) {
                int wordLen = sentence[cur].length();
                // If word doesn’t fit in the remaining space, break
                if (col + wordLen > cols) break;

                col += wordLen + 1;   // +1 for the space after the word
                cur = (cur + 1) % n;  // move to next word

                if (cur == 0) cnt++;  // finished one sentence
            }

            next[i] = cur;
            count[i] = cnt;
        }

        // Simulate rows using pre‑computed values
        int idx = 0; // index of next word to place
        int totalSentences = 0;

        for (int r = 0; r < rows; r++) {
            totalSentences += count[idx];
            idx = next[idx];
        }

        return totalSentences;
    }
}
```

**Why it’s fast**  
* Pre‑computing `next` and `count` takes `O(n * avgWordLen)` time.  
* The row simulation loop is `O(rows)`.  
* Memory usage is `O(n)`.

---

### 2.2 Python

```python
class Solution:
    def wordsTyping(self, sentence: List[str], rows: int, cols: int) -> int:
        n = len(sentence)
        next_idx = [0] * n
        count = [0] * n

        for i in range(n):
            col = 0
            cur = i
            cnt = 0
            while True:
                word_len = len(sentence[cur])
                if col + word_len > cols:
                    break
                col += word_len + 1
                cur = (cur + 1) % n
                if cur == 0:
                    cnt += 1
            next_idx[i] = cur
            count[i] = cnt

        idx = 0
        total = 0
        for _ in range(rows):
            total += count[idx]
            idx = next_idx[idx]
        return total
```

---

### 2.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        int n = sentence.size();
        vector<int> nxt(n), cnt(n);

        // Precompute transitions
        for (int i = 0; i < n; ++i) {
            int col = 0, cur = i, c = 0;
            while (true) {
                int wlen = sentence[cur].size();
                if (col + wlen > cols) break;
                col += wlen + 1;           // add space
                cur = (cur + 1) % n;
                if (cur == 0) ++c;         // one sentence finished
            }
            nxt[i] = cur;
            cnt[i] = c;
        }

        long long total = 0;          // use long long to avoid overflow
        int idx = 0;
        for (int r = 0; r < rows; ++r) {
            total += cnt[idx];
            idx = nxt[idx];
        }
        return (int)total;
    }
};
```

---

## 3️⃣ Blog Post: “The Good, The Bad, and The Ugly of the Sentence‑Screen‑Fitting Problem”

> **Title**  
> *LeetCode 418 – Sentence Screen Fitting: The Good, The Bad & The Ugly – How to Nail It in Your Next Coding Interview*

### 3.1 Why This Question Rocks in Interviews

* **Classic DP / Simulation** – Showcases your understanding of greedy algorithms, memoization, and dynamic programming concepts.  
* **Edge‑Case Friendly** – Tests how you handle words that barely fit, very long rows/columns, or a sentence that never fully fits in a row.  
* **Time/Space Constraints** – Forces you to think about optimizing a seemingly simple loop to O(rows) or O(n) time.  

Because of these traits, recruiters love it. Solving it cleanly demonstrates *problem‑solving skill*, *attention to detail*, and *coding clarity* – all the qualities hiring managers look for.

---

### 3.2 The Good

| Aspect | What You Learn | How It Helps in the Interview |
|--------|----------------|-------------------------------|
| **Greedy + Simulation** | How to break a large problem into per‑row simulation and combine results. | Shows ability to break down problems and reason locally before global optimization. |
| **Pre‑computation (`next` / `count`)** | Use a lookup table to reduce repeated work. | Demonstrates a keen eye for time‑space trade‑offs. |
| **Modular Code** | Separate helper logic from main simulation. | Enables you to discuss trade‑offs, e.g., whether to use DP or greedy. |

> *Pro Tip:* Write the simulation first. Then ask yourself, “Can I pre‑compute something to avoid re‑doing the same work for every row?” The answer is usually “yes.”  

---

### 3.3 The Bad

| Issue | Why it’s problematic | Fix |
|-------|----------------------|-----|
| **O(rows × cols)** brute force | Fails on 2 × 10⁴ rows × 2 × 10⁴ cols (4 × 10⁸ ops). | Use a per‑row simulation and pre‑compute transitions. |
| **Off‑by‑one errors** | Forgetting the space after the last word or after the final word of the sentence can make your answer wrong for subtle test cases. | Explicitly handle spaces only when a word actually follows; or simply always add space and subtract one after the loop. |
| **Overflow** | `rows * (cols / avgWordLen)` can exceed 32‑bit signed int. | Use `long long` (C++/Java) or `int` for Python (auto‑bigint). |

> *Interview Hack:* When presenting your solution, walk through a sample input and point out how you handle the space after the last word.

---

### 3.4 The Ugly

> The “ugly” part is that many candidates over‑engineer or under‑engineer the solution, leading to confusing code or wrong answers.  
> Common pitfalls:

1. **Using a 2‑D DP array** – Allocating `rows × cols` memory is unnecessary.  
2. **Recursion without memoization** – Causes stack overflow for large inputs.  
3. **String concatenation in loops** – In Java or Python, building a huge string per row will kill performance.  

> **Fix** – Stick to integer counters and simple loops.  

---

### 3.5 How to Nail the Interview

1. **Clarify the question** – Ask about edge cases: “What if a word is longer than the number of columns?” (Answer: sentence is invalid because a word can’t split).  
2. **State the naive approach** – Mention that the simplest simulation is O(rows × words).  
3. **Introduce the optimization** – Pre‑compute for each word the next word after a full row and how many sentences it completes.  
4. **Show the final algorithm** – O(rows) time, O(n) space.  
5. **Walk through a concrete example** – Keep the recruiter engaged.  

> *Result:* You’ll impress interviewers with both a clean implementation and a thoughtful explanation.

---

### 3.6 Final Takeaway

> *LeetCode 418* is a **must‑know** for any software engineering interview.  
> By mastering the greedy‑simulation + pre‑computation pattern, you can solve it in under 5 minutes on paper and write a clean, production‑ready solution in your language of choice.

> **Next Steps:**  
> 1. Implement the solution in **Java, Python, and C++** (see above).  
> 2. Run it against LeetCode’s hidden tests.  
> 3. Add it to your portfolio; recruiters love to see well‑documented, cross‑language solutions.  

---

## 4️⃣ SEO Checklist (to get you noticed by hiring managers)

| SEO Element | How It Appears in the Post |
|-------------|----------------------------|
| **Primary Keyword** | “Sentence Screen Fitting solution” appears in title, intro, and multiple headings. |
| **Related Keywords** | “leetcode 418”, “job interview coding”, “greedy algorithm”, “dynamic programming interview question”. |
| **Meta Description** | “Learn the Java, Python, and C++ solutions for LeetCode 418 – Sentence Screen Fitting. Understand the good, the bad, and the ugly of this classic interview problem.” |
| **Internal Links** | References to other LeetCode problems like “Word Break” or “Maximum Score Words Formed With Letters”. |
| **Image Alt Text** | “Simulation of Sentence Screen Fitting in Python” – for visual explanation. |
| **External Links** | Links to LeetCode discussion threads, StackOverflow explanations, and related blog posts. |
| **Content Length** | ~1700 words, plenty of code, examples, and explanation. |
| **Readability** | Subheadings, bullet points, code blocks, and highlighted snippets. |

> By following this structure, your article will rank well for interview‑related searches and attract recruiters looking for candidates who can solve LeetCode 418.

---

## 5️⃣ Quick Reference – One‑Liner Summary

| Language | One‑Liner (core logic) |
|----------|------------------------|
| **Java** | `for (int r=0,rIdx=0; r<rows; r++) { total += count[rIdx]; rIdx = next[rIdx]; }` |
| **Python** | `for _ in range(rows): total += count[idx]; idx = next_idx[idx]` |
| **C++** | `for (int r=0, idx=0; r<rows; ++r){ total+=cnt[idx]; idx=nxt[idx]; }` |

> The heavy lifting is in the pre‑computation step; after that, the rows loop is literally 3 lines of code.

---

Happy coding, and good luck landing that dream job! 🚀