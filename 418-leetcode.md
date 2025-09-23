---
title: LeetCode 418. Sentence Screen Fitting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sentence Screen Fitting – Master the LeetCode Classic in Java, Python & C++  

> **Keywords**: Sentence Screen Fitting, LeetCode 418, algorithm, greedy, dynamic programming, Java, Python, C++, interview prep, coding interview

---

## 1. Problem Recap  

| ✅ | ✔️ |
|---|---|
| **LeetCode #418** | *Medium* |
| **Signature** | `public int wordsTyping(String[] sentence, int rows, int cols)` (Java)<br>`int wordsTyping(vector<string>& sentence, int rows, int cols)` (C++)<br>`def wordsTyping(sentence: List[str], rows: int, cols: int) -> int` (Python) |
| **Goal** | Count how many full times the sentence can be fitted onto an `rows × cols` screen. |
| **Rules** | • Words must keep original order.<br>• No word can be split across lines.<br>• A single space separates two consecutive words. |

---

## 2. Why Is This Problem Worth Studying?  

- **Real‑world vibe**: It’s essentially a “word‑wrapping” problem you’ll see in UI layout, text editors, and printing engines.  
- **Interview favorite**: LeetCode’s 418 is a go‑to “medium” question for front‑end, back‑end, and systems roles.  
- **Multiple solution patterns**: Naïve simulation, greedy with caching, and even dynamic programming.  
- **Time complexity insight**: Learn how to turn an **O(rows × words)** routine into **O(rows + sentence length)**.  

---

## 3. The Naïve Approach (O(rows × sentence length))

```text
for each row
    curCol = 0
    while curCol + len(word) <= cols
        place word, curCol += len(word) + 1
        advance to next word (wrap at end)
```

**Pros**  
- Easy to implement and understand.  

**Cons**  
- Worst‑case time can be huge (rows = 2 × 10⁴, words = 100).  
- Does not reuse information between rows.

---

## 4. The Optimized Greedy + Caching Approach (O(rows + sentence length))

The key insight: **After a row ends, the next row always starts at the same word index** that the previous row finished with.  

1. **Pre‑compute the “next word index” for every starting word.**  
   *Simulate placing words until you exceed `cols`.*  
   This is done only once for each word (max 100).  
2. **Simulate row by row using the cache.**  
   *Start from word 0, repeatedly jump using the cache, increment a counter each time we return to word 0.*  

Because each word index is processed at most once per row, the total work is **O(rows + sentence length)**.  

---

## 5. Corner Cases to Watch

| Case | What Happens? | Why It Matters |
|------|---------------|----------------|
| Word longer than `cols` | Impossible, but constraints forbid it. | Still good to guard in code. |
| `rows` or `cols` = 1 | Minimum screen size – algorithm still works. | Handles tight memory limits. |
| Sentence length = 1 | Only one word repeats. | Cache table has only one entry. |
| Large `rows` (20 000) | Performance critical. | Our greedy solution stays fast. |

---

## 6. Code Implementations

### 6.1 Java (O(rows + sentence length))

```java
import java.util.*;

public class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        int n = sentence.length;
        int[] nextWord = new int[n];
        int[] wordsCount = new int[n];   // how many sentences start from word i

        // Pre‑compute nextWord and wordsCount for each start index
        for (int i = 0; i < n; i++) {
            int col = 0;          // current column position
            int cnt = 0;          // how many full sentences fit
            int idx = i;          // current word index

            while (col + sentence[idx].length() <= cols) {
                col += sentence[idx].length() + 1;   // word + space
                idx = (idx + 1) % n;
                if (idx == 0) cnt++;                 // finished a sentence
            }
            nextWord[i] = idx;
            wordsCount[i] = cnt;
        }

        // Simulate rows
        int wordIndex = 0;
        int totalSentences = 0;
        for (int r = 0; r < rows; r++) {
            totalSentences += wordsCount[wordIndex];
            wordIndex = nextWord[wordIndex];
        }
        return totalSentences;
    }
}
```

### 6.2 Python (O(rows + sentence length))

```python
from typing import List

class Solution:
    def wordsTyping(self, sentence: List[str], rows: int, cols: int) -> int:
        n = len(sentence)
        next_word = [0] * n
        sentences_per_row = [0] * n

        # Pre‑compute for each start word
        for i in range(n):
            col = 0
            cnt = 0
            idx = i
            while col + len(sentence[idx]) <= cols:
                col += len(sentence[idx]) + 1  # word + space
                idx = (idx + 1) % n
                if idx == 0:
                    cnt += 1
            next_word[i] = idx
            sentences_per_row[i] = cnt

        # Simulate rows
        word_idx = 0
        total = 0
        for _ in range(rows):
            total += sentences_per_row[word_idx]
            word_idx = next_word[word_idx]

        return total
```

