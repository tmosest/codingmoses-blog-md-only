---
title: LeetCode 1865. Finding Pairs With a Certain Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 1865 – Finding Pairs With a Certain Sum  
**Medium | LeetCode | Data‑Structure Design**

> *“I want to show recruiters that I can turn a tricky problem into clean, reusable code.”*  
> Here’s a full‑stack walk‑through:  
> * Problem & constraints  
> * Elegant solution (hash‑based frequency map)  
> * Java / Python / C++ implementations  
> * A *blog‑ready* article that highlights the **good, the bad, and the ugly** – perfect for LinkedIn, Medium, or your personal portfolio.  

---

### 1. Problem Recap

| ✅ | **Task** |
|---|----------|
| **Create a data‑structure** that supports two operations on two integer arrays `nums1` and `nums2`: |
| 1. `add(index, val)` – add `val` to `nums2[index]`. |
| 2. `count(tot)` – return the number of pairs `(i, j)` with `nums1[i] + nums2[j] == tot`. |

| 📌 | Constraints |
|---|--------------|
| `nums1.length ≤ 1 000` |
| `nums2.length ≤ 10⁵` |
| `1 ≤ nums1[i], nums2[i] ≤ 10⁵` (except `nums1[i]` can be up to `10⁹`) |
| `add` & `count` called ≤ 1 000 times each. |
| `0 ≤ index < nums2.length` |
| `1 ≤ val ≤ 10⁵` |
| `1 ≤ tot ≤ 10⁹` |

The goal is to keep each operation fast – ideally *O(1)* for `add` and *O(n₁)* for `count`, where *n₁* = `nums1.length`.

---

### 2. Intuition

1. **Static vs. Dynamic**  
   *`nums1`* never changes – we can iterate over it on each `count`.  
   *`nums2`* changes – we need a quick way to know how many times a particular value occurs.

2. **Frequency Map**  
   Keep a hash‑map (`value → count`) for `nums2`.  
   - **add**: decrement the old value’s count, add the new value’s count.  
   - **count**: for every `x` in `nums1`, look up `tot - x` in the map and add its frequency.

3. **Why It Works**  
   Each lookup in the hash‑map is *O(1)* on average.  
   `count` touches every element of `nums1` once → *O(n₁)*, which is fine because `n₁ ≤ 1 000`.  
   `add` is *O(1)*.  
   Memory: store frequencies of at most `10⁵` different numbers → *O(n₂)*.

---

### 3. Code – 3 Languages

#### 3.1 Java

```java
import java.util.HashMap;
import java.util.Map;

public class FindSumPairs {
    private final int[] nums1;
    private final int[] nums2;
    private final Map<Integer, Integer> freq;   // frequency map of nums2

    public FindSumPairs(int[] nums1, int[] nums2) {
        this.nums1 = nums1;
        this.nums2 = nums2;
        this.freq = new HashMap<>();

        for (int val : nums2) {
            freq.put(val, freq.getOrDefault(val, 0) + 1);
        }
    }

    /** Add +val to nums2[index] and update the frequency map. */
    public void add(int index, int val) {
        int old = nums2[index];
        freq.put(old, freq.get(old) - 1);          // decrement old count

        nums2[index] += val;
        int newVal = nums2[index];
        freq.put(newVal, freq.getOrDefault(newVal, 0) + 1); // increment new count
    }

    /** Return the number of pairs (i, j) where nums1[i] + nums2[j] == tot. */
    public int count(int tot) {
        int ans = 0;
        for (int a : nums1) {
            int need = tot - a;
            ans += freq.getOrDefault(need, 0);
        }
        return ans;
    }
}
```

#### 3.2 Python

```python
from collections import Counter
from typing import List

class FindSumPairs:
    def __init__(self, nums1: List[int], nums2: List[int]):
        self.nums1 = nums1
        self.nums2 = nums2
        self.freq = Counter(nums2)          # frequency map of nums2

    def add(self, index: int, val: int) -> None:
        old = self.nums2[index]
        self.freq[old] -= 1                 # decrement old value

        self.nums2[index] += val
        new_val = self.nums2[index]
        self.freq[new_val] += 1             # increment new value

    def count(self, tot: int) -> int:
        return sum(self.freq[tot - a] for a in self.nums1)
```

#### 3.3 C++

```cpp
#include <vector>
#include <unordered_map>

class FindSumPairs {
public:
    FindSumPairs(std::vector<int> nums1, std::vector<int> nums2)
        : nums1(std::move(nums1)), nums2(std::move(nums2)) {
        for (int v : this->nums2) freq[v]++;   // build frequency map
    }

    void add(int index, int val) {
        int old = nums2[index];
        --freq[old];                          // decrement old count

        nums2[index] += val;
        int newVal = nums2[index];
        ++freq[newVal];                       // increment new count
    }

    int count(int tot) {
        int ans = 0;
        for (int a : nums1) {
            int need = tot - a;
            auto it = freq.find(need);
            if (it != freq.end()) ans += it->second;
        }
        return ans;
    }

private:
    std::vector<int> nums1;
    std::vector<int> nums2;
    std::unordered_map<int, int> freq;       // value → count
};
```

All three solutions share the same O(1) *add* and O(`nums1.length`) *count* time, making them ideal for the LeetCode limits.

---

### 4. Blog Article – “The Good, The Bad & The Ugly”

