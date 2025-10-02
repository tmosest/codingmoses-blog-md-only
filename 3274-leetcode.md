---
title: LeetCode 3274. Check if Two Chessboard Squares Have the Same Color - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3274. Check if Two Chessboard Squares Have the Same Color  
### *Clean, Simple, & Job‑Ready Code in 10+ Languages*  

> **Why this matters** – Interviewers love problems that test your ability to *translate* a real‑world concept (chessboard colors) into a mathematically‑sound algorithm.  
> The trick is to recognize that the board’s “black/white” pattern is essentially an *even/odd* parity problem.  
> Mastering this gives you a fast, constant‑time solution that you can drop into almost any interview or coding test.

---

## 📌 Problem Recap (LeetCode 3274)

```text
Given two chessboard coordinates (e.g., "a1", "c3"), return true if both squares
are the same color, otherwise false.
```

Constraints  

| Constraint | Value |
|------------|-------|
| Length of each coordinate | 2 |
| First char ∈ ['a','h'] | Yes |
| Second char ∈ ['1','8'] | Yes |

---

## 🔎 Intuition

- Chessboards are colored alternately.  
- If we convert a coordinate `(letter, number)` to a single integer (e.g., `ord(letter) + int(number)`), **the parity of that integer tells us the color**.  
  - Even → one color (say, black)  
  - Odd  → the other color (white)  
- Two squares share the same color **iff** their parities match.

---

## 💡 The One‑Liner Idea

```text
return (ord(c1[0]) + int(c1[1])) % 2 == (ord(c2[0]) + int(c2[1])) % 2
```

That’s all: constant time, constant memory, no loops, no conditionals, no maps.

---

## 🧑‍💻 Code Implementations (Top 10 Languages + HolyC)

Below you’ll find a ready‑to‑copy implementation in **10 mainstream languages** plus a playful snippet in **HolyC** (TempleOS).  

> **Tip**: Keep your solution concise; interviewers appreciate brevity & clarity.  

---

### 1. Python 3

```python
class Solution:
    def checkTwoChessboards(self, coordinate1: str, coordinate2: str) -> bool:
        """Return True if two coordinates have the same color."""
        return (ord(coordinate1[0]) + int(coordinate1[1])) % 2 == \
               (ord(coordinate2[0]) + int(coordinate2[1])) % 2
```

*Complexity*: **O(1)** time, **O(1)** space.

---

### 2. Java

```java
public class Solution {
    public boolean checkTwoChessboards(String coordinate1, String coordinate2) {
        int c1 = coordinate1.charAt(0) + Character.getNumericValue(coordinate1.charAt(1));
        int c2 = coordinate2.charAt(0) + Character.getNumericValue(coordinate2.charAt(1));
        return (c1 % 2) == (c2 % 2);
    }
}
```

---

### 3. C++

```cpp
class Solution {
public:
    bool checkTwoChessboards(string coordinate1, string coordinate2) {
        int c1 = coordinate1[0] + (coordinate1[1] - '0');
        int c2 = coordinate2[0] + (coordinate2[1] - '0');
        return (c1 % 2) == (c2 % 2);
    }
};
```

---

### 4. JavaScript (ES6)

```js
var checkTwoChessboards = function (coordinate1, coordinate2) {
  const sum1 = coordinate1.charCodeAt(0) + +coordinate1[1];
  const sum2 = coordinate2.charCodeAt(0) + +coordinate2[1];
  return (sum1 & 1) === (sum2 & 1);
};
```

---

### 5. C#

```csharp
public class Solution {
    public bool CheckTwoChessboards(string coordinate1, string coordinate2) {
        int c1 = coordinate1[0] + (coordinate1[1] - '0');
        int c2 = coordinate2[0] + (coordinate2[1] - '0');
        return (c1 % 2) == (c2 % 2);
    }
}
```

---

### 6. Go

```go
package main

func checkTwoChessboards(coordinate1, coordinate2 string) bool {
    c1 := int(coordinate1[0]) + int(coordinate1[1]-'0')
    c2 := int(coordinate2[0]) + int(coordinate2[1]-'0')
    return (c1%2) == (c2%2)
}
```

---

### 7. Rust

```rust
impl Solution {
    pub fn check_two_chessboards(coordinate1: String, coordinate2: String) -> bool {
        let c1 = coordinate1.as_bytes()[0] as i32 + (coordinate1.as_bytes()[1] - b'0') as i32;
        let c2 = coordinate2.as_bytes()[0] as i32 + (coordinate2.as_bytes()[1] - b'0') as i32;
        (c1 % 2) == (c2 % 2)
    }
}
```

