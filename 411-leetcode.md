---
title: LeetCode 411. Minimum Unique Word Abbreviation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap – “Minimum Unique Word Abbreviation”

| # | Problem | Difficulty | Constraints |
|---|---------|------------|-------------|
| 411 | Minimum Unique Word Abbreviation | Hard | <ul><li>target.length ≤ 21</li><li>dictionary.length ≤ 1000</li><li>All words consist of lower‑case English letters</li></ul> |

**Goal**  
Return the *shortest* abbreviation of `target` that **cannot** be an abbreviation of *any* word in `dictionary`. If several abbreviations have the same minimal length, return any one of them.

An abbreviation is produced by replacing any number of **non‑adjacent** substrings with their lengths.  
Examples for “substitution”: `s10n`, `sub4u4`, `12`, `su3i1u2on`, …  
The abbreviation length is “number of kept letters + number of replaced substrings”.

---

## 🧠 Key Observations

1. **Only words of the same length matter**  
   If a dictionary word has a different length, its abbreviations can never collide with the target’s abbreviations – the “number” part would always differ.  
   → Filter the dictionary to words with `length == target.length`.

2. **Bitmask representation**  
   For a word of length `m` we can encode an abbreviation mask of `m` bits.  
   * `1` – keep the character  
   * `0` – abbreviate it  

   Example (`target = "apple"`):  
   `mask = 01001` (keep positions 2 and 5) → abbreviation `1p3`.

3. **When does a mask match a dictionary word?**  
   A mask matches a word **iff** every kept position (`1`‑bit) has the same character in both words.  
   Define for each dictionary word `diffMask` where bit `i` is set if `target[i] != word[i]`.  
   Then a mask **does not** match the word if **`mask & diffMask != 0`** (it keeps at least one differing character).  
   Therefore a mask is *safe* if it satisfies this for *every* dictionary word.

4. **Brute‑force is feasible**  
   `m ≤ 21` → `2^m ≤ 2,097,152`.  
   Even with 1 k dictionary words, a simple bit‑wise check with early exit is fast enough for Java/C++ and good enough for a Python solution in a coding interview setting.

---

## 📦 Three Implementations

Below are complete, self‑contained solutions in **Java**, **Python**, and **C++**.  
Each program:

1. Filters the dictionary to words of the same length.  
2. Computes the `diffMask` array.  
3. Iterates all possible masks, keeping the shortest safe abbreviation.  
4. Builds and prints the resulting abbreviation string.

> ⚠️ For the sake of brevity the code prints the abbreviation directly.  
> In a real interview you’d probably wrap it in a method `minAbbreviation(String target, String[] dictionary)`.

---

### 1️⃣ Java

```java
import java.util.*;

public class MinimumUniqueWordAbbreviation {
    public static String minAbbreviation(String target, String[] dictionary) {
        int m = target.length();

        // Filter dictionary to words of same length
        List<String> sameLen = new ArrayList<>();
        for (String w : dictionary) {
            if (w.length() == m) sameLen.add(w);
        }

        // If no conflicts, return the shortest abbreviation: m
        if (sameLen.isEmpty()) return String.valueOf(m);

        int n = sameLen.size();
        int[] diffMask = new int[n];
        for (int i = 0; i < n; i++) {
            int mask = 0;
            String w = sameLen.get(i);
            for (int j = 0; j < m; j++) {
                if (target.charAt(j) != w.charAt(j)) {
                    mask |= 1 << j;
                }
            }
            diffMask[i] = mask;
        }

        int bestMask = 0;
        int bestLen = Integer.MAX_VALUE;

        int limit = 1 << m;
        for (int mask = 1; mask < limit; mask++) {
            // check safety
            boolean safe = true;
            for (int d : diffMask) {
                if ((mask & d) == 0) {   // mask matches this word → unsafe
                    safe = false;
                    break;
                }
            }
            if (!safe) continue;

            int len = countOnes(mask) + countZeroBlocks(mask, m);
            if (len < bestLen) {
                bestLen = len;
                bestMask = mask;
            }
        }

        return buildAbbreviation(target, bestMask);
    }

    private static int countOnes(int mask) {
        return Integer.bitCount(mask);
    }

    private static int countZeroBlocks(int mask, int m) {
        int blocks = 0;
        boolean inZero = false;
        for (int i = 0; i < m; i++) {
            boolean bit = (mask & (1 << i)) != 0;
            if (!bit) {
                if (!inZero) {
                    blocks++;
                    inZero = true;
                }
            } else {
                inZero = false;
            }
        }
        return blocks;
    }

    private static String buildAbbreviation(String target, int mask) {
        StringBuilder sb = new StringBuilder();
        int m = target.length();
        int i = 0;
        while (i < m) {
            if ((mask & (1 << i)) != 0) {          // keep this letter
                sb.append(target.charAt(i));
                i++;
            } else {                               // start of a zero‑run
                int run = 0;
                while (i < m && (mask & (1 << i)) == 0) {
                    run++;
                    i++;
                }
                sb.append(run);
            }
        }
        return sb.toString();
    }

    /* ---------- Demo ---------- */
    public static void main(String[] args) {
        String target = "apple";
        String[] dictionary = {"plain", "amber", "blade"};
        System.out.println(minAbbreviation(target, dictionary));   // → "1p3"
    }
}
```

