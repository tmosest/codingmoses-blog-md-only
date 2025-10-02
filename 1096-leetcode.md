---
title: LeetCode 1096. Brace Expansion II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Brace‑Expansion II – Recursion + Set‑multiplication**  
*(The canonical solution that most of the accepted Java codes use)*  

```java
import java.util.*;

public class Solution {
    public List<String> braceExpansionII(String expression) {
        // 1. Expand the whole expression into a set of distinct strings
        Set<String> s = new HashSet<>();
        expand(expression, s);

        // 2. Return the sorted list
        return new ArrayList<>(new TreeSet<>(s));
    }

    /* ------------------------------------------------------------------ */
    /*  Recursively expand the *innermost* '{…}' and then walk outwards   */
    /* ------------------------------------------------------------------ */
    private void expand(String expr, Set<String> out) {
        int end = expr.indexOf('}');            // no more braces → finished
        if (end == -1) {
            out.add(expr);
            return;
        }

        // Find the matching '{' for this '}'
        int start = end;
        while (expr.charAt(start) != '{') start--;

        /* Rule 1 – split the contents by ','  */
        String[] parts = expr.substring(start + 1, end).split(",");

        /* Prefix   : everything before '{'     */
        String prefix = expr.substring(0, start);
        /* Suffix   : everything after '}'      */
        String suffix = expr.substring(end + 1);

        /* Rule 2 – concatenate every part with the outer context  */
        for (String part : parts) {
            // e.g.  prefix = "ab", part = "c", suffix = "d"  →  "abcd"
            expand(prefix + part + suffix, out);
        }
    }
}
```

### How it works

| Step | What happens | Why it is correct |
|------|--------------|------------------|
| 1 | `expand()` looks for the **first** `}` from the left. | The innermost `{…}` is the only one that can be expanded next. |
| 2 | Walk left until the matching `{`. | This gives us the complete list that belongs to this pair. |
| 3 | Split the substring between `{` and `}` by commas. | Each token represents one *choice* of the union operation (`∪`). |
| 4 | For every token, recursively call `expand()` on `<prefix><token><suffix>`. | This simulates the concatenation of the current choice with the surrounding context. |
| 5 | When no more `}` exists, the string contains no braces → add it to the result set. | The string is now a concrete word built from the original letters. |

Because we always expand the **innermost** braces first, we never need to explicitly keep track of the precedence or use a stack.  The recursion unwinds naturally and produces every possible string exactly once.

### Correctness proof (sketch)

*Lemma 1 –*  
When `expand()` processes a substring `expr` that contains a pair of braces, the recursion replaces that pair by **all** possible concatenations of the terms inside the braces with the outer context.

*Proof.*  
The terms inside `{…}` are separated by commas, each representing one element of a disjunction (`∪`).  
For each such term `t` the recursive call is `expand(prefix + t + suffix)`.  
The prefix and suffix are exactly the remaining context that must be concatenated with `t`.  
Thus all disjuncts are explored and concatenated with the surrounding words.

*Lemma 2 –*  
When `expand()` reaches a string that contains no braces, that string is a member of the language defined by the original expression.

*Proof.*  
If a string has no braces, it consists only of letters.  
All braces have been resolved by earlier recursive calls, therefore the string is a fully evaluated path from the root of the parse tree to a leaf.  By definition of the grammar, each such path corresponds to a word of the language.

*Theorem –*  
`braceExpansionII` returns the set of all distinct words described by the input expression.

*Proof.*  
By Lemma 1, every `{…}` is replaced by all of its alternatives, and by Lemma 2 each final string is a valid word.  
Conversely, any word in the language is produced by following one path through the recursion tree: at each `{…}` pick the appropriate alternative, and concatenate it with the surrounding context.  
Because we use a `Set` to collect results, duplicates are removed.  
Finally, converting the set to a sorted list preserves the required output order. ∎

### Complexity analysis

Let `n` be the length of the input string and `d` the depth of nested braces.  
The algorithm visits each character a constant number of times, so the
time complexity is **O(n + k)** where `k` is the number of distinct output strings (produced by the recursion).  
The auxiliary space used by the recursion stack is **O(d)**, and the final set stores all output strings, which is **O(k · m)** where `m` is the average word length.

---

**Why this approach is usually accepted**

* It is short (≈ 30 lines) and easy to read.  
* It follows the language definition exactly: first split by commas, then cross‑multiply by concatenation.  
* It works for the worst‑case input size (`n ≤ 60` in the problem).  
* No sophisticated data‑structures or operator parsing are required – a pure recursion suffices.  

Feel free to copy this snippet into your solution editor. Happy coding!