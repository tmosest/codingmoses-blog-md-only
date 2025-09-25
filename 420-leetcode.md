---
title: LeetCode 420. Strong Password Checker - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Leetcode 420 – Strong Password Checker  
**Hard** – Minimum number of steps (insert / delete / replace) to make a password strong.  

| Length | Missing types | Repeating sequences | Minimum steps |
|--------|---------------|---------------------|---------------|
| `< 6` | `missing` | `repeats` | `max(missing, 6‑len, repeats)` |
| `6–20` | `missing` | `repeats` | `max(missing, repeats)` |
| `> 20` | `missing` | `repeats` | `deletes + max(missing, repeatsAfterDeletes)` |

The most delicate part is handling the > 20‑character case: deletes can reduce the number of required replacements for long repeated blocks.  

Below you’ll find three **production‑ready** solutions – Java, Python, and C++ – each fully commented and ready to paste into a coding interview.

---

## 2.  Java Solution (O(n) time, O(1) space)

```java
import java.util.*;

public class Solution {
    public int strongPasswordChecker(String password) {
        int n = password.length();

        // 1. Count missing character types
        int missingLower = 1, missingUpper = 1, missingDigit = 1;
        for (char c : password.toCharArray()) {
            if (Character.isLowerCase(c)) missingLower = 0;
            else if (Character.isUpperCase(c)) missingUpper = 0;
            else if (Character.isDigit(c)) missingDigit = 0;
        }
        int missingTypes = missingLower + missingUpper + missingDigit;

        // 2. Find all sequences of 3+ repeated characters
        int replace = 0;          // replacements needed if length <= 20
        int oneMod = 0;           // count of sequences where len%3==0
        int twoMod = 0;           // count of sequences where len%3==1
        int i = 0;
        while (i < n) {
            int j = i;
            while (j < n && password.charAt(j) == password.charAt(i)) j++;
            int len = j - i;
            if (len >= 3) {
                replace += len / 3;
                if (len % 3 == 0) oneMod++;
                else if (len % 3 == 1) twoMod++;
            }
            i = j;
        }

        // 3. Handle three cases
        if (n < 6) {                       // only insertions needed
            return Math.max(missingTypes, 6 - n);
        } else if (n <= 20) {               // only replacements needed
            return Math.max(missingTypes, replace);
        } else {                            // n > 20: deletions first
            int deleteCnt = n - 20;
            // delete one char from sequences with len%3==0
            int del = Math.min(deleteCnt, oneMod);
            replace -= del;
            deleteCnt -= del;

            // delete two chars from sequences with len%3==1
            del = Math.min(deleteCnt, twoMod * 2) / 2;
            replace -= del;
            deleteCnt -= del * 2;

            // delete three chars from the remaining sequences
            del = deleteCnt / 3;
            replace -= del;

            // after all deletions the required replacements shrink
            return (n - 20) + Math.max(missingTypes, replace);
        }
    }
}
```

**Why it works**

1. *Missing types* – we scan the string once.
2. *Repeats* – for every block of length `L >= 3` we need `⌊L/3⌋` replacements.  
   The modulo 3 of `L` tells us how many deletions are most effective:
   * `len % 3 == 0` → 1 deletion removes one replacement.
   * `len % 3 == 1` → 2 deletions remove one replacement.
   * `len % 3 == 2` → 3 deletions remove one replacement.
3. For `len > 20` we first spend deletions on the “cheapest” blocks, reducing the replacement count. Finally we add the mandatory deletions to the remaining replacements / missing types.

---

## 3.  Python Solution (Python 3.8+)

```python
class Solution:
    def strongPasswordChecker(self, password: str) -> int:
        n = len(password)

        # ---- 1. Count missing character types ----
        missing = 3
        for ch in password:
            if ch.islower(): missing -= 1
            elif ch.isupper(): missing -= 1
            elif ch.isdigit(): missing -= 1

        # ---- 2. Find repeating sequences ----
        i = 0
        replace = 0
        mods = [0, 0, 0]          # mods[0] = #seq with len%3==0, etc.
        while i < n:
            j = i
            while j < n and password[j] == password[i]:
                j += 1
            length = j - i
            if length >= 3:
                replace += length // 3
                mods[length % 3] += 1
            i = j

        # ---- 3. Resolve based on length ----
        if n < 6:
            return max(missing, 6 - n)

        if n <= 20:
            return max(missing, replace)

        # n > 20 : we must delete
        delete_needed = n - 20

        # delete from groups len%3==0 first
        for k in range(3):
            if delete_needed <= 0:
                break
            use = min(delete_needed, mods[k] * (k + 1))
            delete_needed -= use
            replace -= use // (k + 1)

        # after deletions, remaining replacements + missing types
        return (n - 20) + max(missing, replace)
```

**Key differences from Java**

- We use a list `mods` to store counts of sequences by `len % 3`.
- Deletion loop is succinct: we greedily apply deletions to the most beneficial groups.

---

