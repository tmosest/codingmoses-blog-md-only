---
title: LeetCode 418. Sentence Screen Fitting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below are clean, production‑ready solutions in **Java**, **Python** and **C++**.  
All three use the same *greedy simulation* idea that is fast enough for the
LeetCode limits and easy to understand.

---

### Java

```java
/**
 * LeetCode 418 – Sentence Screen Fitting
 *
 * Time:  O(rows * wordsPerLine)  (≈ 2·10⁵ operations)
 * Space: O(1)
 */
class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        int n = sentence.length;
        int wordIndex = 0;      // index of the next word to print
        int colUsed  = 0;       // how many columns are already occupied on this row
        int fullSentences = 0;  // answer

        for (int r = 0; r < rows; ++r) {
            while (true) {
                int wordLen = sentence[wordIndex].length();

                /*  If the word does not fit, break the row   */
                if (colUsed + wordLen > cols) break;

                /*  Put the word into the current row        */
                colUsed += wordLen;
                wordIndex++;

                /*  Count a completed sentence  */
                if (wordIndex == n) {
                    fullSentences++;
                    wordIndex = 0;
                }

                /*  Add a space only if we are not at the end of a sentence  */
                if (wordIndex != n) colUsed++;   // space between words
            }
            /*  Start the next row with a clean slate  */
            colUsed = 0;
        }
        return fullSentences;
    }
}
```

---

### Python

```python
# LeetCode 418 – Sentence Screen Fitting
# Time:  O(rows * words_per_row)  (≈ 2·10⁵ operations)
# Space: O(1)

class Solution:
    def wordsTyping(self, sentence: list[str], rows: int, cols: int) -> int:
        n = len(sentence)
        idx = 0          # next word to place
        used = 0         # columns used in the current row
        total = 0        # answer

        for _ in range(rows):
            while True:
                wlen = len(sentence[idx])

                if used + wlen > cols:
                    break

                used += wlen
                idx += 1

                if idx == n:            # finished a full sentence
                    total += 1
                    idx = 0

                if idx != n:            # space after a word
                    used += 1

            used = 0                     # start new row

        return total
```

---

### C++

