---
title: LeetCode 418. Sentence Screen Fitting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 418. Sentence Screen Fitting  
**Medium** – LeetCode  
> **Problem**:  
> Given a screen of size `rows × cols` and a sentence represented as a list of words, find out how many times the sentence can be fully typed on the screen.  
> Rules:  
> * Words must appear in the original order.  
> * A word can’t be split across two lines.  
> * A single space separates consecutive words in a line.  
> * Empty spaces at the end of a line are allowed.

---

## 1️⃣  Three‑Language Solutions

Below are ready‑to‑run implementations in **Java**, **Python**, and **C++**.  
All solutions use the same greedy simulation approach that runs in **O(rows)** time and **O(1)** additional space.

> **Key idea** –  
> Instead of simulating every character, keep a pointer to the *current word index* and a *running count of used columns* on the current row.  
> Whenever the next word would overflow the row, we move to the next row, reset the column counter, and *continue counting* from the same word index.  
> The total number of words written can be derived from the final pointer and the number of complete sentences that fit.

### 1.1 Java

```java
// 418. Sentence Screen Fitting
// Java 17

import java.util.*;

public class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        // Pre‑compute the length of each word plus a space
        int n = sentence.length;
        int[] wordLen = new int[n];
        for (int i = 0; i < n; ++i) {
            wordLen[i] = sentence[i].length() + 1; // +1 for space
        }

        int wordIdx = 0;          // index in sentence
        long totalChars = 0;      // total chars written including spaces

        for (int r = 0; r < rows; ++r) {
            int cur = 0;          // used columns in this row
            while (cur + wordLen[wordIdx] <= cols) {
                cur += wordLen[wordIdx];
                wordIdx = (wordIdx + 1) % n;   // wrap around
                totalChars += wordLen[wordIdx - 1];
            }
            // We stopped because the next word would overflow.
            // The while loop automatically moves to the next row.
        }

        // Each full sentence contributes sentence.length words.
        // totalChars contains sentence.length words per full cycle.
        // So the answer is totalChars / (sentence.length * 1L)
        return (int)(totalChars / (long)n);
    }

    // Simple driver
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.wordsTyping(new String[]{"hello", "world"}, 2, 8)); // 1
        System.out.println(s.wordsTyping(new String[]{"a", "bcd", "e"}, 3, 6)); // 2
        System.out.println(s.wordsTyping(new String[]{"i","had","apple","pie"}, 4, 5)); // 1
    }
}
```

> **Why this works**  
> * The inner `while` consumes as many words as possible in the current row.  
> * The word index is wrapped around modulo `n`, which keeps track of how many *full* sentences were written.  
> * `totalChars` accumulates the number of words written, which we then divide by the sentence length to get the answer.

---

### 1.2 Python

```python
# 418. Sentence Screen Fitting
# Python 3

class Solution:
    def wordsTyping(self, sentence: list[str], rows: int, cols: int) -> int:
        n = len(sentence)
        # Length of each word + one space
        lens = [len(w) + 1 for w in sentence]

        idx = 0          # current word index
        total = 0        # total words written

        for _ in range(rows):
            used = 0
            while used + lens[idx] <= cols:
                used += lens[idx]
                total += 1
                idx = (idx + 1) % n

        return total // n

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.wordsTyping(["hello","world"], 2, 8))      # 1
    print(sol.wordsTyping(["a","bcd","e"], 3, 6))       # 2
    print(sol.wordsTyping(["i","had","apple","pie"], 4, 5)) # 1
```

---

### 1.3 C++

```cpp
// 418. Sentence Screen Fitting
// C++17

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        int n = sentence.size();
        vector<int> len(n);
        for (int i = 0; i < n; ++i) len[i] = sentence[i].size() + 1; // +1 for space

        int idx = 0;           // current word
        long long total = 0;   // total words written

        for (int r = 0; r < rows; ++r) {
            int used = 0;
            while (used + len[idx] <= cols) {
                used += len[idx];
                ++total;
                idx = (idx + 1) % n;
            }
        }
        return total / n; // integer division
    }
};

int main() {
    Solution s;
    cout << s.wordsTyping({"hello","world"}, 2, 8) << '\n';        // 1
    cout << s.wordsTyping({"a","bcd","e"}, 3, 6) << '\n';         // 2
    cout << s.wordsTyping({"i","had","apple","pie"}, 4, 5) << '\n'; // 1
    return 0;
}
```

---

## 2️⃣  Blog Post – “The Good, The Bad, and The Ugly of Sentence Screen Fitting”

> **SEO Title**: *Sentence Screen Fitting (LeetCode 418) – Master the Problem, Get the Job*

### 2.1  Introduction

