---
title: LeetCode 291. Word Pattern II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Word Pattern II (LeetCode 291) – Backtracking Solutions in Java, Python & C++  
*Word Pattern II – the classic interview problem that tests your recursion & hash‑map skills.*

---

## 📌 Problem Statement (LeetCode 291)

> **Word Pattern II**  
> Given a pattern string and a target string `s`, determine if `s` can be segmented into a sequence of non‑empty substrings such that each **unique** character in the pattern maps to **exactly one** substring and the mapping is *bijective* (no two characters share the same substring).  
> Return `true` if a bijection exists, otherwise `false`.

**Constraints**

- `1 ≤ pattern.length, s.length ≤ 20`
- Both strings contain only lowercase English letters.

> **Examples**  
> 1. `pattern = "abab"`, `s = "redblueredblue"` → `true` (`a→"red"`, `b→"blue"`)  
> 2. `pattern = "aaaa"`, `s = "asdasdasdasd"` → `true` (`a→"asd"`)  
> 3. `pattern = "aabb"`, `s = "xyzabcxzyabc"` → `false`

---

## 🔍 Why This Problem Rocks for Interviews

| Good | Bad | Ugly |
|------|-----|------|
| **Intuitive backtracking** – teaches recursion, pruning, and state restoration. | **Exponential worst‑case** – you may explore many impossible partitions. | **Hard to debug** – subtle off‑by‑one errors, wrong base case, or missing bijection check can lead to wrong results. |
| **Language‑agnostic** – solved in Java, Python, C++, Go, Rust, etc. | **Stack depth limits** – deep recursion may hit language limits on large inputs. | **Time limits on LeetCode** – naïve solutions can be TLE even on short inputs. |
| **Great for showcasing data‑structures** – hash maps and sets in the same solution. | **Implementation variance** – many accepted solutions look vastly different. | **Edge‑case pitfalls** – e.g., mapping a character to the empty string or re‑using substrings. |

---

## ✅ Backtracking Algorithm – The “Good” Core

1. **Recursive function** `dfs(i, j)`  
   - `i` – current index in `s`  
   - `j` – current index in `pattern`

2. **Base Cases**  
   - If both `i` and `j` reach the ends → success (`true`).  
   - If one reaches the end but not the other → failure (`false`).

3. **Current Pattern Character** `c = pattern[j]`.

4. **If `c` already has a mapping**  
   - Retrieve the mapped substring `sub`.  
   - Check whether `s` starts with `sub` at position `i`.  
   - If not, backtrack (`false`).  
   - If yes, recurse on `dfs(i + sub.length(), j + 1)`.

5. **If `c` is new**  
   - Iterate over all possible substrings `s[i : k]` (`k` from `i+1` to `s.length`).  
   - **Prune**: skip if the substring is already mapped to another pattern character.  
   - **Assign**: `map[c] = substring`, `used.insert(substring)`.  
   - Recurse.  
   - **Backtrack**: remove assignment on return.

6. **Return** `false` if all options fail.

> **Key pruning**: the bijection condition (no two characters map to the same substring) dramatically cuts the search space.

---

## ⏱️ Complexity Analysis

| Metric | Worst‑Case | Typical |
|--------|------------|---------|
| **Time** | Exponential – `O(n^k)` where `k = pattern.length` | Usually far less due to pruning |
| **Space** | `O(k)` recursion stack + `O(k)` map + `O(n)` set | `O(k + n)` |

The constraints (`≤20`) keep the worst‑case feasible in practice.

---

## 🧩 Implementation – Code Samples

Below are clean, commented solutions in **Java**, **Python**, and **C++**.  
Each follows the same recursive backtracking template and uses a hash‑map + hash‑set for the bijection.

### 1. Java (Java 17)

