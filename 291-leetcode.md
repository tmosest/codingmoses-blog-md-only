---
title: LeetCode 291. Word Pattern II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅  Word Pattern II – Leetcode 291  
**Java | Python | C++** – Backtracking + Bijection  
**SEO‑optimized guide** to help you nail this interview problem and land your dream job  

---

### 🚀 Blog Outline (SEO Keywords)

- **Leetcode 291 Word Pattern II** – classic string‑mapping challenge  
- **Java backtracking solution** – clean, production‑ready code  
- **Python Word Pattern II** – readable recursion with sets/dicts  
- **C++ implementation** – STL unordered_map / unordered_set  
- **Pattern matching bijection** – why uniqueness matters  
- **Job interview strategy** – how to talk about this problem in a technical interview  
- **Time & space complexity** – O(n^m) worst‑case, but *practically* fast  
- **Common pitfalls** – “duplicate mapping”, “off‑by‑one”, “stack overflow”  

> *Meta Description:*  
> Master Leetcode 291 – Word Pattern II. Get ready with Java, Python and C++ backtracking solutions, complexity analysis, edge‑case tricks, and interview‑ready talking points. Perfect your algorithm skills and impress hiring managers.

---

## 📄 Problem Recap

> **Word Pattern II**  
> Given a pattern string `pattern` and a string `s`, determine if `s` can be segmented into a sequence of non‑empty substrings such that each character of `pattern` maps **bijectively** (one‑to‑one) to a substring.  
> Constraints: `1 <= pattern.length, s.length <= 20`, only lowercase letters.

---

## 🧠 High‑Level Idea

1. **Backtracking** – try every possible substring for each pattern character.  
2. **Mapping (`HashMap / dict / unordered_map`)** – store the chosen substring for each character.  
3. **Used set (`HashSet / set / unordered_set`)** – ensure **bijectivity**: no two pattern characters share the same substring.  
4. **Pruning** – if the current mapping fails to match the prefix of `s`, backtrack immediately.

The algorithm explores a tree of possibilities; because the input size is tiny (≤ 20), the exponential worst‑case is acceptable.

---

## 📦 Code Implementations

### 1️⃣ Java (Backtracking + Sets)

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

public class Solution {
    public boolean wordPatternMatch(String pattern, String s) {
        return backtrack(s, 0, pattern, 0, new HashMap<>(), new HashSet<>());
    }

    private boolean backtrack(String s, int i, String pattern, int j,
                              Map<Character, String> map, Set<String> used) {
        // Base: both strings fully consumed
        if (i == s.length() && j == pattern.length()) return true;
        // Mismatch: one consumed, the other not
        if (i == s.length() || j == pattern.length()) return false;

        char c = pattern.charAt(j);

        // Existing mapping → must match exactly
        if (map.containsKey(c)) {
            String val = map.get(c);
            if (!s.startsWith(val, i)) return false;
            return backtrack(s, i + val.length(), pattern, j + 1, map, used);
        }

        // Try every possible substring for this character
        for (int k = i; k < s.length(); k++) {
            String candidate = s.substring(i, k + 1);
            if (used.contains(candidate)) continue;      // bijection check

            map.put(c, candidate);
            used.add(candidate);

            if (backtrack(s, k + 1, pattern, j + 1, map, used)) return true;

            // Backtrack
            map.remove(c);
            used.remove(candidate);
        }
        return false;
    }
}
```

> **Why it works** – The recursion depth equals `pattern.length()` (≤ 20). Each call attempts all possible substrings; the `used` set guarantees no two pattern characters map to the same substring.

---

### 2️⃣ Python (Elegant Recursion)

```python
def wordPatternMatch(pattern: str, s: str) -> bool:
    def dfs(i: int, j: int, mapping: dict, used: set) -> bool:
        # End conditions
        if i == len(s) and j == len(pattern):
            return True
        if i == len(s) or j == len(pattern):
            return False

        c = pattern[j]

        # Already mapped
        if c in mapping:
            val = mapping[c]
            if not s.startswith(val, i):
                return False
            return dfs(i + len(val), j + 1, mapping, used)

        # Try every possible substring
        for k in range(i, len(s)):
            cand = s[i:k + 1]
            if cand in used:
                continue
            mapping[c] = cand
            used.add(cand)
            if dfs(k + 1, j + 1, mapping, used):
                return True
            del mapping[c]
            used.remove(cand)
        return False

    return dfs(0, 0, {}, set())
