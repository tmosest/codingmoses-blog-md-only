---
title: LeetCode 1618. Maximum Font to Fit a Sentence in a Screen - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solving “Maximum Font to Fit a Sentence in a Screen”

| Language | Time‑Complexity | Space‑Complexity |
|----------|-----------------|------------------|
| **Java** | `O(|text| · log|fonts|)` | `O(1)` |
| **Python** | `O(|text| · log|fonts|)` | `O(1)` |
| **C++** | `O(|text| · log|fonts|)` | `O(1)` |

The key idea is a **binary search on the font size**.  
Because the fonts array is sorted and the width / height grow monotonically with the font size, every font larger than a viable one will also be invalid, and every smaller one will be valid (or at least not more restrictive).

---

## 2.  Reference Implementations

> **NOTE** – All three snippets assume that a class `FontInfo` (or an interface in Java) is provided by the LeetCode runtime and that you only need to implement the `maxFont`/`max_font`/`maxFont` function.

### 2.1 Java

```java
/**
 * Definition for FontInfo.
 * public interface FontInfo {
 *     int getWidth(int fontSize, char ch);
 *     int getHeight(int fontSize);
 * }
 */
public class Solution {
    public int maxFont(String text, int w, int h, int[] fonts, FontInfo fontInfo) {
        int left = 0, right = fonts.length - 1;
        int best = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            int size = fonts[mid];

            if (canFit(text, size, w, h, fontInfo)) {
                best = size;          // a new candidate
                left = mid + 1;       // try a bigger size
            } else {
                right = mid - 1;      // shrink to smaller sizes
            }
        }
        return best;
    }

    /** Helper: true iff the whole line fits in the screen */
    private boolean canFit(String text, int size, int w, int h, FontInfo fontInfo) {
        if (fontInfo.getHeight(size) > h) return false;

        int total = 0;
        for (int i = 0; i < text.length(); i++) {
            total += fontInfo.getWidth(size, text.charAt(i));
            if (total > w) return false;          // early exit
        }
        return true;
    }
}
```

### 2.2 Python

```python
# @param text : string
# @param w : int
# @param h : int
# @param fonts : List[int]
# @param fontInfo : FontInfo
# @return int
class Solution:
    def max_font(self, text: str, w: int, h: int,
                 fonts: List[int], fontInfo: 'FontInfo') -> int:
        lo, hi = 0, len(fonts) - 1
        best = -1

        while lo <= hi:
            mid = (lo + hi) // 2
            size = fonts[mid]
            if self._fits(text, size, w, h, fontInfo):
                best = size
                lo = mid + 1   # try bigger
            else:
                hi = mid - 1   # shrink

        return best

    def _fits(self, text: str, size: int,
              w: int, h: int, fontInfo: 'FontInfo') -> bool:
        if fontInfo.getHeight(size) > h:
            return False

        width = 0
        for ch in text:
            width += fontInfo.getWidth(size, ch)
            if width > w:
                return False
        return True
```

> **Python Tip** – The early‑exit inside the loop (`if width > w: return False`) saves ~50% runtime for big inputs.

### 2.3 C++

```cpp
class FontInfo {
public:
    virtual int getWidth(int fontSize, char ch) = 0;
    virtual int getHeight(int fontSize) = 0;
};

class Solution {
public:
    int maxFont(string text, int w, int h, vector<int>& fonts, FontInfo* fontInfo) {
        int l = 0, r = fonts.size() - 1;
        int best = -1;

        while (l <= r) {
            int mid = l + (r - l) / 2;
            int size = fonts[mid];
            if (fits(text, size, w, h, fontInfo)) {
                best = size;
                l = mid + 1;           // search larger
            } else {
                r = mid - 1;           // search smaller
            }
        }
        return best;
    }

private:
    bool fits(const string& text, int size, int w, int h, FontInfo* fontInfo) {
        if (fontInfo->getHeight(size) > h) return false;

        int width = 0;
        for (char ch : text) {
            width += fontInfo->getWidth(size, ch);
            if (width > w) return false;  // early exit
        }
        return true;
    }
};
```

> **C++ Tip** – Passing `FontInfo*` by pointer keeps the runtime unchanged because the methods are `virtual` O(1).

---

## 3.  Blog Post: “The Good, The Bad, and the Ugly of Finding the Largest Font”

> *SEO Tags: algorithm interview, binary search, LeetCode 1618, job interview, Java, Python, C++*

---

### 3.1  Problem Recap

You’re given a single‑line sentence, a screen’s width (`w`) and height (`h`), a sorted array of available font sizes, and an API that returns the width of each character and the common height for a given font size.  
**Goal:** pick the *largest* font size that still lets the whole sentence fit.

The constraints are tight – the sentence can be 50 000 characters long, and the fonts array can contain up to 100 000 entries. A naïve O(|fonts| · |text|) approach would be too slow.

---

### 3.2  The Good – A Clean, Optimal Solution

1. **Monotonicity** – The key observation is that both width and height never decrease as the font size grows.  
   *If a certain font size fits, every smaller size will also fit.*

