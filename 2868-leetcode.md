---
title: LeetCode 2868. The Wording Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every move we have a word that was already spoken.  
The next player may speak only

* a word that is **lexicographically larger** than the last word and
* a word that starts with the **same first letter** or with the **next**
  letter in the alphabet.

If the player that has to speak next has no such word, he loses immediately.

The task is to decide whether Alice (the owner of array `a`) can win
when she speaks the first word `a[0]`.

--------------------------------------------------------------------

#### 1.  Observations

* Both arrays `a` and `b` are already sorted.
* The whole game only depends on the relative order of the words,  
  not on which array they belong to.
* For a given last word we only have to look at words that are **strictly
  larger**.  
  If we process the words in **reverse lexicographical order** all
  candidates for the next move are already evaluated – perfect for
  dynamic programming.

--------------------------------------------------------------------

#### 2.  Global state

```
owner  : 0 → Alice, 1 → Bob
idx    : index of the word inside its owner’s array
dp[o][i]  : true  → the owner `o` has just spoken word `i`
            and the *other* player will eventually lose
            (the owner can win the whole game)
          false → the owner will lose
```

The game ends when the player that has to speak next cannot find a
valid word – this is exactly the situation described by the definition
of `dp`.

--------------------------------------------------------------------

#### 3.  Data structures

| Purpose                     | Implementation                                 |
|-----------------------------|-----------------------------------------------|
| All words sorted together   | List of objects `{word, owner, idx}`          |
| Fast “next word” queries    | Binary search on the owner’s own array        |
| Mapping word → index       | Hash‑map (Java `HashMap`, Python `dict`)     |

The total number of words is at most `2·10⁵`, so all structures easily
fit into memory.

--------------------------------------------------------------------

#### 4.  The DP recurrence

For a word `w` spoken by owner `o`:

```
op = 1 - o   // opponent

candidates =  words of owner op that
             • start with w[0]        or
             • start with nextLetter(w[0])
             • are lexicographically larger than w
```

*If there is **no** candidate*, the opponent cannot move →  
`dp[o][idx] = true`.

*If there is at least one candidate*, the owner wins
iff **some** candidate leads to the opponent’s loss:

```
dp[o][idx] = true  // initially assume we can win
for each candidate nextIdx:
        if dp[op][nextIdx] == false:   // opponent will lose
                break                   // owner wins
        else
                dp[o][idx] = false       // all candidates lead to opponent win
```

Because we iterate over the words in **descending** lexicographical
order, `dp[op][nextIdx]` is already known.

--------------------------------------------------------------------

#### 5.  Algorithm

```
1. Read a[ ], b[ ]   // already sorted
2. Build a global list ALL = { (word, owner, indexInOwnerArray) } for
   every word of a and b.  Sort ALL lexicographically.
3. Create dp[2][len(a)] and dp[2][len(b)]   // initially all false
4. for each entry of ALL from the last to the first:
        owner = entry.owner
        idx   = entry.idx
        lastWord = entry.word
        opponent = 1 - owner
        foundCandidate = false
        win = true            // will win if opponent has no move

        for letter in [lastWord[0], nextLetter(lastWord[0])]:
                // binary search in opponent’s array for first word > lastWord
                nextIdx = first index in opponentArray where
                          word > lastWord and word starts with letter
                if nextIdx exists:
                        foundCandidate = true
                        if dp[opponent][nextIdx] == false:
                                win = true   // opponent will lose
                                break
                        else
                                win = false  // opponent will win
        if not foundCandidate:
                win = true
        dp[owner][idx] = win

5. The answer is dp[0][0]  // after Alice speaks a[0] it is Bob’s turn
```

--------------------------------------------------------------------

#### 6.  Correctness Proof  

We prove that the algorithm returns **true** iff Alice can force a win.

---

##### Lemma 1  
For any word `w` spoken by its owner `o`, after the algorithm processed
`w` we have

```
dp[o][idx] = true  ⇔  starting from state
                      (last word = w, next to play = 1-o)
                      the owner `o` can force a win.
```

**Proof.**

We process words in strictly decreasing lexicographical order.
Hence when `w` is processed all words lexicographically larger
(and therefore all candidates for the next move) already have their
`dp` values computed.

*If the opponent has no candidate*:  
`dp[o][idx]` stays `true`.  
The opponent cannot move, so `o` wins immediately – statement holds.

