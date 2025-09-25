---
title: LeetCode 418. Sentence Screen Fitting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sentence Screen Fitting – LeetCode 418  
**Brute‑Force ➜ Greedy ➜ O(1) Optimization**  
*A complete, multi‑language walkthrough + SEO‑friendly article that will help you land your next coding interview.*

---

## 1️⃣ Problem Overview

| Topic | Details |
|-------|---------|
| **Problem** | *Sentence Screen Fitting* (LeetCode 418) |
| **Input** | `String[] sentence`, `int rows`, `int cols` |
| **Output** | `int` – the number of times the sentence can be fitted on a screen of `rows × cols` |
| **Constraints** | `1 ≤ sentence.length ≤ 100`, `1 ≤ sentence[i].length ≤ 10`, `1 ≤ rows, cols ≤ 2·10⁴` |

You must preserve the order of words. A word cannot be split; words are separated by a single space. Empty cells on the screen are filled with `-` in the examples.

---

## 2️⃣ Intuition

The screen is scanned line by line. Each line can hold as many *whole* words as the width allows. Once a line is full, the next word starts on the next line.  
The naive simulation is easy but **O(rows × len(sentence))** – far too slow when `rows` is 20 000 and the sentence is long.

The key observation: the *state* of the screen after each line is just the **index of the word** that will be printed next.  
If we know how the index changes after one row, we can “jump” many rows at once.

---

## 3️⃣ Algorithm – O(rows) Greedy + Memorization

1. **Pre‑compute** the next word index for every word in the sentence.  
   `nextIdx[i]` tells you: *if the next word to print is `sentence[i]`, which word will be next after the current row finishes?*
2. **Iterate over rows** while maintaining a pointer `curIdx` to the current word index.  
   On each iteration:  
   `curIdx = nextIdx[curIdx]` – jump to the next starting word.  
   Keep a counter `fits` that increases by 1 each time we complete a full cycle of the sentence (i.e., when `curIdx` returns to 0).
3. **Return** the counter `fits`.

**Why it works**  
Because each row only depends on the starting word. Once we know how a row transforms the starting word, the process is deterministic and can be repeated row‑by‑row.

**Complexity**  
- Time: **O(rows + n)**, where *n* = `sentence.length`.  
- Space: **O(n)** for the `nextIdx` array.

---

## 4️⃣ Edge Cases

| Case | Why it matters | How to handle |
|------|----------------|---------------|
| Word longer than `cols` | Impossible to fit, but constraints forbid it | Not needed |
| Sentence has exactly one word | `nextIdx[0]` will be `0` after each row | Works naturally |
| `rows = 0` | No lines, answer `0` | Loop will not run |

---

## 5️⃣ Code – Three Languages

> **Tip**: The Java version uses `int[] nextIdx`, the Python version uses a list, and the C++ version uses a `vector<int>`. All share the same logic.

### 5.1 Java

```java
public class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        int n = sentence.length;
        int[] nextIdx = new int[n];
        int curIdx = 0;

        // Pre‑compute next index for each word
        for (int i = 0; i < n; i++) {
            int length = 0;
            int j = i;
            while (true) {
                int wordLen = sentence[j].length();
                if (length + wordLen > cols) break;
                length += wordLen;
                j = (j + 1) % n;
                // Add a space after every word except the last one on the line
                if (length < cols) length++; // space
            }
            nextIdx[i] = j;
        }

        int fits = 0;
        for (int r = 0; r < rows; r++) {
            curIdx = nextIdx[curIdx];
            if (curIdx == 0) fits++;
        }
        return fits;
    }
}
```

### 5.2 Python

```python
class Solution:
    def wordsTyping(self, sentence: list[str], rows: int, cols: int) -> int:
        n = len(sentence)
        next_idx = [0] * n

        # Pre‑compute next index for each word
        for i in range(n):
            length = 0
            j = i
            while True:
                word_len = len(sentence[j])
                if length + word_len > cols:
                    break
                length += word_len
                j = (j + 1) % n
                # space after a word, unless it would exceed the line
                if length < cols:
                    length += 1
            next_idx[i] = j

        cur_idx, fits = 0, 0
        for _ in range(rows):
            cur_idx = next_idx[cur_idx]
            if cur_idx == 0:
                fits += 1
        return fits
```

### 5.3 C++

```cpp
class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        int n = sentence.size();
        vector<int> nextIdx(n);

        // Pre‑compute the next index for each word
        for (int i = 0; i < n; ++i) {
            int len = 0;
            int j = i;
            while (true) {
                int wlen = sentence[j].size();
                if (len + wlen > cols) break;
                len += wlen;
                j = (j + 1) % n;
                if (len < cols) ++len; // add space
            }
            nextIdx[i] = j;
        }

        int curIdx = 0, fits = 0;
        for (int r = 0; r < rows; ++r) {
            curIdx = nextIdx[curIdx];
            if (curIdx == 0) ++fits;
        }
        return fits;
    }
};
```

---

## 6️⃣ Blog‑Style Discussion: Good, Bad, and Ugly

> **SEO Keywords**: *sentence screen fitting, leetcode 418 solution, coding interview, job interview, algorithm design, greedy, optimization, Java, Python, C++*  

### 6.1 The “Bad” Brute‑Force

> ```python
> # O(rows * words_in_sentence) – too slow for 20k rows
> for row in range(rows):
>     for word in sentence:
>         ...
> ```
> 
> *Why it fails:*  
> Each row loops over the entire sentence, even when the sentence is long but most words never fit in a single line. Complexity blows up to millions of operations.

### 6.2 The “Good” Greedy + Memorization

*Key idea:* *Remember where you end after each line.*  
- Pre‑compute the transition `nextIdx`.  
- For every row just jump: `curIdx = nextIdx[curIdx]`.  
- Complexity is linear in the number of rows – *optimal for interview coding*.

### 6.3 The “Ugly” DP Variant (from some editorial)

Some solutions use dynamic programming with a 2‑D array to record the state of each row–column pair.  
While technically correct, it uses **O(rows·cols)** memory and time, making it *unnecessary* and *harder to understand*.  
For an interview, keep the solution simple and readable – **the greedy approach wins**.

### 6.4 Interview‑Ready Tips

| Tip | Reason |
|-----|--------|
| **Explain the state transition** (next word index) | Shows deep understanding of the problem structure |
| **Mention edge cases** (single word, sentence length 1, etc.) | Demonstrates thoroughness |
| **Give complexity analysis** | Interviewers expect this |
| **Show code in two languages** | Versatile skillset (Java/Python/C++) |

---

## 7️⃣ Takeaway

- **Problem**: Count how many times a sentence fits in a given screen.
- **Best Solution**: Greedy + memorization (`O(rows + n)`).
- **Key Insight**: The state of the screen after each row is fully determined by the index of the next word to print.
- **Languages**: Java, Python, C++ – all use the same algorithm.

> Mastering this pattern will not only help you ace *LeetCode 418* but also any interview question that involves *string packing*, *cyclic patterns*, or *greedy simulation*. Good luck landing that next job!