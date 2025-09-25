---
title: LeetCode 3324. Find the Sequence of Strings Appeared on the Screen - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Overview – LeetCode 3324  
**Problem:** *Find the Sequence of Strings Appeared on the Screen*  
Given a target string `target`, Alice starts with an empty screen and has only two keys:

| Key | Action |
|-----|--------|
| 1   | Append the character **`a`** to the end of the current string |
| 2   | Change the **last** character of the current string to its next letter in the alphabet (`z` → `a`) |

We must return **every string that appears on the screen** as Alice types `target` using the **minimum number of key presses**. The strings must be in the order they appear.

---

## 2. Key Observations

1. **Appending a new character**  
   To add a new character `c` to the string, Alice must first press **Key 1** (append `a`) – this creates a new *placeholder* at the end of the current string.

2. **Incrementing to the target character**  
   After the placeholder is in place, Alice can use **Key 2** repeatedly to advance the last character from `'a'` up to the desired `c`.  
   The number of Key 2 presses required is `c - 'a'`.

3. **Recording the intermediate strings**  
   Every time a key is pressed, the new screen content must be appended to the answer list.  
   Hence, for each target character `c`, we need to output:
   * `current + 'a'` (after Key 1)  
   * `current + 'b'`, `current + 'c'`, …, `current + c` (after each Key 2 press)

4. **Minimal key‑press sequence**  
   The above procedure is *the* minimal sequence:  
   *We cannot skip the initial Key 1, and we need exactly `c - 'a'` increments to reach `c`.*

5. **Time & Space**  
   For a target of length `n`, at most `26` strings are generated for each character, so the total output size is bounded by `26 × n` (≤ 10 400 for the constraints).  
   The algorithm runs in **O(total output size)** time and uses **O(total output size)** space to store the answer list.

---

## 3. Algorithm (Pseudocode)

```
initialize cur = ""                // current string without the new char
initialize result = []

for each character ch in target:
    for c from 'a' to ch:
        result.add( cur + c )      // screen after each key press
    cur = cur + ch                 // append the final char for next iteration

return result
```

The inner loop runs `ch - 'a' + 1` times, producing the string after the first Key 1 press and after every Key 2 press.

---

## 4. Reference Implementations

### 4.1 Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<String> stringSequence(String target) {
        List<String> result = new ArrayList<>();
        StringBuilder cur = new StringBuilder();

        for (char ch : target.toCharArray()) {
            // Append 'a', record, then increment to ch
            for (char c = 'a'; c <= ch; c++) {
                result.add(cur.toString() + c);
            }
            // Update cur for the next character
            cur.append(ch);
        }

        return result;
    }

    // Optional main for quick manual test
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.stringSequence("abc")); // [a, aa, ab, aba, abb, abc]
    }
}
```

### 4.2 Python

```python
from typing import List

class Solution:
    def stringSequence(self, target: str) -> List[str]:
        res = []
        cur = []

        for ch in target:
            for c in range(ord('a'), ord(ch) + 1):
                res.append(''.join(cur) + chr(c))
            cur.append(ch)

        return res

# Quick manual test
if __name__ == "__main__":
    sol = Solution()
    print(sol.stringSequence("abc"))  # ['a', 'aa', 'ab', 'aba', 'abb', 'abc']
```

### 4.3 C++

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    vector<string> stringSequence(string target) {
        vector<string> res;
        string cur;                       // current string without the new char

        for (char ch : target) {
            for (char c = 'a'; c <= ch; ++c) {
                res.push_back(cur + c);
            }
            cur.push_back(ch);            // add final char for next round
        }
        return res;
    }
};
```

---

## 5. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3324”

> *“Find the Sequence of Strings Appeared on the Screen”*  
> *LeetCode 3324 – Solved in Java, Python, C++ – A Deep Dive into a Charming Simulation Problem*

---

### 5.1 Why This Problem Is a Gem for Interviewers

- **Simple surface**: The wording looks like a playful puzzle, but the underlying logic tests string manipulation, loops, and careful thinking about minimal operations.
- **Space for “clever” thinking**: Candidates can think about building the answer incrementally or simulate key presses in real time.
- **Clear constraints**: 1 ≤ target.length ≤ 400 keeps the solution manageable, but still allows a stress‑test of time‑efficiency.

---

### 5.2 The *Good* – An Elegant Linear Solution

