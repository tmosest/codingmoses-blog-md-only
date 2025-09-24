---
title: LeetCode 1772. Sort Features by Popularity - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 1772 – “Sort Features by Popularity”
> **The good, the bad, and the ugly** of solving a medium‑level frequency‑counting problem  

> **Keywords**: LeetCode 1772, Sort Features by Popularity, Java, Python, C++, interview, algorithm, hash map, stable sort, complexity, job interview tips

---

## 🚀 Quick Overview

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| **Java** | O(F + R + F log F) | O(F + R) | Uses `HashMap` + `Arrays.sort` with a custom comparator. |
| **Python** | O(F + R + F log F) | O(F + R) | Uses `collections.Counter` + `sorted`. |
| **C++** | O(F + R + F log F) | O(F + R) | Uses `unordered_map` + `sort` with a lambda. |

> **F** – number of features (`≤ 10⁴`)  
> **R** – number of responses (`≤ 10²`, each up to `10³` chars)

---

## 🧩 Problem Recap

You’re given:

- `features`: an array of **unique** words (length ≤ 10).
- `responses`: an array of sentences, each word separated by a single space.

**Popularity** of a feature = number of responses that contain the feature **at least once**.

Sort the `features` array in **non‑increasing** order of popularity.  
If two features tie, keep the original order (stable sort).

---

## 🔎 What Makes This Problem “Medium”

1. **Deduplication per response** – a single response may mention a feature multiple times, but it should count only once.
2. **Stable ordering** – you cannot simply sort by frequency; ties must respect the original index.
3. **Large input size** – up to 10⁴ features, so an O(F²) approach would time‑out.

---

## 🎯 Solution Idea

1. **Frequency map** – store a count for each feature.  
   Use a `HashMap<String,Integer>` (Java), `unordered_map<string,int>` (C++), or `Counter` (Python).

2. **Process each response**  
   * Split into words.  
   * Put the words into a `HashSet` / `unordered_set` / `set` to keep only unique words.  
   * For every unique word that exists in the feature set, increment its frequency.

3. **Sort**  
   * In Java: `Arrays.sort(features, comparator)` – comparator looks up the frequency from the map.  
   * In Python: `sorted(features, key=lambda f: (-freq[f], index_map[f]))`.  
   * In C++: `std::sort(features.begin(), features.end(), comp)` with a lambda that uses the map.

The comparator automatically keeps the original index for ties because the `features` array is sorted **in place**, and the original order is preserved when frequencies are equal (stable sort behaviour of the Java/Python `sorted` and C++ `sort` with a stable key).

---

## 📄 Code

### Java (Java 17)

```java
import java.util.*;

class Solution {
    public String[] sortFeatures(String[] features, String[] responses) {
        // 1. Frequency map
        Map<String, Integer> freq = new HashMap<>();
        for (String f : features) {
            freq.put(f, 0);
        }

        // 2. Count appearances
        for (String response : responses) {
            String[] words = response.split(" ");
            Set<String> seen = new HashSet<>(Arrays.asList(words));
            for (String w : seen) {
                if (freq.containsKey(w)) {
                    freq.put(w, freq.get(w) + 1);
                }
            }
        }

        // 3. Stable sort by frequency (descending)
        // Java's Arrays.sort is stable only for objects,
        // but using a comparator that keeps the original order works.
        Integer[] idx = new Integer[features.length];
        for (int i = 0; i < features.length; i++) idx[i] = i;

        Arrays.sort(idx, (i, j) -> {
            int fi = freq.get(features[i]);
            int fj = freq.get(features[j]);
            if (fi != fj) return Integer.compare(fj, fi); // desc
            return Integer.compare(i, j); // original order
        });

        // Build result
        String[] result = new String[features.length];
        for (int i = 0; i < features.length; i++) {
            result[i] = features[idx[i]];
        }
        return result;
    }
}
```

> **Why this Java version?**  
> *Uses `Integer[]` for stable ordering because `Arrays.sort` on primitive `int[]` is not stable.*  
> *The index array keeps original indices for tie‑breaking.*

---

### Python 3 (≥ 3.7)

```python
from collections import Counter
from typing import List

class Solution:
    def sortFeatures(self, features: List[str], responses: List[str]) -> List[str]:
        # Frequency map
        freq = Counter({f: 0 for f in features})

        # Count appearances per response
        for resp in responses:
            words = resp.split()
            for w in set(words):
                if w in freq:
                    freq[w] += 1

        # Original index map for tie‑breaking
        idx = {f: i for i, f in enumerate(features)}

        # Sort: first by -freq (desc), then by original index (asc)
        return sorted(features, key=lambda f: (-freq[f], idx[f]))
```

