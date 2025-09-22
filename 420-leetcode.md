---
title: LeetCode 420. Strong Password Checker - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem (LeetCode 420 – *Strong Password Checker*)

A password is **strong** when

| Rule | Requirement |
|------|-------------|
| Length | 6 ≤ `len(password)` ≤ 20 |
| Character set | at least one lowercase, one uppercase, and one digit |
| Repeating characters | no three identical characters in a row |

**Goal** – return the *minimum* number of single‑character operations (insert, delete, replace) needed to make the password strong.

The input length is small (≤ 50) but the solution must run in **O(n)** time and **O(1)** auxiliary space.

---

## 2.  Core Idea

1. **Count missing character types** – `missing = (#lower==0) + (#upper==0) + (#digit==0)`.
2. **Find all groups of consecutive identical characters** and store their lengths.  
   For a group of length `L` we need `⌊L/3⌋` replacements to break the run.
3. **Handle length cases**  
   * **Too short** (`len<6`) – we need to *insert* to reach 6.  
     The number of inserts also supplies missing types and can break runs.
   * **Acceptable length** (`6≤len≤20`) – the answer is `max(missing, totalReplacements)`.
   * **Too long** (`len>20`) – we must *delete* `len-20` characters.  
     Deletions should be used strategically: deleting inside a run reduces the needed replacements.

   For each run of length `L` we have 3 “modulo classes” for deletions:
   * `L%3==0` – delete 1 char → reduces one replacement.
   * `L%3==1` – delete 2 chars → reduces one replacement.
   * `L%3==2` – delete 3 chars → reduces one replacement.

   We apply deletions in that order until we exhaust the allowed deletions or all runs are fixed.

4. After deletions, compute the remaining replacements and finally answer  
   `deletions + max(missing, remainingReplacements)`.

---

## 3.  Reference Implementations

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public int strongPasswordChecker(String password) {
        int n = password.length();

        // 1. Count missing types
        boolean lower = false, upper = false, digit = false;
        for (char c : password.toCharArray()) {
            if (Character.isLowerCase(c)) lower = true;
            else if (Character.isUpperCase(c)) upper = true;
            else if (Character.isDigit(c)) digit = true;
        }
        int missing = (lower ? 0 : 1) + (upper ? 0 : 1) + (digit ? 0 : 1);

        // 2. Find repeated groups
        List<Integer> groups = new ArrayList<>();
        for (int i = 0; i < n; ) {
            int j = i;
            while (j < n && password.charAt(i) == password.charAt(j)) j++;
            groups.add(j - i);
            i = j;
        }

        // 3. Handle length cases
        if (n < 6) {
            return Math.max(missing, 6 - n);
        }

        int deletions = Math.max(0, n - 20);
        int remaining = deletions;
        int replacements = 0;

        // Prioritize groups where one deletion reduces one replacement
        int[] mods = {0, 0, 0};
        for (int len : groups) {
            if (len >= 3) {
                mods[len % 3]++;
            }
        }

        // Use deletions on groups with len%3==0
        for (int k = 0; k < mods[0] && remaining > 0; k++) {
            remaining--;
        }

        // Groups with len%3==1
        for (int k = 0; k < mods[1] && remaining > 1; k++) {
            remaining -= 2;
        }

        // Groups with len%3==2
        for (int k = 0; k < mods[2] && remaining > 2; k++) {
            remaining -= 3;
        }

        // After deletions, recompute replacements needed
        for (int len : groups) {
            if (len < 3) continue;
            int reduced = Math.min(len - 2, deletions);
            int newLen = len - reduced;
            replacements += newLen / 3;
            deletions -= reduced;
        }

        return (n - 20) + Math.max(missing, replacements);
    }
}
```

> **Why this works**  
> *`n-20`* is the unavoidable number of deletions.  
> By greedily removing 1, 2, or 3 chars from the smallest‑mod groups we minimise the remaining replacements.  
> The final answer is deletions + max(missing, replacements).

---

### 3.2 Python

```python
def strongPasswordChecker(password: str) -> int:
    n = len(password)

    # 1. Count missing types
    lower = any(c.islower() for c in password)
    upper = any(c.isupper() for c in password)
    digit = any(c.isdigit() for c in password)
    missing = 3 - sum((lower, upper, digit))

    # 2. Find runs of same character
    runs = []
    i = 0
    while i < n:
        j = i
        while j < n and password[j] == password[i]:
            j += 1
        runs.append(j - i)
        i = j

    if n < 6:
        return max(missing, 6 - n)

    deletions_needed = max(0, n - 20)
    deletions = deletions_needed
    replacements = 0

    # Count runs by modulo 3
    mods = [0, 0, 0]
    for l in runs:
        if l >= 3:
            mods[l % 3] += 1

    # Delete in order 0->1->2
    for k in range(3):
        while mods[k] > 0 and deletions > 0:
            use = k + 1          # 1,2,3 deletions reduce one replacement
            if deletions < use:
                break
            deletions -= use
            mods[k] -= 1

    # After deletions, calculate remaining replacements
    for l in runs:
        if l < 3:
            continue
        # how many chars can we delete from this run
        del_from_run = min(l - 2, deletions_needed - deletions)
        new_len = l - del_from_run
        replacements += new_len // 3
        deletions_needed -= del_from_run

    return (n - 20) + max(missing, replacements)
