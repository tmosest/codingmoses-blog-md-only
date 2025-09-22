---
title: LeetCode 1897. Redistribute Characters to Make All Strings Equal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 1897  
**Problem Statement**  
You’re given an array of strings `words`. In one operation you can pick two different indices `i` and `j` (with `words[i]` non‑empty) and move **any** single character from `words[i]` to **any** position in `words[j]`.  
Return **true** if it’s possible to make every string in `words` identical after any number of such operations, otherwise return **false**.

---

## 2.  Intuition  

If we can rearrange the characters arbitrarily between the strings, the *only* thing that matters is:

1. **Total length** – Every final string must have the same length, otherwise it’s impossible.  
2. **Character frequencies** – For each letter `a…z`, the total number of that letter must be divisible by `words.length`. If not, the remaining characters can never be split evenly among all strings.

The operation is essentially a *redistribution* of characters; the relative order of characters inside a string does not affect feasibility.

---

## 3.  Algorithm (Hash‑Table / Array Counter)

1. If `words.length == 1` → trivially true.  
2. Compute `totalLen = Σ |words[i]|`.  
   - If `totalLen % words.length != 0`, return `false`.  
3. Count frequencies of each letter in a 26‑element array `cnt[26]`.  
4. For every `cnt[c]`  
   - If `cnt[c] % words.length != 0`, return `false`.  
5. All checks passed → return `true`.

### Why It Works  

- **Necessity** –  
  *Length*: After all moves, every string will have length `totalLen / words.length`.  
  *Letter counts*: Each string will contain exactly `cnt[c] / words.length` of letter `c`. If any `cnt[c]` isn’t divisible, the integer division would leave a remainder that can’t be distributed.

- **Sufficiency** –  
  We can always move excess characters from longer strings to shorter ones. Because we can insert a character anywhere, we can reorder characters arbitrarily. Hence, if the two conditions above hold, a constructive sequence of moves exists (the proof is a simple greedy redistribution).

---

## 4.  Complexities  

| Measure | Complexity |
|---------|------------|
| **Time** | `O(N * M)` where `N = words.length`, `M` = average string length (at most 100) |
| **Space** | `O(1)` – only a fixed 26‑element array regardless of input size |

---

## 5.  Code Implementations  

### 5.1 Java  

```java
class Solution {
    public boolean makeEqual(String[] words) {
        // 1. Single string → already equal
        if (words.length == 1) return true;

        // 2. Total length must be divisible by number of strings
        int totalLen = 0;
        for (String w : words) totalLen += w.length();
        if (totalLen % words.length != 0) return false;

        // 3. Count frequencies
        int[] freq = new int[26];
        for (String w : words) {
            for (char ch : w.toCharArray()) freq[ch - 'a']++;
        }

        // 4. Each frequency must be divisible by words.length
        for (int f : freq) {
            if (f % words.length != 0) return false;
        }
        return true;
    }
}
```

### 5.2 Python  

```python
class Solution:
    def makeEqual(self, words: List[str]) -> bool:
        n = len(words)
        if n == 1:
            return True

        total_len = sum(len(w) for w in words)
        if total_len % n:
            return False

        # Counter for 26 lowercase letters
        freq = [0] * 26
        for w in words:
            for ch in w:
                freq[ord(ch) - 97] += 1

        return all(f % n == 0 for f in freq)
```

### 5.3 C++  

```cpp
class Solution {
public:
    bool makeEqual(vector<string>& words) {
        int n = words.size();
        if (n == 1) return true;

        int totalLen = 0;
        for (const auto& w : words) totalLen += w.size();
        if (totalLen % n) return false;

        array<int, 26> freq{};
        for (const auto& w : words)
            for (char ch : w) freq[ch - 'a']++;

        for (int f : freq)
            if (f % n) return false;
        return true;
    }
};
```

All three solutions are **O(1) extra space** and **O(N·M)** time, satisfying the problem’s constraints.

---

## 6.  Blog Article – “Redistribute Characters to Make All Strings Equal: The Good, The Bad, and The Ugly”

