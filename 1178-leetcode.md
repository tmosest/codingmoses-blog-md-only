---
title: LeetCode 1178. Number of Valid Words for Each Puzzle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For a word to be *valid* for a puzzle

* every letter of the word must appear in the puzzle
* the puzzle’s first letter must appear in the word

Because a puzzle has **exactly 7 distinct letters**, a word that contains more than 7
different letters can never be valid.  
The puzzle can contain each letter at most once, so for counting purposes we only
care about the *set* of letters of a word – duplicates do not matter.

--------------------------------------------------------------------

#### 1.  Represent a set of letters with a bitmask

For the 26 lowercase letters we use a 26‑bit integer.

```
bit i (0‑based) is 1  ⇔  the letter (i + 'a') is present
```

Example  

```
word = "apple"          → letters {a, p, l, e}
mask = 0b000...00101110 (bits for a,l,p,e are 1)
```

* Building a mask for a word is `O(|word|)`.
* Two words that use exactly the same set of letters get the same mask.

--------------------------------------------------------------------

#### 2.  Count words for every mask

`freq[mask]` = how many words in the input use exactly the letter set described
by `mask`.  
Only masks that contain at most 7 bits are stored – all other words are ignored
because they can never match a puzzle.

Building `freq` is linear in the total length of all words (≤ 3 × 10⁶).

--------------------------------------------------------------------

#### 3.  Enumerate all subsets of a puzzle that contain the first letter

For a puzzle `puz` let

```
mask_p = bitmask of all 7 letters of puz
first_bit = bit corresponding to puz[0]
```

All valid words for this puzzle must use a subset of `mask_p` that also
contains `first_bit`.  
The puzzle has only 7 letters, so there are at most `2^7 = 128` subsets.
We enumerate them efficiently by iterating over all submasks of `mask_p`:

```
sub = mask_p
while sub:
    if sub contains first_bit:
        answer += freq.get(sub, 0)
    sub = (sub - 1) & mask_p
```

The loop runs in `O(2^7)` per puzzle – fast enough for 10⁵ puzzles.

--------------------------------------------------------------------

#### 4.  Correctness Proof  

We prove that the algorithm returns the exact number of valid words for every
puzzle.

---

##### Lemma 1  
For every word `w` the algorithm increases `freq[mask(w)]` exactly once.

**Proof.**

* While processing a word we build a mask from its distinct letters
  (`mask(w)`).
* We increment `freq[mask(w)]` once after inserting the word into the trie
  (or directly into the dictionary).  
  No other step modifies `freq[mask(w)]`. ∎



##### Lemma 2  
For a puzzle `p` and any subset `S ⊆ letters(p)` that contains `p[0]`,
the algorithm adds `freq[mask(S)]` to the answer of `p`.

**Proof.**

During the enumeration of submasks of `mask(p)` every subset `S`
occurs exactly once as `sub`.  
If `sub` contains the first bit, the algorithm executes

```
answer += freq.get(sub, 0)   // sub == mask(S)
```

Thus `freq[mask(S)]` is added exactly once. ∎



##### Lemma 3  
A word `w` is counted in the answer for a puzzle `p` **iff**
`w` is a valid word for `p`.

**Proof.**

*(**If part**)*
Assume `w` is valid for `p`.  
All letters of `w` are in `p`, so `mask(w) ⊆ mask(p)`.  
Also `p[0] ∈ w`, so `mask(w)` contains the first bit.  
Therefore `mask(w)` appears as a submask `sub` of `mask(p)` that contains the
first bit, and by Lemma&nbsp;2 its frequency is added to the answer.

*(**Only if part**)*
Assume the algorithm adds `freq[mask(S)]` for some subset `S ⊆ letters(p)`
containing `p[0]`.  
Every word counted in this frequency uses exactly the letters of `S`.
All those letters are in `p` (by definition of `S`) and `p[0]` is in `S`,
hence every such word satisfies the two conditions for being a valid word
for `p`. ∎



##### Theorem  
For every puzzle `p` the algorithm outputs the exact number of valid words
from the input list.

**Proof.**

By Lemma&nbsp;3 a word contributes to the answer for `p` exactly when it is
valid for `p`.  
Lemma&nbsp;1 guarantees that the contribution of each word is counted once
in `freq[mask]`.  
Therefore the sum produced by the algorithm equals the number of valid words
for `p`. ∎



--------------------------------------------------------------------

#### 5.  Complexity Analysis

Let

```
N = number of words   (≤ 10^5)
M = number of puzzles (≤ 10^5)
```

*Building `freq`*  
`O(total word length)`  ≤ 3 × 10⁶

*Query per puzzle*  
Each puzzle enumerates ≤ 128 submasks  
`O(2^7) = O(1)`  →  `O(M)` overall

*Memory*  
At most one dictionary entry per distinct mask  
`≤ 2^26` but only those with ≤ 7 bits are stored – at most 2⁷ × N ≤ 10^5 entries.

--------------------------------------------------------------------

#### 6.  Reference Implementation (Python 3)

```python
from typing import List, Dict

class Solution:
    def findNumOfValidWords(self, words: List[str], puzzles: List[str]) -> List[int]:
        # ---------- build frequency dictionary ----------
        freq: Dict[int, int] = {}
        for w in words:
            mask = 0
            for ch in set(w):          # distinct letters only
                mask |= 1 << (ord(ch) - 97)
            # skip words that cannot match any puzzle
            if mask == 0 or mask.bit_count() > 7:
                continue
            freq[mask] = freq.get(mask, 0) + 1

        # ---------- answer for each puzzle ----------
        res = []
        for puz in puzzles:
            mask_p = 0
            for ch in puz:
                mask_p |= 1 << (ord(ch) - 97)
            first_bit = 1 << (ord(puz[0]) - 97)

            ans = 0
            sub = mask_p
            while sub:
                if sub & first_bit:
                    ans += freq.get(sub, 0)
                sub = (sub - 1) & mask_p
            res.append(ans)

        return res
```

The code follows exactly the algorithm proven correct above and is
fully compliant with the LeetCode function signature.

--------------------------------------------------------------------

#### 6.  Reference Implementation (C++)

If you prefer C++ the same idea can be written succinctly:

```cpp
class Solution {
public:
    vector<int> findNumOfValidWords(vector<string>& words, vector<string>& puzzles) {
        unordered_map<int,int> freq;   // mask -> count
        freq.reserve(words.size()*2);

        for (const string& w : words) {
            int mask = 0;
            for (char c : w) mask |= 1 << (c - 'a');
            if (__builtin_popcount((unsigned)mask) <= 7) {
                freq[mask]++;               // Lemma 1
            }
        }

        vector<int> ans;
        ans.reserve(puzzles.size());

        for (const string& puz : puzzles) {
            int mask = 0;
            for (char c : puz) mask |= 1 << (c - 'a');
            int firstBit = 1 << (puz[0] - 'a');

            int cnt = 0;
            int sub = mask;
            while (sub) {                     // enumerate submasks
                if (sub & firstBit) {         // contains first letter
                    auto it = freq.find(sub);
                    if (it != freq.end()) cnt += it->second;
                }
                sub = (sub - 1) & mask;
            }
            ans.push_back(cnt);
        }
        return ans;
    }
};
```

Both implementations run easily within the LeetCode limits.