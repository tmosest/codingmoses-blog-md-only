---
title: LeetCode 3557. Find Maximum Number of Non Intersecting Substrings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3557. Find Maximum Number of Non‑Intersecting Substrings  
**Medium – LeetCode**

> **Problem**  
> Given a string `word`, return the maximum number of *non‑intersecting* substrings that  
> 1. are at least 4 characters long  
> 2. start and end with the same letter.  

> **Constraints**  
> * `1 ≤ word.length ≤ 2·10⁵`  
> * `word` consists of lowercase English letters.

---

### Why this problem matters for coding interviews
* It blends **string manipulation**, **dynamic programming** and **greedy reasoning** – all core interview topics.  
* The input size (200 000) forces you to think about **linear‑time** solutions.  
* The problem is a perfect example of “pick the earliest finishing interval” – a classic greedy pattern.

---

## 1.  The Easy Greedy Solution (O(n) time, O(1) space)

### Observation
If you keep track of the *first* occurrence of every letter since the last time you “cut” a substring, then whenever the same letter reappears at a distance **≥ 3** (length ≥ 4) you have found a valid substring.  
Because the substring must be non‑overlapping, we can immediately “reset” the tracking after counting it – the next substring must start after this one ends.

### Algorithm
```text
result = 0
last[26] = { -1 }   // last occurrence of each letter
mask = 0            // bitmask of letters seen since the last reset

for i = 0 … n-1
    c = word[i] - 'a'
    if (mask & (1 << c)) == 0          // first time we see this letter
        last[c] = i
        mask |= (1 << c)               // mark it as seen
    else if (i - last[c] + 1 >= 4)     // same letter again, enough length
        result += 1
        mask = 0                       // reset – start new interval
return result
```

* **Time** – One pass over the string: **O(n)**.  
* **Space** – Fixed array of 26 ints + one bitmask: **O(1)**.

### Edge Cases
* Multiple occurrences of the same letter before we reach length 4 – ignored until we hit the length requirement.  
* If the string ends without completing a valid substring, it is simply discarded – we never count it.

---

## 2.  The Dynamic‑Programming Knapsack Solution (O(n) time, O(n) space)

If you want to “play it safe” and compute the optimum via DP:

* `dp[i]` – maximum number of substrings in `word[0…i‑1]`.  
* For each position `i`, look at all previous indices `j` where the same character appears.  
* If `word[j…i‑1]` is valid (`i‑j+1 ≥ 4`), update `dp[i] = max(dp[i], dp[j] + 1)`.

Implementation tricks:
* Keep for each character a deque of its last four indices (any earlier indices cannot help because we only need the most recent one to form the shortest valid substring).  
* The deque guarantees **O(1)** per character, thus overall **O(n)** time.

---

## 3.  Full Code (Java, Python, C++)

> All implementations below use the greedy approach – the simplest, fastest, and most interview‑friendly.

### Java (O(n) time, O(1) space)

```java
import java.util.*;

public class Solution {
    public int maxSubstrings(String word) {
        int n = word.length();
        int[] last = new int[26];
        Arrays.fill(last, -1);
        int mask = 0;          // bitmask of seen letters since last reset
        int res = 0;

        for (int i = 0; i < n; i++) {
            int c = word.charAt(i) - 'a';
            if ((mask & (1 << c)) == 0) {          // first time this letter
                last[c] = i;
                mask |= (1 << c);
            } else if (i - last[c] + 1 >= 4) {      // valid substring found
                res++;
                mask = 0;                            // reset for next interval
            }
        }
        return res;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maxSubstrings("abcdeafdef")); // 2
        System.out.println(s.maxSubstrings("bcdaaaab"));   // 1
    }
}
```

### Python (O(n) time, O(1) space)

```python
class Solution:
    def maxSubstrings(self, word: str) -> int:
        last = [-1] * 26
        mask = 0
        res = 0
        for i, ch in enumerate(word):
            c = ord(ch) - 97
            if (mask & (1 << c)) == 0:          # first time we see this letter
                last[c] = i
                mask |= (1 << c)
            elif i - last[c] + 1 >= 4:          # found a valid substring
                res += 1
                mask = 0                        # reset for next interval
        return res


if __name__ == "__main__":
    sol = Solution()
    print(sol.maxSubstrings("abcdeafdef"))  # 2
    print(sol.maxSubstrings("bcdaaaab"))    # 1
```