If you’re prepping for a **software engineering interview**, you’ll often encounter “sentence fitting” puzzles. They’re deceptively simple but a great way to gauge your problem‑solving mindset, handling of corner cases, and ability to write clean, efficient code.  

Today we dive into **LeetCode 418 – Sentence Screen Fitting**. We’ll walk through the *good* (efficient greedy simulation), the *bad* (brute‑force pitfalls), and the *ugly* (common edge‑case nightmares). By the end, you’ll know exactly how to rock this problem in any language and how to explain it to a hiring manager.

---

### 2.2  Problem Recap

> **Given** a screen with `rows × cols` characters and an array of words (`sentence`).  
> **Return** how many times the entire sentence can be typed on the screen under the constraints above.

> **Example**  
> `sentence = ["hello", "world"]`, `rows = 2`, `cols = 8` → **Output:** `1`

---

### 2.3  The Good – Greedy Simulation

**Why it’s good**  
* **Time Complexity**: `O(rows)` – each row is processed once.  
* **Space Complexity**: `O(1)` – only a few integer variables.  
* **Simplicity**: No recursion, no DP tables, just a loop and a counter.  
* **Language‑agnostic**: Works identically in Java, Python, C++ – great for interview versatility.

#### How it works

1. **Pre‑compute** the length of every word plus a space.  
2. Keep a pointer `idx` to the current word.  
3. For each row, keep a counter `used` of used columns.  
4. While adding the next word does not exceed `cols`, add its length to `used`, increment the global word counter, and advance `idx`.  
5. After finishing all rows, divide the total number of words by `sentence.length` to get the number of complete sentences.

> **Why divide?**  
> Every time `idx` wraps around to `0`, one full sentence has been written. The global counter tells us how many words total, so the integer division gives us the number of sentences.

---

### 2.4  The Bad – Brute Force Character by Character

A common *bad* approach is to simulate every single character, keeping a 2‑D array or string for the screen. This leads to:

* **Time**: `O(rows * cols)` – far too slow for `rows, cols ≤ 20,000`.  
* **Memory**: `O(rows * cols)` – impossible for large screens.  
* **Bug‑prone**: handling spaces, word boundaries, and line breaks is tedious.

> **Takeaway** – never waste time simulating the screen. Think in terms of *words*, not *characters*.

---

### 2.5  The Ugly – Edge‑Case Nightmares

| Edge Case | What can go wrong? | Fix |
|-----------|--------------------|-----|
| Word length > `cols` | Your algorithm might get stuck in an infinite loop (the word never fits). | If any word length > `cols`, immediately return `0`. |
| Sentence length 1 | `idx` wrapping logic can miscount if you forget to divide by `n`. | Always divide the total words by `sentence.length`. |
| Huge `rows`, small `cols` | Your inner `while` might run many times per row, but still O(rows). | No change needed; the greedy loop remains linear. |
| Leading/trailing spaces in input | Unlikely in LeetCode, but if present, `len + 1` could miscount. | Trim words before measuring length. |

> **Tip** – Write unit tests for these corner cases before coding the full solution.

---

### 2.6  Why This Problem Rocks for Interviews

1. **Conceptual depth in a simple loop** – Shows you can spot a pattern and turn it into an O(n) algorithm.  
2. **Language‑agnostic discussion** – You can discuss Java vs. Python vs. C++ trade‑offs.  
3. **Opportunity to talk about complexity** – Emphasize why the greedy approach beats the brute force.  
4. **Real‑world relevance** – Think of how text editors, subtitles, or streaming services wrap words on screens.  

---

### 2.7  How to Nail the Explanation

1. **State the problem clearly** (use the LeetCode description).  
2. **Explain the greedy insight** – “We only care about words, not characters.”  
3. **Show the loop** – Highlight the `while` condition `used + len[idx] <= cols`.  
4. **Handle wrap‑around** – Show `idx = (idx + 1) % n`.  
5. **Compute the answer** – `total / n`.  
6. **Discuss edge cases** – Mention word longer than `cols`.  
7. **Show runtime** – Linear in rows, constant extra space.  

A concise, bullet‑point explanation leaves a strong impression.

---

### 2.8  SEO Keywords & Final Tips

| Keyword | Purpose |
|---------|---------|
| *Sentence screen fitting* | Core problem title |
| *LeetCode 418* | Problem number reference |
| *job interview algorithm* | Hiring focus |
| *Java Python C++ solutions* | Show multi‑language expertise |
| *O(rows) solution* | Highlight efficiency |
| *software engineer interview* | Target audience |

**Final advice**:  
*Practice the code in your favorite language.  
*Explain it out loud (mock interview).  
*Highlight the complexity and edge‑case handling.  

Good luck – and remember, a clean solution tells the interviewer you’re ready to write production‑ready code!