> **Target audience**: recruiters, interviewers, and peers on LinkedIn/Medium.  
> **SEO‑friendly keywords**: *LeetCode 1865*, *FindSumPairs*, *data‑structure interview*, *hash map solution*, *O(1) add*, *job interview coding*, *Python Counter*, *C++ unordered_map*, *Java HashMap*.

---

#### 4.1 Title & Meta

```
<title>LeetCode 1865 – Design a Frequency‑Based FindSumPairs Data Structure | Interview Success</title>
<meta name="description" content="Master LeetCode 1865 (FindSumPairs). Learn a clean Java, Python, C++ solution, complexity analysis, and interview‑ready tips. Perfect for coding interviews and portfolio showcase.">
```

#### 4.2 Table of Contents (for readability)

1. [The Problem](#the-problem)  
2. [Why the Hash‑Map Trick Is the Secret Sauce](#hash-map-sauce)  
3. [Algorithm & Complexity](#complexity)  
4. [Full Code](#code)  
5. [The Good, The Bad & The Ugly](#pros-cons-ugly)  
6. [Interview Tips](#interview-tips)  
7. [Conclusion & Next Steps](#conclusion)

---

#### 4.3 The Good

| ✅ | What I Loved |
|---|--------------|
| **Simplicity** – one frequency map + two straightforward loops. |
| **Performance** – *O(1)* `add`, *O(n₁)* `count`. |
| **Language Agnostic** – the same idea works in Java, Python, C++, Rust, Go, etc. |
| **Memory Footprint** – only counts of `nums2` are stored, not the entire array. |
| **Reusability** – can be adapted to other “dynamic‑frequency + static‑array” problems. |

#### 4.4 The Bad

| ⚠️ | Potential Pitfalls |
|---|--------------------|
| **Large Frequency Map** – if `nums2` contains many unique values, the unordered_map/Counter may use up to ~200 KB per unique entry, which is still fine for `10⁵` numbers but worth noting for truly massive inputs. |
| **Zero‑Count Keys** – we decrement counts but never delete keys when the count hits zero. In a real‑world system you might want to prune to keep the map lean, but the extra overhead is negligible given the constraints. |
| **Repeated `count` Calls** – iterating over `nums1` on each call is *O(n₁)*. If `nums1` were larger, you’d need to pre‑compute something else (e.g., a 2‑D prefix sum). The problem limits keep it acceptable, but the interviewee should mention this trade‑off. |

#### 4.5 The Ugly

| 👿 | Edge Cases & Hidden Gotchas |
|---|-----------------------------|
| **Integer Overflow** – `tot` can be up to `10⁹`. Adding two ints in C++/Java can overflow if you use 32‑bit signed types; using `long long` or `long` for the intermediate sum is safer in languages that don’t have arbitrary‑precision ints (JavaScript, C). In this problem the sums stay within 32‑bit, but always keep an eye on the limits. |
| **Multiple Updates to the Same Index** – If `add` is called repeatedly on the same index, make sure the frequency map stays in sync with the underlying array. A common bug is to forget to update the array after decrementing the old count. |
| **Negative Numbers?** – The official constraints forbid negative values, but if you tweak the problem to allow them, the algorithm still works because the hash‑map can store negative keys. Just remember to use a *signed* integer type. |

---

#### 4.6 Interview‑Ready Pseudocode

```text
class FindSumPairs:
    freq = map of nums2 values

    init(nums1, nums2):
        build freq from nums2

    add(idx, val):
        freq[nums2[idx]]--
        nums2[idx] += val
        freq[nums2[idx]]++

    count(tot):
        res = 0
        for x in nums1:
            res += freq[tot - x]
        return res
```

> **Why it’s a “show‑off” for recruiters**  
> * The interviewer can instantly see you used a *hash map* to turn a dynamic array into a static lookup table.  
> * You explained the *amortized* *O(1)* update, the *O(n₁)* query, and the space trade‑off.  
> * You showcased clean, testable methods—ready for a production‑grade API.

---

### 5. Putting It Into Your Portfolio

1. **Embed the Code** – copy the Java, Python, and C++ snippets into a README or a GitHub Gist.  
2. **Add a README** that includes:  
   - Problem statement (copy from LeetCode)  
   - Test harness (unit tests for all three languages)  
   - “Run this to see the expected output” block.  
3. **Link It** – in your LinkedIn article or on your personal site, add a link: `https://github.com/yourusername/leetcode-1865-findsumpairs`.  
4. **Share on Medium** – publish the blog with the headings above. Tag with `#LeetCode`, `#CodingInterview`, `#DataStructures`, `#AlgorithmDesign`.

---

### 6. Conclusion

- **LeetCode 1865** is a classic “design‑a‑data‑structure” problem that tests your ability to balance **time‑complexity** and **memory‑efficiency**.  
- The **hash‑map frequency trick** is the canonical solution: O(1) updates, O(n₁) queries.  
- Implementations in **Java, Python, and C++** are straightforward and demonstrate cross‑language competence.  
- The **blog article** above is SEO‑optimized and ready to share—complete with keywords that recruiters love (`LeetCode 1865`, `FindSumPairs`, `Java HashMap`, `Python Counter`, `C++ unordered_map`, `job interview coding`).  

💡 **Next step:**  
Add your own unit tests, push the repo to GitHub, and start a LinkedIn post: *“Solved LeetCode 1865 in Java, Python, and C++ – learn how I designed an efficient data‑structure!”*  

Happy coding, and best of luck landing that next interview! 🚀