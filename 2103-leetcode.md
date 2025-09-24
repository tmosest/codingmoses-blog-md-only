---
title: LeetCode 2103. Rings and Rods - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 🎯 LeetCode 2103 – *Rings and Rods*  
## A Deep Dive into the Problem, the Code, and the Interview Mindset  

> **Keywords**: LeetCode 2103, Rings and Rods, coding interview, Java, Python, C++, bitmask, algorithm, job interview, data structures, time‑complexity, space‑complexity, interview preparation

---

## 1. Problem Recap

You are given a string `rings` of length `2 * n`.  
Each **pair of characters** describes a ring:

| Position in the pair | Meaning |
|----------------------|---------|
| 0 (even index)       | Color – `'R'`, `'G'`, or `'B'` |
| 1 (odd index)        | Rod – a digit `'0'` … `'9'` |

The task: **Count how many rods contain at least one ring of each of the three colors.**

---

## 2. Why This Problem Is Interview‑Friendly

| ✅  | 💡 |
|----|----|
| Simple constraints (≤ 100 rings) | Encourages a quick‑thought O(n) solution |
| Two‑character encoding | Demonstrates string parsing skills |
| Bitwise manipulation | Showcases knowledge of low‑level tricks |
| 10 fixed rods | Encourages constant‑space approaches |

---

## 3. The “Good” – A Clean, Bit‑Mask Solution

### 3.1 Idea

- Use an array `masks[10]` – one element per rod.
- Each bit of an integer represents a color:  
  - `1` → Red  
  - `2` → Green  
  - `4` → Blue  
  - `7` (`1|2|4`) means *all three* colors are present.
- Iterate through `rings` two characters at a time, update the mask, then count how many masks equal `7`.

### 3.2 Code

#### Java

```java
class Solution {
    public int countPoints(String rings) {
        // 10 rods → 10 masks, each mask stores bits for R/G/B
        int[] masks = new int[10];

        for (int i = 0; i < rings.length(); i += 2) {
            char color = rings.charAt(i);
            int rod = rings.charAt(i + 1) - '0';

            // set the bit corresponding to the color
            int bit = 0;
            switch (color) {
                case 'R': bit = 1; break;
                case 'G': bit = 2; break;
                case 'B': bit = 4; break;
            }
            masks[rod] |= bit;          // OR to keep all seen colors
        }

        int count = 0;
        for (int mask : masks) {
            if (mask == 7) count++;     // 7 = 0b111 → all colors present
        }
        return count;
    }
}
```

#### Python

```python
class Solution:
    def countPoints(self, rings: str) -> int:
        masks = [0] * 10  # 10 rods

        for i in range(0, len(rings), 2):
            color = rings[i]
            rod = int(rings[i + 1])

            if color == 'R':
                masks[rod] |= 1
            elif color == 'G':
                masks[rod] |= 2
            else:  # 'B'
                masks[rod] |= 4

        return sum(1 for m in masks if m == 7)
```

#### C++

```cpp
class Solution {
public:
    int countPoints(string rings) {
        vector<int> masks(10, 0);

        for (int i = 0; i < rings.size(); i += 2) {
            char color = rings[i];
            int rod = rings[i + 1] - '0';

            int bit = 0;
            if (color == 'R') bit = 1;
            else if (color == 'G') bit = 2;
            else bit = 4;           // 'B'

            masks[rod] |= bit;
        }

        int count = 0;
        for (int m : masks)
            if (m == 7) ++count;
        return count;
    }
};
```

### 3.3 Complexity

| Metric | Java/Python/C++ |
|--------|-----------------|
| **Time** | **O(n)** – single pass over the string. |
| **Space** | **O(1)** – fixed 10‑int array; constant additional memory. |

---

## 4. The “Bad” – Over‑Thinking or Misreading

| Pitfall | Explanation |
|---------|-------------|
| **Using a HashMap of Sets** | A `Map<Integer, Set<Character>>` works but incurs log‑time insertions and memory overhead. |
| **Storing Full Strings** | Keeping the whole pair `"R3"` per rod leads to unnecessary string allocations. |
| **Double Counting** | Forgetting that a rod might receive multiple rings of the same color – counting unique colors is key. |