*If the opponent has at least one candidate*:  
`dp[o][idx]` is set to `true` only if there exists a candidate `c`
such that `dp[1-o][c] == false`.  
`dp[1-o][c] == false` means the owner of `c` (the opponent)
will eventually lose, therefore `o` wins – statement holds.

*If all candidates lead to a win for the opponent*:  
`dp[o][idx]` becomes `false`.  
In this case `o` loses – statement holds.

∎



##### Lemma 2  
After Alice plays the first word `a[0]` the game is won by Alice
iff `dp[0][0]` is `true`.

**Proof.**

The state after Alice’s first move is *exactly* the state used in the
definition of `dp[0][0]`:

* Last spoken word is `a[0]` (owned by Alice, index `0`).
* Next to play is Bob.

Therefore, by Lemma&nbsp;1, `dp[0][0]` tells whether Alice can force a
win from this position.

∎



##### Theorem  
The algorithm outputs **true** iff Alice can win the game.

**Proof.**

By Lemma&nbsp;2 the algorithm outputs `dp[0][0]`.  
By Lemma&nbsp;1 this value is true exactly when Alice can force a win.
Thus the algorithm is correct.

∎



--------------------------------------------------------------------

#### 7.  Complexity Analysis

Let  

```
n = a.length   (1 ≤ n ≤ 10⁵)
m = b.length   (1 ≤ m ≤ 10⁵)
```

*Sorting the global list*: `O((n+m) log(n+m))`  
*Processing each word*: two binary searches → `O(log n)` or `O(log m)`  
*Total*: `O((n+m) log (n+m))` time, `O(n+m)` memory.

--------------------------------------------------------------------

#### 7.  Reference Implementation

##### Java 17

```java
import java.io.*;
import java.util.*;

public class Main {

    /* ---------- data structure for a global word ---------- */
    private static class WordEntry {
        String word;
        int owner;          // 0 – Alice, 1 – Bob
        int idx;            // index inside its owner array

        WordEntry(String w, int o, int i) {
            word = w;
            owner = o;
            idx = i;
        }
    }

    /* ---------- helper: next alphabetic letter ---------- */
    private static char nextLetter(char c) {
        return (char) (c + 1);          // 'a' -> 'b', ..., 'z' -> '{'
    }

    /* ---------- binary search: first word > key and starting with letter ---------- */
    private static int firstGreaterWithPrefix(List<String> arr, String key, char prefix) {
        int lo = 0, hi = arr.size();
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            String cur = arr.get(mid);
            if (cur.compareTo(key) <= 0) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        // lo is first index with arr[lo] > key
        if (lo < arr.size() && arr.get(lo).charAt(0) == prefix) {
            return lo;
        }
        return -1;
    }

    /* ---------- main ---------- */
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        String[] a = br.readLine().trim().split("\\s+");
        String[] b = br.readLine().trim().split("\\s+");

        int n = a.length;
        int m = b.length;

        // global list
        List<WordEntry> all = new ArrayList<>(n + m);
        for (int i = 0; i < n; ++i) all.add(new WordEntry(a[i], 0, i));
        for (int i = 0; i < m; ++i) all.add(new WordEntry(b[i], 1, i));

        all.sort(Comparator.comparing(e -> e.word));

        // dp arrays
        boolean[][] dp = new boolean[2][];
        dp[0] = new boolean[n];
        dp[1] = new boolean[m];

        // Process in reverse order
        for (int pos = all.size() - 1; pos >= 0; --pos) {
            WordEntry e = all.get(pos);
            int owner = e.owner;
            int idx = e.idx;
            String last = e.word;
            char first = last.charAt(0);
            char next = nextLetter(first);

            boolean foundCandidate = false;
            boolean win = true;                 // default: we win if opponent cannot move

            int op = 1 - owner;
            List<String> oppArray = (op == 0) ? Arrays.asList(a) : Arrays.asList(b);

            // try both possible first letters
            for (char letter : new char[]{first, next}) {
                int nextIdx = firstGreaterWithPrefix(oppArray, last, letter);
                if (nextIdx != -1) {
                    foundCandidate = true;
                    if (!dp[op][nextIdx]) {    // opponent will lose
                        win = true;
                        break;
                    } else {                   // opponent will win
                        win = false;
                    }
                }
            }
            if (!foundCandidate) win = true;
            dp[owner][idx] = win;
        }

        System.out.println(dp[0][0] ? "true" : "false");
    }
}
```

##### Python 3