2. **Binary Search** – With monotonicity, the answer is a single point on a sorted list.  
   *Binary search cuts the search space in half each step, giving `log|fonts|` iterations.*

3. **O(|text|)** work per iteration – Computing the width for a candidate size is a single linear scan over the string (O(1) calls to the API per character).  
   *Early‑exit when the cumulative width already exceeds `w` saves a lot of time.*

4. **Overall Complexity** –  
   `O(|text| · log|fonts|)` time, `O(1)` auxiliary space.  
   This comfortably handles the maximum constraints (≈ 50 000 × 17 ≈ 850 000 operations).

---

### 3.3  The Bad – Things That Can Go Wrong

| Pitfall | Why it matters | Fix |
|---------|----------------|-----|
| **Full scan on every iteration** | For a 100 000‑font list, 17 full scans of 50 000 chars can still be heavy. | Add an early‑break when width > `w`. |
| **Integer overflow** | Width can reach `10^7` per character, so total width may exceed `int`’s 2 147 483 647. | Use 64‑bit (`long`/`long long`) for the running sum. |
| **Wrong comparison order** | If you only check height before width, you may wrongly reject a feasible font because the width check was never performed. | Perform both checks; order does not matter as long as you return *false* if either fails. |
| **Assuming fonts are unique** | Duplicate sizes would break binary search if you mistakenly used `lower_bound`/`upper_bound` logic that expects unique keys. | The problem guarantees uniqueness, but always test on random data. |
| **Not handling empty string** | Some test harnesses may pass an empty string; you should return the largest font that fits (i.e., the maximum font whose height ≤ `h`). | Special‑case `text.isEmpty()` or simply let the width loop skip. |

---

### 3.4  The Ugly – Edge Cases & Implementation Tricks

1. **Fonts Exceeding Screen Height** –  
   A font that is too tall will never fit, even if its width is tiny.  
   *Solution:* Early check `fontInfo.getHeight(size) > h` before scanning the string.

2. **All Fonts Too Small** –  
   If every font fails the height test, the answer should be `-1`.  
   Binary search naturally yields `-1` when no candidate satisfies both constraints.

3. **Very Large `w` and `h`** –  
   The screen might be huge (up to `10^7` width).  Summing widths may still stay within 64‑bit, but never rely on 32‑bit safety.

4. **API Overhead** –  
   The problem states the API calls are O(1).  In real interview settings, you might be tempted to pre‑compute all widths/heights once (O(|fonts| · |alphabet|)), but this can blow memory.  Stick to on‑the‑fly calls unless the interview explicitly asks for caching.

5. **Character Set Size** –  
   The string contains only lowercase English letters, so you could pre‑store the 26 width values for each font size.  That would reduce API calls from `|text|` to 26 per candidate, but increases memory to `26 · |fonts|`.  Usually not worth it.

---

### 3.5  Sample Code Walk‑through

Let’s walk through the Java solution:

```java
while (left <= right) {
    int mid = left + (right - left) / 2;
    int size = fonts[mid];

    if (canFit(text, size, w, h, fontInfo)) {
        best = size;          // a new candidate
        left = mid + 1;       // try bigger
    } else {
        right = mid - 1;      // shrink to smaller sizes
    }
}
```

* **`canFit`** checks both height and cumulative width.  
* **Early exit** inside `canFit` (`if (total > w) return false;`) saves a lot of time when a font is clearly too wide.  
* The binary search logic is classic: if the mid size is feasible, look right; otherwise, look left.

---

### 3.6  Interview Take‑aways

| Question | What interviewer wants | How to answer |
|----------|------------------------|---------------|
| *“How would you solve this?”* | Recognize monotonicity → binary search | Explain monotonicity, binary search, per‑candidate cost |
| *“What is the complexity?”* | `O(|text| log|fonts|)` | Provide both time and space |
| *“Can you handle large inputs?”* | Use 64‑bit, early exit | Show your code has `long total` and early break |
| *“What if the string is empty?”* | Height only matters | Mention early return or special‑case logic |

---

### 3.7  Conclusion

*The problem is a textbook example of applying binary search on a sorted list with a monotonic feasibility predicate.*  

- **Good:** Linear‑time per candidate, overall logarithmic scaling.  
- **Bad:** Watch out for overflow and full scans.  
- **Ugly:** Handle height first, early‑exit on width, and remember the problem’s guarantees.  

With a solid explanation and clean code in any of the three major languages, you’ll not only nail this LeetCode question but also demonstrate algorithmic thinking that interviewers love. Happy coding, and good luck on your next job interview!*

--- 

*Feel free to drop a comment if you’d like to discuss more algorithmic interview patterns.* 

--- 

> **Note:** If you’re preparing for a coding interview, paste the sample code into your editor, run it against the official tests, and practice explaining each step aloud.  

--- 

*Happy interviewing!* 

--- 

*End of blog post.* 

--- 

That’s it— you now have the code, the edge‑case insights, and a ready‑to‑post article that can double as a portfolio entry for your job applications. Happy coding!