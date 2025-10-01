---
title: LeetCode 2516. Take K of Each Character From Left and Right - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

The string `s` consists only of the three characters `'a'`, `'b'` and `'c'`.
After the deletions the remaining string must contain at least `k` copies of
each of the three characters.

Because we may delete *any* characters, we can always keep exactly `k`
copies of every character and delete all the others.
If we keep more than `k` copies of a character, no additional deletion is
necessary – we would only delete extra characters unnecessarily.

So the optimal strategy is:

* keep exactly `k` copies of `'a'`, exactly `k` copies of `'b'`
  and exactly `k` copies of `'c'`;
* delete every other character.

The number of deleted characters is simply

```
total length of s   –   (k + k + k)   =   |s| – 3·k
```

(If the string were shorter than `3·k` we could not satisfy the condition,
but the statement guarantees that a solution always exists, so this case
does not appear.)

The algorithm is therefore O(1) in time and memory.



--------------------------------------------------------------------

#### Algorithm
```
read s
read k
answer = |s| - 3 * k
output answer
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the minimum possible number of
deletions.

---

##### Lemma 1  
After deleting all characters except `k` copies of each of `'a'`,
`'b'` and `'c'` the remaining string satisfies the requirement
(contains at least `k` of every type).

**Proof.**  
The remaining string contains exactly `k` copies of each of the three
characters, which is certainly at least `k`. ∎



##### Lemma 2  
No solution can delete fewer than `|s| – 3·k` characters.

**Proof.**  
The remaining string must contain at least `k` copies of each of the three
characters, so it must contain at least `3·k` characters in total.
Therefore at least `|s| – 3·k` characters must be deleted. ∎



##### Lemma 3  
The algorithm deletes exactly `|s| – 3·k` characters.

**Proof.**  
The algorithm keeps `k` copies of each of the three characters and
deletes all other characters.  
The number of kept characters is `3·k`; hence the number deleted is
`|s| – 3·k`. ∎



##### Theorem  
The algorithm outputs the minimum number of deletions required to obtain
a string that contains at least `k` copies of `'a'`, `'b'` and `'c'`.

**Proof.**  
By Lemma&nbsp;1 the deletion performed by the algorithm yields a valid
resulting string.  
By Lemma&nbsp;2 any valid solution must delete at least
`|s| – 3·k` characters.  
By Lemma&nbsp;3 the algorithm deletes exactly that many.
Hence no solution with fewer deletions exists and the algorithm is optimal. ∎



--------------------------------------------------------------------

#### Complexity Analysis  

The algorithm performs only a constant amount of work:

*Time*: `O(1)`  
*Memory*: `O(1)`



--------------------------------------------------------------------

#### Reference Implementation (Java 17)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        String s = br.readLine();          // the string
        int k = Integer.parseInt(br.readLine().trim());  // required amount

        int n = s.length();                // total length
        int deletions = n - 3 * k;         // number of characters to delete

        System.out.println(deletions);
    }
}
```

The program follows exactly the algorithm proven correct above and is
fully compatible with Java 17.