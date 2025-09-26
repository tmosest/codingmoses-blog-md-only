---
title: LeetCode 3324. Find the Sequence of Strings Appeared on the Screen - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3324 – “Find the Sequence of Strings Appeared on the Screen”

> **Problem**  
> Alice has a very special keyboard with only two keys:  
> 1️⃣ **Key 1** – append `'a'` to the string on the screen.  
> 2️⃣ **Key 2** – change the *last* character of the string to the next letter in the alphabet (wrapping `z → a`).  
>  
> Starting from an empty string, she types a target string `target` using the *minimum* number of key presses.  
>  
> **Goal** – return every intermediate string that appears on the screen in the exact order of appearance.

> **Input** – `target` (1 ≤ len ≤ 400, only lowercase letters).  
> **Output** – `List<String>` of all intermediate strings.

---

### 1️⃣ The “good” – Why the solution is elegant

* **Deterministic simulation** – Once we know the current string and the next target character, there is a *unique* optimal move sequence.  
* **Linear time & space** – We traverse `target` once, and every key press is represented by a string we immediately push to the answer list.  
* **No hidden tricks** – The algorithm uses only simple loops and character arithmetic.

---

### 2️⃣ The “bad” – Things to be mindful of

| Issue | Why it matters | How we mitigate |
|-------|----------------|-----------------|
| **Large output** | In the worst case (`target = "z"*400`) we may output ~13,200 strings. | Constraints are small (400), so this is fine for LeetCode. |
| **String immutability in Java/Python** | Re‑creating strings repeatedly can be expensive. | Use `StringBuilder` (Java) / `list` + `''.join()` (Python) / `std::string` (C++). |
| **Edge‑case: first character** | The screen is empty; we must start with `Key 1` before any increments. | Special‑case the first character separately. |

---

### 3️⃣ The “ugly” – A few subtle corner cases

| Corner | Problem | Fix |
|--------|---------|-----|
| `ch == last` | You might think no key press is needed, but to get a *new* character we must still press **Key 1**. | Explicitly handle `==` separately. |
| `ch < last` | Incrementing the last character can never decrease it; we must **append** first. | Append `'a'`, then increment until `ch`. |
| `last == 'z'` | Increment wraps to `'a'`; if `ch` is `'a'`, we just press **Key 1** again. | The algorithm naturally handles it. |

---

## 📦 Implementation (Java, Python, C++)

Below are clean, production‑ready implementations for the three most popular interview languages.

---

### Java

```java
import java.util.*;

public class Solution {
    public List<String> stringSequence(String target) {
        List<String> result = new ArrayList<>();
        StringBuilder cur = new StringBuilder();

        for (int i = 0; i < target.length(); i++) {
            char ch = target.charAt(i);

            if (cur.length() == 0) {                // first character
                cur.append('a');
                result.add(cur.toString());
                while (cur.charAt(cur.length() - 1) < ch) {
                    char last = cur.charAt(cur.length() - 1);
                    cur.setCharAt(cur.length() - 1, (char)(last + 1));
                    result.add(cur.toString());
                }
                continue;
            }

            char last = cur.charAt(cur.length() - 1);

            if (ch > last) {                          // only increment
                while (last < ch) {
                    last++;
                    cur.setCharAt(cur.length() - 1, last);
                    result.add(cur.toString());
                }
            } else if (ch == last) {                  // append new 'a'
                cur.append('a');
                result.add(cur.toString());
            } else {                                  // ch < last: append then increment
                cur.append('a');
                result.add(cur.toString());
                last = 'a';
                while (last < ch) {
                    last++;
                    cur.setCharAt(cur.length() - 1, last);
                    result.add(cur.toString());
                }
            }
        }

        return result;
    }
}
```

---

### Python

```python
class Solution:
    def stringSequence(self, target: str) -> list[str]:
        res = []
        cur = []

        for ch in target:
            if not cur:                 # first character
                cur.append('a')
                res.append(''.join(cur))
                while cur[-1] < ch:
                    cur[-1] = chr(ord(cur[-1]) + 1)
                    res.append(''.join(cur))
                continue

            last = cur[-1]
            if ch > last:               # only increment
                while last < ch:
                    last = chr(ord(last) + 1)
                    cur[-1] = last
                    res.append(''.join(cur))
            elif ch == last:            # append new 'a'
                cur.append('a')
                res.append(''.join(cur))
            else:                       # ch < last: append then increment
                cur.append('a')
                res.append(''.join(cur))
                last = 'a'
                while last < ch:
                    last = chr(ord(last) + 1)
                    cur[-1] = last
                    res.append(''.join(cur))

        return res
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> stringSequence(string target) {
        vector<string> res;
        string cur;

        for (char ch : target) {
            if (cur.empty()) {                 // first character
                cur.push_back('a');
                res.push_back(cur);
                while (cur.back() < ch) {
                    cur.back() = char(cur.back() + 1);
                    res.push_back(cur);
                }
                continue;
            }

            char last = cur.back();

            if (ch > last) {                    // only increment
                while (last < ch) {
                    last = char(last + 1);
                    cur.back() = last;
                    res.push_back(cur);
                }
            } else if (ch == last) {            // append new 'a'
                cur.push_back('a');
                res.push_back(cur);
            } else {                            // ch < last: append then increment
                cur.push_back('a');
                res.push_back(cur);
                last = 'a';
                while (last < ch) {
                    last = char(last + 1);
                    cur.back() = last;
                    res.push_back(cur);
                }
            }
        }
        return res;
    }
};
```

---

## 📈 Time / Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java     | **O(|target|)** key presses → O(total output) | **O(total output)** strings |
| Python   | **O(|target|)** | **O(total output)** |
| C++      | **O(|target|)** | **O(total output)** |

Because each key press generates exactly one string that we append to the answer list, the algorithm is *optimal* and *simple*.

---

## 🎯 Why this solution shines in interviews

* **Pattern recognition** – Recognizing that the optimal path is deterministic saves you from exploring a huge search space.  
* **Clear logic** – Every branch of the algorithm corresponds to a distinct user action (`append` or `increment`).  
* **Adaptable** – The same idea works for any string‑generation puzzle with a limited action set.

If you want to impress interviewers on **LeetCode** or at your next **coding interview**, this pattern‑matching trick will be a valuable tool in your repertoire.

---

## 🔑 SEO‑Ready Summary (for your LinkedIn / portfolio)

* **Keywords** – LeetCode, string sequence, keyboard simulation, interview algorithm, Java solution, Python solution, C++ solution, coding interview, coding challenge, software engineer interview, algorithmic problem solving.  
* **Meta‑description** – “Discover a linear‑time solution to LeetCode 3324 – Find the Sequence of Strings Appeared on the Screen. Detailed Java, Python & C++ code, explanation, edge‑case handling, and interview‑ready insights.”  
* **Target audience** – Software engineers, hiring managers, interview coaches, coding bootcamps, students preparing for technical interviews.  

---

### 📚 Next Steps

1. **Run the code** – Copy the implementation into your LeetCode sandbox and hit “Run”.  
2. **Explore variations** – Try a version where the first key can be any letter (`Key 1` = any letter) – how does the logic change?  
3. **Add unit tests** – Write tests for edge‑cases (`"aa"`, `"az"`, `"ba"`, `"abc"`) to cement your understanding.  

Happy coding, and may your interviews be as smooth as Alice’s key presses!