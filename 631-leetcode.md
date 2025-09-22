---
title: LeetCode 631. Design Excel Sum Formula - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1️⃣  Problem Recap (LeetCode 631 – Design Excel Sum Formula)

You are asked to implement a tiny Excel‐like spreadsheet that supports three operations:

| Method | Description |
|--------|-------------|
| `Excel(int height, char width)` | Initialise a `height × width` grid (rows 1‑height, columns 'A'‑width) with all zeros. |
| `void set(int row, char column, int val)` | Write an integer value into a cell. This clears any formula that might have been there. |
| `int get(int row, char column)` | Read the current integer value of a cell. |
| `int sum(int row, char column, List<String> numbers)` | Define a formula for a cell. The cell’s value becomes the sum of the cells/ranges in `numbers`. The formula stays until the cell is overwritten by a `set` or another `sum`. The references never form a cycle. |

**Constraints**

* `1 ≤ height ≤ 26` (max 26 rows)
* `'A' ≤ width ≤ 'Z'` (max 26 columns)
* `1 ≤ number of calls ≤ 100`
* `numbers` contains 1‑5 references, each either `"ColRow"` or `"ColRow1:ColRow2"` (inclusive rectangle)
* `-100 ≤ val ≤ 100`

---

## 📦 2️⃣  High‑Level Design

The key challenge: when a cell’s value changes (either by `set` or by redefining its own formula), every cell that depends on it must be updated too – *and* that propagation may cascade.

The simplest way to handle this is:

1. **Keep the raw value of every cell** – a 2‑D array (or a 1‑D hash‑map keyed by `"ColRow"`).
2. **Store a formula definition** per cell when `sum` is called – essentially a list of all the cells it looks at.
3. **Maintain a reverse‑dependency graph**:  
   `dependent[x] → { y | y depends on x }`.  
   Whenever a formula is defined, for every referenced cell `x` we add the current cell to `dependent[x]`.
4. **Propagate changes**:  
   *When a cell’s value changes* – run a depth‑first (or breadth‑first) walk over the reverse graph and recompute each child. Because the graph is acyclic, this walk will always terminate.

The propagation is *not* expensive because the spreadsheet is tiny (≤ 26×26) and we have at most 100 operations. No need for a full topological sort; a simple DFS/BFS is enough.

---

## ✅ 3️⃣  Full Code

Below are **three complete, self‑contained implementations** (Java, Python, C++).  
All three share the same algorithmic idea described above.

> **Tip for Interviews** – In an interview, you can explain that you use an *adjacency list* for reverse dependencies, and that propagation is done by DFS/BFS, making the solution O(N) per update where N is the number of dependent cells.

### 2.1 Java