## 4.  C++ Solution (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int strongPasswordChecker(string password) {
        int n = password.size();

        // 1. Count missing character types
        int missing = 3;
        for(char c : password){
            if(islower(c)) missing--;
            else if(isupper(c)) missing--;
            else if(isdigit(c)) missing--;
        }

        // 2. Count repeating sequences
        vector<int> mods(3,0);
        int replace = 0;
        for(int i=0;i<n;){
            int j=i;
            while(j<n && password[j]==password[i]) j++;
            int len=j-i;
            if(len>=3){
                replace += len/3;
                mods[len%3]++;
            }
            i=j;
        }

        // 3. Resolve
        if(n<6) return max(missing, 6-n);
        if(n<=20) return max(missing, replace);

        int deleteNeeded = n-20;

        // delete optimally from sequences
        for(int k=0;k<3 && deleteNeeded>0;k++){
            int canDelete = mods[k] * (k+1);
            int use = min(deleteNeeded, canDelete);
            deleteNeeded -= use;
            replace -= use/(k+1);
        }
        return (n-20) + max(missing, replace);
    }
};
```

The C++ implementation mirrors the Python logic, using a `vector<int>` for the modulo buckets and a single pass to find repeats.

---

## 5.  Blog Article – *The Good, The Bad, and The Ugly of the Strong Password Checker*

> **Title:** *“Strong Password Checker: A Deep Dive into Leetcode 420 – Code, Strategy, and Career‑Boosting Tips”*  
> **Meta description:** Learn how to crack Leetcode 420 in Java, Python, and C++. Understand the pitfalls, edge‑cases, and interview‑style solutions that make this hard problem a job‑interview staple.

### 5.1. Why this problem matters
In today’s security‑first world, interviewers love to see if you can turn a real‑world requirement—*password strength*—into clean, efficient code. Leetcode 420 forces you to juggle three constraints simultaneously: length, character variety, and repeated patterns. Solving it convincingly showcases:

- **Algorithmic thinking** – balancing insert/delete/replace operations.
- **Edge‑case awareness** – handling very short or very long passwords.
- **Performance mindset** – O(n) time, constant extra space.

### 5.2. The Good – A Solid, Reusable Strategy

1. **Greedy with Modulo 3** – The most beautiful trick is the observation that deleting 1, 2, or 3 characters from a repeated block reduces the number of required replacements by 1, depending on `len % 3`. This gives us an optimal delete‑first policy for passwords longer than 20.
2. **One‑pass Character Type Scan** – A single linear scan for missing lowercase, uppercase, and digit types.  
   *Why it’s good:* Simplicity + constant extra memory.
3. **Clear Separation of Cases** – `<6`, `6–20`, `>20` are treated separately, reducing cognitive load.

### 5.3. The Bad – Hidden Traps That Break Your Code

| Trap | What Happens | How to Avoid |
|------|--------------|--------------|
| **Ignoring the “missing type” requirement when only inserting** | You may output the wrong answer for a short password that is otherwise fine. | Always compute `max(missingTypes, neededInsertions)` for `len < 6`. |
| **Miscounting repeats** | Off‑by‑one errors cause under‑ or over‑replacement counts. | Use a clean loop: `while j < n && s[j] == s[i]`. |
| **Not updating replacement count after deletions** | Overestimates required changes for long passwords. | After each deletion phase, reduce `replace` accordingly (`replace -= used/(k+1)`). |
| **Assuming `delete_needed` will be zero** | For passwords like `"aaaaaaaaaaaaaaaaaaaaaaaaaaa"` you may delete too many or too few. | Keep `delete_needed` as a variable; after the loop it may still be >0; still add `delete_needed` to answer. |

### 5.4. The Ugly – Complexity Surprises & Edge‑Case Nightmares

- **Large Input (n=50)** – Although the constraints are small, a quadratic solution can still hit the time limit on some judges. Always aim for O(n).
- **Special Characters** – The problem statement allows `.` and `!` in addition to letters/digits. Many naïve solutions ignore them and incorrectly flag missing types.  
  *Fix:* treat any non‑alphanumeric char as “other” – it doesn’t affect the three required types.
- **Empty Repeat Blocks** – When a repeated block is exactly 3 characters long, a single deletion reduces one replacement. Forgetting this case leads to `replace - 1` errors.

### 5.5. Interview‑Ready Takeaway

> **“When tackling Leetcode 420, remember:**
> 
> *First, tally the missing types.  
> Second, enumerate all runs of 3+ identical chars and compute replacements as `len/3`.  
> Third, if the length exceeds 20, delete strategically from the runs with `len%3==0`, then `1`, then `2`.  
> Finally, the answer is `deletes + max(missing, replacementsAfterDeletes)`.”**

This 4‑step mantra is exactly what recruiters look for in a clean solution.

### 5.6. Code Snippets

> **Java:** `public int strongPasswordChecker(String password)` – see the Java section above.  
> **Python:** `def strongPasswordChecker(self, password: str) -> int:` – see the Python section above.  
> **C++:** `int strongPasswordChecker(string password)` – see the C++ section above.

Feel free to copy‑paste any of these into your local IDE or online compiler. The logic is identical; the syntax just changes.

### 5.7. SEO Checklist

| SEO Element | Implementation |
|-------------|----------------|
| **Primary keyword** | “Strong Password Checker Leetcode” |
| **Secondary keywords** | “Leetcode 420 solution”, “Password strength algorithm”, “Java Python C++ code”, “job interview coding” |
| **Meta description** | 155‑character summary including primary keyword. |
| **Header structure** | H1 for title, H2 for sections (Why, Good, Bad, Ugly, Takeaway, Code). |
| **Internal linking** | Link to your other blog posts on algorithm patterns. |
| **Code formatting** | Use fenced code blocks with language tags (```java, ```python, ```cpp). |

### 5.8. Final Thought

Mastering Leetcode 420 is more than just writing correct code—it’s about mastering a **framework**: identify constraints, separate concerns, use greedy optimizations, and validate edge cases. Bring this mindset to every interview problem and watch recruiters notice the **clean, efficient, and battle‑tested** programmer you’ve become.

Happy coding—and good luck landing that dream job! 🚀

---