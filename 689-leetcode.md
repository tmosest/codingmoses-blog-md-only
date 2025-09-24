---
title: LeetCode 689. Maximum Sum of 3 Non-Overlapping Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  The “Maximum Sum of 3 Non‑Overlapping Subarrays” – LeetCode 689  
*(Hard – 73 % of submissions beat it – a perfect interview question for a software‑engineering role)*

| Language | Code |
|----------|------|
| **Java** | <details><summary>Show</summary> ... </details> |
| **Python** | <details><summary>Show</summary> ... </details> |
| **C++** | <details><summary>Show</summary> ... </details> |

> *Want to land your next job?  Write the solution, explain the trick, and share it on LinkedIn or your portfolio.  That’s exactly what this post is about – a deep dive, a ready‑to‑copy solution, and a polished blog article that’s SEO‑friendly and interview‑ready.*

---

## 2️⃣  Problem Recap

> **Input**  
> `nums`: `int[]` – 1 ≤ nums.length ≤ 2 × 10⁴  
> `k`: length of each subarray (1 ≤ k ≤ floor(nums.length/3))  
> **Output**  
> `int[3]` – the starting indices of the three *non‑overlapping* subarrays of length `k` that give the maximum total sum.  
> If several triples give the same sum, return the **lexicographically smallest** one.

> **Example**  
> `nums = [1,2,1,2,6,7,5,1], k = 2 → [0,3,5]`  

> Explanation: sub‑arrays `[1,2]`, `[2,6]`, `[7,5]` sum to 18, the maximum possible.

---

## 3️⃣  High‑Level Strategy (the “Good”)

1. **Sliding Window** – compute the sum of every sub‑array of length `k`.  
   *`subSum[i]` = sum of `nums[i .. i+k-1]`*  
   O(n) time, O(n) space.

2. **Pre‑compute best left/right indices**  
   * `bestLeft[i]` – index of the *maximum* sub‑sum in the prefix `[0 .. i]`.  
   * `bestRight[i]` – index of the *maximum* sub‑sum in the suffix `[i .. n-k]`.  
   For ties we keep the *earliest* index (lexicographically smallest).

3. **Try every possible middle sub‑array**  
   For middle start `m` (must satisfy `k ≤ m ≤ n-2k`):  
   ```
   left  = bestLeft[m - k]
   right = bestRight[m + k]
   total = subSum[left] + subSum[m] + subSum[right]
   ```
   Keep the triple with the largest `total`.  
   If `total` ties, the lexicographic order is automatically handled by the construction of `bestLeft/right`.

The whole algorithm is **O(n) time, O(n) space** – the best possible for this problem.

---

## 4️⃣  Detailed Walk‑through (the “Bad” & “Ugly”)

| Issue | Why it’s bad | Fix |
|-------|--------------|-----|
| **Naïve O(n³)** | Enumerate all triples → too slow (n up to 20 000). | Use sliding window + pre‑computed best indices. |
| **Double counting overlaps** | Middle `m` must leave `k` slots on each side. | Iterate `m` from `k` to `n-2k`. |
| **Ties broken incorrectly** | Picking the *latest* max index would yield a lexicographically larger answer. | In `bestLeft`, when sums tie, keep the *smaller* index; similarly in `bestRight`. |
| **Off‑by‑one errors** | `subSum` length is `n-k+1`. | Carefully index arrays: `subSum[i]` corresponds to start `i`. |
| **Edge cases** | `nums.length == 3k` – only one triple possible. | The loops handle this naturally; just make sure indices are within bounds. |
| **Large numbers** | Sum of up to 2 × 10⁴ elements, each < 2¹⁶ → fits in 32‑bit signed int. | Use `int` in Java/C++, `int` in Python (arbitrary precision). |
| **Memory waste** | Storing whole `subSum` is necessary for O(1) look‑ups. | Accept O(n) space; the constraints allow it. |

---

## 5️⃣  Code Implementations

> **Note**: All three solutions use the *same* algorithmic idea – only syntax changes.

### 5.1 Java (LeetCode style)

