---
title: LeetCode 555. Split Concatenated Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Answer**

The easiest way to construct a *maximum string* is to reverse every input string only
if it makes the string larger.  
After that the problem is reduced to a single string:  
`S = s1' + s2' + … + sn'`.  
This string is already the lexicographically greatest one that can be produced by
any sequence of “reversals” on the individual input strings.  
(If you reverse a string and it becomes smaller, you would never do it,
because a larger string is always preferable – there is no advantage to
making it smaller.)

After this construction the task of finding the *maximum string* is trivial:
`S` is already the largest string possible, so it is also the “maximum string”
of the original list.

--------------------------------------------------------------------

### 1.  Algorithm

```
maxString(strings):
    result = ""
    for each string s in strings:
        rev = reverse(s)
        if rev > s          // lexicographic comparison
            result += rev
        else
            result += s
    return result
```

* **Time complexity** –  
  Each string `s` is examined once.  
  The comparison of `rev` and `s` takes `O(|s|)` time (the lengths are at most
  10 000).  
  Hence the whole routine is `O(n·k)` where `n ≤ 10 000` and `k ≤ 10 000`.

* **Space complexity** –  
  Only a few auxiliary variables are needed; the memory consumption is
  `O(k)` (the length of the final string).

--------------------------------------------------------------------

### 2.  Correctness Proof

We prove that the algorithm returns the lexicographically greatest string
that can be produced by any sequence of reversals of the input strings.

---

#### Lemma 1  
For any input string `s` the string `max(s, reverse(s))` is at least as large
as any other string that can be obtained from `s` by a single reversal.

*Proof.*  
If `s` is lexicographically larger than `reverse(s)`, then any string that
starts with `s` is not larger than a string that starts with `reverse(s)`,
so choosing `s` is optimal.  
If `reverse(s)` is larger, the same argument holds. ∎



#### Lemma 2  
Let `T = t1 + t2 + … + tm` be any string that can be obtained from the
input strings after a series of reversals.  
Let `T' = t1' + t2' + … + tm'` be the string produced by the algorithm.
Then `T' ≥ T`.

*Proof.*  
We proceed by induction over the position of the first difference between
`T` and `T'`.

*Base case.*  
If all strings are identical (`t1 = t1'`, …, `tm = tm'`), the statement is
obviously true.

*Induction step.*  
Assume the first `i−1` strings of `T` and `T'` are equal.  
At position `i` the algorithm chooses between  
`si + rest` and `reverse(si) + rest`, where `rest` is the rest of the
string that is fixed by the later strings.
By Lemma 1 the chosen option is not smaller than the other one.
Hence the first string in which `T` and `T'` differ is not larger than the
corresponding part of `T'`.  
Consequently `T' ≥ T`. ∎



#### Theorem  
`maxString` returns the lexicographically maximum string that can be produced
by any sequence of reversals of the input strings.

*Proof.*  
Let `S` be the string returned by the algorithm.
For any other string `T` that can be produced by a sequence of reversals,
Lemma 2 gives `S ≥ T`.  
Therefore `S` is the lexicographically greatest among all obtainable
strings, which is exactly the definition of a *maximum string*. ∎



--------------------------------------------------------------------

### 3.  Reference Implementation (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int n;
    if (!(cin >> n)) return 0;
    string result;
    result.reserve(n * 10);              // a small optimisation
    
    for (int i = 0; i < n; ++i) {
        string s; 
        cin >> s;
        string rev = s;
        reverse(rev.begin(), rev.end());
        if (rev > s)            // lexicographic comparison
            result += rev;
        else
            result += s;
    }
    
    cout << result << '\n';
    return 0;
}
```

The program follows the algorithm described above:
for every input string it keeps the larger of the string itself and its
reversed form, concatenates all the chosen strings and finally prints the
result.  
The code satisfies the required time and memory limits.