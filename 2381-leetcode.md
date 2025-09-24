---
title: LeetCode 2381. Shifting Letters II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2381 – *Shifting Letters II*: A Full‑Stack Solution (Java | Python | C++)  
> **The good, the bad, and the ugly – and how to ace this problem in a job interview**

---

## 🎯 Problem Recap (Easy → Medium)

> **Shifting Letters II**  
> Given a lowercase string `s` and a 2‑D array `shifts`, each entry `[start, end, dir]` represents a range‑update:
> - `dir = 1` → shift each letter in `s[start … end]` **forward** (`a → b`, `z → a`).  
> - `dir = 0` → shift each letter in `s[start … end]` **backward** (`a → z`, `b → a`).  
> Return the final string after all operations.

**Constraints**

| | | |
|---|---|---|
| `1 ≤ s.length ≤ 5·10⁴` | `1 ≤ shifts.length ≤ 5·10⁴` | `0 ≤ start ≤ end < s.length` |
| `direction ∈ {0,1}` | `s` contains only lowercase letters | |

---

## 📖 Why This Problem is a *Goldmine* for Interviews

1. **Range update + point query** – classic use‑case for difference arrays / prefix sums.  
2. **String manipulation + modulo arithmetic** – tests low‑level understanding of ASCII/Unicode.  
3. **Time‑efficiency** – naive `O(n · m)` is too slow; optimal `O(n+m)` is expected.  
4. **Language‑agnostic** – can be solved in any popular language; shows you know data‑structures regardless of syntax.  

---

## 🧠 Intuition: From “Apply Every Shift” to “Apply All Shifts at Once”

Imagine you had to process 50 k updates on a 50 k string – applying each shift one by one would be ~2.5 billion character changes, far too slow.  
The trick: **record the net effect for each index in a single pass**.

1. **Difference array** – store the *difference* between consecutive indices.  
2. **Prefix sum** – transform the difference array into the actual net shift for every character.  
3. **Apply the net shift** – wrap around the alphabet with modulo 26.  

This is a textbook *range update, point query* problem.

---

## 🏗️ Step‑by‑Step Algorithm

| Step | Description | Code Sketch |
|------|-------------|-------------|
| 1 | Create `diff` array of size `n+1` (all zeros). | `int[] diff = new int[n+1];` |
| 2 | For each `[l, r, dir]`:  
&nbsp;&nbsp;• `shift = dir == 1 ? +1 : -1`  
&nbsp;&nbsp;• `diff[l] += shift`  
&nbsp;&nbsp;• `diff[r+1] -= shift` | `diff[l] += shift; diff[r+1] -= shift;` |
| 3 | Compute prefix sum → `net[i]` (cumulative shift up to index `i`). | `int cur = 0; for (i …) cur += diff[i]; net[i] = cur;` |
| 4 | For each character `c` at position `i`:  
&nbsp;&nbsp;`shiftVal = ((net[i] % 26) + 26) % 26` (ensures positivity)  
&nbsp;&nbsp;`c' = (char)('a' + (c - 'a' + shiftVal) % 26)` |  |
| 5 | Join all modified characters → final string. | `return new StringBuilder(...).toString();` |

**Why modulo 26?**  
Shifting forward by `+k` or backward by `-k` is equivalent to `+k mod 26`.  
Adding 26 before `% 26` guarantees the result stays non‑negative.

---

## 📦 Code Implementations

Below are clean, idiomatic solutions for **Java**, **Python**, and **C++**.  
All run in **O(n + m)** time and **O(n)** extra space.

> **Tip** – In interviews, ask whether you need to handle extremely large numbers of shifts (e.g., > 10⁶). The same difference‑array idea scales; just use a `long` accumulator to avoid overflow.

---

### Java (Java 17+)

```java
class Solution {
    public String shiftingLetters(String s, int[][] shifts) {
        int n = s.length();
        int[] diff = new int[n + 1];          // difference array

        // 1. Apply all range updates
        for (int[] op : shifts) {
            int l = op[0], r = op[1];
            int dir = op[2];
            int delta = dir == 1 ? 1 : -1;    // +1 for forward, -1 for backward
            diff[l] += delta;
            diff[r + 1] -= delta;
        }

        // 2. Prefix sum + apply shifts to each character
        StringBuilder sb = new StringBuilder(n);
        int cur = 0;
        for (int i = 0; i < n; i++) {
            cur += diff[i];
            int shift = ((cur % 26) + 26) % 26;          // ensure [0,25]
            char base = s.charAt(i);
            char newChar = (char) ('a' + (base - 'a' + shift) % 26);
            sb.append(newChar);
        }
        return sb.toString();
    }
}
```

---

### Python 3

```python
class Solution:
    def shiftingLetters(self, s: str, shifts: List[List[int]]) -> str:
        n = len(s)
        diff = [0] * (n + 1)

        # Range updates
        for l, r, dir in shifts:
            delta = 1 if dir else -1
            diff[l] += delta
            diff[r + 1] -= delta

        # Prefix sum + apply shifts
        cur = 0
        res = []
        for i, ch in enumerate(s):
            cur += diff[i]
            shift = cur % 26                 # Python handles negatives fine
            new_ch = chr((ord(ch) - 97 + shift) % 26 + 97)
            res.append(new_ch)
        return "".join(res)
```