```java
import java.util.*;

class Excel {

    private final int rows, cols;
    private final int[][] value;               // raw numeric value
    private final Map<String, List<String>> formula;   // cell → list of references
    private final List<Set<String>> dependents;        // cell → set of cells that depend on it

    // Helper to turn "B5" into "B5" (canonical key)
    private String key(int r, char c) { return "" + c + r; }

    public Excel(int height, char width) {
        this.rows = height;
        this.cols = width - 'A' + 1;
        this.value   = new int[rows][cols];
        this.formula = new HashMap<>();
        this.dependents = new ArrayList<>(rows * cols);
        for (int i = 0; i < rows * cols; ++i) dependents.add(new HashSet<>());
    }

    public void set(int row, char column, int val) {
        String cell = key(row, column);
        // Remove any old formula
        formula.remove(cell);
        // Update reverse edges
        clearDependents(cell);
        value[row-1][column-'A'] = val;
        // Propagate the change
        propagate(cell);
    }

    public int get(int row, char column) {
        return value[row-1][column-'A'];
    }

    public int sum(int row, char column, List<String> numbers) {
        String cell = key(row, column);
        // Remove previous formula and its edges
        formula.remove(cell);
        clearDependents(cell);

        // Build the reference list (with duplicates counted)
        List<String> refs = buildRefs(numbers);

        // Calculate the value now
        int sum = 0;
        for (String ref : refs) {
            sum += getCellValue(ref);
        }
        value[row-1][column-'A'] = sum;
        formula.put(cell, refs);

        // Register reverse dependencies
        for (String ref : refs) {
            dependents.get(cellToIndex(ref)).add(cell);
        }

        // Propagate the new value
        propagate(cell);
        return sum;
    }

    // ---- Helpers -----------------------------------------------------------

    // Return numeric value for a reference string (handles plain cell or a range)
    private int getCellValue(String ref) {
        int r1 = ref.charAt(1) - '0';
        if (ref.length() == 3) { // plain cell e.g., "B5"
            return value[r1-1][ref.charAt(0)-'A'];
        } else { // range "B1:D3"
            String[] parts = ref.split(":");
            int rStart = Integer.parseInt(parts[0].substring(1));
            int rEnd   = Integer.parseInt(parts[1].substring(1));
            char cStart = parts[0].charAt(0);
            char cEnd   = parts[1].charAt(0);
            int sum = 0;
            for (int r = rStart; r <= rEnd; ++r) {
                for (char c = cStart; c <= cEnd; ++c) {
                    sum += value[r-1][c-'A'];
                }
            }
            return sum;
        }
    }

    // Build a list of references; duplicates are preserved (they add up twice)
    private List<String> buildRefs(List<String> numbers) {
        List<String> refs = new ArrayList<>();
        for (String s : numbers) {
            if (!s.contains(":")) {
                refs.add(s);
            } else {
                String[] parts = s.split(":");
                int r1 = Integer.parseInt(parts[0].substring(1));
                int r2 = Integer.parseInt(parts[1].substring(1));
                char c1 = parts[0].charAt(0);
                char c2 = parts[1].charAt(0);
                for (int r = r1; r <= r2; ++r) {
                    for (char c = c1; c <= c2; ++c) {
                        refs.add("" + c + r);
                    }
                }
            }
        }
        return refs;
    }

    // Remove all reverse edges that go out of this cell
    private void clearDependents(String cell) {
        int idx = cellToIndex(cell);
        for (Set<String> set : dependents) set.remove(cell);
    }

    // Propagate value change to all dependents
    private void propagate(String cell) {
        Deque<String> q = new ArrayDeque<>();
        q.add(cell);
        Set<String> visited = new HashSet<>();
        visited.add(cell);

        while (!q.isEmpty()) {
            String cur = q.poll();
            for (String child : dependents.get(cellToIndex(cur))) {
                // Recalculate child’s value
                List<String> refs = formula.get(child);
                int newVal = 0;
                for (String ref : refs) newVal += getCellValue(ref);
                value[child.charAt(1)-'1'][child.charAt(0)-'A'] = newVal;
                if (visited.add(child)) q.add(child);
            }
        }
    }

    // Convert cell string "B5" into an index in the dependents list
    private int cellToIndex(String cell) {
        return (cell.charAt(1) - '1') * cols + (cell.charAt(0) - 'A');
    }
}
```

---

### 2.2 Python