```

> **Pythonic Highlights** – `s.startswith(val, i)` is a fast prefix check; `set` ensures bijectivity in O(1).  

---

### 3️⃣ C++ (STL, Recursive)

```cpp
class Solution {
public:
    bool wordPatternMatch(string pattern, string s) {
        unordered_map<char, string> mp;
        unordered_set<string> used;
        return backtrack(s, 0, pattern, 0, mp, used);
    }

private:
    bool backtrack(const string& s, int i, const string& pattern, int j,
                   unordered_map<char, string>& mp, unordered_set<string>& used) {
        if (i == s.size() && j == pattern.size()) return true;
        if (i == s.size() || j == pattern.size()) return false;

        char c = pattern[j];
        auto it = mp.find(c);

        // Existing mapping
        if (it != mp.end()) {
            const string& val = it->second;
            if (s.compare(i, val.size(), val) != 0) return false;
            return backtrack(s, i + val.size(), pattern, j + 1, mp, used);
        }

        // New mapping: try all substrings
        for (int k = i; k < s.size(); ++k) {
            string cand = s.substr(i, k - i + 1);
            if (used.count(cand)) continue;

            mp[c] = cand;
            used.insert(cand);

            if (backtrack(s, k + 1, pattern, j + 1, mp, used)) return true;

            mp.erase(c);
            used.erase(cand);
        }
        return false;
    }
};
```

> **C++ Efficiency** – `s.compare(i, len, val)` does a quick substring comparison; `unordered_map / unordered_set` give O(1) average lookup.

---

## ⏱️ Time & Space Complexity

- **Worst‑Case**:  
  - Every pattern character can map to any of the remaining substrings.  
  - The search space ≈ `O(n^m)` where `n = s.length()` and `m = pattern.length()`.  
  - With `n, m ≤ 20`, this is practically fast (most branches are pruned early).  
- **Space**:  
  - `O(m)` recursion stack + `O(m)` for the mapping + `O(m)` for the used set.  
  - Total: **O(m)** extra space, negligible for the constraints.

---

## 🔍 Edge‑Case Tricks (Interview Tips)

| Issue | Fix |
|-------|-----|
| **Duplicate mapping** (`"abba"` → `"xyz"` for `a` and `b`) | Use a *used* set to block reuse. |
| **Off‑by‑one substring** (`s.substr(i, k-i+1)`) | Remember inclusive end index `k`. |
| **Empty substring** | The loop starts at `i` and stops at `len(s)-1`; `k-i+1 >= 1`. |
| **Stack overflow** | Depth ≤ 20 → safe in Java/Python/C++; if you run into deeper patterns, convert to an explicit stack. |
| **Early pruning** | If the mapping doesn’t match the next part of `s`, skip the rest of the loop. |

---

## 🎯 Interview‑Ready Talking Points

1. **Clarify the bijection requirement**  
   *“Each letter must map to a unique substring; no two letters can share the same block.”*

2. **Explain your backtracking strategy**  
   *“I recursively assign substrings to characters, storing the assignment in a dictionary, and immediately backtrack if the prefix of the input string doesn’t match.”*

3. **Show how you handle pruning**  
   *“Using `startsWith`/`s.compare`, I can detect a mismatch in constant time, cutting off entire branches.”*

4. **Complexity discussion**  
   *“Worst‑case is exponential, but the length constraints make it tractable. In practice, the algorithm runs in milliseconds.”*

5. **Optional optimisations**  
   - Memoising `(i, j)` pairs (dynamic programming over state).  
   - Pre‑computing prefix sums to cut branches faster.

---

## 📌 Quick Reference Summary

| Language | Key Data Structures | Recursion Depth | Bijectivity Check |
|----------|---------------------|-----------------|-------------------|
| **Java** | `HashMap<Character,String>` + `HashSet<String>` | ≤ 20 | `used.contains(candidate)` |
| **Python** | `dict` + `set` | ≤ 20 | `cand in used` |
| **C++** | `unordered_map<char,string>` + `unordered_set<string>` | ≤ 20 | `used.count(cand)` |

---

## 🎉 Final Verdict

- **Correctness:** ✔️ Each algorithm guarantees a one‑to‑one mapping and covers all partition possibilities.  
- **Efficiency:** For the given constraints, runs comfortably within time limits.  
- **Readability:** Java code is production‑ready; Python code is concise; C++ code is STL‑friendly.  
- **Interviewability:** The problem is a great showcase of recursion, backtracking, and careful state management—perfect for a top‑tier interview.

---

### 🎯 Next Steps for Your Interview Prep

1. **Implement each version yourself** – copy‑paste won’t earn points.  
2. **Run the provided test cases** plus *your own* edge cases (e.g., `pattern="a"`, `s="abcd"`).  
3. **Explain the bijection constraint** – a key part of the problem statement.  
4. **Practice a walk‑through** – narrate the recursion tree, how you prune branches, and when you backtrack.  

Happy coding and best of luck on your next technical interview! 🚀