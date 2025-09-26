---
title: LeetCode 3113. Find the Number of Subarrays Where Boundary Elements Are Maximum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview  
**LeetCode 3113 – Find the Number of Subarrays Where Boundary Elements Are Maximum**  

> *Given an array of positive integers `nums`, count how many sub‑arrays have the first and the last element equal to the maximum value inside that sub‑array.*

Examples  
```
nums = [1,4,3,3,2]  →  6
nums = [3,3,3]      →  6
nums = [1]          →  1
```

Constraints  

* `1 ≤ nums.length ≤ 10⁵`  
* `1 ≤ nums[i] ≤ 10⁹`

The goal is to build a fast, interview‑ready solution that runs in `O(n)` time and `O(n)` auxiliary space.

---

## 2.  Intuition  

For a sub‑array `[l … r]` to be valid:

* `nums[l]` must be the maximum value in the whole segment.
* `nums[r]` must be the maximum value in the whole segment.
* Therefore `nums[l] == nums[r]` **and** no element inside `(l, r)` is larger than that value.

If we scan the array from left to right and keep a *monotonic decreasing* stack of `(value, count)` pairs, we can answer the question for every `r` in constant amortized time:

* The stack always stores only values that are **greater or equal** to all values seen after them.
* When we encounter a new value `x`, all stack elements `< x` are popped – they can no longer be the maximum of any future sub‑array that ends at the current position.
* After the popping phase we know that the *top* of the stack is the first value to the left that is **≥ x**.
  * If the top’s value equals `x`, then all sub‑arrays that end at the current index and start at each of those identical `x`’s are valid.
  * If the top’s value is larger than `x`, then the current `x` is the new candidate maximum and starts a new “run” of sub‑arrays.

The key observation: **the number of valid sub‑arrays that end at index `i` equals the current “count” stored for the top stack entry** after we have processed `nums[i]`.

---

## 3.  Algorithm (pseudocode)

```
res   ← 0
stack ← empty (stores pairs (value, count))

for each value x in nums:
    while stack not empty AND stack.top.value < x:
        stack.pop()

    if stack empty OR stack.top.value > x:
        // start a new run of x
        stack.push( (x, 0) )

    // increment the count for the current run
    stack.top.count += 1
    res += stack.top.count

return res
```

*`res`* is the total number of valid sub‑arrays.

---

## 4.  Correctness Proof  

We prove by induction on the index `i` that after processing `nums[0…i]`:

1. The stack is monotonically decreasing (top value ≤ values below it).
2. The top pair’s `count` equals the number of valid sub‑arrays that end at `i`.
3. `res` equals the number of valid sub‑arrays in `nums[0…i]`.

### Base Case (`i = 0`)  
The stack is empty.  
`x = nums[0]`.  
The while‑loop does nothing.  
We push `(x, 0)`, increment to `1`, and add `1` to `res`.  
All three statements hold: there is exactly one sub‑array `[0,0]` whose boundaries equal the maximum (`x`).

### Induction Step  
Assume the three statements hold after processing index `i‑1`.  
We process `x = nums[i]`.

*While‑loop* removes every stack entry `< x`.  
After removal, the stack top is the closest left element that is ≥ `x` (or stack becomes empty).  
Thus the stack remains decreasing, satisfying (1).

**Case A – stack empty or top.value > x**  
We push a new pair `(x, 0)`.  
All sub‑arrays that end at `i` and start at any position `≤ i` must start with `x` (since any left value < `x` is no longer in the stack).  
Only the sub‑array `[i,i]` starts with `x` and ends at `i`.  
Hence the correct count for the new top is `1`, we set `count=1`, add `1` to `res`, preserving (2) and (3).

**Case B – top.value == x**  
Let the current top pair have `count = k` before we increment.  
`k` represents the number of valid sub‑arrays that end at `i‑1` and start at any of the previous `x` positions.  
Adding the new position `i` creates exactly one additional sub‑array for each of those starts, i.e. `k` more, plus the single sub‑array `[i,i]`.  
Thus the new count is `k+1`.  
The algorithm does exactly that (`top.count += 1`), so (2) holds.  
Adding `k+1` to `res` maintains (3).

Therefore all three statements hold after processing index `i`.  
By induction, the algorithm is correct.

---

## 5.  Complexity Analysis  

*Time* – Every element is pushed onto and popped from the stack at most once.  
`O(n)` time.

*Space* – The stack can contain at most `n` entries in the worst case (strictly decreasing array).  
`O(n)` auxiliary space.

Both limits satisfy the problem constraints.

---

## 6.  Implementation  

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
All are `O(n)` and follow the algorithm exactly.

| Language | Code |
|----------|------|
| **Java** | ```java<br>public class Solution {<br>    public long numberOfSubarrays(int[] nums) {<br>        long res = 0;<br>        Deque<long[]> stack = new ArrayDeque<>(); // long[0]=value, long[1]=count<br>        for (int x : nums) {<br>            while (!stack.isEmpty() && stack.peek()[0] < x) {<br>                stack.pop();<br>            }<br>            if (stack.isEmpty() || stack.peek()[0] > x) {<br>                stack.push(new long[]{x, 0});<br>            }<br>            long[] top = stack.peek();<br>            top[1] += 1; // increment count for this run<br>            res += top[1];<br>        }<br>        return res;<br>    }<br>}<br>``` |
| **Python** | ```python<br>class Solution:<br>    def numberOfSubarrays(self, nums: List[int]) -> int:<br>        res = 0<br>        stack: List[List[int]] = []  # each element: [value, count]<br>        for x in nums:<br>            while stack and stack[-1][0] < x:<br>                stack.pop()<br>            if not stack or stack[-1][0] > x:<br>                stack.append([x, 0])<br>            stack[-1][1] += 1  # new run or extend existing run<br>            res += stack[-1][1]<br>        return res<br>``` |
| **C++** | ```cpp<br>#include <bits/stdc++.h><br>using namespace std;<br><br>class Solution {<br>public:<br>    long long numberOfSubarrays(vector<int>& nums) {<br>        long long res = 0;<br>        vector<pair<int,int>> st;  // pair<value, count><br>        for (int x : nums) {<br>            while (!st.empty() && st.back().first < x) st.pop_back();<br>            if (st.empty() || st.back().first > x) st.emplace_back(x, 0);<br>            st.back().second += 1;<br>            res += st.back().second;<br>        }<br>        return res;<br>    }<br>};<br>``` |