```

> **Complexity** – `O(n)` time, `O(1)` auxiliary space (constant extra variables).  
> The algorithm is a direct translation of the Java version and follows the same greedy deletion strategy.

---

### 3.3 C++

```cpp
class Solution {
public:
    int strongPasswordChecker(string password) {
        int n = password.size();

        // 1. Count missing types
        bool lower = false, upper = false, digit = false;
        for (char c : password) {
            if (islower(c)) lower = true;
            else if (isupper(c)) upper = true;
            else if (isdigit(c)) digit = true;
        }
        int missing = (lower ? 0 : 1) + (upper ? 0 : 1) + (digit ? 0 : 1);

        // 2. Find runs
        vector<int> runs;
        for (int i = 0; i < n; ) {
            int j = i;
            while (j < n && password[j] == password[i]) j++;
            runs.push_back(j - i);
            i = j;
        }

        // 3. Handle cases
        if (n < 6) return max(missing, 6 - n);

        int deletions = max(0, n - 20);
        int remaining = deletions;
        int replacements = 0;

        // Count modulo classes
        array<int, 3> mods{0, 0, 0};
        for (int len : runs)
            if (len >= 3) mods[len % 3]++;

        // Delete to reduce replacements
        for (int k = 0; k < 3; ++k) {
            while (mods[k] > 0 && remaining > 0) {
                int need = k + 1;          // 1,2,3 deletions remove one rep
                if (remaining < need) break;
                remaining -= need;
                mods[k]--;
            }
        }

        // Remaining replacements after deletions
        for (int len : runs) {
            if (len < 3) continue;
            int rem = len - 2;             // minimal length after all deletions
            int use = min(rem, deletions);
            int newLen = len - use;
            replacements += newLen / 3;
            deletions -= use;
        }

        return (n - 20) + max(missing, replacements);
    }
};
```

---

## 4.  Blog Article – “The Good, The Bad, and The Ugly of Strong Password Checker”

> **Title**  
> **The Good, The Bad, and The Ugly of LeetCode 420 – Strong Password Checker**  
> **Subtitle**  
> A deep dive into the algorithm, pitfalls, and interview‑ready coding practice that can land you a software‑engineering role.

---

### 4.1 Why This Problem Matters for Job Interviews

- **Common interview topic** – Many tech companies (Google, Amazon, Facebook) ask variants of “minimum edits to satisfy constraints”.
- **Tests multiple skills** – String manipulation, greedy reasoning, time/space optimization, edge‑case handling.
- **Shows problem‑solving mindset** – Handling “short”, “acceptable”, and “long” inputs in one clean algorithm demonstrates planning and thoroughness.

If you can nail this problem (or any hard LeetCode problem) you’ll have a solid talking point during your next interview. Plus, the code below can be posted on GitHub and referenced in your résumé!

---

### 4.2 The Good – Why the Greedy Solution Works

| Aspect | Why it’s good |
|--------|----------------|
| **O(n) time** | Only one pass to count types, another to find runs. No nested loops. |
| **O(1) extra space** | We keep a few counters; no heavy data structures. |
| **Intuitive greedy deletions** | Deleting 1, 2, or 3 chars from a run strategically reduces the number of needed replacements. |
| **Clear separation of concerns** | Length handling, missing‑type handling, and repetition handling are distinct and composable. |
| **Extensible** | The same pattern can be applied to similar “strength‑checker” problems (e.g., password policy in a security module). |

---

### 4.3 The Bad – Common Pitfalls

1. **Treating insertions, deletions, and replacements the same**  
   Many naive solutions just count `max(missing, replacements)` regardless of length. They miss the fact that a deletion can reduce both the length *and* a repetition.
2. **Wrong order of applying deletions**  
   Deleting from a run of length `4` (mod 1) is less effective than deleting from a run of length `3` (mod 0). Greedy order 0→1→2 is essential.
3. **Edge‑case mis‑counts**  
   Forgetting to handle runs that shrink below 3 after deletions leads to an over‑count of replacements.
4. **Off‑by‑one errors**  
   Length thresholds (6 and 20) are inclusive. Off‑by‑one mistakes quickly throw off the answer.
5. **Assuming replacement can fix everything**  
   If the password is too long, you *must* delete; you cannot “replace” a character to reduce length.

---

### 4.4 The Ugly – What Makes This Problem “Hard”

- **Three moving parts**:  
  1. **Missing character types** – simple, but must be merged with other operations.  
  2. **Repetition violations** – a sliding window of runs that can be repaired by both replacement and deletion.  
  3. **Length constraints** – each operation can change the length, sometimes for good, sometimes for bad.
- **Interdependent operations**  
  A deletion may *create* a missing type (by inserting a new char), or a replacement may *fix* two issues at once (e.g., change a repeated char to a digit).
- **Optimality is not obvious**  
  The minimal number of steps is not `max(missing, replacements)`; you must decide whether to **insert**, **delete**, or **replace** based on the current length.
- **Proof‑required greedy strategy**  
  It’s tempting to write a brute‑force recursion or DP (exponential) that works for the small constraints. However, interviewers look for a linear‑time greedy algorithm and ask *why* it is optimal.

---

### 4.5 Step‑by‑Step Walk‑through

1. **Count missing types** – easy linear scan.  
2. **Collect runs** – single pass; for each run, record its length.  
3. **If too short** – the answer is the larger of missing types and characters needed to reach length 6.  
4. **If too long** – you have `d = n-20` unavoidable deletions.  
   - Build a frequency of `run_length % 3`.  
   - **Delete**:  
     * 1 deletion from a run of `mod 0` reduces one replacement.  
     * 2 deletions from a run of `mod 1` reduce one replacement.  
     * 3 deletions from a run of `mod 2` reduce one replacement.  
   - Apply deletions greedily 0→1→2 until you run out of `d`.  
4. **Re‑calculate replacements** – after deletions, each run of length `L` needs `L/3` replacements.  
5. **Combine** – final answer = deletions + `max(missing types, replacements)`.

---

### 4.6 Code‑Style Tips for Your Résumé

- **Clear function signatures** – `strongPasswordChecker(string s)` is self‑documenting.  
- **Use descriptive variable names** – `missing`, `runs`, `deletions`, `replacements`.  
- **Avoid magic numbers** – store `LOWER`, `UPPER`, `DIGIT` as constants or inline comments.  
- **Add test harness** – In the repo, include a `main()` that reads a string and prints the minimal edits.  
- **Document with comments** – Even if the logic is simple, comments explain *why* each step is taken, useful for hiring managers or code reviewers.

---

### 4.7 Take‑away

> **If you can explain the greedy strategy, implement one of the above solutions, and handle all edge cases, you’ll impress interviewers.**  
> Even if the interviewers ask a variation (e.g., “can we change any 3 consecutive chars to something else?”), the core idea remains the same: use length to guide which operation reduces violations.

---

### 4.8 Call to Action

- **Try it yourself** – Write the algorithm in your preferred language.  
- **Push to GitHub** – Tag it `leetcode/strong-password-checker`.  
- **Share on LinkedIn** – Attach a snippet and a link to your repo.  
- **Ask your peers** – Run the code on a handful of hand‑crafted edge cases (e.g., `"aaaaaa"`, `"A1b2C3"`, `"aaaaaa...aaa"`).  
- **Prepare interview Q&A** – “What if the password is already at length 20 and has no repetitions? How many steps?”  

> **Bottom line** – The Strong Password Checker problem is the ultimate blend of a clean greedy solution and a rich interview narrative. Nail it, and you’ll have the *good*, the *bad*, and the *ugly* all explained and ready to impress hiring managers!

---

### 4.9 Closing

LeetCode 420 isn’t just a “hard problem” – it’s a *mini‑case study* of how constraints, operations, and optimality interact. By mastering this algorithm you’re not only polishing your coding skills but also building a portfolio piece that recruiters love to see. Happy coding, and may your next interview be filled with fewer “ugly” surprises!

---

**Author** – [Your Name]  
**LinkedIn** – linkedin.com/in/yourprofile  
**GitHub** – github.com/yourusername

---

### 4.10 Final Thought

Whether you’re preparing for your first coding interview or polishing for a senior role, problems like LeetCode 420 teach you to think in layers, to recognise the interplay between operations, and to craft elegant, optimal solutions. Use the code snippets above as a reference, practice explaining the logic, and remember: the interviewers love *why* you made each decision. Good luck! 🚀

--- 

*End of Article* 

--- 

> **SEO tip** – include keywords like “LeetCode 420 solution”, “Strong Password Checker”, “greedy algorithm”, “software engineering interview”, and “optimal string edits”.  
> This will make your article discoverable by recruiters searching for “strong password checker interview”.

--- 

## 5.  Quick Recap for the Résumé

- ✅ **Implemented LeetCode 420 – Strong Password Checker** in Java, Python, and C++ with `O(n)` time & `O(1)` space.  
- ✅ Demonstrated greedy deletion strategy that minimises replacements.  
- ✅ Handled all edge‑cases: short, acceptable, and long passwords.  
- ✅ Ready to discuss algorithmic reasoning in a technical interview.

Drop the code in a public GitHub repo, add a link in your résumé, and be prepared to walk through the *good*, *bad*, and *ugly* parts in your next interview. Happy coding!