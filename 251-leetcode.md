---
title: LeetCode 251. Flatten 2D Vector - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem Recap  
**Flatten 2‑D Vector – LeetCode 251**  

> Design an iterator that flattens a 2‑D vector.  
> The iterator must support the two operations  
> `next()` – return the next element,  
> `hasNext()` – return whether more elements remain.  

The input vector can be empty, may contain empty sub‑arrays, and has up to `200 × 500` elements. Up to `10⁵` calls to `next()` and `hasNext()` will be made.

---

## 2.  Why this is a “nice” interview question  
* **Core concepts** – iterator design, pointer manipulation, O(1) amortised operations.  
* **Edge cases** – empty rows, varying row lengths, large data sets.  
* **Follow‑up** – implement the iterator **without any extra data structures** (pure iterator pattern).  

Answering this question clearly demonstrates your ability to work with iterators, keep state correctly, and optimise for time & space.

---

## 3.  The “Two‑Pointer” (Pure Iterator) Solution  

The simplest and most efficient approach keeps **two indices**:

| variable | meaning | invariant |
|---------|--------|------------|
| `row`   | index of current row | `0 ≤ row < vec.size()` or `row == vec.size()` if finished |
| `col`   | index inside current row | `0 ≤ col < vec[row].size()` or `col == vec[row].size()` if finished |

The algorithm:

1. **Constructor** – initialise `row = 0`, `col = 0`.  
   Skip over empty rows so that the iterator starts at the first real element.
2. **hasNext()** – returns `true` iff `row < vec.size()`.  
   (If we’re past the last row we’re done.)
3. **next()** –  
   * Grab `vec[row][col]`.  
   * Increment `col`.  
   * If `col` reaches the end of the current row, move to the next non‑empty row (increment `row`, reset `col` to `0`).  

All operations run in **O(1) amortised time** and use **O(1) extra space**.

---

## 4.  Code Implementations  

### 4.1 Java  

```java
import java.util.List;

/**
 * Iterator that flattens a 2‑D integer vector.
 * Uses only two indices – pure iterator pattern.
 */
public class Vector2D {
    private final int[][] vec;   // the 2‑D array
    private int row = 0;         // current row
    private int col = 0;         // current column

    public Vector2D(int[][] vec) {
        this.vec = vec;
        advanceToNext();        // skip any leading empty rows
    }

    /** Return the next element. */
    public int next() {
        int value = vec[row][col];
        col++;                 // move to next column
        advanceToNext();       // skip to next valid element
        return value;
    }

    /** Are there more elements? */
    public boolean hasNext() {
        return row < vec.length;
    }

    /** Move row/col to the next non‑empty element. */
    private void advanceToNext() {
        // If we finished the current row, skip to next non‑empty row.
        while (row < vec.length && col >= vec[row].length) {
            row++;
            col = 0;
        }
    }
}
```

#### Usage

```java
int[][] data = {{1, 2}, {3}, {4}};
Vector2D vec = new Vector2D(data);
while (vec.hasNext()) {
    System.out.println(vec.next());
}
```

---

### 4.2 Python  

```python
class Vector2D:
    """
    Iterator that flattens a 2-D list.
    Uses two indices: self.row, self.col.
    """
    def __init__(self, vec):
        self.vec = vec
        self.row = 0
        self.col = 0
        self._skip_empty_rows()

    def next(self):
        """Return next element."""
        val = self.vec[self.row][self.col]
        self.col += 1
        self._skip_empty_rows()
        return val

    def hasNext(self):
        """Return True if more elements remain."""
        return self.row < len(self.vec)

    def _skip_empty_rows(self):
        """Advance to the next non‑empty element."""
        while self.row < len(self.vec) and self.col >= len(self.vec[self.row]):
            self.row += 1
            self.col = 0
```

#### Usage

```python
data = [[1, 2], [3], [4]]
vec = Vector2D(data)
while vec.hasNext():
    print(vec.next())
```

---

### 4.3 C++  

```cpp
#include <vector>

class Vector2D {
private:
    const std::vector<std::vector<int>>& vec;
    size_t row = 0, col = 0;

    // Move to the next valid element
    void advance() {
        while (row < vec.size() && col >= vec[row].size()) {
            ++row;
            col = 0;
        }
    }

public:
    Vector2D(const std::vector<std::vector<int>>& v) : vec(v) {
        advance();          // skip leading empty rows
    }

    int next() {
        int val = vec[row][col++];
        advance();
        return val;
    }

    bool hasNext() const {
        return row < vec.size();
    }
};
```

#### Usage

```cpp
std::vector<std::vector<int>> data = {{1,2}, {3}, {4}};
Vector2D v(data);
while (v.hasNext()) {
    std::cout << v.next() << " ";
}
```

---

## 5.  The Good, The Bad, and The Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | Two indices, no container copy – easy to reason about. | Requires careful handling of empty rows. | Forgetting to skip empty rows → crashes on `next()` or wrong output. |
| **Time Complexity** | O(1) amortised per call. | The constructor is O(1) – no pre‑processing cost. | Some interviewers might expect a *lazy* approach that also works in `O(1)` without scanning rows each time. |
| **Space Complexity** | O(1) additional space. | Must store the original reference; can't copy entire vector. | Using a queue or `deque` can waste memory for very large data. |
| **Follow‑Up (Iterator‑Only)** | Works only with raw indices – no helper iterators. | Java/C++ have built‑in iterators; Python uses indices too. | Over‑engineering: using multiple iterators, or `NoSuchElementException` handling can obfuscate the core logic. |

**Takeaway:** The two‑pointer approach is the most interview‑friendly “pure iterator” solution. It balances clarity, speed, and memory usage.

---

## 6.  SEO‑Optimised Blog Pointers  

To land that data‑structure interview job, sprinkle the article with these keywords:

* `Java iterator interview question`
* `flatten 2D array interview`
* `LeetCode 251 solution`
* `two pointer technique`
* `Python iterator design`
* `C++ vector iterator`
* `O(1) time iterator`

Use clear H1‑H3 headings (the ones in this article) and insert keyword‑rich meta‑description:

> “Learn the top interview‑ready solution for LeetCode 251 – Flatten 2‑D Vector. Get Java, Python, and C++ code examples, plus a step‑by‑step guide to implement a pure iterator that runs in O(1) time and O(1) space.”

Also add a **tags** section at the bottom for quick reference:

```text
Tags: Java, Python, C++, Iterator, LeetCode 251, Flatten 2D Vector, Interview
```

---

## 6.  Conclusion  

Flattening a 2‑D vector with an iterator is a classic interview problem that tests your grasp of stateful iteration, edge‑case handling, and algorithmic optimisation.  

The two‑pointer, pure‑iterator pattern is the most concise, time‑efficient, and space‑conservative solution. It is straightforward to implement in **Java, Python, and C++** – just tweak the index logic to the language’s idioms.

By mastering this question, you’ll impress interviewers who value clean, efficient code and who want to see you solve tough edge cases with minimal overhead. Good luck on your next interview!