> **SEO Keywords**: LeetCode 1897, Redistribute Characters to Make All Strings Equal, interview problem, Java solution, Python solution, C++ solution, hash table, character counting, algorithmic interview, coding interview prep

---

### Introduction  

When preparing for a coding interview, one quickly learns that **problem‑solving style** often matters more than language specifics. LeetCode’s “Redistribute Characters to Make All Strings Equal” (Problem 1897) is a textbook example: the solution hinges on a simple invariant rather than clever data‑structures. In this article, we dissect the *good*, *bad*, and *ugly* aspects of this problem, explore the most efficient solution, and show how you can package your mastery into a job‑ready portfolio.

---

#### The Good: Simple Invariants, Clean Code  
- **Linear scan**: Only two passes over the data—one for total length, another for character counts.  
- **Constant space**: 26‑element array for lowercase letters, no dynamic containers.  
- **Universal applicability**: Works for Java, Python, C++, or any language with array support.  

#### The Bad: Edge Cases & Misconceptions  
- **Empty strings**: The problem guarantees `words[i].length >= 1`, but a naive implementation might still trip on an empty string when counting.  
- **Divisibility traps**: Forgetting to check `totalLen % words.length` is a common pitfall; it leads to correct outputs on many tests but fails on hidden ones.  
- **Assuming order matters**: Some contestants think they must preserve the original order of characters; the operation actually lets you insert anywhere, making order irrelevant.  

#### The Ugly: Over‑engineering & Performance Jitters  
- **Using hash maps**: A `HashMap<Character, Integer>` in Java or `unordered_map` in C++ works, but introduces unnecessary overhead.  
- **Recursive solutions**: Trying to simulate the operations recursively results in exponential time and stack overflow.  
- **Misreading constraints**: For strings up to 100 characters and up to 100 strings, the solution’s O(N·M) is trivial; over‑optimizing with suffix arrays or segment trees is a waste of effort.  

---

### The Core Idea – A Walkthrough  

Let’s walk through a **dry run** with `words = ["abc","aabc","bc"]`.

1. **Total length** = 3 + 4 + 2 = 9.  
   `9 % 3 == 0` → **possible length** = 3.  
2. **Character counts**  
   - `a`: 3  
   - `b`: 2  
   - `c`: 2  
   Each count is divisible by 3 → **feasible distribution**.  
3. **Result**: true.

---

### Code Show‑down  

#### Java (fast, type‑safe)  
(See section 5.1 above)

#### Python (concise, readable)  
(See section 5.2 above)

#### C++ (performance‑oriented)  
(See section 5.3 above)

Each snippet follows the exact same logic, differing only in syntax. Feel free to copy‑paste into your favorite IDE.

---

### Why This Problem Is Interview Gold  

- **Shows you understand invariants**: Candidates who grasp that the operation is a *redistribution* rather than a *reordering* tend to impress interviewers.  
- **Demonstrates clean coding**: Using a fixed‑size array instead of a map reflects thoughtful space optimization.  
- **Adaptable**: The solution can be extended to similar problems (e.g., “Make All Strings Equal With Swaps” or “Redistribute Numbers”).  

---

### How to Use This Article to Land a Job  

1. **Blog on Medium / Dev.to**  
   Post this article, tag it with `leetcode`, `coding-interview`, `algorithm`, `java`, `python`, `cpp`. The SEO keywords above will help recruiters searching for LeetCode solutions find you.  
2. **Add to GitHub**  
   Create a repo `leetcode-1897` containing the three code files, a README with the problem statement, and the blog link.  
3. **Leverage LinkedIn**  
   Share the article with a short caption: *“Solved LeetCode 1897 in 5 minutes with a clean O(1) solution. Check out my blog for the full walk‑through!”*  
4. **Apply to Companies**  
   Search for “Java backend”, “Python data engineer”, or “C++ systems engineer” roles that mention LeetCode in the job description. Attach a link to your blog or GitHub repo in your resume or cover letter.

---

### Final Takeaway  

The *Redistribute Characters to Make All Strings Equal* problem is a beautiful example of how a simple invariant can collapse a seemingly complex operation set into a trivial check. Master it, write clean code, share it, and let recruiters see the clarity of your thought process. Good luck on your interview journey!