```java
// 689. Maximum Sum of 3 Non-Overlapping Subarrays
class Solution {
    public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
        int n = nums.length;
        if (n < 3 * k) return new int[0];

        // 1. Compute sums of all sub‑arrays of length k
        int numSub = n - k + 1;
        int[] subSum = new int[numSub];
        int sum = 0;
        for (int i = 0; i < k; i++) sum += nums[i];
        subSum[0] = sum;
        for (int i = k; i < n; i++) {
            sum += nums[i] - nums[i - k];
            subSum[i - k + 1] = sum;
        }

        // 2. bestLeft[i] – index with max sum in [0..i]
        int[] bestLeft = new int[numSub];
        bestLeft[0] = 0;
        for (int i = 1; i < numSub; i++) {
            if (subSum[i] > subSum[bestLeft[i - 1]]) {
                bestLeft[i] = i;
            } else {
                bestLeft[i] = bestLeft[i - 1];
            }
        }

        // 3. bestRight[i] – index with max sum in [i..end]
        int[] bestRight = new int[numSub];
        bestRight[numSub - 1] = numSub - 1;
        for (int i = numSub - 2; i >= 0; i--) {
            if (subSum[i] >= subSum[bestRight[i + 1]]) {
                bestRight[i] = i;
            } else {
                bestRight[i] = bestRight[i + 1];
            }
        }

        // 4. Try every middle sub‑array
        int[] best = new int[3];
        int bestTotal = -1;
        for (int m = k; m <= numSub - k - 1; m++) {
            int left = bestLeft[m - k];
            int right = bestRight[m + k];
            int total = subSum[left] + subSum[m] + subSum[right];
            if (total > bestTotal) {
                bestTotal = total;
                best[0] = left;
                best[1] = m;
                best[2] = right;
            }
        }
        return best;
    }
}
```

### 5.2 Python 3

```python
# 689. Maximum Sum of 3 Non-Overlapping Subarrays
class Solution:
    def maxSumOfThreeSubarrays(self, nums: list[int], k: int) -> list[int]:
        n = len(nums)
        if n < 3 * k:
            return []

        # 1. subarray sums
        num_sub = n - k + 1
        sub_sum = [0] * num_sub
        curr = sum(nums[:k])
        sub_sum[0] = curr
        for i in range(k, n):
            curr += nums[i] - nums[i - k]
            sub_sum[i - k + 1] = curr

        # 2. best left
        best_left = [0] * num_sub
        for i in range(1, num_sub):
            if sub_sum[i] > sub_sum[best_left[i - 1]]:
                best_left[i] = i
            else:
                best_left[i] = best_left[i - 1]

        # 3. best right
        best_right = [0] * num_sub
        best_right[-1] = num_sub - 1
        for i in range(num_sub - 2, -1, -1):
            if sub_sum[i] >= sub_sum[best_right[i + 1]]:
                best_right[i] = i
            else:
                best_right[i] = best_right[i + 1]

        # 3. middle candidate loop
        best = [0, 0, 0]
        best_total = -1
        for m in range(k, num_sub - k):
            l = best_left[m - k]
            r = best_right[m + k]
            total = sub_sum[l] + sub_sum[m] + sub_sum[r]
            if total > best_total:
                best_total = total
                best = [l, m, r]

        return best
```

### 5.3 C++ (17)

```cpp
// 689. Maximum Sum of 3 Non-Overlapping Subarrays
class Solution {
public:
    vector<int> maxSumOfThreeSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        if (n < 3 * k) return {};

        // 1. subarray sums
        int numSub = n - k + 1;
        vector<int> subSum(numSub);
        int sum = 0;
        for (int i = 0; i < k; ++i) sum += nums[i];
        subSum[0] = sum;
        for (int i = k; i < n; ++i) {
            sum += nums[i] - nums[i - k];
            subSum[i - k + 1] = sum;
        }

        // 2. best left
        vector<int> bestLeft(numSub);
        bestLeft[0] = 0;
        for (int i = 1; i < numSub; ++i)
            bestLeft[i] = subSum[i] > subSum[bestLeft[i - 1]] ? i : bestLeft[i - 1];

        // 3. best right
        vector<int> bestRight(numSub);
        bestRight[numSub - 1] = numSub - 1;
        for (int i = numSub - 2; i >= 0; --i)
            bestRight[i] = subSum[i] >= subSum[bestRight[i + 1]] ? i : bestRight[i + 1];

        // 4. middle loop
        vector<int> ans(3);
        int bestTotal = -1;
        for (int m = k; m <= numSub - k - 1; ++m) {
            int l = bestLeft[m - k];
            int r = bestRight[m + k];
            int total = subSum[l] + subSum[m] + subSum[r];
            if (total > bestTotal) {
                bestTotal = total;
                ans = {l, m, r};
            }
        }
        return ans;
    }
};
```

---

## 6️⃣  Writing a Winning Blog Post – SEO & Interview Ready

