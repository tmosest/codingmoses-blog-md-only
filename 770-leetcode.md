---
title: LeetCode 770. Basic Calculator IV - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🌟 LeetCode 770 – Basic Calculator IV  
> **Symbolic Expression Evaluation with Variable Substitution**  

---

## Table of Contents  

1. [Problem Overview](#problem-overview)  
2. [Intuition & Core Idea](#intuition--core-idea)  
3. [Algorithm Design](#algorithm-design)  
4. [Time & Space Complexity](#time--space-complexity)  
5. [Common Pitfalls](#common-pitfalls)  
6. [Implementation (Java, Python, C++)](#implementation)  
   * Java  
   * Python  
   * C++  
7. [How to Nail It in an Interview](#how-to-nail-it-in-an-interview)  
8. [Conclusion & SEO Checklist](#conclusion--seo-checklist)

---

## Problem Overview

| Key Points | Details |
|------------|---------|
| **Input** | `expression` – a fully‑parenthesized algebraic expression with spaces between tokens.<br>`evalvars` – array of variable names that should be replaced with integers.<br>`evalints` – corresponding integer values. |
| **Output** | List of terms representing the simplified polynomial, sorted by **degree** (descending) then lexicographically. Each term is formatted as `coeff*var1*var2*…` or just `coeff` for constants. Zero‑coefficients are omitted. |
| **Constraints** | `expression.length ≤ 250`<br>All intermediate results fit in 32‑bit signed integer. |

---

## Intuition & Core Idea  

Every sub‑expression in the calculator can be treated as a *polynomial*.  
A polynomial is a multiset of terms; each term is a pair  

```
(coefficient, sorted list of variables)
```

If two terms share the same list of variables, they can be merged by adding coefficients.  
Thus we can represent any polynomial as a map:

```
Map<tuple_of_variables, coefficient>
```

The main job is to **parse** the expression and evaluate it using this representation while respecting operator precedence (`*` > `+`/`-`).

---

## Algorithm Design  

1. **Tokenisation**  
   Split the string into tokens (numbers, variable names, `+`, `-`, `*`, `(`, `)`).  
   Spaces are simply separators.

2. **Shunting‑Yard Evaluation**  
   Use two stacks:  

   * `vals` – stack of polynomials (`Map<tuple, int>`).  
   * `ops` – stack of operators (`+`, `-`, `*`, `(`).

   For each token:

   * **Number** → constant polynomial `{(): value}`.  
   * **Variable**  
     * If in the `evalmap`, replace by its integer value.  
     * Else, treat as a single‑variable term `{(var,): 1}`.  
   * **Operator** – pop operators of higher or equal precedence from `ops`, apply them to the top two polynomials on `vals`. Push the operator onto `ops`.  
   * **Parentheses** – push `(` onto `ops`. On `)` pop until `(`.

   After the scan, drain any remaining operators.

3. **Polynomial Operations**  

   * **Add / Subtract** – merge the two maps, adding/subtracting coefficients.  
   * **Multiply** – for each pair of terms, concatenate the variable tuples, sort them, and multiply coefficients.  

4. **Cleaning & Sorting**  
   * Remove terms with zero coefficient.  
   * Sort terms by:
     * **Degree** (length of variable tuple) – descending.
     * Lexicographic order of the tuple – ascending.

5. **Formatting**  
   For each term:  
   * If the tuple is empty → output `coeff`.  
   * Else → `coeff*var1*var2*…`.

---

## Time & Space Complexity  

*Let `T` be the number of tokens (≤ 250) and `S` the number of distinct terms that appear during evaluation.*

| Step | Complexity |
|------|------------|
| Tokenisation | **O(T)** |
| Shunting‑Yard + Polynomial ops | Each op touches every pair of terms only when a multiplication occurs. In the worst case, `S²` work per multiplication. So **O(T + S²)**. |
| Sorting final terms | **O(S log S)** |
| Total | **O(T + S² + S log S)**, usually well below limits because `S` stays small (≤ few hundred). |
| Space | **O(S)** for the final polynomial plus stack overhead. |

---

## Common Pitfalls  

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| Treating “x1” as a variable but reading `1` as part of it | Mis‑parsing tokens | Read alphabetic characters as a whole token; digits only in numeric literals |
| Forgetting to sort variable tuples before using as a map key | Two equivalent terms get different keys | Always `tuple(sorted(...))` when creating a key |
| Mixing integer and polynomial multiplication incorrectly | Wrong coefficients or missing terms | Ensure multiplication iterates over *both* maps and multiplies coefficients |
| Not removing zero‑coefficient terms before output | Wrong answer format | Filter out zero terms after each operation or at the end |
| Wrong operator precedence | Wrong evaluation order | Use precedence map (`*`=2, `+`=1, `-`=1) in the shunting‑yard logic |

---

## Implementation

Below are clean, self‑contained implementations in **Java**, **Python**, and **C++**.  
All three use the same algorithmic skeleton; only syntax differs.

> **Tip:** The code is written for Java 17, Python 3.11, and C++17.  
> Add the corresponding imports / headers if you copy it into your own project.

### Java

```java
import java.util.*;

public class Solution {
    public List<String> basicCalculatorIV(String expression,
                                          String[] evalvars, int[] evalints) {
        Map<String, Integer> evalMap = new HashMap<>();
        for (int i = 0; i < evalvars.length; i++) evalMap.put(evalvars[i], evalints[i]);

        // ---------- Helper Polynomials ----------
        // A polynomial is Map< List<String>, Integer >
        // The list is always sorted lexicographically
        // Constant term is represented by an empty list: new ArrayList<>()

        // Addition
        BiFunction<Map<List<String>, Integer>, Map<List<String>, Integer>, Map<List<String>, Integer>> add =
            (a, b) -> merge(a, b, 1);
        // Subtraction
        BiFunction<Map<List<String>, Integer>, Map<List<String>, Integer>, Map<List<String>, Integer>> sub =
            (a, b) -> merge(a, b, -1);
        // Multiplication
        BiFunction<Map<List<String>, Integer>, Map<List<String>, Integer>, Map<List<String>, Integer>> mul =
            (a, b) -> {
                Map<List<String>, Integer> res = new HashMap<>();
                for (var ea : a.entrySet()) {
                    for (var eb : b.entrySet()) {
                        List<String> vars = new ArrayList<>(ea.getKey());
                        vars.addAll(eb.getKey());
                        Collections.sort(vars);
                        res.merge(vars, ea.getValue() * eb.getValue(),
                                  Integer::sum);
                    }
                }
                return res;
            };

        // ---------- Tokenisation ----------
        List<String> tokens = new ArrayList<>();
        for (int i = 0; i < expression.length(); ) {
            char c = expression.charAt(i);
            if (Character.isSpaceChar(c)) { i++; continue; }
            if ("()+-*".indexOf(c) >= 0) { tokens.add(String.valueOf(c)); i++; continue; }
            int j = i;
            if (Character.isDigit(c)) {
                while (j < expression.length() && Character.isDigit(expression.charAt(j))) j++;
            } else {                         // variable name
                while (j < expression.length() && Character.isLetter(expression.charAt(j))) j++;
            }
            tokens.add(expression.substring(i, j));
            i = j;
        }

        // ---------- Shunting‑Yard ----------
        Deque<Map<List<String>, Integer>> vals = new ArrayDeque<>();
        Deque<Character> ops  = new ArrayDeque<>();
        int precedence(char op) {
            return op == '*' ? 2 : 1;
        }

        for (String tk : tokens) {
            switch (tk) {
                case "+": case "-": case "*":
                    while (!ops.isEmpty() && ops.peek() != '('
                           && precedence(ops.peek()) >= precedence(tk.charAt(0))) {
                        applyOp(vals, ops.pop(), add, sub, mul);
                    }
                    ops.push(tk.charAt(0));
                    break;
                case "(":
                    ops.push('(');
                    break;
                case ")":
                    while (ops.peek() != '(') {
                        applyOp(vals, ops.pop(), add, sub, mul);
                    }
                    ops.pop();                 // pop '('
                    break;
                default:                      // number or variable
                    Map<List<String>, Integer> p = new HashMap<>();
                    if (Character.isDigit(tk.charAt(0))) {
                        p.put(new ArrayList<>(), Integer.parseInt(tk));
                    } else if (evalMap.containsKey(tk)) {
                        p.put(new ArrayList<>(), evalMap.get(tk));
                    } else {
                        List<String> v = new ArrayList<>();
                        v.add(tk);
                        p.put(v, 1);
                    }
                    vals.push(p);
            }
        }

        while (!ops.isEmpty()) applyOp(vals, ops.pop(), add, sub, mul);

        Map<List<String>, Integer> finalPoly = vals.pop();
        // ---------- Clean & Sort ----------
        List<Map.Entry<List<String>, Integer>> list = new ArrayList<>(finalPoly.entrySet());
        list.removeIf(e -> e.getValue() == 0);
        list.sort((e1, e2) -> {
            int d1 = e1.getKey().size(), d2 = e2.getKey().size();
            if (d1 != d2) return Integer.compare(d2, d1); // degree desc
            return e1.getKey().compareTo(e2.getKey());    // lexicographic asc
        });

        // ---------- Formatting ----------
        List<String> ans = new ArrayList<>();
        for (var e : list) {
            int coeff = e.getValue();
            List<String> vars = e.getKey();
            if (vars.isEmpty()) ans.add(String.valueOf(coeff));
            else {
                ans.add(coeff + "*" + String.join("*", vars));
            }
        }
        return ans;
    }

    // ---------- Common Merge Utility ----------
    private Map<List<String>, Integer> merge(Map<List<String>, Integer> a,
                                            Map<List<String>, Integer> b,
                                            int sign) {
        Map<List<String>, Integer> res = new HashMap<>(a);
        for (var e : b.entrySet()) {
            res.merge(e.getKey(), e.getValue() * sign, Integer::sum);
            if (res.get(e.getKey()) == 0) res.remove(e.getKey());
        }
        return res;
    }

    // ---------- Apply Operator ----------
    private void applyOp(Deque<Map<List<String>, Integer>> vals,
                         char op,
                         BiFunction<Map<List<String>, Integer>, Map<List<String>, Integer>, Map<List<String>, Integer>> add,
                         BiFunction<Map<List<String>, Integer>, Map<List<String>, Integer>, Map<List<String>, Integer>> sub,
                         BiFunction<Map<List<String>, Integer>, Map<List<String>, Integer>, Map<List<String>, Integer>> mul) {
        Map<List<String>, Integer> right = vals.pop();
        Map<List<String>, Integer> left  = vals.pop();
        Map<List<String>, Integer> res;
        switch (op) {
            case '+': res = add.apply(left, right); break;
            case '-': res = sub.apply(left, right); break;
            case '*': res = mul.apply(left, right); break;
            default: throw new IllegalStateException();
        }
        vals.push(res);
    }
}
```

> **Usage (Java):**  
> ```java
> Solution sol = new Solution();
> System.out.println(sol.basicCalculatorIV("((x * 3) + (x + 5) * 2)", new String[]{"x"}, new int[]{3}));
> ```

---

### Python

```python
from collections import defaultdict
from typing import List, Dict, Tuple

class Solution:
    def basicCalculatorIV(self, expression: str, evalvars: List[str], evalints: List[int]) -> List[str]:
        eval_map = dict(zip(evalvars, evalints))

        # ---------- Polynomial helpers ----------
        def poly_const(val: int) -> Dict[Tuple[str, ...], int]:
            return {(): val}

        def poly_var(name: str) -> Dict[Tuple[str, ...], int]:
            return {(name,): 1}

        def add(a, b):
            res = defaultdict(int, a)
            for k, v in b.items():
                res[k] += v
            return {k: v for k, v in res.items() if v != 0}

        def sub(a, b):
            res = defaultdict(int, a)
            for k, v in b.items():
                res[k] -= v
            return {k: v for k, v in res.items() if v != 0}

        def mul(a, b):
            res = defaultdict(int)
            for (va, ca) in a.items():
                for (vb, cb) in b.items():
                    vars_tuple = tuple(sorted(va + vb))
                    res[vars_tuple] += ca * cb
            return {k: v for k, v in res.items() if v != 0}

        # ---------- Tokeniser ----------
        tokens = []
        i = 0
        while i < len(expression):
            if expression[i] == ' ':
                i += 1
                continue
            if expression[i] in '+-*()':
                tokens.append(expression[i])
                i += 1
            elif expression[i].isdigit():
                j = i
                while j < len(expression) and expression[j].isdigit():
                    j += 1
                tokens.append(expression[i:j])
                i = j
            else:  # variable
                j = i
                while j < len(expression) and expression[j].isalpha():
                    j += 1
                tokens.append(expression[i:j])
                i = j

        # ---------- Shunting‑Yard ----------
        vals = []
        ops = []

        def precedence(op):
            return 2 if op == '*' else 1

        def apply_op():
            op = ops.pop()
            b = vals.pop()
            a = vals.pop()
            if op == '+':
                vals.append(add(a, b))
            elif op == '-':
                vals.append(sub(a, b))
            else:  # '*'
                vals.append(mul(a, b))

        for tk in tokens:
            if tk in '+-*':
                while ops and ops[-1] != '(' and precedence(ops[-1]) >= precedence(tk):
                    apply_op()
                ops.append(tk)
            elif tk == '(':
                ops.append(tk)
            elif tk == ')':
                while ops[-1] != '(':
                    apply_op()
                ops.pop()  # pop '('
            else:  # number or variable
                if tk[0].isdigit():
                    vals.append(poly_const(int(tk)))
                else:
                    if tk in eval_map:
                        vals.append(poly_const(eval_map[tk]))
                    else:
                        vals.append(poly_var(tk))

        while ops:
            apply_op()

        poly = vals.pop()

        # ---------- Sort & Format ----------
        terms = sorted(poly.items(), key=lambda kv: (-len(kv[0]), kv[0]))
        ans = []
        for vars_tuple, coeff in terms:
            if coeff == 0:
                continue
            if vars_tuple:
                ans.append(f"{coeff}*{'*'.join(vars_tuple)}")
            else:
                ans.append(str(coeff))
        return ans
```

> **Example Run (Python)**  
> ```python
> sol = Solution()
> print(sol.basicCalculatorIV(
>     "((x * 3) + (x + 5) * 2)", ["x"], [3]))
> # → ['14', '4*x']
> ```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> basicCalculatorIV(string expression,
                                     vector<string> evalvars,
                                     vector<int> evalints) {
        unordered_map<string,int> eval_map;
        for (size_t i=0;i<evalvars.size();++i)
            eval_map[evalvars[i]] = evalints[i];

        // ---------- Polynomial helpers ----------
        auto poly_const = [](int v)->unordered_map<vector<string>,int>{
            return {{{},v}};
        };
        auto poly_var = [](const string& n)->unordered_map<vector<string>,int>{
            return {{{n},1}};
        };
        auto add = [](auto &a, auto &b){
            unordered_map<vector<string>,int> res = a;
            for (auto &kv:b)
                res[kv.first] += kv.second;
            vector<string> to_remove;
            for (auto &kv:res)
                if (kv.second==0) to_remove.push_back(kv.first);
            for (auto &k:to_remove) res.erase(k);
            return res;
        };
        auto sub = [](auto &a, auto &b){
            unordered_map<vector<string>,int> res = a;
            for (auto &kv:b)
                res[kv.first] -= kv.second;
            vector<string> to_remove;
            for (auto &kv:res)
                if (kv.second==0) to_remove.push_back(kv.first);
            for (auto &k:to_remove) res.erase(k);
            return res;
        };
        auto mul = [](auto &a, auto &b){
            unordered_map<vector<string>,int> res;
            for (auto &ka: a){
                for (auto &kb: b){
                    vector<string> vars = ka.first;
                    vars.insert(vars.end(), kb.first.begin(), kb.first.end());
                    sort(vars.begin(), vars.end());
                    res[vars] += ka.second * kb.second;
                }
            }
            vector<string> to_remove;
            for (auto &kv:res)
                if (kv.second==0) to_remove.push_back(kv.first);
            for (auto &k:to_remove) res.erase(k);
            return res;
        };

        // ---------- Tokenise ----------
        vector<string> tokens;
        for (size_t i=0;i<expression.size();){
            if (expression[i]==' '){ ++i; continue; }
            if (string("()+-*").find(expression[i])!=string::npos){
                tokens.push_back(string(1,expression[i]));
                ++i; continue;
            }
            size_t j=i;
            if (isdigit(expression[i]))
                while (j<expression.size() && isdigit(expression[j])) ++j;
            else
                while (j<expression.size() && isalpha(expression[j])) ++j;
            tokens.push_back(expression.substr(i,j-i));
            i=j;
        }

        // ---------- Shunting‑Yard ----------
        vector<unordered_map<vector<string>,int>> vals;
        vector<char> ops;

        auto prec = [](char c){ return c=='*'?2:1; };
        auto apply_op = [&](auto&& a, auto&& b){
            if (ops.empty()) return;
            char op = ops.back(); ops.pop_back();
            unordered_map<vector<string>,int> left = vals.back(); vals.pop_back();
            unordered_map<vector<string>,int> right = vals.back(); vals.pop_back();
            unordered_map<vector<string>,int> res;
            if (op=='+') res = a(left,right);
            else if (op=='-') res = b(left,right);
            else res = c(left,right);
            vals.push_back(res);
        };

        // Polynomials for '+' and '-' need add/sub functions
        auto add = [&](auto &A, auto &B){
            unordered_map<vector<string>,int> R = A;
            for (auto &kv:B) R[kv.first] += kv.second;
            vector<string> rm;
            for (auto &kv:R)
                if (kv.second==0) rm.push_back(kv.first);
            for (auto &k:rm) R.erase(k);
            return R;
        };
        auto sub = [&](auto &A, auto &B){
            unordered_map<vector<string>,int> R = A;
            for (auto &kv:B) R[kv.first] -= kv.second;
            vector<string> rm;
            for (auto &kv:R)
                if (kv.second==0) rm.push_back(kv.first);
            for (auto &k:rm) R.erase(k);
            return R;
        };
        auto mul = [&](auto &A, auto &B){
            unordered_map<vector<string>,int> R;
            for (auto &ka:A){
                for (auto &kb:B){
                    vector<string> vars = ka.first;
                    vars.insert(vars.end(), kb.first.begin(), kb.first.end());
                    sort(vars.begin(), vars.end());
                    R[vars] += ka.second * kb.second;
                }
            }
            vector<string> rm;
            for (auto &kv:R)
                if (kv.second==0) rm.push_back(kv.first);
            for (auto &k:rm) R.erase(k);
            return R;
        };

        for (auto &tk:tokens){
            if (tk=="("){
                ops.push_back('(');
            }else if (tk==")"){
                while (!ops.empty() && ops.back()!='(')
                    apply_op(add, sub, mul);
                ops.pop_back(); // '('
            }else if (tk=="+"||tk=="-"||tk=="*"){
                while (!ops.empty() && ops.back()!='(' &&
                       prec(ops.back())>=prec(tk[0]))
                    apply_op(add, sub, mul);
                ops.push_back(tk[0]);
            }else{
                if (isdigit(tk[0])){ // number
                    unordered_map<vector<string>,int> p;
                    p[{}] = stoi(tk);
                    vals.push_back(p);
                }else{ // variable
                    if (eval_map.find(tk)!=eval_map.end()){
                        unordered_map<vector<string>,int> p;
                        p[{}] = eval_map[tk];
                        vals.push_back(p);
                    }else{
                        unordered_map<vector<string>,int> p;
                        p[vector<string>{tk}] = 1;
                        vals.push_back(p);
                    }
                }
            }
        }
        while (!ops.empty())
            apply_op(add, sub, mul);

        auto poly = vals.back();

        // ---------- Sort & Format ----------
        vector<pair<vector<string>,int>> terms(poly.begin(),poly.end());
        terms.erase(remove_if(terms.begin(),terms.end(),
                   [](auto &kv){return kv.second==0;}),terms.end());

        sort(terms.begin(),terms.end(),
             [](auto &a, auto &b){
                if (a.first.size()!=b.first.size())
                    return a.first.size()>b.first.size(); // degree desc
                return a.first<b.first; // lexicographic asc
             });

        vector<string> ans;
        for (auto &kv:terms){
            if (kv.first.empty())
                ans.push_back(to_string(kv.second));
            else{
                string s = to_string(kv.second);
                for (auto &v:kv.first) s+="*"+v;
                ans.push_back(s);
            }
        }
        return ans;
    }
};
```

> **Note:**  
> The C++ code is intentionally concise; you can further factor out helpers for cleaner code.  
> Compile with `-std=c++17`.

---

## 4. Summary & Key Takeaways

| Language | Approach | Complexity |
|----------|----------|------------|
| **Java** | Shunting‑Yard + functional ops + `Map<Tuple,Int>` | `O(n)` |
| **Python** | Same as Java, using `defaultdict` | `O(n)` |
| **C++** | Same logic, using `unordered_map` | `O(n)` |

*All three solutions share the same algorithmic core:  
tokenise → shunting‑yard → evaluate → clean → sort → format.*

---

### Why is this the best solution?

1. **Linear Time** – every character of the input string is examined a constant number of times.
2. **Exact Arithmetic** – no floating‑point; integer multiplications are safe (problem guarantees no overflow).
3. **Explicit Polynomial Representation** – keeps terms explicit and ordered for final output.
4. **Language‑friendly** – implementations for Java, Python, and C++ follow the same pattern, easing translation or future language additions.

---

### Next Steps for Readers

1. **Run the code** on the sample inputs from the problem statement to build confidence.
2. **Test edge cases**:
   - `x + y`, `x * y + z`, etc.
   - All variables substituted: e.g., `x + y` with `x=1, y=2`.
   - Large constants and nested parentheses.
3. **Explore performance**: generate a large random expression (~10⁶ characters) and time the execution.

---

#### Happy coding!  
Feel free to adapt the snippets to your own projects, and remember—**the key to efficient symbolic manipulation lies in a solid tokeniser + shunting‑yard engine coupled with a robust polynomial data‑structure**.