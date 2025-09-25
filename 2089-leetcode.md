---
title: LeetCode 2089. Find Target Indices After Sorting Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – LeetCode 2089  
**Find Target Indices After Sorting Array**  
Given a 0‑indexed array `nums` and an integer `target`, return all indices `i` such that `nums[i] == target` **after** sorting `nums` in non‑decreasing order.  
If the target does not exist, return an empty list.  
All returned indices must be in ascending order.

**Constraints**

| Parameter | Range |
|-----------|-------|
| `nums.length` | 1 – 100 |
| `nums[i]`, `target` | 1 – 100 |

> The constraints are small, but we’ll still demonstrate a *canonical* `O(n log n)` solution (sort + linear scan) and a *tiny‑optimization* `O(n)` counting‑sort solution that leverages the limited value range.

---

## 2. Algorithm Overview

1. **Sort** the array (non‑decreasing order).  
2. **Scan** the sorted array once and record the positions where the value equals `target`.  
3. Return the list of collected indices.

### Complexity

| Step | Time | Space |
|------|------|-------|
| Sort | `O(n log n)` | `O(1)` (in‑place) |
| Scan | `O(n)` | `O(m)` (`m` = number of target occurrences) |
| Total | **`O(n log n)`** | **`O(m)`** |

> With `nums[i] ≤ 100`, a counting‑sort version would reduce the time to `O(n)` and use only `O(1)` extra space. We’ll show that below.

---

## 3. Reference Implementations

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public List<Integer> targetIndices(int[] nums, int target) {
        // 1. Sort the array in-place
        Arrays.sort(nums);

        // 2. Collect indices of target
        List<Integer> indices = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) {
                indices.add(i);
            }
        }
        return indices;
    }
}
```

**Counting‑Sort Variant (O(n) time, O(1) extra space)**

```java
public List<Integer> targetIndices(int[] nums, int target) {
    // Frequency array for values 1..100
    int[] freq = new int[101];
    for (int x : nums) freq[x]++;

    // Find the starting position of the target
    int start = 0;
    for (int v = 1; v < target; v++) start += freq[v];

    // Number of occurrences of target
    int count = freq[target];

    List<Integer> ans = new ArrayList<>();
    for (int i = 0; i < count; i++) ans.add(start + i);
    return ans;
}
```

---

### 3.2 Python

```python
class Solution:
    def targetIndices(self, nums: List[int], target: int) -> List[int]:
        nums.sort()                     # O(n log n)
        return [i for i, v in enumerate(nums) if v == target]
```

**Counting‑Sort Variant**

```python
class Solution:
    def targetIndices(self, nums: List[int], target: int) -> List[int]:
        freq = [0] * 101
        for v in nums: freq[v] += 1

        start = sum(freq[:target])      # sum of all values < target
        count = freq[target]

        return list(range(start, start + count))
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> targetIndices(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());          // O(n log n)
        vector<int> res;
        for (int i = 0; i < (int)nums.size(); ++i)
            if (nums[i] == target) res.push_back(i);
        return res;
    }
};
```

**Counting‑Sort Variant**

```cpp
class Solution {
public:
    vector<int> targetIndices(vector<int>& nums, int target) {
        int freq[101] = {0};
        for (int v : nums) ++freq[v];

        int start = 0;
        for (int v = 1; v < target; ++v) start += freq[v];
        int count = freq[target];

        vector<int> ans;
        for (int i = 0; i < count; ++i) ans.push_back(start + i);
        return ans;
    }
};
```

---

## 4. Blog Article – “The Good, The Bad, and the Ugly of LeetCode 2089”

### Title  
**“Cracking LeetCode 2089: The Good, The Bad, and The Ugly – A Job‑Interview Friendly Guide”**

### Meta‑Description  
Learn how to master LeetCode 2089 – “Find Target Indices After Sorting Array”. This SEO‑optimized article covers the simplest solution, subtle pitfalls, and interview‑ready tricks. Get code in Java, Python, and C++ and boost your coding interview chances.

---

### 1. Introduction

LeetCode is the playground where job‑seekers sharpen their algorithmic muscles. Among the *“Easy”* problems, 2089 – **Find Target Indices After Sorting Array** – is deceptively simple but packed with subtle teaching moments. In this article we dissect the problem, reveal the cleanest solution, expose common mistakes, and present advanced tricks that can impress hiring managers.

---

### 2. The Good – Why the Problem is a Great Interview Starter

| Feature | Why It Matters |
|---------|----------------|
| **Clear, deterministic** | You’re given an array and a target; the task is well‑defined and there is only one correct output. |
| **Minimal constraints** | `nums.length ≤ 100` – you can afford an `O(n log n)` solution without fear of timeouts. |
| **Single pass after sorting** | A classic pattern: sort, then linear scan. Most interviewees nail this. |
| **Easy to test** | Small inputs make it trivial to build unit tests and sanity‑check your code. |
| **Language‑agnostic** | The same logic translates to Java, Python, C++, Go, etc., which is great for coding interviews that allow any language. |

**Takeaway:** LeetCode 2089 is a perfect “warm‑up” problem that lets you focus on coding style and clarity, rather than algorithmic novelty.

---

### 3. The Bad – Common Pitfalls and Anti‑Patterns

1. **Ignoring the “sorted” requirement**  
   *Many candidates try to find indices in the *original* array.*  
   ```java
   // ❌ Wrong: uses indices of unsorted array
   for (int i = 0; i < nums.length; i++) {
       if (nums[i] == target) ans.add(i);
   }
   ```
   *Fix:* Sort first, then search.

2. **Using built‑in methods incorrectly**  
   In Java, `Arrays.binarySearch()` returns an arbitrary index if duplicates exist.  
   *If you rely on that, you’ll miss preceding occurrences.*  
   *Solution:* Perform a full linear scan after sorting.

3. **Excessive memory use**  
   Some solutions create a new array for the sorted copy, leading to `O(n)` extra memory when an in‑place sort suffices.

4. **Over‑engineering**  
   A 2‑line lambda solution in Python (`sorted(enumerate(nums), key=lambda x: x[1])`) is elegant but obscures the *sorting‑then‑scanning* pattern that interviewers expect.

5. **Not handling “no target” cases**  
   Returning an empty list is trivial, but forgetting to handle it gracefully can cause subtle bugs in production‑grade code.

---

### 4. The Ugly – What to Avoid in Real Interviews

| Bad Practice | Why it’s Ugly |
|--------------|---------------|
| **Hard‑coding the value range** | Assuming `nums[i] ≤ 100` and using a static frequency array may work for the test cases but is fragile for future changes. |
| **Blindly using `Collections.sort()` on a list of primitive ints** | Java’s `Collections.sort()` works on `List<Integer>`, but autoboxing each element adds overhead and makes the code unreadable. |
| **Ignoring time‑space trade‑offs** | A counting‑sort solution is faster but uses more lines and hides the algorithmic reasoning behind a “magic array” trick. |
| **Writing untested code** | A missing `assert` or a single `System.out.println` in the final submission signals a lack of professionalism. |

---

### 5. Interview‑Ready Tricks

| Trick | Implementation Snippet | Why it matters |
|-------|------------------------|----------------|
| **Counting‑sort for bounded values** | ```int[] freq = new int[101];``` | O(n) time, O(1) extra space. Shows you can optimize when constraints permit. |
| **One‑liner Java (but not recommended for interviews)** | ```return IntStream.range(0, nums.length).filter(i -> nums[i] == target).boxed().collect(Collectors.toList());``` | Demonstrates Java 8+ features, but keep it simple for a live interview. |
| **Python generator** | ```indices = [i for i, v in enumerate(sorted(nums)) if v == target]``` | Concise, readable, uses Python idioms. |
| **C++ STL** | ```sort(nums.begin(), nums.end()); for (int i = 0; i < nums.size(); ++i) if (nums[i] == target) res.push_back(i);``` | Shows you’re comfortable with STL containers. |

---

### 6. Code Showcase

> Below are clean, ready‑to‑paste implementations for Java, Python, and C++ that follow the canonical **sort‑then‑scan** pattern.  
> Copy, paste, and run in your favourite IDE or LeetCode sandbox.

```java
// Java
import java.util.*;