> **Why Python?**  
> *Built‑in `Counter` + `sorted` makes the code concise.  
> *The lambda gives O(F log F) sorting, which is optimal.*

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> sortFeatures(vector<string>& features, vector<string>& responses) {
        // Frequency map
        unordered_map<string, int> freq;
        for (const string& f : features) freq[f] = 0;

        // Count appearances per response
        for (const string& resp : responses) {
            unordered_set<string> seen;
            string word;
            istringstream iss(resp);
            while (iss >> word) seen.insert(word);
            for (const string& w : seen)
                if (freq.find(w) != freq.end()) freq[w] += 1;
        }

        // Original index map
        unordered_map<string, int> idx;
        for (int i = 0; i < (int)features.size(); ++i) idx[features[i]] = i;

        // Sort with lambda
        sort(features.begin(), features.end(),
             [&](const string& a, const string& b) {
                 if (freq[a] != freq[b]) return freq[a] > freq[b]; // desc
                 return idx[a] < idx[b];                           // original
             });

        return features;
    }
};
```

> **C++ notes**  
> *Uses `istringstream` for word extraction and `unordered_set` for deduplication.  
> *Lambda captures the frequency & index maps by reference.*

---

## 📈 Complexity Analysis

| Step | Java | Python | C++ |
|------|------|--------|-----|
| Build freq map | **O(F)** | **O(F)** | **O(F)** |
| Process responses | **O(total words)** ≤ O(R × maxLen) | **O(total words)** | **O(total words)** |
| Deduplication per response | **O(words per resp)** | same | same |
| Sort | **O(F log F)** | **O(F log F)** | **O(F log F)** |
| **Total** | **O(F + R + F log F)** | same | same |
| **Space** | **O(F + R)** | same | same |

> **Why we’re not using a priority queue**  
> A min‑heap would give O(F log F) as well, but the simple stable sort is easier to reason about and less error‑prone in interview settings.

---

## ⚠️ Common Pitfalls (The Bad & Ugly)

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Counting duplicates across a response** | If you just split and loop, you’ll double‑count words appearing twice in the same sentence. | Deduplicate with a set before incrementing. |
| **Non‑stable sorting** | `Arrays.sort` on `int[]` is not stable, so tie‑breaks may shuffle features incorrectly. | Use an index array or an object array to preserve order. |
| **Splitting on spaces blindly** | Sentences may contain multiple spaces or leading/trailing spaces. | Use `split()` (Python), `istringstream` (C++), or `String.split(" ")` (Java). All ignore consecutive spaces by default. |
| **Large responses** | Splitting every response with `String.split()` is O(length of sentence). | Acceptable because R is small (`≤ 10²`). |
| **Hash collisions in unordered_map** | In C++ extreme inputs may trigger worst‑case O(F) per lookup. | Reserve bucket count: `unordered_map<string,int> freq; freq.reserve(features.size()*2);` |

---

## 🎤 Interview Tips

| Topic | What interviewers look for | How our solution shows it |
|-------|---------------------------|---------------------------|
| **Data structures** | Use of hash maps / hash sets for O(1) lookups. | All three codes build a hash map for frequency and a hash set for deduplication. |
| **Algorithmic thinking** | Decompose the problem into counting + sorting. | Clear separation of steps in explanation. |
| **Edge‑case handling** | Empty responses, features never appearing, ties. | Code accounts for all; `set()`/`unordered_set` ensures no double counting. |
| **Performance awareness** | Complexity O(F log F) is optimal given constraints. | Time & space analysis provided. |
| **Language features** | Use of built‑in libraries (`Counter`, `unordered_map`, `std::sort`). | Shows familiarity with standard tools. |

> **Show off your thought process**  
> When you’re asked to implement this, walk through the reasoning: “Why deduplication? Why we need stable sorting? How do we keep track of indices?” That narrative impresses interviewers far more than the final code.

---

## 🌟 The Good

- **Simplicity** – frequency counting + sorting is straightforward once you understand the constraints.
- **Language‑agnostic** – the same logic works in Java, Python, C++ and many other languages.
- **Stable sort** – built‑in sorting functions naturally preserve original order for ties, so you don’t need a separate pass.

---

## ⚠️ The Bad

- **Large input size** – naive O(F²) sorting (e.g., bubble sort) will TLE.  
- **Deduplication cost** – creating a `HashSet` per response can be expensive if responses are long, but here the response count is small, so it’s fine.
- **Memory overhead** – storing all words of all responses in a set could double memory usage if not careful; we only keep a set per response to keep the memory footprint low.

---

## 😱 The Ugly

- **Using a non‑stable sort with a simple comparator**  
  *In Java, `Arrays.sort` on primitive types is **not** stable, so you must handle indices yourself (as in the code above).*

- **Misinterpreting “at least once”**  
  *Many candidates count total mentions, not unique responses.*  
  *This leads to wrong answers on test cases like:*
  ```text
  features = ["alpha", "beta"]
  responses = ["alpha alpha beta", "beta beta"]
  ```
  *Correct popularity: alpha = 1, beta = 2.*

- **Hard‑coded separators**  
  *Assuming only one space can cause crashes if the input contains tabs or multiple spaces.*  
  *Robust splitting with regex or stream extraction is safer.*

---

## 📚 Take‑Home Messages

1. **Frequency + deduplication** is a common interview pattern.  
2. **Keep original indices** handy for tie‑breaking or stable sorts.  
3. **Use standard libraries** to keep code clean and reduce bugs.  
4. **Analyze complexity early** – O(F log F) is the minimal cost for sorting `features`.

---

## 🎯 Ready to Ace Your Next Interview

- **Practice** the problem on LeetCode; it’s a perfect *frequency‑count* training ground.
- **Explain** the approach aloud; interviewers love to hear your reasoning.
- **Mention** the edge‑cases (empty sentences, duplicate words) – shows attention to detail.
- **Talk about performance** – why you chose O(F log F) sorting and not something slower.

> **Pro tip**: While coding, keep a mental checklist:  
> 1. Do we deduplicate per response?  
> 2. Is the sort stable or do we manually tie‑break?  
> 3. Did we handle all words that might not be in `features`?  

Once you can answer these confidently, you’ll impress hiring managers looking for clean, efficient, and reliable code.

---

## 📣 Final Thoughts

LeetCode 1772 may look “medium” on paper, but it hides subtle details that are golden interview material. Mastering this problem demonstrates:

- **Strong grasp of data structures** (hash maps, sets).  
- **Algorithmic clarity** (deduplication, stable sorting).  
- **Code‑quality awareness** (readability, edge‑case handling).

Show it off in your portfolio or GitHub – it’s a quick win for your next coding interview! Happy coding 🚀.