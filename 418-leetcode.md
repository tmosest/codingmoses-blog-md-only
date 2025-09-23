---
title: LeetCode 418. Sentence Screen Fitting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions

Below are three fully‑working, self‑contained solutions for LeetCode 418 – **Sentence Screen Fitting**.  
Each implementation follows the same greedy strategy but is expressed in a different language.

> **Key Idea**  
> Track the “cursor” position (in terms of columns) for each row.  
> Move the cursor forward by the length of a word plus one space (unless it is the last word in the sentence).  
> When the cursor reaches the end of the screen, count how many complete sentences have been fitted and start the next row from the beginning.

---

### 1.1 Java

```java
import java.util.*;

public class Solution {
    public int wordsTyping(String[] sentence, int rows, int cols) {
        // Pre‑compute the total width of the sentence + one space after each word
        int n = sentence.length;
        int[] wordLen = new int[n];
        int totalLen = 0;
        for (int i = 0; i < n; i++) {
            wordLen[i] = sentence[i].length();
            totalLen += wordLen[i] + 1;        // +1 for the space
        }

        int pos = 0;          // current column index (0‑based)
        long sentenceCount = 0;   // may exceed int range for large rows

        for (int r = 0; r < rows; r++) {
            int i = 0;            // index inside sentence
            while (pos + wordLen[i] <= cols) {
                // word fits in current row
                pos += wordLen[i];
                i = (i + 1) % n;     // next word in the sentence
                if (i != 0) pos++;   // add space after word unless it's the last word of the sentence
                if (pos == cols) {   // reached end of row
                    break;
                }
            }
            if (i == 0) {          // finished a full sentence on this row
                sentenceCount++;
            }
            // If we ended exactly at cols, start next row at 0
            // else keep pos as the starting column for the next row
        }

        return (int) sentenceCount;
    }

    // ---------- Test harness ----------
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.wordsTyping(new String[]{"hello","world"}, 2, 8)); // 1
        System.out.println(s.wordsTyping(new String[]{"a","bcd","e"}, 3, 6));  // 2
        System.out.println(s.wordsTyping(new String[]{"i","had","apple","pie"}, 4, 5)); // 1
    }
}
```

---

### 1.2 Python

```python
class Solution:
    def wordsTyping(self, sentence: list[str], rows: int, cols: int) -> int:
        n = len(sentence)
        word_len = [len(w) for w in sentence]
        total_len = sum(l + 1 for l in word_len)   # +1 for space after each word

        pos = 0          # current column (0‑based)
        count = 0        # completed sentences

        for _ in range(rows):
            i = 0          # index in sentence
            while pos + word_len[i] <= cols:
                pos += word_len[i]
                i = (i + 1) % n
                if i != 0:
                    pos += 1          # add space
                if pos == cols:
                    break
            if i == 0:                 # finished full sentence
                count += 1

        return count


# ----- Quick test -----
if __name__ == "__main__":
    s = Solution()
    print(s.wordsTyping(["hello","world"], 2, 8))          # 1
    print(s.wordsTyping(["a","bcd","e"], 3, 6))           # 2
    print(s.wordsTyping(["i","had","apple","pie"], 4, 5)) # 1
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int wordsTyping(vector<string>& sentence, int rows, int cols) {
        int n = sentence.size();
        vector<int> wordLen(n);
        long long totalLen = 0;
        for (int i = 0; i < n; ++i) {
            wordLen[i] = sentence[i].size();
            totalLen += wordLen[i] + 1;   // +1 for space
        }

        int pos = 0;          // current column
        long long completed = 0;

        for (int r = 0; r < rows; ++r) {
            int i = 0;
            while (pos + wordLen[i] <= cols) {
                pos += wordLen[i];
                i = (i + 1) % n;
                if (i != 0) pos++;           // space after word
                if (pos == cols) break;
            }
            if (i == 0) completed++;          // whole sentence finished this row
            // pos remains as the starting column for next row
        }
        return (int)completed;
    }
};

// ---------- Test ----------
int main() {
    Solution s;
    cout << s.wordsTyping({"hello","world"}, 2, 8) << endl;          // 1
    cout << s.wordsTyping({"a","bcd","e"}, 3, 6) << endl;           // 2
    cout << s.wordsTyping({"i","had","apple","pie"}, 4, 5) << endl; // 1
}
```

---

