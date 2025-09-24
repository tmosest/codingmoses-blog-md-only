---
title: LeetCode 3078. Match Alphanumerical Pattern in Matrix I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3078. Match Alphanumerical Pattern in Matrix I  
**Medium** | **Java** | **Python** | **C++**  

---

### 1. Problem Recap  

You are given

* `board`: a `m × n` matrix of digits (0‑9).  
* `pattern`: a `p × q` matrix of characters – each character is either a digit (`'0'`‑`'9'`) or a lowercase letter (`'a'`‑`'z'`).

A sub‑matrix of `board` **matches** `pattern` if there exists a mapping from each distinct letter in `pattern` to a *unique* digit such that after applying the mapping every cell in the sub‑matrix equals the corresponding cell in `pattern`.  
Digits in `pattern` must match the board exactly.  

Return the coordinates `[row, col]` of the **upper‑left corner** of the earliest matching sub‑matrix (lowest row, then lowest column).  
If no match exists, return `[-1, -1]`.

---

### 2. Why This Problem is a Great Interview Question  

| Good | Bad | Ugly |
|------|-----|------|
| **Conceptual depth** – tests your understanding of pattern matching, bijective mapping, and 2‑D traversal. | **Edge‑case fatigue** – many off‑by‑one errors, especially when mapping letters to digits. | **Time‑pressure trap** – naive O(m²n²) solutions may still pass but risk “time‑limit exceeded” on hidden tests. |

- **Good**: The problem is small enough (≤ 50×50) to let you brute‑force, yet it forces you to think about *bijection* between letters and digits.  
- **Bad**: It’s easy to write a correct algorithm but get wrong answers on corner cases such as repeated letters, digit‑letter conflicts, or patterns that touch the board boundary.  
- **Ugly**: If you forget to reset your mapping between different top‑left positions, you’ll get false positives.  

Preparing for this problem will make you comfortable with:
* Two‑dimensional array traversal
* Mapping constraints and conflict detection
* Early exit conditions (as soon as a mismatch is found)

All of which are staples of senior‑level coding interviews at FAANG, Amazon, Google, etc.

---

### 3. Solution Overview  

1. **Iterate over all possible starting positions**  
   For every `r` in `[0, m - p]` and `c` in `[0, n - q]` we consider the sub‑matrix whose upper‑left corner is `(r, c)`.

2. **Maintain two mappings**  
   * `digit → letter` (array of size 10, initialized to `-1`)  
   * `letter → digit` (array of size 128 or a hashmap)  

   These two maps guarantee the bijection requirement.

3. **Validate the sub‑matrix**  
   For each cell `(i, j)` inside the pattern:
   * If `pattern[i][j]` is a digit, it must equal the board value at `(r+i, c+j)`.  
   * If it is a letter:
     * If we have already mapped that board digit, the mapped letter must be the same.  
     * If we have already mapped that letter, the mapped digit must be the same.  
     * If neither mapping exists, establish both.

4. **Return the first matching coordinates**  
   Because we iterate rows first, columns second, the first successful match is automatically the lexicographically smallest.

5. **If none match, return `[-1, -1]`.**

Complexity:  
*Time*: `O((m-p+1)(n-q+1) * p * q)` – worst case ≈ `50³ = 125 000` operations.  
*Space*: `O(1)` – only fixed‑size arrays for the two maps.

---

### 4. Reference Implementations  

Below you’ll find clean, idiomatic solutions in **Java, Python, and C++**.  
All three follow the same logic as described above.

---

#### 4.1 Java  

```java
import java.util.Arrays;

class Solution {
    public int[] findPattern(int[][] board, String[] pattern) {
        int m = board.length, n = board[0].length;
        int p = pattern.length, q = pattern[0].length();

        for (int r = 0; r + p <= m; r++) {
            for (int c = 0; c + q <= n; c++) {
                int[] digitToLetter = new int[10];   // 0 means unmapped
                int[] letterToDigit = new int[128]; // -1 means unmapped
                Arrays.fill(digitToLetter, 0);
                Arrays.fill(letterToDigit, -1);

                boolean ok = true;
                for (int i = 0; i < p && ok; i++) {
                    for (int j = 0; j < q && ok; j++) {
                        int boardVal = board[r + i][c + j];
                        char pat = pattern[i].charAt(j);

                        if (Character.isDigit(pat)) {
                            if (pat - '0' != boardVal) ok = false;
                        } else { // letter
                            if (digitToLetter[boardVal] == 0) {
                                // new mapping
                                if (letterToDigit[pat] == -1) {
                                    digitToLetter[boardVal] = pat;
                                    letterToDigit[pat] = boardVal;
                                } else {
                                    // same letter mapped to a different digit
                                    ok = false;
                                }
                            } else if (digitToLetter[boardVal] != pat) {
                                // board digit already mapped to a different letter
                                ok = false;
                            }
                        }
                    }
                }

                if (ok) return new int[]{r, c};
            }
        }
        return new int[]{-1, -1};
    }
}
```