---

### C++ (C++17)

```cpp
class Solution {
public:
    string shiftingLetters(string s, vector<vector<int>>& shifts) {
        int n = s.size();
        vector<int> diff(n + 1, 0);

        // 1. Range updates
        for (auto &op : shifts) {
            int l = op[0], r = op[1], dir = op[2];
            int delta = dir ? 1 : -1;          // +1 forward, -1 backward
            diff[l] += delta;
            diff[r + 1] -= delta;
        }

        // 2. Prefix sum + apply
        string result;
        result.reserve(n);
        int cur = 0;
        for (int i = 0; i < n; ++i) {
            cur += diff[i];
            int shift = ((cur % 26) + 26) % 26;  // normalize to [0,25]
            char newChar = 'a' + (s[i] - 'a' + shift) % 26;
            result.push_back(newChar);
        }
        return result;
    }
};
```

---

## 📊 Complexity Analysis

| | Time | Extra Space |
|---|------|-------------|
| **All 3 solutions** | `O(n + m)` (linear sweep over string + updates) | `O(n)` (difference array + result buffer) |
| **Worst‑case shift magnitude** | `O(1)` per character (modulo 26) | |

**Why is `O(n+m)` enough?**  
- Each update touches two positions in `diff` → `O(m)`.  
- One sweep over `s` to build prefix sums and mutate characters → `O(n)`.

---

## 🔍 Good, Bad & Ugly: A Deep Dive

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Clear separation between *update* and *query* → difference array. | Students often forget to add the `+1` sentinel (`diff[r+1] -= delta`). | Failing to normalize negative modulo values can produce `char` underflows (e.g., `-1 % 26 = -1` in some languages). |
| **Implementation** | One‑pass prefix sum + single `StringBuilder` / `StringBuilder`. | Using `ArrayList<Integer>` or `int[]` of wrong size may cause `ArrayIndexOutOfBoundsException`. | Over‑optimizing with bitwise tricks (`+26) % 26`) can hurt readability. |
| **Edge Cases** | Modulo 26 ensures wrap‑around for any shift amount. | Forgetting `+26` before `% 26` gives negative results in Java & C++. | In Python, forgetting to convert `shift % 26` to positive may still work, but clarity is lost. |
| **Readability** | `diff[l] += delta; diff[r+1] -= delta;` is crystal clear. | `for i in range(n): cur += diff[i];` is concise but may hide intent. | Using `cur` as a running total is fine; avoid confusing variable names like `sum` (reserved keyword in C++). |
| **Testing** | Create tests with alternating forward/backward shifts to ensure wrap‑around. | Check `s="az"`, shifts=[[0,1,0]] → `"yz"`. | Stress test with 5 × 10⁴ operations to confirm performance. |

---

## 🎤 Interview Checklist

| ✔️ | Question | Explanation |
|----|----------|-------------|
| ✔️ | “Do you know the difference array technique?” | Show you can transform a range‑update problem into a linear‑time solution. |
| ✔️ | “Why do we use modulo 26?” | Demonstrates understanding of cyclic alphabets. |
| ✔️ | “What about overflow? Can shift amounts be huge?” | Discuss using `long` or `BigInteger` if necessary. |
| ✔️ | “How would you modify this for a single shift direction?” | Talk about partial sums vs. cumulative sums. |
| ✔️ | “What if we needed to query the string after each shift?” | Range update, point query vs. prefix sum; mention Fenwick tree for dynamic updates. |

---

## 💡 Bonus: Extending the Idea

- **Generalize to arbitrary alphabets** – replace `26` with `ALPHA`.  
- **Multiple test cases** – wrap the whole logic into a loop and reuse the `diff` array.  
- **Streaming input** – in a real interview, you could stream the string and apply the prefix sum on the fly to keep memory usage minimal.

---

## 🌟 The Takeaway

- **Good**: The difference array is *intuitive*, *fast*, and *language‑agnostic*.  
- **Bad**: A naive `O(n·m)` solution is tempting but will get you stuck on time limits.  
- **Ugly**: Forgetting to normalize negative modulo values is a common pitfall that crashes your code in Java/C++.  

Master this pattern, and you’ll win points for **efficient thinking** and **clean implementation**—two qualities any hiring manager loves.

---

## 🎓 How to Use This in Your Next Interview

1. **Explain the problem** in your own words (range update + point query).  
2. **Show the difference‑array trick** on a whiteboard before coding.  
3. **Ask clarifying questions** (e.g., “Is the string guaranteed to be small?”) – shows active listening.  
4. **Write clean code** with comments (as above).  
5. **Discuss complexity** explicitly.  
6. **Wrap up** with a quick test example to demonstrate correctness.  

---

## 🔚 Final Words

LeetCode 2381 is more than a string‑shifting puzzle – it’s a micro‑lesson in **efficient data manipulation**.  
Implement it in Java, Python, and C++, present the algorithm clearly, and you’ll be ready to impress hiring managers at tech giants, startups, or your next coding interview.

> **Remember:** The key is *“Record the differences, then sum them”*. Once you internalize that, the same mindset solves a huge spectrum of interview questions (range updates, interval problems, cumulative sums, etc.).

Happy coding—and may your next interview go *shiftingly* smooth!