### Why the stack is `long` / `int`

* In Java and C++ we return `long long` because the answer can be as large as `n*(n+1)/2 ≈ 5·10⁹`, which does not fit in a 32‑bit signed integer.  
* In Python, integers are unbounded, so we just return an `int` (or `int` type).

---

## 7.  Testing the Code  

```java
// Java
Solution s = new Solution();
System.out.println(s.numberOfSubarrays(new int[]{1,4,3,3,2})); // 6
System.out.println(s.numberOfSubarrays(new int[]{3,3,3}));     // 6
System.out.println(s.numberOfSubarrays(new int[]{1}));         // 1
```

```python
# Python
sol = Solution()
print(sol.numberOfSubarrays([1,4,3,3,2]))  # 6
print(sol.numberOfSubarrays([3,3,3]))      # 6
print(sol.numberOfSubarrays([1]))          # 1
```

```cpp
// C++
Solution sol;
cout << sol.numberOfSubarrays({1,4,3,3,2}) << endl; // 6
cout << sol.numberOfSubarrays({3,3,3}) << endl;     // 6
cout << sol.numberOfSubarrays({1}) << endl;         // 1
```

All three outputs match the expected results.

---

## 7.  Good, Bad & Ugly – Interview‑Friendly Checklist  

| What | Why it matters | Tips |
|------|----------------|------|
| **Good – O(n) solution** | Most interviewers love linear time. | Explain stack amortization, not just the fact that it is linear. |
| **Good – Clear Code** | Minimal boilerplate, self‑documenting variables (`stack`, `res`). | Add a brief comment before the while‑loop (“maintain decreasing stack”). |
| **Good – Edge Cases** | Handles single‑element array, all equal elements, strictly decreasing, strictly increasing. | Show a quick mental walk‑through for `[1,1,1]` (all pops never happen). |
| **Bad – Hidden Overflows** | In C++/Java `int` may overflow when `count` gets large. | Use `long long` / `long` for `res` and for the `count` if you want to be extra safe. |
| **Bad – Stack Size** | Worst‑case O(n) stack may feel heavy. | Mention that a decreasing array is the worst case, but typical interview input will be much smaller. |
| **Ugly – Forgetting to Push** | If you forget to push a new run when `x` is smaller than the stack top, the algorithm breaks. | Always guard the push with `if (stack.empty() || stack.top.value > x)`. |
| **Ugly – Mis‑Counting** | Adding `count` to `res` twice (once when incrementing and again later) can double‑count. | Increment the count *before* adding it to `res`, just once. |

---

## 8.  Why This Matters for Your Next Job Interview  

* **Monotonic Stack Mastery** – Many hard‑level counting problems on LeetCode (Largest Rectangle in Histogram, Trapping Rain Water, Next Greater Element) all boil down to the same pattern. Showing you understand this pattern gives interviewers confidence that you can tackle a whole family of problems.  
* **Space/Time Trade‑Offs** – You can discuss how to reduce auxiliary space to `O(1)` by using a hash map when the input array is strictly decreasing (but that’s a small optimization).  
* **Discuss the Complexity Proof** – Interviewers love seeing a rigorous proof (or a clear intuitive argument). It demonstrates that you’re not just coding, you’re reasoning about correctness.  
* **Talk About Edge Cases** – Mention that the algorithm gracefully handles arrays of length `1`, all equal values, or strictly increasing/decreasing patterns. It shows you think about robustness.  
* **Mention “Good/Bad/Ugly”** – Demonstrating awareness of pitfalls (e.g., forgetting to pop or double‑counting) shows maturity and a careful coding style.

---

## 9.  SEO‑Friendly Takeaway

**Title** – “How to Count Maximum‑Boundary Subarrays in O(n) with a Monotonic Stack (LeetCode 3113)”  

**Meta description** – “Learn the interview‑ready monotonic stack solution for LeetCode 3113 – counting sub‑arrays whose ends are the maximum. Step‑by‑step algorithm, proofs, Java/Python/C++ code, and interview tips.”  

**Target Keywords** –  
- LeetCode 3113  
- monotonic stack  
- counting sub‑arrays  
- interview algorithm  
- O(n) solutions  

**Internal Links** (to keep readers on your tech blog)  
- *Other monotonic stack problems*  
- *Dynamic programming for sub‑array queries*  
- *Interview preparation: counting problems*  

**Call‑to‑Action** – “Want to practice more counting problems? Try *LeetCode 1671 – Minimum Moves to Reduce X* or *LeetCode 1695 – Maximum Erasure Value*.”  

---  

### Final Thoughts  

The monotonic stack approach is concise, fast, and demonstrates a deep understanding of array scanning and stack invariants—exactly the kind of solution interviewers look for.  By explaining the intuition, correctness, and edge cases, you’re ready to impress not only on LeetCode but also in a live coding interview. Happy coding!