```python
class Excel:
    def __init__(self, height: int, width: str):
        self.rows, self.cols = height, ord(width) - 64   # 'A' -> 1
        self.val = [[0] * self.cols for _ in range(height)]
        self.formula = {}          # cell -> list of refs
        self.dependents = {}       # cell -> set of cells that depend on it

    def _cell_key(self, r: int, c: str) -> str:
        return f"{c}{r}"

    def set(self, row: int, column: str, val: int) -> None:
        key = self._cell_key(row, column)
        self.val[row-1][ord(column)-65] = val
        self.formula.pop(key, None)
        self._propagate(key)

    def get(self, row: int, column: str) -> int:
        return self.val[row-1][ord(column)-65]

    def sum(self, row: int, column: str, numbers: list[str]) -> int:
        key = self._cell_key(row, column)
        refs = []
        for ref in numbers:
            if ':' not in ref:
                refs.append(ref)
            else:
                a, b = ref.split(':')
                c1, r1 = a[0], int(a[1:])
                c2, r2 = b[0], int(b[1:])
                for r in range(r1, r2+1):
                    for c in range(ord(c1), ord(c2)+1):
                        refs.append(f"{chr(c)}{r}")
        self.formula[key] = refs
        self._calc(key, refs)
        self._propagate(key)
        return self.val[row-1][ord(column)-65]

    def _calc(self, key: str, refs: list[str]) -> None:
        total = 0
        for r in refs:
            if ':' not in r:
                cell = r
                total += self.val[int(cell[1:])-1][ord(cell[0])-65]
            else:
                # this block is never hit because we expand ranges before
                pass
        self.val[int(key[1:])-1][ord(key[0])-65] = total

    def _propagate(self, key: str) -> None:
        # build reverse dependency graph lazily
        if key not in self.dependents:
            self.dependents[key] = set()
        for dep, refs in self.formula.items():
            if key in refs:
                self.dependents.setdefault(dep, set()).add(key)

        # BFS/DFS
        stack = [key]
        seen = {key}
        while stack:
            cur = stack.pop()
            cur_val = self.val[int(cur[1:])-1][ord(cur[0])-65]
            for parent, refs in self.formula.items():
                if cur in refs:
                    self._calc(parent, refs)
                    if parent not in seen:
                        seen.add(parent)
                        stack.append(parent)
```

---

### 2.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Excel {
public:
    Excel(int height, char width) {
        R = height;
        C = width - 'A' + 1;
        val.assign(R, vector<int>(C, 0));
        depend.assign(R*C, unordered_set<string>());
    }

    void set(int r, char c, int v) {
        string key = string(1, c) + to_string(r);
        val[r-1][c-'A'] = v;
        formula.erase(key);
        propagate(key);
    }

    int get(int r, char c) {
        return val[r-1][c-'A'];
    }

    int sum(int r, char c, const vector<string>& nums) {
        string key = string(1, c) + to_string(r);
        formula.erase(key);
        clear_dependents(key);

        vector<string> refs;
        for (auto &s : nums) {
            if (s.find(':') == string::npos) refs.push_back(s);
            else {
                auto parts = split(s, ':');
                char c1 = parts[0][0], c2 = parts[1][0];
                int r1 = stoi(parts[0].substr(1));
                int r2 = stoi(parts[1].substr(1));
                for (int rr = r1; rr <= r2; ++rr)
                    for (char cc = c1; cc <= c2; ++cc)
                        refs.push_back(string(1, cc) + to_string(rr));
            }
        }

        formula[key] = refs;
        recalc(key, refs);
        for (auto &ref : refs) depend[idx(ref)].insert(key);
        propagate(key);
        return val[r-1][c-'A'];
    }

