---
title: LeetCode 2525. Categorize Box According to Criteria - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 2525 – “Categorize Box According to Criteria”  
**A quick‑look, complete solution in Java, Python & C++ (O(1) time, O(1) space)**  

---

### 1️⃣ Problem Overview  

- **Input**: 4 integers – `length`, `width`, `height`, `mass`  
- **Output**: `String` representing the category:  
  - `"Bulky"` – any dimension ≥ 10 000 **or** volume ≥ 1 000 000 000  
  - `"Heavy"` – mass ≥ 100  
  - `"Both"` – satisfies both conditions  
  - `"Neither"` – satisfies none  
- **Constraints**  
  - `1 ≤ length, width, height ≤ 10⁵`  
  - `1 ≤ mass ≤ 10³`  
  - The volume can be as large as 10¹⁵ → 64‑bit integer required  

> **Goal**: Return the correct category in constant time and memory.

---

### 2️⃣ Core Idea  

All checks are simple comparisons – no loops, no recursion.  
The only subtlety is avoiding integer overflow when computing the volume.  
We cast to `long` (Java), `long long` (C++), or use Python’s unbounded integers.  

```text
bulky  = (dim ≥ 10_000)  OR  (volume ≥ 1_000_000_000)
heavy  = mass ≥ 100
```

Return the result based on the four possible boolean combinations.

---

### 3️⃣ Implementation

Below are clean, idiomatic solutions for **Java**, **Python**, and **C++**.

---

#### 3.1 Java

```java
/**
 * LeetCode 2525 – Categorize Box According to Criteria
 * Java 17
 */
public class Solution {
    public String categorizeBox(int length, int width, int height, int mass) {
        // Use long to prevent overflow of length*width*height
        long volume = 1L * length * width * height;

        boolean bulky  = length >= 10_000 || width >= 10_000 || height >= 10_000
                         || volume >= 1_000_000_000L;
        boolean heavy  = mass >= 100;

        if (bulky && heavy) return "Both";
        if (bulky)          return "Bulky";
        if (heavy)          return "Heavy";
        return "Neither";
    }
}
```

*Complexity*: `O(1)` time, `O(1)` extra space.

---

#### 3.2 Python

```python
# LeetCode 2525 – Categorize Box According to Criteria
# Python 3

def categorizeBox(length: int, width: int, height: int, mass: int) -> str:
    volume = length * width * height  # Python ints are unbounded

    bulky = length >= 10_000 or width >= 10_000 or height >= 10_000 \
            or volume >= 1_000_000_000
    heavy = mass >= 100

    if bulky and heavy:
        return "Both"
    if bulky:
        return "Bulky"
    if heavy:
        return "Heavy"
    return "Neither"
```

*Complexity*: `O(1)` time, `O(1)` space.

---

#### 3.3 C++

```cpp
// LeetCode 2525 – Categorize Box According to Criteria
// C++17

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string categorizeBox(int length, int width, int height, int mass) {
        // 64‑bit multiplication to avoid overflow
        long long volume = 1LL * length * width * height;

        bool bulky = length >= 10000 || width >= 10000 || height >= 10000
                     || volume >= 1000000000LL;
        bool heavy = mass >= 100;

        if (bulky && heavy) return "Both";
        if (bulky)          return "Bulky";
        if (heavy)          return "Heavy";
        return "Neither";
    }
};
```

*Complexity*: `O(1)` time, `O(1)` auxiliary space.

---

### 4️⃣ The Good, the Bad, and the Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | One pass, no loops. | The logic is trivial but easy to read. | Original solutions sometimes used multiple temporary strings (`box`, `box1`), making the flow harder to follow. |
| **Safety** | Uses 64‑bit arithmetic to avoid overflow. | None. | Forgetting the cast can cause incorrect results on edge cases. |
| **Readability** | Clear boolean flags (`bulky`, `heavy`). | Requires a mental model of four boolean combos. | Nested `if` chains or overly verbose variable names can obfuscate intent. |
| **Performance** | `O(1)` time & space. | None. | None. |
| **Extensibility** | Easy to add more categories. | None. | Over‑engineering (e.g., creating a struct for each condition) would bloat the code. |

> **Bottom line** – Keep it short, use `bool` flags, and cast to `long`/`long long` to protect against overflow. That’s all you need for a production‑ready interview answer.

---

### 5️⃣ Why This Matters for Your Job Hunt  

- **LeetCode Mastery** – Demonstrates quick reasoning and correct use of data types.  
- **Interview Readiness** – Shows you can produce clean, O(1) solutions.  
- **Cross‑Language Proficiency** – Solved in Java, Python, and C++ → ideal for tech stacks that use any of these.  
- **Explainability** – The blog post itself is a great talking‑point in behavioral interviews: “Here’s a problem I solved, and I’ll walk through the good, the bad, and the ugly.”

---

### 6️⃣ Final Checklist (Before Your Next Interview)

- ✅ Confirm the constraints to decide the integer size.  
- ✅ Use boolean flags instead of multiple strings.  
- ✅ Write a single return block (or a clear chain of `if`/`else`).  
- ✅ Add comments for clarity (especially for edge‑case handling).  
- ✅ Test with boundary values:  
  - `length = 10_000, width = 1, height = 1, mass = 99` → `"Bulky"`  
  - `volume = 1_000_000_000, mass = 100` → `"Both"`  
  - `mass = 99, all dimensions < 10_000` → `"Neither"`  

---

## 🚀 Ready to Land That Job?

Share this solution on your GitHub, write a blog post (like the one above), and showcase the clean, efficient code in your next coding interview.  
Happy coding and best of luck on your career journey!