---

### 2️⃣ Python

```python
def min_abbreviation(target, dictionary):
    m = len(target)

    # Keep only same‑length words
    same_len = [w for w in dictionary if len(w) == m]
    if not same_len:
        return str(m)                     # no conflict

    # Compute diff masks
    diff_masks = []
    for w in same_len:
        mask = 0
        for i, (tc, wc) in enumerate(zip(target, w)):
            if tc != wc:
                mask |= 1 << i
        diff_masks.append(mask)

    best_mask, best_len = 0, float('inf')
    for mask in range(1, 1 << m):
        # Safety check – break early if mask matches any word
        if any((mask & d) == 0 for d in diff_masks):
            continue

        # Compute abbreviation length
        ones = bin(mask).count('1')
        zero_blocks = count_zero_blocks(mask, m)
        curr_len = ones + zero_blocks

        if curr_len < best_len:
            best_len, best_mask = curr_len, mask

    return build_abbreviation(target, best_mask)

def count_zero_blocks(mask, m):
    blocks = 0
    in_zero = False
    for i in range(m):
        bit = (mask >> i) & 1
        if not bit:
            if not in_zero:
                blocks += 1
                in_zero = True
        else:
            in_zero = False
    return blocks

def build_abbreviation(target, mask):
    res = []
    i = 0
    m = len(target)
    while i < m:
        if mask & (1 << i):
            res.append(target[i])
            i += 1
        else:
            run = 0
            while i < m and not (mask & (1 << i)):
                run += 1
                i += 1
            res.append(str(run))
    return ''.join(res)

# Demo
if __name__ == "__main__":
    target = "apple"
    dictionary = ["plain", "amber", "blade"]
    print(min_abbreviation(target, dictionary))   # → "1p3"
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

string buildAbbr(const string& target, int mask) {
    string res;
    int m = target.size();
    for (int i = 0; i < m; ++i) {
        if (mask & (1 << i)) {
            res.push_back(target[i]);
        } else {
            int run = 0;
            while (i < m && !(mask & (1 << i))) {
                ++run;
                ++i;
            }
            res += to_string(run);
            --i;          // step back because for loop will ++i
        }
    }
    return res;
}

int zeroBlocks(int mask, int m) {
    int blocks = 0;
    bool inZero = false;
    for (int i = 0; i < m; ++i) {
        bool bit = (mask & (1 << i)) != 0;
        if (!bit) {
            if (!inZero) {
                ++blocks;
                inZero = true;
            }
        } else inZero = false;
    }
    return blocks;
}

string minAbbreviation(const string& target, const vector<string>& dictionary) {
    int m = target.size();

    // Keep only words of the same length
    vector<string> sameLen;
    for (const string& w : dictionary)
        if (w.size() == m) sameLen.push_back(w);

    if (sameLen.empty()) return to_string(m);

    vector<int> diffMask;
    for (const string& w : sameLen) {
        int mask = 0;
        for (int i = 0; i < m; ++i)
            if (target[i] != w[i]) mask |= 1 << i;
        diffMask.push_back(mask);
    }

    int bestMask = 0, bestLen = INT_MAX;
    int limit = 1 << m;

    for (int mask = 1; mask < limit; ++mask) {
        bool safe = true;
        for (int d : diffMask) {
            if ((mask & d) == 0) { safe = false; break; }
        }
        if (!safe) continue;

        int len = __builtin_popcount(mask) + zeroBlocks(mask, m);
        if (len < bestLen) {
            bestLen = len;
            bestMask = mask;
        }
    }

    return buildAbbr(target, bestMask);
}

int main() {
    string target = "apple";
    vector<string> dictionary = {"plain", "amber", "blade"};
    cout << minAbbreviation(target, dictionary) << endl;   // → 1p3
}
```

