---
title: LeetCode 2089. Find Target Indices After Sorting Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – “Find Target Indices After Sorting Array” (LeetCode 2089)

| Language | Code |
|----------|------|
| **Java** | <details><summary>Click to expand</summary><pre>import java.util.*;<br><br>public class Solution {<br>    public List<Integer> targetIndices(int[] nums, int target) {<br>        // 1️⃣ Sort the array – O(n log n)<br>        Arrays.sort(nums);<br>        List<Integer> ans = new ArrayList<>();<br>        // 2️⃣ Linear scan to collect all indices of `target` – O(n)<br>        for (int i = 0; i < nums.length; i++) {<br>            if (nums[i] == target) ans.add(i);<br>        }<br>        return ans;<br>    }<br>}<br></pre></details> |
| **Python** | <details><summary>Click to expand</summary><pre>class Solution:<br>    def targetIndices(self, nums: List[int], target: int) -> List[int]:<br>        # 1️⃣ Sort the list – O(n log n)<br>        nums.sort()<br>        # 2️⃣ Gather all indices where the element equals `target` – O(n)<br>        return [i for i, x in enumerate(nums) if x == target]<br></pre></details> |
| **C++** | <details><summary>Click to expand</summary><pre>#include &lt;bits/stdc++.h&gt;<br>using namespace std;<br><br>class Solution {<br>public:<br>    vector<int> targetIndices(vector&lt;int&gt;&amp; nums, int target) {<br>        // 1️⃣ Sort – O(n log n)<br>        sort(nums.begin(), nums.end());<br>        vector&lt;int&gt; ans;<br>        // 2️⃣ Linear pass – O(n)<br>        for (int i = 0; i &lt; (int)nums.size(); ++i) {<br>            if (nums[i] == target) ans.push_back(i);<br>        }\n        return ans;<br>    }<br>};<br></pre></details> |

> **Why this solution?**  
> *Sorting guarantees that the indices of the target in the sorted array are already in ascending order.*  
> *Time complexity:* `O(n log n)` due to the sort.  
> *Space complexity:* `O(m)` where `m` is the number of occurrences of `target` (the answer list).  
> *The constraints (`n ≤ 100`) make this approach perfectly fine, but you can also solve it in `O(n)` time using counting sort if you want to showcase advanced optimization.*

---

## 2.  Blog Article – “LeetCode 2089: Find Target Indices After Sorting Array – Good, Bad & Ugly”

> **SEO keywords**: LeetCode 2089, Find Target Indices After Sorting Array, coding interview, Java solution, Python solution, C++ solution, time complexity, sorting algorithm, interview tips, job interview, algorithm interview.

---

### 2.1 Introduction

In the world of software engineering interviews, LeetCode problems are the *show‑stopper* that recruiters love to throw at candidates. One of the recent additions is **LeetCode 2089 – “Find Target Indices After Sorting Array.”** Though it’s labeled “Easy,” it has a twist that makes it a perfect teaching moment for *sorting*, *linear scan*, and *algorithmic thinking*.  

If you’re preparing for a data‑structure interview or simply want to polish your Java/Python/C++ skills, this post walks you through the problem, shows you clean code in three major languages, dives into the trade‑offs (the good, the bad, and the ugly), and ends with job‑interview‑ready tips.

---

### 2.2 Problem Statement (Simplified)

> Given an integer array `nums` and an integer `target`,  
> **Return a list of all indices where `target` appears in `nums` after the array is sorted in non‑decreasing order.**  
> The answer list must be sorted in ascending order.  
> If the target never appears, return an empty list.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,2,5,2,3]`, `2` | `[1,2]` | Sorted → `[1,2,2,3,5]`; indices of `2` are 1 & 2 |
| `[1,2,5,2,3]`, `3` | `[3]` | Sorted → `[1,2,2,3,5]`; index of `3` is 3 |
| `[1,2,5,2,3]`, `5` | `[4]` | Sorted → `[1,2,2,3,5]`; index of `5` is 4 |

**Constraints**

* `1 ≤ nums.length ≤ 100`
* `1 ≤ nums[i], target ≤ 100`

Because the array is tiny, any algorithm that runs in `O(n log n)` or even `O(n)` will pass. The canonical solution uses **sorting + linear scan**, which is both intuitive and easy to explain in an interview.

---

### 2.3 The Good – What Makes This Problem a Classic

1. **Simplicity & Clarity**  
   *Sorting an array and then scanning for a value is a textbook pattern.* Interviewers love questions where a clear, canonical approach exists – it allows you to showcase clean coding style.

2. **Multiple Language Support**  
   *Java, Python, and C++ solutions are almost identical.* This demonstrates language‑agnostic thinking – a skill recruiters value when your team uses polyglot stacks.

3. **Time Complexity Awareness**  
   *You’re forced to talk about `O(n log n)` for sorting vs. `O(n)` if you use counting sort.* Interviewers often look for this awareness.

4. **Edge‑Case Handling**  
   *Empty result, duplicate values, target out of range.* This problem forces you to think about edge cases and write defensive code.

---

### 2.4 The Bad – Where You Can Lose Points

| Pitfall | Why It’s Bad | Fix |
|---------|--------------|-----|
| **Assuming `target` is always present** | Returning an empty list when it isn’t will make you lose points. | After sorting, simply return the empty `ans` list if nothing is found. |
| **Not sorting** | Skipping the sort step yields wrong indices because the array is unsorted. | Explicitly sort (`Arrays.sort(nums)`, `nums.sort()`, or `std::sort`). |
| **Using O(n²) brute force** | In a bigger test set, this would time‑out. | Stick to the canonical sort + scan approach. |
| **Ignoring the “sorted order” requirement** | If you return indices from the original array order, you’ll fail hidden tests. | Ensure you’re iterating over the *sorted* array. |

---

### 2.5 The Ugly – Optimizing for Speed & Space

While `O(n log n)` is acceptable, you can shave off the sorting step entirely:

1. **Counting Sort** –  
   Since `nums[i] ≤ 100`, you can create a frequency array of size 101.  
   Then, while traversing the counts, keep a running index pointer to know where the target will land after sorting.

2. **Early Termination** –  
   If the sorted array starts with numbers larger than `target`, you can break early.  

3. **Memory‑Efficient Answer** –  
   Instead of building the entire sorted array, you can compute the start and end indices of `target` directly from the frequency array.

> *Why do this in an interview?*  
> It shows you’re thinking about *algorithmic optimization* and *problem constraints*. But, if the interviewer isn’t looking for micro‑optimizations, stick to the clean sort‑and‑scan approach – clarity wins.

---

### 2.6 Code Walkthrough (Python)

```python
class Solution:
    def targetIndices(self, nums: List[int], target: int) -> List[int]:
        # 1️⃣ Sort the array (O(n log n))
        nums.sort()
        # 2️⃣ Gather indices of the target (O(n))
        return [i for i, x in enumerate(nums) if x == target]
```

*Line 3:* Sorting is done in place.  
*Line 5:* List comprehension gives a concise answer.

---

### 2.7 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | `O(n log n)` | `O(1)` (in‑place for Python/C++/Java) |
| Linear Scan | `O(n)` | `O(m)` (answer list; `m` ≤ `n`) |
| **Total** | `O(n log n)` | `O(m)` |

With `n ≤ 100`, the runtime is negligible, but the complexity is the *right* one to mention in an interview.

---

### 2.8 Interview Tips

| Scenario | What to say | Why it matters |
|----------|-------------|----------------|
| **Starter** | “I’ll sort the array first, then scan for the target.” | Shows you’re using a standard pattern. |
| **Complexity** | “Sorting takes `O(n log n)` time, but we only scan once.” | Demonstrates algorithmic awareness. |
| **Edge Cases** | “If the target isn’t present, I return an empty list.” | Covers hidden tests. |
| **Optimization** | “Because all numbers are ≤ 100, we could use counting sort to get `O(n)`.” | Highlights knowledge of advanced techniques. |
| **Testing** | “I’ll test with duplicate targets, single‑element arrays, and all‑different arrays.” | Shows thoroughness. |

---

### 2.9 Conclusion

LeetCode 2089 is deceptively simple but packs a great lesson in *sorting*, *linear traversal*, and *handling edge cases*. The canonical solution is a “sort‑and‑scan” pattern that’s easy to code and easy to explain. For a job interview, the **good** is showing clean, readable code; the **bad** is overlooking the need to sort; the **ugly** is over‑engineering the solution when a straightforward approach is sufficient.

> **Ready to land that data‑structure interview?**  
> Practice this problem in Java, Python, and C++, write your own test suite, and rehearse the “why” behind each step. Employers love candidates who can *explain* their solution as clearly as they code it.

Happy coding, and may the interview gods be ever in your favor!