### C++ (O(n) time, O(1) space)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxSubstrings(string word) {
        int last[26] = {0};
        memset(last, -1, sizeof(last));
        int mask = 0;
        int res = 0;
        for (int i = 0; i < (int)word.size(); ++i) {
            int c = word[i] - 'a';
            if ((mask & (1 << c)) == 0) {       // first time this letter
                last[c] = i;
                mask |= (1 << c);
            } else if (i - last[c] + 1 >= 4) {   // found a valid substring
                ++res;
                mask = 0;                        // reset
            }
        }
        return res;
    }
};

int main() {
    Solution s;
    cout << s.maxSubstrings("abcdeafdef") << endl; // 2
    cout << s.maxSubstrings("bcdaaaab")   << endl; // 1
}
```

---

## 4.  The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Greedy: simple, fast, one pass | DP: works for all, but overkill | Bit‑mask version (too fancy) |
| **Complexity** | `O(n)` time, `O(1)` space | `O(n)` time, `O(n)` space | Still `O(n)` time, but unnecessary constants |
| **Implementation** | Tiny, 9–12 lines | More lines, careful indices | Requires bit‑twiddling that may confuse interviewers |
| **Intuition** | “Earliest ending interval” | Classic knapsack DP | Uses masks like a puzzle – hard to explain |
| **Interview‑friendly** | Very high | Medium | Low (unless you’re a power‑user) |

> **Bottom line:** Stick with the greedy solution. It’s short, elegant, and guarantees optimality. The DP solution is useful if you need to demonstrate a deeper understanding of dynamic programming, but for this problem it’s a *nice-to-know* rather than *must‑know*.

---

## 5.  Testing Your Solution

| Test | Expected | Reason |
|------|----------|--------|
| `"aaaa"` | 1 | Single valid substring. |
| `"abcdaaaab"` | 1 | Only `"aaaa"` fits. |
| `"abcdabcd"` | 1 | Only `"abcd"` or `"abcd"` (but overlapping). |
| `"abcaabcd"` | 2 | `"abca"` and `"abcd"`. |
| `"abcd"` | 0 | Length < 4. |
| `"aaabaaa"` | 1 | `"aaaa"` in the middle. |
| `"xyzabcabcyz"` | 2 | `"abc...abcy"` and `"xyz...yz"`. |

Run a quick unit‑test in your favourite language and make sure all edge cases pass.

---

## 6.  Interview Tips

1. **Explain the greedy idea clearly** – “the first time we see a letter, remember it; if the same letter shows up later with enough distance, we cut here and reset.”  
2. **Why it’s optimal** – Any valid substring must end at the *last* position of its final character. Choosing the earliest finishing one never hurts the remaining string.  
3. **Talk about the bitmask** – Show that you’re comfortable with bit‑wise ops, but don’t over‑explain.  
4. **Complexity talk** – Emphasise that one pass over 200 000 chars is perfectly fine.  
5. **Edge‑case safety** – Mention that we ignore incomplete substrings at the end.

---

## 6.  Take‑away for Your Next Coding Interview

* **Look for “interval” patterns** – a lot of interview questions boil down to selecting non‑overlapping segments.  
* **Track only what you need** – here, the first occurrence per letter is all we need.  
* **Prefer greedy over DP when the constraints are large** – interviewers love O(n) solutions that are also concise.  
* **Always double‑check the *non‑overlap* requirement** – it’s the “killer” part of the problem.  

---

### SEO Meta‑Description  
> “3557 LeetCode – Find maximum number of non‑intersecting substrings (length ≥ 4, same start/end letter). Linear‑time greedy solution in Java, Python, and C++ with O(n) time and O(1) space. Dynamic‑programming backup – interview‑ready guide.”

### Suggested Keywords  
* 3557 Find Maximum Number of Non‑Intersecting Substrings  
* LeetCode Medium problem  
* greedy algorithm string manipulation  
* O(n) time, O(1) space  
* interview coding challenge  
* dynamic programming solution  
* non‑overlapping intervals  
* coding interview tips  

Feel free to copy the code into your own IDE or submit it directly on LeetCode – you’re all set for a smooth interview pass!