The minimal key‑press sequence is deterministic:  
1. **Append `'a'`** (Key 1).  
2. **Increment until the target letter** (Key 2 repeated `c - 'a'` times).

Thus, for each character we simply:

```text
current + 'a', current + 'b', … , current + targetChar
```

And then append the `targetChar` to the current string for the next round.

Why is this “good”?

| Criterion | How the solution shines |
|-----------|------------------------|
| **Time Complexity** | O(total number of output strings) ≤ 26 × n |
| **Space Complexity** | O(total number of output strings) – unavoidable because we must return them |
| **Readability** | Two nested loops, clear variable names (`cur`, `res`) |
| **No extra data structures** | Only a `StringBuilder` (Java) or `string` (C++) and a list/vec |
| **Minimal key‑press logic** | No need for fancy state machines or DP |

---

### 5.3 The *Bad* – Common Pitfalls

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Skipping the initial `'a'`** | Missing the string that appears after the first Key 1 press. | Ensure the inner loop starts at `'a'` for every character. |
| **Using a single mutable string for all steps** | Overwriting previous results; you’ll only see the final screen content. | Create a *fresh* string for each intermediate step (`cur + c`). |
| **Incorrectly resetting the builder** | If you mutate the builder in place without restoring it for the next character, the prefix `current` changes unexpectedly. | Append the target character only after the inner loop. |
| **Assuming we need to increment past `targetChar`** | Generates 27 or more strings for `'a'`. | Loop only up to `targetChar`. |
| **Treating the problem as a “minimum press” DP** | Overcomplicating a simple deterministic sequence | Just loop; DP is unnecessary. |

---

### 5.4 The *Ugly* – Over‑engineering the Simulation

Some candidates implement a full “keyboard simulator” that tracks key states, uses a stack, or even a custom queue of operations. While it works, it adds:

- Extra indirection layers  
- Hard‑to‑follow code  
- A risk of subtle bugs (e.g., mis‑ordering the output)

> **Bottom line:** Keep it simple – the simulation is *simple* once you realize that each new character is just a “placeholder” that you gradually raise to the correct letter.

---

### 5.5 What to Highlight on Your Resume

- **Problem name & ID** – “LeetCode 3324: Find the Sequence of Strings Appeared on the Screen”.
- **Language** – “Implemented in Java, Python, and C++”.
- **Algorithmic insights** – “Linear time & space, deterministic minimal‑press logic”.
- **Key take‑away** – “Solved with clear, concise string loops and minimal auxiliary storage.”

These bullet points signal to recruiters that you understand how to read a problem statement, identify the underlying algorithmic pattern, and implement it cleanly.

---

### 5.6 Wrap‑Up: How This Problem Helps You Land Your Next Job

- **Demonstrates string handling skills** – a common requirement in many backend, mobile, and data‑processing roles.
- **Shows your ability to think about minimalism** – a valuable trait for optimization tasks in real production code.
- **Provides a tangible showcase** – You can paste the Java, Python, or C++ code into a GitHub Gist or a personal blog and present it during interviews or portfolio reviews.

---

### 5.7 Take‑Home Messages

1. **Read the constraints carefully** – 400 × 26 is tiny, so a brute‑force approach is fine.
2. **Simulate what the interviewer sees** – Every key press produces a new string; don’t forget the intermediate `'a'`.
3. **Write clean loops** – Two loops are all you need.  
4. **Test edge cases** – `'a'`, `'z'`, `"zzz"`, `"abcde"`.
5. **Add a quick `main` or `if __name__ == "__main__":` block** – helps you verify the solution locally before submission.

---

### 5.8 Quick Code Checklist

| Language | Function Signature | Return Type | Complexity |
|----------|--------------------|-------------|------------|
| **Java** | `public List<String> stringSequence(String target)` | `List<String>` | O(26 × n) |
| **Python** | `def stringSequence(self, target: str) -> List[str]` | `List[str]` | O(26 × n) |
| **C++** | `vector<string> stringSequence(string target)` | `vector<string>` | O(26 × n) |

---

#### 🎉 Final Thought

LeetCode 3324 is the perfect playground to demonstrate *clean* algorithm design, *concise* code, and a *solid* understanding of simulation concepts. Solve it once, publish the solution in three major languages, and showcase it on your portfolio or LinkedIn. Good luck – you’re one step closer to that dream job!