### 6.3 C++ (O(rows + sentence length))

```cpp
#include <vector>
#include <string>

class Solution {
public:
    int wordsTyping(std::vector<std::string>& sentence, int rows, int cols) {
        int n = sentence.size();
        std::vector<int> nextWord(n), cntPerRow(n);

        // Pre‑compute
        for (int i = 0; i < n; ++i) {
            int col = 0, cnt = 0, idx = i;
            while (col + sentence[idx].size() <= cols) {
                col += sentence[idx].size() + 1; // word + space
                idx = (idx + 1) % n;
                if (idx == 0) ++cnt;
            }
            nextWord[i] = idx;
            cntPerRow[i] = cnt;
        }

        // Simulate rows
        int wordIdx = 0, total = 0;
        for (int r = 0; r < rows; ++r) {
            total += cntPerRow[wordIdx];
            wordIdx = nextWord[wordIdx];
        }
        return total;
    }
};
```

> **Tip**: In production code, guard against words longer than `cols`.  
> **Testing**: Run the three provided examples and add edge‑case tests.

---

## 7. “Good, Bad, & Ugly” of the Optimized Solution  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(rows + sentence length)` → fast even for 20 k rows | Still linear in `rows`; cannot be sub‑linear | None (fast enough for constraints) |
| **Space Complexity** | `O(sentence length)` for cache | Requires two arrays of size `n` | None |
| **Readability** | Clear separation: *pre‑computation* + *simulation* | Cache logic may be a bit opaque to newcomers | If you skip the cache, you’ll write an O(rows × n) loop—hard to spot inefficiency |
| **Edge‑case handling** | Handles wrap‑around elegantly | Must be careful with `idx = (idx + 1) % n` | None |
| **Reusability** | Cache can be reused for multiple test cases on same sentence | Cache invalidates if sentence changes | None |

---

## 8. Interview‑Ready Tips

1. **Explain the observation**: “After a row, we always know which word the next row starts with.”  
2. **Show the DP / cache idea**: “We pre‑compute next word and sentence count for each start word.”  
3. **Complexity trade‑off**: “We only simulate `rows` once; pre‑computation is cheap (≤ 100 words).”  
4. **Edge case talk**: “If a word’s length > cols, we return 0 (though constraints forbid it).”  
5. **Discuss alternatives**: “We could also simulate row by row directly, but that would be O(rows × n).”

---

## 9. Testing Checklist

| Test | Description | Expected Output |
|------|-------------|-----------------|
| Example 1 | `["hello","world"]`, rows=2, cols=8 | 1 |
| Example 2 | `["a","bcd","e"]`, rows=3, cols=6 | 2 |
| Example 3 | `["i","had","apple","pie"]`, rows=4, cols=5 | 1 |
| Single word | `["a"]`, rows=10, cols=1 | 10 |
| Max rows | 20 000 rows, 100 words of length 1, cols=10 | 200 000 (if words fit exactly) |
| Long word | `["longword"]`, rows=3, cols=10 | 0 (if length > cols, otherwise 3) |

Run each implementation against this suite to guarantee correctness.

---

## 10. Closing Thoughts

- **Why you should master this**: It blends greedy logic with caching—a pattern that appears in many interview problems (e.g., *Text Justification*, *Largest Rectangle in Histogram*).  
- **Career angle**: Demonstrating the ability to turn an `O(n*m)` solution into `O(n+m)` shows *algorithmic maturity*—exactly what hiring managers look for.  
- **Next steps**: Try implementing a **dynamic programming** variant that stores the exact number of words placed per row; or explore **bit‑mask** optimization for extreme constraints.

---

## 11. SEO‑Optimized Meta (for your blog)

**Title**:  
*Sentence Screen Fitting – LeetCode 418 Solution in Java, Python & C++ (Optimized O(rows + n) Approach)*

**Meta Description**:  
“Learn how to solve LeetCode 418 – Sentence Screen Fitting – in Java, Python, and C++ with an optimized greedy solution. Boost your coding interview skills and land your next job!”

**Keywords**: Sentence Screen Fitting, LeetCode 418, Java solution, Python solution, C++ solution, greedy algorithm, interview prep, coding interview, job interview, algorithm optimization.

---

### 🎯 Call‑to‑Action  

- **Code & Run**: Paste the snippets into your IDE and test with custom inputs.  
- **Discuss**: Drop a comment below with any edge cases you’d like to tackle next.  
- **Share**: If you found this helpful, share on LinkedIn – recruiters love to see deep dives!

Happy coding, and good luck with your next interview!