public class Solution {
    public List<Integer> targetIndices(int[] nums, int target) {
        Arrays.sort(nums);
        List<Integer> res = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) res.add(i);
        }
        return res;
    }
}
```

```python
# Python
class Solution:
    def targetIndices(self, nums: List[int], target: int) -> List[int]:
        nums.sort()
        return [i for i, v in enumerate(nums) if v == target]
```

```cpp
// C++
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> targetIndices(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        vector<int> ans;
        for (int i = 0; i < nums.size(); ++i)
            if (nums[i] == target) ans.push_back(i);
        return ans;
    }
};
```

---

### 7. Closing Thoughts – How This Problem Prepares You for the Next Round

* **Algorithmic clarity** – You learn to translate a problem statement into a concise plan (sort → scan).  
* **Language idioms** – Each language’s standard library (Java `Arrays.sort`, Python `list.sort`, C++ `std::sort`) is exercised.  
* **Edge‑case vigilance** – Handling empty results, duplicates, and small constraints is common in interviews.  
* **Optimization mindset** – Recognizing when a counting‑sort or prefix sum can beat a generic sort shows depth.  

Mastering LeetCode 2089 gives you a reliable “starter” that you can solve with style, and the skills you practice will carry over to harder problems like *“Find the Second Minimum Element”*, *“Find All Possible Results”*, and many array‑centric interview questions.

---

### 8. Call‑to‑Action

> **Want more interview‑ready solutions?**  
> 👉 Subscribe to our newsletter for weekly LeetCode problem walkthroughs.  
> 👉 Check out our GitHub repo for a complete collection of solutions in all major languages.  

Happy coding, and may your indices always come sorted!

---

### 9. Keywords (for SEO)

* LeetCode 2089
* Find Target Indices After Sorting Array
* Java solution LeetCode 2089
* Python solution LeetCode 2089
* C++ solution LeetCode 2089
* counting sort bounded values
* interview algorithm sorting then scanning
* LeetCode Easy problems
* coding interview tips 2024

---

### 10. References

1. LeetCode Official Problem 2089  
2. “Cracking the Coding Interview” – Gayle Laakmann McDowell  
3. Java 8 Stream API Tutorial  
4. Python 3.9 Documentation – `list.sort()`  
5. C++ STL `<algorithm>` – `std::sort`

---  

*End of article.*

---  

With this guide, you’re armed with the cleanest code, an understanding of common mistakes, and interview‑ready tricks that demonstrate both breadth and depth. Good luck cracking LeetCode 2089—and the next round of your job search!