Below is a ready‑to‑publish article that uses **rich headings, keyword‑dense sections, code screenshots, and practical interview tips**. Copy‑paste it into Medium, Dev.to, or your own tech blog. Tag it with *LeetCode 689*, *job interview*, *software engineering*, *coding challenge* and watch the traffic grow.

> **Pro‑Tip**: Add a **“Try it yourself”** interactive snippet (e.g., embed on GitHub Gist or CodePen) so recruiters can run the solution instantly.

---

### 6.1 Blog Outline

| Section | Purpose |
|---------|---------|
| **Headline** | Grab attention & include the LeetCode problem number. |
| **Problem Recap** | Quick refresher for readers who skim. |
| **Key Take‑aways** | List the *why* this problem matters in interviews. |
| **Algorithm Walk‑through** | Step‑by‑step explanation (include a flow diagram). |
| **Code** | Highlight the core logic in a language of choice. |
| **Complexity Analysis** | Show why O(n) beats all other approaches. |
| **Common Pitfalls** | Show what interviewers love to hear you avoid. |
| **Edge‑Case Checklist** | Reassure recruiters you understand constraints. |
| **Performance Tips** | Discuss constant factors, how to optimize Java's `Arrays.fill`, etc. |
| **Final Thought** | Encourage practicing on LeetCode, sharing on LinkedIn, building a portfolio. |
| **Call‑to‑Action** | “Download the code”, “Apply now”, “Share your own solutions”. |

---

### 6.2 Full Article

> (Copy‑paste into Medium or your own blog – you can even add a hero image of a binary tree or sliding window animation.)

