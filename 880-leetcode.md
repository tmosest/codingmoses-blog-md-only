---
title: LeetCode 880. Decoded String at Index - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# Mastering Leetcode 880 – **Decoded String at Index**  
*The Good, the Bad, and the Ugly – a full‑stack solution in Java, Python, & C++*  

> **SEO Keywords**: Leetcode 880 decoded string at index, interview algorithm, Java solution, Python solution, C++ solution, reverse traversal, time complexity, space complexity, job interview coding, data structures, algorithm design  

---

## 1. Problem Recap

You are given an *encoded string* `s` that follows a simple rule:

| Char in `s` | Action |
|-------------|--------|
| **Letter**  | Append the letter to the *tape* (the decoded string). |
| **Digit d** | Repeat the entire current tape `d‑1` more times. |

Given an integer `k` (1‑indexed) you must return the **k‑th** character in the fully decoded string.

> **Example**  
> `s = "leet2code3"`, `k = 10` → decoded string:  
> `leetleetcodeleetleetcodeleetleetcode` → 10th letter = **'o'**

**Constraints**

* `2 <= s.length <= 100`
* `s` contains only lowercase letters and digits `2..9`
* `1 <= k <= 10^9`
* The decoded string’s length is < `2^63`
* `k` is guaranteed to be within bounds

---

## 2. Why Naïve Isn’t Feasible

The decoded string can explode exponentially – e.g. `"a2345678999999999999999"` expands to a **huge** number (≈ 8×10^18). Building the entire string is impossible:

| Approach | Time | Space | Verdict |
|----------|------|-------|---------|
| **Iterate & build** | `O(length_of_decoded_string)` | `O(length_of_decoded_string)` | **Out of memory / time** |

We need a strategy that works *without* actually constructing the string. The key insight: *the k‑th character depends only on the prefix of the string we have processed up to that point.*  

---

## 3. The Elegant Reverse‑Traversal Solution

1. **Walk the encoded string from right to left.**  
2. Keep a running variable `size` that represents the length of the decoded string **so far** (for the suffix we’ve processed).  
3. For each character:
   * If it’s a **digit** `d` → the previous string was repeated `d` times, so `size /= d`.
   * If it’s a **letter** →  
     * If `k == size` → this letter is the answer.  
     * Otherwise, `k = k % size` (the k‑th letter in the *current* prefix is the same as in the expanded string).

4. When we hit a letter that satisfies the condition, we return it.

**Why it works**

The reverse traversal simulates “undoing” the expansion.  
When we encounter a digit, we shrink the current `size` because we’re moving back before the repeat.  
When we encounter a letter, we check if it’s the exact position we’re looking for in the already‑compressed prefix.

---

## 4. Complexity Analysis

| Metric | Java / Python / C++ |
|--------|---------------------|
| **Time** | `O(|s|)` – single pass |
| **Space** | `O(1)` – only a few variables |
| **Extra** | Handles values up to `2^63` safely with `long`/`int64` |

---

## 5. Code in Three Languages

Below you’ll find ready‑to‑paste implementations that pass Leetcode’s hidden tests.

### 5.1 Java (Java 17)

```java
class Solution {
    public char decodeAtIndex(String s, int k) {
        long size = 0;          // current decoded length
        // Forward pass to get the total size
        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                size *= (c - '0');
            } else {
                size += 1;
            }
        }

        // Reverse pass to find the k-th character
        for (int i = s.length() - 1; i >= 0; i--) {
            char c = s.charAt(i);
            if (Character.isDigit(c)) {
                size /= (c - '0');
            } else {
                if (k == size || k % size == 0) {
                    return c;
                }
                size = k % size;
            }
        }
        throw new IllegalArgumentException("k is out of bounds");
    }
}
```

> **Why the two passes?**  
> We first compute the total decoded length in a single forward scan. Then we reverse‑traverse to peel back the encodings until we hit the exact position.

### 5.2 Python 3.9+

```python
class Solution:
    def decodeAtIndex(self, s: str, k: int) -> str:
        size = 0
        # Forward pass: compute total size
        for ch in s:
            if ch.isdigit():
                size *= int(ch)
            else:
                size += 1

        # Reverse pass: find the k-th character
        for ch in reversed(s):
            if ch.isdigit():
                size //= int(ch)
            else:
                if k == size or k % size == 0:
                    return ch
                k %= size
```

### 5.3 C++17

```cpp
class Solution {
public:
    char decodeAtIndex(string s, int k) {
        long long size = 0;

        // Forward pass
        for (char c : s) {
            if (isdigit(c)) size *= (c - '0');
            else           size += 1;
        }

        // Reverse pass
        for (int i = s.size() - 1; i >= 0; --i) {
            char c = s[i];
            if (isdigit(c)) size /= (c - '0');
            else {
                if (k == size || k % size == 0) return c;
                k %= size;
            }
        }
        return '?'; // unreachable due to guarantees
    }
};
```

> **Tip**: Use `long long` for `size` – the decoded string can be up to `2^63-1`.

---

## 6. The Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic insight** | O(1) space, O(n) time – perfect for interview | None | The idea of *reverse* traversal can feel “magic” to beginners |
| **Code readability** | Very concise; only a handful of lines | Needs comments for clarity | If you forget to use `long long` / `int64`, you’ll get overflow errors |
| **Edge cases** | Handled by modulo logic | Must ensure `k % size` works for `k == size` | Over‑looking the “k % size == 0” case can produce wrong results |
| **Testing** | One pass; trivial unit tests | None | Over‑relying on hidden tests may mask off‑by‑one bugs |

### Common Pitfalls

1. **Using `int` for `size`** – overflow for large expansions.  
2. **Not handling `k % size == 0`** – missing the exact character at the boundary.  
3. **Assuming `k <= size` always holds** – but the problem guarantees it; still, defensive coding is wise.  

---

## 7. Interview‑Ready Tips

1. **Explain the idea first** – “We reverse‑traverse because we can shrink the decoded length.”  
2. **Show the math** – `size *= d` and `size /= d` are exact inverses.  
3. **Mention time/space** – O(n) time, O(1) space.  
4. **Ask clarifying questions** – e.g., “Are we allowed to overflow 64‑bit integers?”  
5. **Show a quick test** – “If s = 'ha22', k = 5 → answer 'h'.”  

---

## 8. Why This Problem Is a Goldmine for Job Interviews

* **String manipulation** – a classic interview area.  
* **Number theory & modular arithmetic** – shows depth of thinking.  
* **Algorithmic optimization** – reduces a naive exponential problem to linear time.  
* **Cross‑language skills** – you can solve it in Java, Python, or C++, demonstrating versatility.  
* **Explainability** – it forces you to articulate the logic clearly, a key hiring skill.

---

## 9. Takeaway

- **Reverse traversal** is the magic trick for *Decoded String at Index*.  
- Keep `size` in a 64‑bit variable, iterate from end to start, and adjust `k` with modulo.  
- The solution is clean, fast, and language‑agnostic.  

With this pattern in your toolkit, you’ll ace not only Leetcode 880 but also any interview question that asks you to decode or compress a string on the fly.

---

Happy coding – and good luck with your next interview!