---

#### 4.2 Python  

```python
from typing import List

class Solution:
    def findPattern(self, board: List[List[int]], pattern: List[str]) -> List[int]:
        m, n = len(board), len(board[0])
        p, q = len(pattern), len(pattern[0])

        for r in range(m - p + 1):
            for c in range(n - q + 1):
                # digit -> letter (0 means unmapped)
                d2l = [0] * 10
                # letter -> digit (-1 means unmapped)
                l2d = [-1] * 128
                ok = True

                for i in range(p):
                    if not ok:
                        break
                    for j in range(q):
                        num = board[r + i][c + j]
                        ch = pattern[i][j]

                        if ch.isdigit():
                            if int(ch) != num:
                                ok = False
                                break
                        else:  # letter
                            if d2l[num] == 0:
                                if l2d[ord(ch)] == -1:
                                    d2l[num] = ch
                                    l2d[ord(ch)] = num
                                else:
                                    ok = False
                                    break
                            elif d2l[num] != ch:
                                ok = False
                                break

                if ok:
                    return [r, c]

        return [-1, -1]
```

---

#### 4.3 C++  

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<int> findPattern(vector<vector<int>>& board, vector<string>& pattern) {
        int m = board.size(), n = board[0].size();
        int p = pattern.size(), q = pattern[0].size();

        for (int r = 0; r + p <= m; ++r) {
            for (int c = 0; c + q <= n; ++c) {
                int d2l[10] = {0};      // digit -> letter (0 means unmapped)
                int l2d[128];
                fill(begin(l2d), end(l2d), -1); // letter -> digit

                bool ok = true;
                for (int i = 0; i < p && ok; ++i) {
                    for (int j = 0; j < q && ok; ++j) {
                        int num = board[r + i][c + j];
                        char ch = pattern[i][j];

                        if (ch >= '0' && ch <= '9') {
                            if (ch - '0' != num) ok = false;
                        } else { // letter
                            if (d2l[num] == 0) {
                                if (l2d[(int)ch] == -1) {
                                    d2l[num] = ch;
                                    l2d[(int)ch] = num;
                                } else {
                                    ok = false;
                                }
                            } else if (d2l[num] != ch) {
                                ok = false;
                            }
                        }
                    }
                }

                if (ok) return {r, c};
            }
        }
        return {-1, -1};
    }
};
```

---

### 5. Testing & Edge Cases  

| Test | Reason |
|------|--------|
| `board = [[0]]`, `pattern = ["a"]` | Single cell, letter mapping. |
| `board = [[5,5],[5,5]]`, `pattern = ["aa","aa"]` | All same digits – mapping must be consistent. |
| `board = [[1,2],[3,4]]`, `pattern = ["a1","2b"]` | Mixed digit/letter mismatch. |
| `board` or `pattern` at boundary | Ensure we never read out‑of‑bounds. |
| `pattern` containing repeated letters with different digits | Should fail. |

Running the provided implementations against the above cases (and the sample tests from LeetCode) all yield the expected outputs.

---

### 6. Interview Tips  

1. **Clarify the bijection** – ask the interviewer if different letters must map to different digits.  
2. **Talk through your mapping arrays** – explain why you need two maps and how you enforce uniqueness.  
3. **Discuss complexity** – point out that the brute‑force is fine for the constraints but can be optimized if the board is large.  
4. **Edge‑case thinking** – mention off‑by‑one errors and how your loops ensure we stay inside the board.  
5. **Return ordering** – emphasize that iterating rows first guarantees the smallest coordinates.

---

### 7. SEO‑Optimized Takeaway  

> Master **LeetCode 2384** (Board Pattern Matching) – a classic 2‑D pattern‑matching challenge that teaches bijective mapping, two‑dimensional traversal, and early exit strategies. Dive into clean **Java, Python, and C++ solutions**, test edge cases, and walk interviewers through your reasoning to land that senior‑level role at Google, Amazon, or any top‑tech company.

Happy coding, and good luck in your next interview! 🚀

--- 

*Prepared by [Your Name], Senior Software Engineer & Coding Interview Coach.*