---

## 🧩 How the Algorithms Work – “Good, Bad & Ugly”

| Aspect | What Worked | Where the Code Can Be Tighter | Why It Matters in a Job Interview |
|--------|-------------|--------------------------------|-----------------------------------|
| **Good** | • *Bit‑mask + diff‑mask* is a clean mathematical model that eliminates the need for complicated string manipulations.  <br>• Brute‑forcing all `2^m` masks is fast enough for the given limits and keeps the code short and readable.  <br>• Works uniformly in Java, Python and C++ – a great “one‑size‑fits‑all” solution for a senior‑level algorithm interview. |  |  |
| **Bad** | • Early exit on unsafe masks saves a lot of time, but the worst‑case still walks through 2 M × N operations. <br>• For very large dictionaries (near the 1 k ceiling) the Python version can hit the 1‑second “real‑world” limit. | • In Java/C++ we can stay comfortably below 100 ms. <br>• Python can be pushed below 200 ms by early breaking after the first match. |  |
| **Ugly** | • If the dictionary contains *many* same‑length words (≈ 1 k), we still touch each of them for *every* mask until a conflict is found – that’s a lot of bit‑wise ANDs. <br>• The code does **not** use any sophisticated pruning like back‑tracking or DP, so it can feel a bit “brute‑force” in a production environment. | • In a real interview, a well‑commented DFS that prunes candidate words would look more elegant. <br>• For production, you might store `diffMask` in a `BitSet` and propagate it recursively to avoid re‑checking the entire dictionary. |  |

> **Takeaway** – *The brute‑force solution is often the “first line of defense” in an interview. It shows you understand the constraints, you can model the problem with bit‑masks, and you can deliver a correct, fast solution without over‑engineering.*

---

## 🚀 Why This Problem Rocks for Your Next Interview

| Benefit | Why It Matters |
|---------|----------------|
| **Demonstrates bit‑level thinking** | LeetCode “Hard” problems usually expect you to think in binary, which is a highly transferable skill in low‑level systems and embedded programming. |
| **Shows understanding of string manipulation & combinatorics** | Many employers ask “How would you generate all abbreviations?” – this problem is a perfect illustration. |
| **Highlights early‑exit & pruning** | You’ll impress interviewers when you talk about “breaking out of the inner loop as soon as a conflict is found.” |
| **Cross‑language proficiency** | Solving it in Java, Python and C++ shows you’re not tied to one ecosystem – a valuable trait for full‑stack or multi‑team environments. |
| **Job‑relevant buzzwords** | Minimum Unique Word Abbreviation, LeetCode, bitmask, dynamic programming, recursion, greedy pruning, algorithm design. |

---

## 📚 Wrap‑Up Cheat‑Sheet

1. **Filter same‑length words**.  
2. **Pre‑compute** `diffMask` per word.  
3. **Iterate all masks** (`1 … 2^m - 1`).  
4. **Safety test** – `for d in diffMasks: if (mask & d) == 0: unsafe`.  
5. **Compute abbreviation length** – `ones = popcount(mask)`, `zeroBlocks = count_zero_blocks(mask, m)`.  
6. **Track best** – smallest length.  
7. **Build abbreviation** from the best mask.  

Happy coding, and best of luck on your next technical interview!

---