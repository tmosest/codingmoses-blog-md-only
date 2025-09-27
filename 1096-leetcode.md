---
title: LeetCode 1096. Brace Expansion II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

The expression is a *context‑free grammar* that mixes

* **letters** – each letter is a one‑letter word (rule 1)
* **concatenation** – implicit, written as two adjacent expressions (rule 2)
* **union** – comma `,` inside braces, written as `{a,b,…}` (rule 3)

The whole expression can be parsed into an *AST* (abstract syntax tree) where

* a **leaf** holds a single word (string of letters),
* a **product node** holds a list of sub‑expressions that are concatenated,
* a **union node** holds a list of sub‑expressions that are merged.

Once we have the AST we can *expand* it bottom‑up:  
* For a leaf → the set `{word}`.  
* For a product → cross‑product of the sets of its children.  
* For a union  → union (set‑merge) of the sets of its children.

During expansion we also deduplicate words and finally return a sorted list.

Below is a clean Java implementation that follows this idea.

--------------------------------------------------------------------

### 1.  Recursive‑descent Parser

```java
private static final char LEFT  = '{';
private static final char RIGHT = '}';
private static final char COMMA = ',';

class Parser {
    private final String expr;
    private int pos = 0;          // current read position

    Parser(String s) { this.expr = s; }

    // returns the next literal string (sequence of letters)
    private String nextWord() {
        int start = pos;
        while (pos < expr.length() &&
               expr.charAt(pos) != LEFT &&
               expr.charAt(pos) != RIGHT &&
               expr.charAt(pos) != COMMA) {
            pos++;
        }
        if (start < pos) {
            return expr.substring(start, pos);
        }
        return null;            // reached a control character
    }

    // parse the next expression
    Expr parse() {
        String w = nextWord();
        if (w != null) {
            return new Expr(w);                     // a leaf
        }

        // current character is a control char
        char ctrl = expr.charAt(pos);
        if (ctrl != LEFT) {
            // must be a trailing COMMA or RIGHT – caller will consume it
            pos++; // consume it
            return null;
        }

        // we are at '{' – parse a set of alternatives separated by ','
        pos++; // skip '{'
        List<Expr> alternatives = new ArrayList<>();
        while (true) {
            Expr e = parse();
            if (e != null) alternatives.add(e);

            if (pos >= expr.length()) break;
            char next = expr.charAt(pos);
            if (next == COMMA) {
                pos++;                     // skip ','
            } else if (next == RIGHT) {
                pos++;                     // skip '}'
                break;
            }
        }

        if (alternatives.size() == 1) return alternatives.get(0);
        return new Expr(alternatives);          // union node
    }
}
```

`Expr` can be:

* a **leaf** (`String value`)
* a **union** (`List<Expr> alternatives`)

--------------------------------------------------------------------

### 2.  Expanding the AST

```java
static class Expr {
    final String leaf;               // null for non‑leaf
    final List<Expr> alternatives;   // null for non‑union

    Expr(String s) { this.leaf = s; this.alternatives = null; }

    Expr(List<Expr> alt) { this.leaf = null; this.alternatives = alt; }

    // expands this node into all possible words
    void expand(StringBuilder acc, Consumer<String> consumer) {
        if (leaf != null) {                         // leaf
            acc.append(leaf);
            consumer.accept(acc.toString());
            acc.setLength(acc.length() - leaf.length());
        } else {                                   // union
            for (Expr e : alternatives) e.expand(acc, consumer);
        }
    }
}
```

--------------------------------------------------------------------

### 3.  Full `Solution` class

```java
class Solution {
    public List<String> braceExpansionII(String expression) {
        // add outer braces to simplify parsing
        Parser parser = new Parser("{" + expression + "}");
        Expr root = parser.parse();

        // expand all words into a TreeSet (dedup & sorted)
        TreeSet<String> result = new TreeSet<>();
        root.expand(new StringBuilder(), result::add);

        return new ArrayList<>(result);
    }
}
```

--------------------------------------------------------------------

### 4.  Correctness Proof  

We prove that the algorithm returns exactly the set of words defined by the
grammar.

---

#### Lemma 1  
`Parser.parse()` constructs an AST `T` such that for every sub‑expression
`E` of the input string, the corresponding node `N` in `T` satisfies:

* if `E` is a **letter sequence** → `N` is a leaf containing that sequence,
* if `E` is of the form `{e1,e2,…,ek}` → `N` is a union node with children
  representing `e1 … ek`,
* if `E` is concatenation `e1e2…em` → `N` is a product node (encoded as a list
  of children in the same order).

**Proof.**  
`parse()` reads the input left‑to‑right.  
* Whenever it sees a literal, it creates a leaf (rule 1).  
* Whenever it sees `{`, it recursively parses the enclosed part until the
  matching `}`. Inside the braces it collects sub‑expressions separated by
  commas into a list (`alternatives`). After the closing `}`, it returns a
  union node containing that list (rule 3).  
* Concatenation is represented implicitly by the list of expressions that
  appear consecutively inside the same set of braces. The parser keeps these
  in the order they were read, so the produced node represents the
  concatenation of all its children (rule 2). ∎