private:
    int R, C;
    vector<vector<int>> val;
    unordered_map<string, vector<string>> formula;
    vector<unordered_set<string>> depend;

    string key(int r, char c) { return string(1,c) + to_string(r); }
    int idx(const string& s){ return (s[1]-'1')*C + (s[0]-'A'); }

    void recalc(const string& key, const vector<string>& refs){
        int total = 0;
        for (auto &ref : refs) {
            if (ref.find(':') == string::npos)
                total += val[ref[1]-'1'][ref[0]-'A'];
        }
        val[key[1]-'1'][key[0]-'A'] = total;
    }

    void propagate(string key){
        queue<string> q;
        q.push(key);
        unordered_set<string> visited{key};

        while (!q.empty()){
            string cur = q.front(); q.pop();
            for (auto &child : depend[idx(cur)]) {
                // recompute child
                auto refs = formula[child];
                int tot = 0;
                for (auto &r : refs)
                    if (r.find(':') == string::npos)
                        tot += val[r[1]-'1'][r[0]-'A'];
                val[child[1]-'1'][child[0]-'A'] = tot;
                if (visited.insert(child).second) q.push(child);
            }
        }
    }

    vector<string> split(const string &s, char delim){
        vector<string> res;
        string cur;
        for(char ch : s){
            if(ch==delim){ res.push_back(cur); cur.clear(); }
            else cur.push_back(ch);
        }
        res.push_back(cur);
        return res;
    }
};
```

---

### 2.4 C++ (modern)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Excel {
private:
    int R, C;
    vector<vector<int>> val;
    unordered_map<string, vector<string>> formula;
    vector<unordered_set<string>> rev; // reverse edges

    string key(int r, char c) { return string(1, c) + to_string(r); }

    int toIdx(const string &s) {
        return (s[1]-'1') * C + (s[0]-'A');
    }

    void clearEdges(const string &k) {
        for (auto &s : rev) s.erase(k);
    }

    void propagate(const string &k) {
        queue<string> q;
        unordered_set<string> vis;
        q.push(k); vis.insert(k);

        while (!q.empty()) {
            string cur = q.front(); q.pop();
            for (auto child : rev[toIdx(cur)]) {
                // recompute child's value
                auto refs = formula[child];
                int total = 0;
                for (auto &ref : refs) {
                    if (ref.find(':') == string::npos) {
                        total += val[ref[1]-'1'][ref[0]-'A'];
                    }
                }
                val[child[1]-'1'][child[0]-'A'] = total;
                if (vis.insert(child).second) q.push(child);
            }
        }
    }

public:
    Excel(int h, char w) : R(h), C(w - 'A' + 1), val(h, vector<int>(C, 0)),
        rev(R*C) {}

    void set(int r, char c, int v) {
        string k = key(r, c);
        val[r-1][c-'A'] = v;
        formula.erase(k);
        clearEdges(k);
        propagate(k);
    }

    int get(int r, char c) {
        return val[r-1][c-'A'];
    }

    int sum(int r, char c, const vector<string> &nums) {
        string k = key(r, c);
        formula.erase(k);
        clearEdges(k);

        vector<string> refs;
        for (auto &s : nums) {
            if (s.find(':') == string::npos) refs.push_back(s);
            else {
                auto p = split(s, ':');
                int r1 = stoi(p[0].substr(1));
                int r2 = stoi(p[1].substr(1));
                char c1 = p[0][0];
                char c2 = p[1][0];
                for (int rr=r1; rr<=r2; ++rr)
                    for (char cc=c1; cc<=c2; ++cc)
                        refs.push_back(string(1, cc) + to_string(rr));
            }
        }

        formula[k] = refs;
        int tot = 0;
        for (auto &ref : refs) tot += val[ref[1]-'1'][ref[0]-'A'];
        val[r-1][c-'A'] = tot;
        // update reverse graph
        for (auto &ref : refs) rev[toIdx(ref)].insert(k);

        propagate(k);
        return tot;
    }

private:
    vector<string> split(const string &s, char delim) {
        vector<string> res;
        string cur;
        for (char ch : s) {
            if (ch == delim) { res.push_back(cur); cur.clear(); }
            else cur.push_back(ch);
        }
        res.push_back(cur);
        return res;
    }
};
```

---

---

## 🧩 4️⃣  Why It Works

- **Adjacency list** – `dependents` stores the *reverse* edges, so we can walk from a changed cell outward without recursion depth problems.
- **Acyclic** – Because only `sum` can add edges, and we never add an edge that creates a cycle (the spec guarantees acyclic reference). DFS/BFS is safe.
- **Complexity** – Each update visits each child once, so the cost is proportional to the number of cells affected, far below the grid size.

---

## 📌 5️⃣  Interview Talking Points

1. **Explain data structures** – 2‑D array for values, hash‑map for formulas, reverse‑dependency adjacency list.
2. **Show the propagation algorithm** – DFS/BFS that recomputes children.
3. **Discuss edge‑cases** – Duplicate references (counts twice), range expansion before evaluation.
4. **Complexity** – O(N) per update, N = number of affected cells; for ≤ 26×26 this is trivial.

Good luck in your interview! 🚀

---

### 🎉 6️⃣  Final Note

The above solutions are compact and easily testable.  
If you want to extend the spreadsheet later (more rows/cols, larger data), just switch from arrays to dictionaries – the propagation logic stays the same.  
Happy coding!