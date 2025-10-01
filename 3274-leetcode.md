---
title: LeetCode 3274. Check if Two Chessboard Squares Have the Same Color - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3274 – “Check if Two Chessboard Squares Have the Same Color”  
## 🧩 A Quick, 3‑Line Solution in Java, Python & C++ + A Job‑Seeking Blog

---

## Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Intuition & Key Insight](#intuition)  
3. [Solution Overview](#solution-overview)  
4. [Reference Implementations](#implementations)  
   - Java  
   - Python  
   - C++  
5. [Complexity Analysis](#complexity)  
6. [Common Pitfalls & Edge Cases](#pitfalls)  
7. [Extending the Idea](#extensions)  
8. [Conclusion & Job‑Search Tips](#conclusion)  
9. [Meta & SEO Checklist](#seo)

---

<a name="problem-recap"></a>
## 1. Problem Recap  
**LeetCode 3274 – Check if Two Chessboard Squares Have the Same Color**  
Given two valid chessboard coordinates (`"a1"`‑`"h8"`), determine if both squares share the same color (both black or both white).

> **Return** `true` if they have the same color, otherwise `false`.

---

<a name="intuition"></a>
## 2. Intuition & Key Insight  
A chessboard alternates colors like a checkerboard.  
If we assign an integer value to each square, the parity (even/odd) of that value tells us the color:

| Column | ASCII | Row | Sum | Parity | Color |
|--------|-------|-----|-----|--------|-------|
| `a`    | 97    | 1   | 98  | Even   | Black |
| `b`    | 98    | 1   | 99  | Odd    | White |
| `c`    | 99    | 1   | 100 | Even   | Black |

**Rule**  
> Two squares have the same color **iff** the sums `(letter + number)` have the **same parity**.

This gives a one‑liner:  
`(c1 % 2) == (c2 % 2)`.

---

<a name="solution-overview"></a>
## 3. Solution Overview  

1. **Extract** the letter and number from each coordinate.  
2. **Convert** the letter to its ASCII code (`'a'` → 97).  
3. **Convert** the number character to an integer.  
4. **Add** the two values → a single integer per coordinate.  
5. **Check** parity (even/odd) for both sums.  
6. **Return** `true` if parity matches, else `false`.

The algorithm runs in **O(1)** time and uses **O(1)** extra space.

---

<a name="implementations"></a>
## 4. Reference Implementations  

> All snippets can be dropped into your LeetCode/IDE environment.  
> Test harness is optional but shown for completeness.

### Java  
```java
public class Solution {
    /**
     * LeetCode 3274
     *
     * @param coordinate1 first square (e.g., "a1")
     * @param coordinate2 second square (e.g., "c3")
     * @return true if both squares share the same color
     */
    public boolean checkTwoChessboards(String coordinate1, String coordinate2) {
        int sum1 = coordinate1.charAt(0) + (coordinate1.charAt(1) - '0');
        int sum2 = coordinate2.charAt(0) + (coordinate2.charAt(1) - '0');
        return (sum1 % 2) == (sum2 % 2);
    }

    // Optional: quick test runner
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.checkTwoChessboards("a1", "c3")); // true
        System.out.println(s.checkTwoChessboards("a1", "h3")); // false
    }
}
```

### Python  
```python
class Solution:
    def checkTwoChessboards(self, coordinate1: str, coordinate2: str) -> bool:
        """
        LeetCode 3274
        :param coordinate1: str, e.g., "a1"
        :param coordinate2: str, e.g., "c3"
        :return: bool, True if same color
        """
        sum1 = ord(coordinate1[0]) + int(coordinate1[1])
        sum2 = ord(coordinate2[0]) + int(coordinate2[1])
        return (sum1 % 2) == (sum2 % 2)

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.checkTwoChessboards("a1", "c3"))  # True
    print(sol.checkTwoChessboards("a1", "h3"))  # False
```

### C++  
```cpp
class Solution {
public:
    bool checkTwoChessboards(string coordinate1, string coordinate2) {
        int sum1 = coordinate1[0] + (coordinate1[1] - '0');
        int sum2 = coordinate2[0] + (coordinate2[1] - '0');
        return (sum1 % 2) == (sum2 % 2);
    }
};

/* Optional test harness
#include <iostream>
int main() {
    Solution s;
    std::cout << std::boolalpha
              << s.checkTwoChessboards("a1", "c3") << '\n' // true
              << s.checkTwoChessboards("a1", "h3") << '\n'; // false
    return 0;
}
*/
```

---

<a name="complexity"></a>
## 5. Complexity Analysis  

| Metric | Calculation |
|--------|-------------|
| **Time** | `O(1)` – constant operations (2 char accesses, 2 conversions, 2 adds, 2 mods). |
| **Space** | `O(1)` – only a few integer variables. |

---

<a name="pitfalls"></a>
## 6. Common Pitfalls & Edge Cases  

| Issue | Fix |
|-------|-----|
| Using `Integer.parseInt` on `"01"` | Convert the digit directly (`coordinate[1] - '0'`) to avoid leading‑zero pitfalls. |
| Forgetting that `'0'` is ASCII 48 | Subtract `'0'` to get the numeric value. |
| Off‑by‑one errors when treating the letter as index | Use ASCII code directly; no mapping array needed. |
| Misinterpreting parity (even=black vs. even=white) | Either convention works as long as both squares use the same rule. |

---

<a name="extensions"></a>
## 7. Extending the Idea  

1. **Multiple Squares** – Extend to a list of coordinates and check if all share the same color.  
2. **Color Mapping** – Return `"black"` or `"white"` instead of a boolean.  
3. **Board Variants** – Support larger boards (`a1`‑`z100`) with the same parity trick.  

---

<a name="conclusion"></a>
## 8. Conclusion & Job‑Search Tips  

*The 3‑line solution showcases:*

- **Mathematical insight** (parity)  
- **Clean coding** (no extra data structures)  
- **Efficiency** (O(1) time & space)  

When preparing for coding interviews, highlight such tricks:

- Emphasize *why* the solution works, not just *how*.  
- Be ready to discuss *edge cases* (even/odd, ASCII vs. numeric).  
- Show the ability to write idiomatic code in multiple languages (Java, Python, C++).  

**SEO‑Friendly Hook**: “Learn the 3‑line Java/Python/C++ solution to LeetCode 3274 and boost your interview score.”

---

<a name="seo"></a>
## 9. Meta & SEO Checklist  

| SEO Element | Implementation |
|-------------|----------------|
| **Title** | “LeetCode 3274 – 3‑Line Java/Python/C++ Solution: Check if Two Chessboard Squares Have the Same Color” |
| **Meta Description** | “Solve LeetCode 3274 in just 3 lines of code. This guide covers Java, Python, and C++ implementations, a deep dive into the parity trick, and interview‑ready insights.” |
| **Keywords** | `leetcode 3274`, `chessboard color same`, `check two chessboard squares`, `java solution`, `python solution`, `c++ solution`, `parity trick`, `O(1) solution` |
| **Headers** | H1 (Title), H2 (Problem Recap, Intuition, etc.) |
| **Internal Links** | Link to related LeetCode topics: “Array”, “String”, “Math”. |
| **External Links** | Reference LeetCode problem URL and solution discussions. |
| **Alt Text for Images** | If including a chessboard diagram, alt: “Chessboard with a1, c3, h3 squares highlighted.” |

> *With a well‑structured article, clear code snippets, and targeted keywords, you’ll rank higher in search results and impress hiring managers looking for concise, elegant problem‑solving skills.*