```markdown
# 🚀 LeetCode 689: “Maximum Sum of 3 Non‑Overlapping Subarrays” – A Complete Guide
*Hard interview problem, 73 % of solutions beat it, perfect for your next software‑engineering interview.*

---

## 📌 Problem Statement

> **Goal**: Find three non‑overlapping sub‑arrays of equal length `k` in an array `nums` that maximize the total sum.  
> **Return**: The three starting indices, lexicographically smallest on ties.

> Constraints:  
> * 1 ≤ nums.length ≤ 20 000  
> * 1 ≤ k ≤ floor(nums.length/3)  
> * Each element < 2¹⁶

> **Why it matters**:  
> * Requires sliding window + dynamic programming in a single pass.  
> * Demonstrates a deep understanding of time/space trade‑offs – a must‑know for coding interviews at Google, Amazon, Microsoft, Stripe, etc.

---

## 🔍 High‑Level Insight – The “Good” Approach

1. **Sliding Window**  
   *Compute every sub‑array sum in linear time.*  
   ```
   subSum[i] = sum(nums[i … i+k-1])
   ```
   Complexity: **O(n)**.

2. **Prefix / Suffix Max Indices**  
   * `bestLeft[i]` – the index of the maximum `subSum` in `[0 … i]` (earliest on ties).  
   * `bestRight[i]` – the index of the maximum `subSum` in `[i … end]` (latest on ties – but we pick the *earliest* when we build).  

   These arrays let us instantly know *the best left candidate* and *the best right candidate* for any middle sub‑array.

3. **Enumerate the middle sub‑array**  
   *Middle start `m` must leave `k` slots on each side → `k ≤ m ≤ n-2k`.*  
   Compute total sum for `left = bestLeft[m-k]`, `right = bestRight[m+k]`.  
   Keep the triple with the largest total; ties are automatically resolved because we always use the earliest indices.

> Result: **O(n)** time, **O(n)** space – the optimum for this problem.

---

## 📚 Code – Java / Python / C++

> *Feel free to copy the snippets below into your own IDE or LeetCode test runner.*

```java
// 689. Maximum Sum of 3 Non-Overlapping Subarrays
class Solution {
    public int[] maxSumOfThreeSubarrays(int[] nums, int k) {
        int n = nums.length;
        if (n < 3 * k) return new int[0];

        int numSub = n - k + 1;
        int[] subSum = new int[numSub];
        int sum = 0;
        for (int i = 0; i < k; i++) sum += nums[i];
        subSum[0] = sum;
        for (int i = k; i < n; i++) {
            sum += nums[i] - nums[i - k];
            subSum[i - k + 1] = sum;
        }

        int[] bestLeft = new int[numSub];
        bestLeft[0] = 0;
        for (int i = 1; i < numSub; i++)
            bestLeft[i] = subSum[i] > subSum[bestLeft[i-1]] ? i : bestLeft[i-1];

        int[] bestRight = new int[numSub];
        bestRight[numSub-1] = numSub-1;
        for (int i = numSub-2; i >= 0; i--)
            bestRight[i] = subSum[i] >= subSum[bestRight[i+1]] ? i : bestRight[i+1];

        int[] ans = new int[3];
        int bestTotal = -1;
        for (int m = k; m <= numSub - k - 1; m++) {
            int l = bestLeft[m - k];
            int r = bestRight[m + k];
            int total = subSum[l] + subSum[m] + subSum[r];
            if (total > bestTotal) {
                bestTotal = total;
                ans = new int[]{l, m, r};
            }
        }
        return ans;
    }
}
```

```python
# 689. Maximum Sum of 3 Non-Overlapping Subarrays
class Solution:
    def maxSumOfThreeSubarrays(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        if n < 3 * k: return []

        num_sub = n - k + 1
        sub_sum = [0] * num_sub
        cur = sum(nums[:k])
        sub_sum[0] = cur
        for i in range(k, n):
            cur += nums[i] - nums[i-k]
            sub_sum[i-k+1] = cur

        best_left = [0] * num_sub
        for i in range(1, num_sub):
            best_left[i] = i if sub_sum[i] > sub_sum[best_left[i-1]] else best_left[i-1]

        best_right = [0] * num_sub
        best_right[-1] = num_sub-1
        for i in range(num_sub-2, -1, -1):
            best_right[i] = i if sub_sum[i] >= sub_sum[best_right[i+1]] else best_right[i+1]

        best = [0,0,0]
        best_total = -1
        for m in range(k, num_sub - k):
            l, r = best_left[m-k], best_right[m+k]
            total = sub_sum[l] + sub_sum[m] + sub_sum[r]
            if total > best_total:
                best_total = total
                best = [l, m, r]
        return best
```

```cpp
// 689. Maximum Sum of 3 Non-Overlapping Subarrays
class Solution {
public:
    vector<int> maxSumOfThreeSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        if (n < 3*k) return {};

        int numSub = n-k+1;
        vector<int> subSum(numSub);
        int s=0; for(int i=0;i<k;i++) s+=nums[i];
        subSum[0]=s;
        for(int i=k;i<n;i++){
            s+=nums[i]-nums[i-k];
            subSum[i-k+1]=s;
        }

        vector<int> bestLeft(numSub);
        bestLeft[0]=0;
        for(int i=1;i<numSub;i++)
            bestLeft[i]=subSum[i]>subSum[bestLeft[i-1]]?i:bestLeft[i-1];

        vector<int> bestRight(numSub);
        bestRight[numSub-1]=numSub-1;
        for(int i=numSub-2;i>=0;--i)
            bestRight[i]=subSum[i]>=subSum[bestRight[i+1]]?i:bestRight[i+1];

        vector<int> ans(3);
        int bestTotal=-1;
        for(int m=k;m<=numSub-k-1;m++){
            int l=bestLeft[m-k], r=bestRight[m+k];
            int total=subSum[l]+subSum[m]+subSum[r];
            if(total>bestTotal){
                bestTotal=total;
                ans={l,m,r};
            }
        }
        return ans;
    }
};
```

---

## ⏱️ Complexity Breakdown

| Step | Time | Space |
|------|------|-------|
| Sliding window | **O(n)** | `subSum` array **O(n)** |
| Prefix / Suffix max | **O(n)** | Two helper arrays **O(n)** |
| Middle loop | **O(n)** | Constant extra space |

Total: **Time O(n)**, **Space O(n)**.  
All other naïve solutions (double or triple nested loops) would be **O(n³)**, which is *impossible* for `n = 20 000`.

---

## ❌ Common Pitfalls & How to Avoid Them

| Pitfall | Why recruiters care | Fix |
|---------|---------------------|-----|
| Using two loops for prefix and suffix but *recomputing* sub‑array sums | Wastes time, O(n²) | Pre‑compute `subSum` once. |
| Not handling tie-breaking correctly | Results in wrong answer on subtle tests | Store *earliest* index on ties when building `bestLeft` and `bestRight`. |
| Off‑by‑one in middle candidate range (`k ≤ m ≤ n-2k`) | Fails on small edge cases | Explicitly compute `numSub = n-k+1` and loop `for m in range(k, numSub - k)`. |
| Using 32‑bit int overflow (in languages like C++) | Wrong answer on large inputs | Elements < 2¹⁶, sum < 2¹⁶*k*3; 32‑bit int is safe, but use `long long` for safety. |
| Neglecting `n < 3k` check | Null pointer / array index error | Quick guard clause at start. |

---

## 🔧 Edge‑Case Checklist

- `nums` length exactly `3k` → only one possible triple.  
- `k = 1` → trivial, just pick three max elements without adjacency.  
- `nums` all zeros → should return `[0, 1, 2]` (lexicographically smallest).  
- `k` equals `floor(n/3)` → only one or two possible placements.  

Test these before posting.

---

## ⚡ Performance Tips

| Language | Tip |
|----------|-----|
| **Java** | Use `Arrays.fill` for initializing arrays if you need large defaults. Avoid `HashMap` lookups – we use simple arrays. |
| **Python** | Use list comprehensions sparingly; loops are fast enough. |
| **C++** | Use `std::vector<int>` with `reserve` to avoid reallocations. Use `std::max_element` if you prefer readability, but manual loop is marginally faster. |

---

## 🎯 Final Thought & Call‑to‑Action

> *“I’ve solved LeetCode 689 in Java, Python, and C++ and published the code on GitHub. I’ve also shared my solution on LinkedIn and received interview calls from Google and Stripe.”*  

> If you’re preparing for a coding interview, practice this problem *daily*. Add the solution to your portfolio, write a blog post, or create a short video explaining the algorithm. Recruiters notice when candidates *prove* their problem‑solving skills, not just *talk* about them.

---

### 📥 Download the full solution (all languages) → [GitHub Gist](https://gist.github.com/your-username/leetcode-689)

### 💬 Want to discuss other LeetCode challenges? Drop a comment or DM me on LinkedIn.

> *Ready to crack your next interview? Let’s code, share, and get hired! 🚀*
```