```python
import sys
import bisect

def solve(a, b):
    n, m = len(a), len(b)

    # global list of all words
    all_words = []
    for i, w in enumerate(a):
        all_words.append((w, 0, i))
    for i, w in enumerate(b):
        all_words.append((w, 1, i))
    all_words.sort(key=lambda x: x[0])          # lexicographic order

    dp_a = [False] * n
    dp_b = [False] * m

    # helper: first index in arr that is > key and starts with letter
    def first_greater_with_prefix(arr, key, letter):
        idx = bisect.bisect_right(arr, key)
        while idx < len(arr) and arr[idx][0] != letter:
            idx += 1
        return idx if idx < len(arr) else -1

    for word, owner, idx in reversed(all_words):
        opp = 1 - owner
        found = False
        win = True                         # will win if opponent has no move

        for letter in (word[0], chr(ord(word[0]) + 1)):
            if opp == 0:
                arr = a
                dp_opp = dp_a
            else:
                arr = b
                dp_opp = dp_b

            # binary search for first word > word that starts with 'letter'
            # arr is a list of strings
            lo = bisect.bisect_right(arr, word)
            # ensure the first character matches
            while lo < len(arr) and arr[lo][0] != letter:
                lo += 1

            if lo < len(arr):
                found = True
                if not dp_opp[lo]:
                    win = True
                    break
                else:
                    win = False

        if not found:
            win = True

        if owner == 0:
            dp_a[idx] = win
        else:
            dp_b[idx] = win

    # after Alice speaks a[0] it is Bob's turn
    return dp_a[0]


if __name__ == "__main__":
    a = sys.stdin.readline().split()
    b = sys.stdin.readline().split()
    print("true" if solve(a, b) else "false")
```

--------------------------------------------------------------------

#### 7.  Reference Implementation – C++ (for completeness)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Entry{
    string w;
    int owner;          // 0: Alice, 1: Bob
    int idx;            // index in its own array
};

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    string line;
    // read whole line, split by whitespace
    vector<string> a, b;
    getline(cin, line);
    stringstream ss(line);
    string tok;
    while (ss >> tok) a.push_back(tok);
    getline(cin, line);
    stringstream ss2(line);
    while (ss2 >> tok) b.push_back(tok);

    int n = a.size(), m = b.size();

    vector<Entry> all;
    all.reserve(n+m);
    for(int i=0;i<n;i++) all.push_back({a[i],0,i});
    for(int i=0;i<m;i++) all.push_back({b[i],1,i});

    sort(all.begin(), all.end(), [](const Entry& x, const Entry& y){
        return x.w < y.w;
    });

    vector<vector<char>> dp(2);
    dp[0].assign(n,0);
    dp[1].assign(m,0);

    auto firstGreater = [&](const vector<string>& arr, const string& key, char letter){
        int l=0,r=arr.size();
        while(l<r){
            int mid=(l+r)/2;
            if(arr[mid] <= key) l=mid+1;
            else r=mid;
        }
        if(l< arr.size() && arr[l][0]==letter) return l;
        return -1;
    };

    for(int pos=(int)all.size()-1;pos>=0;--pos){
        const auto &e = all[pos];
        int owner=e.owner, idx=e.idx;
        const string &last=e.w;
        int opp=1-owner;
        bool win=true;      // will win if opponent has no move
        bool found=false;

        char letters[2] = { last[0], (char)(last[0]+1) };

        for(char letter: letters){
            int nextIdx;
            if(opp==0){
                nextIdx = firstGreater(a, last, letter);
            }else{
                nextIdx = firstGreater(b, last, letter);
            }
            if(nextIdx!=-1){
                found=true;
                if(opp==0){
                    if(!dp[0][nextIdx]){ win=true; break;}
                    else win=false;
                }else{
                    if(!dp[1][nextIdx]){ win=true; break;}
                    else win=false;
                }
            }
        }
        if(!found) win=true;
        if(owner==0) dp[0][idx]=win;
        else dp[1][idx]=win;
    }
    cout << (dp[0][0]?"true":"false") << '\n';
}
```

--------------------------------------------------------------------

### 8.  Conclusion

The problem reduces to checking whether the last processed word can be beaten by the opponent in a deterministic game.  
By exploring the game backwards (from the lexicographically largest word) and applying simple DP with binary searches, we can determine the winner in `O((n+m) log(n+m))` time.  
All reference implementations above adhere to the problem constraints and are fully compatible with Java 17, Python 3 and (for completeness) C++17.