```cpp
// LeetCode 418 – Sentence Screen Fitting
// Time:  O(rows * words_per_row)  (≈ 2e5 operations)
// Space: O(1)

class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        int n = sentence.size();
        int idx = 0;          // next word index
        int used = 0;         // columns used in the current row
        int total = 0;        // answer

        for (int r = 0; r < rows; ++r) {
            while (true) {
                int wlen = sentence[idx].size();

                if (used + wlen > cols) break;

                used += wlen;
                ++idx;

                if (idx == n) {          // finished a sentence
                    ++total;
                    idx = 0;
                }

                if (idx != n) used++;    // add space
            }
            used = 0;                    // new row
        }
        return total;
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad, and The Ugly of LeetCode 418”

> **How to master the Sentence Screen Fitting problem, with Java, Python, and C++ examples that interviewers love.**

---

### Table of Contents
1. [Why Sentence Screen Fitting Matters](#why)
2. [Problem Overview](#overview)
3. [Intuition & Edge Cases](#intuition)
4. [Solution 1 – Straightforward Greedy Simulation](#simulation)
5. [Solution 2 – Optimized DP / Pre‑computation](#dp)
6. [Time & Space Complexity](#complexity)
7. [Testing & Common Pitfalls](#testing)
8. [Take‑aways for Your Next Interview](#takeaways)
9. [SEO Checklist](#seo)
10. [Final Code Snippets](#code)

---

#### 1. Why Sentence Screen Fitting Matters <a name="why"></a>

The **Sentence Screen Fitting** problem (LeetCode #418) is a classic interview question that tests:
- **Greedy thinking** – “Can I keep fitting words until I run out of space?”
- **Simulation skills** – “Can I model the screen line by line?”
- **Attention to detail** – “What about the space between words? What if a word is longer than a row?”

Mastering this problem shows recruiters you can turn an abstract requirement into an efficient algorithm – a key trait for a software engineer.

---

#### 2. Problem Overview <a name="overview"></a>

> *Given a `rows x cols` screen and a sentence (list of words), how many times can the sentence fully appear on the screen? Words cannot be split across lines, and one space must separate adjacent words.*

Key constraints:

- `1 ≤ sentence.length ≤ 100`
- `1 ≤ sentence[i].length ≤ 10`
- `1 ≤ rows, cols ≤ 2·10⁴`

The challenge is to process up to 200 000 rows *without* iterating over each character.

---

#### 3. Intuition & Edge Cases <a name="intuition"></a>

1. **Greedy**: Start from the first word, keep adding words to the current row until you cannot fit the next word.  
2. **Wrap‑around**: When the last word of the sentence is placed, we count one complete fit and reset the index.  
3. **Space handling**: After every word *except* the last in a sentence we need a space.  
4. **Edge cases**  
   - A word is exactly the same length as a column.  
   - The screen is just one row/column.  
   - All words are tiny (≤ 1 char) – this creates many words per row.

---

#### 4. Solution 1 – Straightforward Greedy Simulation <a name="simulation"></a>

**Idea**  
Simulate the screen line by line, word by word. Keep three state variables:

| Variable | Meaning |
|----------|---------|
| `idx`    | Index of the next word to print |
| `used`   | How many columns are already filled in the current row |
| `total`  | Number of completed sentences |

**Loop**  
For each of the `rows`:
1. While we can fit the next word, append it (`used += wordLength`).
2. If we just finished a full sentence (`idx == n`), increment `total` and reset `idx`.
3. If we are not at the end of the sentence, add a space (`used++`).
4. When the next word would overflow, break and move to the next row.

**Why it works**  
Because the problem is *local* – the outcome on a given row depends only on the current word index and the amount of free space. The greedy choice of “fit as many words as possible” is optimal, since no later word can take more space than a smaller prefix of the sentence.

**Complexity**  
- **Time**: `O(rows * words_per_line)` – worst case about 2 × 10⁵ operations.  
- **Space**: `O(1)` – only a handful of integers.

---

#### 5. Solution 2 – Optimized DP / Pre‑computation <a name="dp"></a>

If `rows` were **extremely** large (e.g., 10⁹), we would pre‑compute for each starting word:

| start | nextIndex | sentencesCompleted |
|-------|-----------|--------------------|
| 0     | …         | …                  |
| 1     | …         | …                  |
| …     | …         | …                  |

Then we can simulate row‑by‑row in `O(rows)` time but with *O(sentence.length)* extra memory – essentially a simple DP that records the effect of one row.

This approach is overkill for the LeetCode constraints but is valuable to mention during an interview to show awareness of optimization strategies.

---

#### 6. Time & Space Complexity <a name="complexity"></a>

| Implementation | Time | Space |
|-----------------|------|-------|
| Greedy Simulation | `O(rows * words_per_line)`  (≤ 2·10⁵) | `O(1)` |
| DP Pre‑computation | `O(rows + sentence.length)` | `O(sentence.length)` |

Both satisfy the LeetCode limits comfortably.

---

#### 7. Testing & Common Pitfalls <a name="testing"></a>

| Test | Expected | Why it matters |
|------|----------|----------------|
| `sentence = ["a"]`, `rows=1`, `cols=1` | `1` | Word fits exactly. |
| `sentence = ["a", "bcd", "e"]`, `rows=3`, `cols=6` | `2` | Spaces after words are counted. |
| `sentence = ["longword"]`, `rows=1000`, `cols=10` | `1000` | Single word per row. |
| `sentence = ["ab", "cd"]`, `rows=2`, `cols=4` | `1` | Two words per row, no wrap. |
| `sentence = ["a","b","c","d"]`, `rows=1000`, `cols=1000` | Large number | Stress test. |

**Common mistakes**

- Forgetting to reset `used` at the start of each new row.
- Adding a space after the *last* word of the sentence.
- Off‑by‑one when counting completed sentences (`idx == n`).

---

#### 8. Take‑aways for Your Next Interview <a name="takeaways"></a>

1. **Explain your intuition** before diving into code. Interviewers appreciate the ability to articulate the problem’s structure.  
2. **Mention edge cases** you considered; it shows depth.  
3. **Discuss complexity** and why your solution is optimal for the constraints.  
4. **Show alternate approaches** (DP, pre‑computation) to demonstrate thoroughness.  
5. **Write clean, commented code** in the chosen language.  

---

#### 9. SEO Checklist <a name="seo"></a>

| Target Keyword | How it’s used |
|----------------|---------------|
| LeetCode 418 | Title, H1, intro |
| Sentence Screen Fitting | Throughout article |
| Java solution | Code section, intro |
| Python solution | Code section, intro |
| C++ solution | Code section, intro |
| interview question | Intro, takeaway |
| algorithm | Complexity section |
| job interview | Take‑aways, conclusion |

**Meta Description (≈160 chars)**  
“Master LeetCode 418 – Sentence Screen Fitting. Read our deep dive, Java/Python/C++ solutions, edge cases, complexity analysis, and interview prep tips.”

---

#### 10. Final Code Snippets <a name="code"></a>

> *For quick reference, copy the code below in your favorite IDE.*

- **Java** (above)
- **Python** (above)
- **C++** (above)

Feel free to paste them into your GitHub Gist or your personal coding library. Good luck on the screen!

---

> **Word of advice:** Show up, explain the problem in your own words, and present the greedy simulation. Recruiters will see you can turn a 200 000‑row problem into clean, O(1) space code – a perfect showcase for a senior developer role.

---



--- 

> *Thanks for reading! If you found this guide helpful, hit the like button and subscribe for more interview‑ready algorithm tutorials.*



--- 

### End of Article

--- 

> *This article is optimized for both readability and search engines. Use it in your portfolio or as a talking point in a technical interview.* 

--- 

## 3. Final Touches

- **Publish** the article on a personal blog or Medium.  
- **Add the code** as a single page or link to a GitHub repository.  
- **Share** the link on LinkedIn, Twitter, and coding forums.  

With this article and the accompanying code, you’ll not only nail LeetCode 418 but also boost your visibility to recruiters looking for sharp algorithmic thinkers. Good luck! 🚀

--- 

*End of Blog Article.*