```java
import java.util.*;

public class Solution {
    public boolean wordPatternMatch(String pattern, String str) {
        // Map: pattern char -> assigned substring
        Map<Character, String> map = new HashMap<>();
        // Set: all substrings already assigned (to enforce bijection)
        Set<String> used = new HashSet<>();
        return backtrack(str, 0, pattern, 0, map, used);
    }

    private boolean backtrack(String s, int i, String p, int j,
                              Map<Character, String> map, Set<String> used) {
        // Both strings exhausted → success
        if (i == s.length() && j == p.length()) return true;
        // One exhausted but not the other → failure
        if (i == s.length() || j == p.length()) return false;

        char c = p.charAt(j);

        // If character already mapped, must match the exact substring
        if (map.containsKey(c)) {
            String sub = map.get(c);
            if (!s.startsWith(sub, i)) return false;
            return backtrack(s, i + sub.length(), p, j + 1, map, used);
        }

        // Try all possible substrings for new mapping
        for (int k = i + 1; k <= s.length(); k++) {
            String sub = s.substring(i, k);

            if (used.contains(sub)) continue;   // bijection check

            map.put(c, sub);
            used.add(sub);

            if (backtrack(s, k, p, j + 1, map, used)) return true;

            // Backtrack
            map.remove(c);
            used.remove(sub);
        }
        return false;
    }
}
```

---

### 2. Python 3

```python
class Solution:
    def wordPatternMatch(self, pattern: str, s: str) -> bool:
        def dfs(i, j, mapping, used):
            # Success
            if i == len(s) and j == len(pattern):
                return True
            # Failure
            if i == len(s) or j == len(pattern):
                return False

            c = pattern[j]

            if c in mapping:
                sub = mapping[c]
                if not s.startswith(sub, i):
                    return False
                return dfs(i + len(sub), j + 1, mapping, used)

            # Try all possible substrings
            for k in range(i + 1, len(s) + 1):
                sub = s[i:k]
                if sub in used:
                    continue
                mapping[c] = sub
                used.add(sub)
                if dfs(k, j + 1, mapping, used):
                    return True
                # Backtrack
                del mapping[c]
                used.remove(sub)
            return False

        return dfs(0, 0, {}, set())
```

---

### 3. C++ (C++17)

```cpp
#include <string>
#include <unordered_map>
#include <unordered_set>

class Solution {
public:
    bool wordPatternMatch(const std::string &pattern, const std::string &s) {
        std::unordered_map<char, std::string> mp;
        std::unordered_set<std::string> used;
        return backtrack(s, 0, pattern, 0, mp, used);
    }

private:
    bool backtrack(const std::string &s, int i,
                   const std::string &p, int j,
                   std::unordered_map<char, std::string> &mp,
                   std::unordered_set<std::string> &used) {
        if (i == s.size() && j == p.size()) return true;
        if (i == s.size() || j == p.size()) return false;

        char c = p[j];
        if (mp.count(c)) {
            const std::string &sub = mp[c];
            if (s.compare(i, sub.size(), sub) != 0) return false;
            return backtrack(s, i + sub.size(), p, j + 1, mp, used);
        }

        for (int k = i + 1; k <= s.size(); ++k) {
            std::string sub = s.substr(i, k - i);
            if (used.count(sub)) continue;

            mp[c] = sub;
            used.insert(sub);

            if (backtrack(s, k, p, j + 1, mp, used)) return true;

            mp.erase(c);
            used.erase(sub);
        }
        return false;
    }
};
```

---

> **Tip:** All three snippets use the same *pruning* rule (`sub in used`), which is the secret sauce that keeps the runtime reasonable on LeetCode.

---

## 🎯 Interview‑Ready Checklist

| Check | What to do |
|-------|------------|
| **Base‑case correctness** | Ensure both strings end simultaneously. |
| **Bijectivity** | Keep a `used` set; never assign a substring that’s already taken. |
| **No empty mapping** | Always start the inner loop from `i+1` (non‑empty substring). |
| **Return early** | If a recursive call returns `true`, unwind immediately. |
| **Edge‑case tests** | Write a few unit tests: single‑char pattern, identical pattern/​string, pattern longer than string, etc. |
| **Stack safety** | For Java/C++, guard against `StackOverflowError` by limiting recursion depth or converting to an iterative approach if necessary. |

---

## 🚀 Final Thoughts

- **Word Pattern II** is a *must‑know* recursion problem that beautifully blends string manipulation with hash‑maps/sets.  
- The backtracking template is **portable**: pick your language, keep the `map` + `set`, and you’re good to go.  
- On LeetCode, a well‑pruned solution passes all tests comfortably within the 1 second limit even with the exponential worst‑case.

---

### 📞 Want to practice more?

| Platform | Problem |
|----------|---------|
| LeetCode | [Word Pattern II](https://leetcode.com/problems/word-pattern-ii/) |
| CodeSignal | “Pattern Matching” |
| InterviewBit | “Bijective String Partitioning” |

Happy coding, and may your bijection always be *unique*!