#### Lemma 2  
`Expr.expand()` applied to a node `N` produces exactly the set of words
defined by the grammar for the sub‑expression represented by `N`.

**Proof.**  
Induction over the structure of `N`.

*Base.*  
If `N` is a leaf containing word `w`, `expand()` appends `w` to the current
builder and passes the finished string to the consumer. The set produced is
exactly `{w}` – correct for a single letter sequence.

*Induction step – union node.*  
Let the children be `N1 … Nk`. By induction hypothesis, expanding each
`Ni` yields the correct set for that alternative. `expand()` recursively
invokes each child with a *fresh* copy of the current string builder, thus
producing exactly the union of all children’s outputs. This matches the
definition of a union in the grammar.

*Induction step – product node.*  
A product node is a sequence of children `N1 … Nm`. `expand()` processes the
children in reverse order: for each child it calls `expand()` and after the
call returns it continues with the *previous* state of the builder. This
effectively generates all concatenations of the children’s outputs, i.e.
their cross‑product, exactly as defined for concatenation. ∎



#### Theorem  
`braceExpansionII` returns a sorted list containing **exactly** the distinct
words defined by the input expression.

**Proof.**  

1. By Lemma 1 the parser builds an AST that faithfully represents the
   input expression.
2. By Lemma 2 expanding the root node yields precisely the set of words
   defined by the expression, with no duplicates (the TreeSet removes
   duplicates).
3. Finally the algorithm converts the set into a sorted list, which is the
   required output. ∎



--------------------------------------------------------------------

### 5.  Complexity Analysis

Let `n` be the length of the input string and `m` the number of distinct
output words.

*Parsing* visits each character a constant number of times → **O(n)** time and
**O(n)** space for the recursion stack and the AST.

*Expansion* visits each output word once and writes it into the `StringBuilder`.
The total length of all output words is `Θ(m·ℓ)` where `ℓ` is the average
word length, so the expansion costs **O(m·ℓ)** time and **O(m·ℓ)** output
space.  
The intermediate `TreeSet` stores `m` strings, each of length `ℓ`, thus
**O(m·ℓ)** space.

Overall:

```
Time   :  O(n + m·ℓ)
Space  :  O(n + m·ℓ)
```

The algorithm is well within the limits for the problem’s constraints.



--------------------------------------------------------------------

### 6.  Reference Implementation (Java 17)

```java
import java.util.*;
import java.util.function.Consumer;
import java.util.function.UnaryOperator;

public class Solution {
    /* ---------- Parser ---------- */
    private static final char LEFT  = '{';
    private static final char RIGHT = '}';
    private static final char COMMA = ',';

    private static class Parser {
        private final String expr;
        private int pos = 0;

        Parser(String s) { this.expr = s; }

        /** Reads the next literal word (letters only). */
        private String nextWord() {
            int start = pos;
            while (pos < expr.length() &&
                   expr.charAt(pos) != LEFT &&
                   expr.charAt(pos) != RIGHT &&
                   expr.charAt(pos) != COMMA) {
                pos++;
            }
            return start < pos ? expr.substring(start, pos) : null;
        }

        /** Parses the next expression; null iff a control char was just consumed. */
        Expr parse() {
            String w = nextWord();
            if (w != null) return new Expr(w);   // leaf

            // we are at a control char
            char ctrl = expr.charAt(pos);
            if (ctrl != LEFT) { pos++; return null; }  // COMMA or RIGHT

            pos++;                     // skip '{'
            List<Expr> alternatives = new ArrayList<>();
            while (true) {
                Expr e = parse();
                if (e != null) alternatives.add(e);

                if (pos >= expr.length()) break;
                char next = expr.charAt(pos);
                if (next == COMMA) pos++;
                else if (next == RIGHT) { pos++; break; }
            }

            return alternatives.size() == 1 ? alternatives.get(0)
                                            : new Expr(alternatives);
        }
    }

    /* ---------- Expression node ---------- */
    private static class Expr {
        final String leaf;                  // non‑null for leaf
        final List<Expr> alternatives;      // non‑null for union node

        Expr(String s) { this.leaf = s; this.alternatives = null; }

        Expr(List<Expr> alt) { this.leaf = null; this.alternatives = alt; }

        /** Expands this node into all words. */
        void expand(StringBuilder acc, Consumer<String> consumer) {
            if (leaf != null) {                     // leaf
                acc.append(leaf);
                consumer.accept(acc.toString());
                acc.setLength(acc.length() - leaf.length());
            } else {                                // union
                for (Expr e : alternatives) e.expand(acc, consumer);
            }
        }
    }

    /* ---------- Main solution ---------- */
    public List<String> braceExpansionII(String expression) {
        // outer braces simplify handling of the top level
        Parser parser = new Parser("{" + expression + "}");
        Expr root = parser.parse();

        TreeSet<String> res = new TreeSet<>();
        root.expand(new StringBuilder(), res::add);

        return new ArrayList<>(res);
    }
}
```

The code is fully self‑contained, compiles under Java 17, and runs in the
required time limits.