---

### 6.3 Visual Enhancements

| Resource | How to add |
|----------|------------|
| **Diagram of sliding window** | Use an SVG or PNG that shows moving window across the array. |
| **Code syntax highlight** | Use markdown code blocks; Medium automatically highlights. |
| **Edge‑Case Table** | Use markdown table for quick reference. |
| **Link to GitHub repo** | Provide a link to a repo with all three language implementations. |

---

## 7️⃣  Next Steps for You

1. **Run the code** – paste the snippets into your favourite IDE.  
2. **Test edge cases** – e.g., `nums=[1,2,3,4,5,6,7,8,9], k=3`.  
3. **Publish** – copy the article into Medium/Dev.to with relevant tags.  
4. **Share** – post on LinkedIn with a comment: “Solved LeetCode 689 in Java, feel ready for the next coding interview!”  
5. **Apply** – use the conversation as a talking point in your next interview.

---

> **Happy coding & good luck on your next interview!** 🎉
```

---

**That’s all!** You now have the *full solution*, *time‑space analysis*, *common pitfalls*, and a *ready‑to‑publish SEO‑rich article*. Drop this into your portfolio, keep practicing, and you’ll land that software‑engineering role faster than you can say “LeetCode 689”.

Happy coding! 🧩
```

---

> **Meta‑Note**: If you’re using the article on Medium, set the **“Read time”** to about 7–8 minutes to match typical LeetCode solution posts, and add a **“Read More”** section for the next LeetCode challenge.

---

## 8️⃣  How to Share on LinkedIn

```text
✅ Problem: LeetCode 689 – Maximum Sum of 3 Non‑Overlapping Subarrays
✅ Solution: O(n) linear pass + prefix/suffix max indices
✅ Languages: Java, Python, C++
✅ Check out the full code & walkthrough: https://github.com/your-username/leetcode-689
💬 Let’s discuss how this demonstrates sliding window + DP mastery! #leetcode #interviewprep #softwareengineering
```

Drop the link to your GitHub or the blog post. Recruiters love seeing actual code, and the discussion hashtag will surface your profile to hiring managers.

---

### 📅 Weekly Practice Routine

1. **Day 1** – Read the problem & write the algorithm on paper.  
2. **Day 2** – Implement in one language, run on sample cases.  
3. **Day 3** – Add edge‑case tests (`n = 3k`, all zeros, large `k`).  
4. **Day 4** – Write a blog post or video explaining the solution.  
5. **Day 5** – Share on social networks, engage with comments.  
6. **Day 6** – Review others’ solutions or submit to online judge.  
7. **Day 7** – Reflect on mistakes & refine your code.

Repeat with a new LeetCode problem.

---

**And that’s a comprehensive guide** from algorithm to posting, ensuring you get the full benefit of the solution and exposure to recruiters.

Good luck and enjoy your interview prep! 🚀

--- 

*Feel free to adapt the article, code, and social media posts to fit your own style and brand.*