---

### 8. Kotlin

```kotlin
class Solution {
    fun checkTwoChessboards(coordinate1: String, coordinate2: String): Boolean {
        val c1 = coordinate1[0] + (coordinate1[1] - '0')
        val c2 = coordinate2[0] + (coordinate2[1] - '0')
        return (c1 % 2) == (c2 % 2)
    }
}
```

---

### 9. Swift

```swift
class Solution {
    func checkTwoChessboards(_ coordinate1: String, _ coordinate2: String) -> Bool {
        let c1 = coordinate1.first!.unicodeScalars.first!.value +
                 UInt32(coordinate1.last! - "0")
        let c2 = coordinate2.first!.unicodeScalars.first!.value +
                 UInt32(coordinate2.last! - "0")
        return (c1 & 1) == (c2 & 1)
    }
}
```

---

### 10. PHP

```php
<?php
class Solution {
    /** @param String $coordinate1
     *  @param String $coordinate2
     *  @return Boolean
     */
    function checkTwoChessboards($coordinate1, $coordinate2) {
        $c1 = ord($coordinate1[0]) + intval($coordinate1[1]);
        $c2 = ord($coordinate2[0]) + intval($coordinate2[1]);
        return ($c1 % 2) == ($c2 % 2);
    }
}
?>
```

---

### 11. HolyC (TempleOS)

HolyC is a niche language, but the idea still holds.

```holyC
Bool CheckTwoChessboards(Char* coord1, Char* coord2) {
    Int sum1 = coord1[0] + (coord1[1] - '0');
    Int sum2 = coord2[0] + (coord2[1] - '0');
    Return (sum1 & 1) == (sum2 & 1);
}
```

> **Note**: HolyC uses 0‑based indexing and simple arithmetic; the parity trick works unchanged.

---

## 📊 Complexity Summary

| Language | Time | Space |
|----------|------|-------|
| All | **O(1)** | **O(1)** |

The solution is truly *instant* – no loops, no recursion, no extra data structures.

---

## 🧩 The Good, The Bad, and The Ugly

### ✅ The Good
- **Simplicity** – A single arithmetic expression explains the entire logic.
- **Speed** – Constant‑time execution; perfect for large test sets.
- **Portability** – Works unchanged across most programming languages.
- **Interview Appeal** – Shows you can turn a visual pattern into math.

### ⚠️ The Bad
- **Subtle Off‑By‑One** – Forgetting that `'a'` is 97 (not 1) can break the parity.
- **Language‑Specific Char Handling** – Some languages (e.g., Go, Rust) need careful byte manipulation.
- **Error Handling** – The problem guarantees valid input, but real code should validate coordinates.

### 🧨 The Ugly
- **Readability vs. Brevity** – One‑liners are neat, but some interviewers prefer a step‑by‑step explanation.  
  *Solution*: Comment each line or provide an alternate, more verbose implementation for clarity.  
- **HolyC’s Niche** – It’s great for a challenge, but rarely used in production interviews.  
  *Tip*: Show it only if you’re interviewing for TempleOS or a niche role.  

---

## 🚀 Interview Hacks & Final Thoughts

1. **Explain the parity trick**: “If you add the column index (a=1) and the row number, the sum’s parity gives the color.”
2. **Show a quick test**: “a1 → 1+1=2 (even) → black. c3 → 3+3=6 (even) → black.”
3. **Mention edge cases**: “All inputs are guaranteed valid, so we skip bounds checking.”
4. **Highlight O(1)**: “Even with millions of queries, the runtime stays constant.”

---

## 🌟 SEO‑Optimized Summary

If you’re preparing for coding interviews, mastering the **“Check if Two Chessboard Squares Have the Same Color”** problem will give you a quick win. This article shows you the **cleanest, most efficient solution** in **Python, Java, C++, JavaScript, C#, Go, Rust, Kotlin, Swift, PHP**, and even **HolyC**. By explaining the parity trick, providing multi‑language code, and covering common pitfalls, you’ll impress interviewers and boost your résumé.

**Keywords**: LeetCode 3274, chessboard color check, parity trick, interview coding problem, O(1) solution, multi-language coding, job interview tips, programming challenge, clean code example, HolyC code.

---

> **Takeaway**: When faced with a pattern‑recognition problem, translate the visual to a mathematical property. The parity trick is a classic example that shows depth without complexity—exactly what interviewers look for. Happy coding!