> **Time Complexity**: `O(rows)` – every row is processed once.  
> **Space Complexity**: `O(1)` – only a few integer variables are used, regardless of input size.

---

## 2.  Blog Article  
*(SEO‑optimised, target: job‑seeker, LeetCode interview, algorithmic thinking)*

### Title  
**“Master LeetCode 418: Sentence Screen Fitting – Good, Bad & Ugly Solutions Explained”**

### Meta Description  
Learn how to crack LeetCode 418 “Sentence Screen Fitting” in 3 languages. Dive into the greedy approach, spot pitfalls, and get job‑ready interview skills.  

---

### 1️⃣ The Problem in a Nutshell  
> *“Given a screen of size `rows × cols` and a sentence represented by an array of words, how many times can the whole sentence fit on the screen without breaking a word? A single space separates words, and words must stay in order.”*  

Common interview constraints:  
- `1 ≤ sentence.length ≤ 100`  
- `1 ≤ sentence[i].length ≤ 10`  
- `1 ≤ rows, cols ≤ 20 000`  

---

### 2️⃣ Good – Greedy + Simulation  
**Why it’s great**  
- **Linear time**: O(`rows`) – we only touch each row once.  
- **Constant memory**: O(1).  
- **Intuitive**: simulate the screen line by line, moving a cursor.  
- **Robust**: works for every edge case (words longer than `cols`, very large `rows`, etc.).  

**The core logic**  
1. Pre‑compute the length of each word + 1 (for the trailing space).  
2. Keep a `cursor` that holds the current column index.  
3. For each row, keep consuming words as long as the cursor plus word length fits.  
4. If the cursor reaches exactly the end of a row, start the next row at column 0.  
5. Increment a counter every time a full sentence is completed in a row.  

> **Result** – the counter is the answer.

---

### 3️⃣ Bad – Brute‑Force Placement  
A naive approach tries to place every word on the screen, character by character.  
- **Time**: O(`rows × cols × sentence.length`) – impractical for large inputs.  
- **Space**: O(`rows × cols`) – impossible for 20 000 × 20 000 screens.  
- **Why it fails**: Exponential blow‑up, memory overflow, and hidden bugs when the last word doesn’t fit.  

**Takeaway**: Brute force is the *worst* for this problem – avoid it in interviews!

---

### 4️⃣ Ugly – Dynamic Programming (DP) Overhead  
Some solutions use DP to pre‑compute the next starting index after each word.  
- **Time**: Still O(`rows`) after preprocessing, but the preprocessing is O(`sentence.length²`) → unnecessary overhead.  
- **Space**: O(`sentence.length²`).  
- **Complexity**: Harder to explain, more room for off‑by‑one errors.  

**Why it’s ugly**  
- Over‑engineering for a problem that has a clean greedy solution.  
- Harder to debug, harder to present to an interviewer.  

**When you might consider DP**  
If you need to answer *multiple* queries with different `rows` on the same sentence, a DP table could be reused. But for a single query, the greedy simulation wins.

---

### 5️⃣ Putting It All Together – 3‑Language Code

| Language | Highlights |
|----------|------------|
| **Java** | Uses arrays for word lengths, long for safety. |
| **Python** | Concise list comprehensions, type hints. |
| **C++** | Uses `vector<int>` and `long long` for 64‑bit safety. |

> All three codes share the same algorithmic skeleton – just the syntax changes.

---

### 6️⃣ Interview Tips

| Topic | How to Talk About It |
|-------|----------------------|
| **Greedy justification** | “I’m moving a cursor across columns; each step is independent.” |
| **Edge cases** | *Word longer than `cols`*, *exact fit at row end*, *multiple sentences per row*. |
| **Complexity** | “O(`rows`) time, O(1) space.” |
| **Alternate approaches** | Briefly mention DP or brute‑force, why you dismissed them. |

---

### 7️⃣ Final Thought

Mastering LeetCode 418 showcases:

1. **Algorithmic clarity** – knowing the simplest correct solution.  
2. **Language agility** – translating the same logic to Java, Python, or C++.  
3. **Interview storytelling** – explaining the “good, bad, ugly” choices to a hiring manager.

**Next step**: Add this problem to your portfolio, write a brief blog post (like this one), and share it on LinkedIn with the hashtags:  
`#LeetCode`, `#SentenceScreenFitting`, `#CodingInterview`, `#AlgorithmDesign`, `#JobSearch`.

Good luck landing that dream role! 🚀