---

## 5. The “Ugly” – Common Mistakes

1. **Indexing Off‑by‑One**  
   Forgetting that the rod digit is a character, not an integer. Always subtract `'0'`.

2. **Wrong Bit Mapping**  
   Mixing up `1`, `2`, `4` leads to masks like `6` (`0b110`) – you’ll never hit `7`.

3. **Loop Bound Error**  
   Using `i < rings.length() - 1` instead of `i < rings.length()` with step `2` can skip the last pair.

4. **Ignoring Constraints**  
   Assuming more than 10 rods or non‑RGB colors will break your logic.

---

## 6. A Step‑by‑Step Execution Example

| `rings` | Iteration | `color` | `rod` | Bit | `masks` after update |
|---------|-----------|---------|-------|-----|----------------------|
| `B0R0G0R9R0B0G0` | 0 | `B` | 0 | 4 | `[4,0,0,0,0,0,0,0,0,0]` |
|  | 2 | `R` | 0 | 1 | `[5,0,0,0,0,0,0,0,0,0]` |
|  | 4 | `G` | 0 | 2 | `[7,0,0,0,0,0,0,0,0,0]` |
|  | 6 | `R` | 9 | 1 | `[7,0,0,0,0,0,0,0,0,1]` |
|  | 8 | `0` | 0 | 4 | `[7,0,0,0,0,0,0,0,0,1]` |
|  | 10 | `G` | 0 | 2 | `[7,0,0,0,0,0,0,0,0,1]` |
|  | 12 | `0` | 9 | 1 | `[7,0,0,0,0,0,0,0,0,7]` |

Final masks: rod 0 → `7`, rod 9 → `7`.  
**Answer = 2.**

---

## 7. Interview‑Style Questions to Prove Your Depth

1. **“Can you adapt the solution if the rods were 1000 instead of 10?”**  
   *Hint*: Use a `int[1000]` array – still O(1) space relative to input size.

2. **“What if we had 5 colors?”**  
   You’d need 5 bits (`1,2,4,8,16`) and check for `31`.

3. **“Could you implement this without bit operations?”**  
   Show a `HashSet`‑based solution, but discuss trade‑offs.

4. **“Explain why a single integer per rod is sufficient.”**  
   Talk about *bitmask representation* and *constant‑space guarantees*.

---

## 7. How This Blog Helps Your Job Hunt

1. **SEO Boost** – The article contains repeated target keywords that recruiters search for:  
   *“LeetCode Rings and Rods Java solution”*, *“coding interview Python bitmask”*, *“C++ interview problems”*.

2. **Structured Learning** – By reading the “good”, “bad”, and “ugly” sections you’ll see both the optimal strategy and the common traps, giving you confidence in a live interview.

3. **Show‑Off on Your Portfolio** – Embed this post on your GitHub README or personal blog. Recruiters love to see real‑world solutions explained clearly.

4. **LinkedIn / Medium Post** – Share this article on LinkedIn or Medium with the title **“LeetCode 2103 – Rings & Rods: Java/Python/C++ Code & Interview Tips”** to increase visibility among hiring managers.

---

## 7. TL;DR – Quick Code Snippets

```java
// Java
new Solution().countPoints("R0G0B0R0G0B0");
```

```python
# Python
Solution().countPoints("R0G0B0R0G0B0")
```

```cpp
// C++
Solution().countPoints("R0G0B0R0G0B0");
```

---

## 8. Final Thoughts

- **Read the constraints** carefully – 10 rods is a hard limit that saves space.
- **Prefer constant‑size arrays** over dynamic containers when the domain is fixed.
- **Bitwise tricks** are a staple in coding interviews; they demonstrate mastery over binary representation and efficiency.
- **Explain your logic** during the interview. Walk through a small example; it shows the interviewer you’re thinking clearly.

---

### 🚀 Takeaway

Mastering *Rings and Rods* is a quick win in your interview arsenal. By showcasing the optimal bit‑mask approach in Java, Python, and C++, you demonstrate not only coding skill but also the analytical mindset that recruiters crave. Post this solution, add a short video walkthrough, and you’ll be a strong candidate for any backend or system‑design interview.

Happy coding! 🌟

---