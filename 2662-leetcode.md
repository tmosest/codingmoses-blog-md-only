---
title: LeetCode 2662. Minimum Cost of a Path With Special Roads - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Storing a “point‑array” –  x, y, w 

When you say **2‑D array of points** you usually mean a collection of triples  
`(x, y, w)` – e.g. a point *P* in a 2‑D plane together with a weight
or cost *w* that belongs to that point.  
The exact container you choose depends on the language and on the
operations you need to perform on the data:

| Use‑case | Language | Suggested container | Why it fits |
|----------|----------|---------------------|------------|
| **Fixed size, fast indexing** | C/C++ | `std::array<std::tuple<int,int,int>, N>` (or `int[N][3]`) | Compile‑time size, cache‑friendly |
| **Dynamic list of points** | C/C++ | `std::vector<std::tuple<int,int,int>>` or `std::vector<Point>` | Push_back, random access |
| **Fast lookup by (x,y)** | C/C++ | `std::unordered_map<std::pair<int,int>, int>` (with a custom hash) | Constant‑time lookup |
| **Memory‑critical** | C/C++ | `int* data = new int[N*3];` and index as `data[i*3 + 0/1/2]` | 1‑D array, no overhead |
| **Object‑oriented** | Java | `class Point { int x, y, w; }` + `ArrayList<Point>` | Readable, encapsulation |
| **Java 8+** | Java | `List<int[]> list = new ArrayList<>();` | Primitive arrays avoid boxing |
| **Python** | Python | `list_of_tuples = [(x,y,w), …]` | Simple, native |
| **Large sparse graph** | Python | `defaultdict(lambda: float('inf'))` | Sparse representation |

---

### 1.  C/C++ examples

#### a) Simple struct + vector

```cpp
struct Point {
    int x, y, w;
};

std::vector<Point> points;

// Add a point
points.push_back({10, 20, 5});

// Access
for (const auto& p : points) {
    std::cout << p.x << "," << p.y << " w=" << p.w << '\n';
}
```

#### b) Using a tuple

```cpp
std::vector<std::tuple<int,int,int>> pts;
pts.emplace_back(10, 20, 5);        // equivalent to push_back({10,20,5})
```

#### c) Map keyed by (x,y)

If you frequently look up the weight *w* for a given coordinate pair:

```cpp
struct pair_hash {
    std::size_t operator()(const std::pair<int,int>& p) const noexcept {
        return std::hash<int>()(p.first) ^ (std::hash<int>()(p.second) << 1);
    }
};

std::unordered_map<std::pair<int,int>, int, pair_hash> point_map;
point_map[{10,20}] = 5;      // w = 5
int w = point_map[{10,20}];  // w == 5
```

The `pair_hash` is required because `std::unordered_map` needs a hash
function for non‑built‑in keys.

---

### 2.  Java examples

#### a) POJO + ArrayList

```java
public final class Point {
    public final int x, y, w;
    public Point(int x, int y, int w) { this.x = x; this.y = y; this.w = w; }
}

List<Point> points = new ArrayList<>();
points.add(new Point(10, 20, 5));
for (Point p : points) {
    System.out.printf("%d,%d w=%d%n", p.x, p.y, p.w);
}
```

#### b) int‑array + ArrayList

```java
List<int[]> points = new ArrayList<>();
points.add(new int[]{10, 20, 5});     // [x, y, w]
```

#### c) Map with pair key (requires a key class)

```java
class Coord {
    final int x, y;
    Coord(int x, int y) { this.x = x; this.y = y; }
    @Override public boolean equals(Object o) { … }
    @Override public int hashCode() { … }
}

Map<Coord, Integer> map = new HashMap<>();
map.put(new Coord(10,20), 5); // w = 5
```

(Implement `equals` and `hashCode` – or use `javafx.util.Pair` if
available.)

---

### 3.  Python example

```python
points = []          # list of triples
points.append((10, 20, 5))    # (x, y, w)

for x, y, w in points:
    print(f"{x},{y} w={w}")
```

Or, if you need fast lookup by `(x, y)`:

```python
point_map = {(10,20): 5}      # key = (x, y), value = w
```

---

## Choosing the right container

| Need | Container | Pros | Cons |
|------|-----------|------|------|
| **Frequent random access by index** | `vector` (C++) / `ArrayList` (Java) | O(1) access | Requires known size |
| **Dynamic growth** | `vector` / `ArrayList` | amortised O(1) push_back | Some overhead per element |
| **Lookup by (x,y)** | `unordered_map` / `HashMap` | O(1) expected | Extra memory, need hash |
| **Minimal memory** | Raw array (`int[N*3]` or `int[][]`) | No object overhead | Harder to read, no bounds check |
| **Readability & safety** | Struct/class | Named fields | Slightly more code, small overhead |

For a Dijkstra‑style algorithm where you *often* need to look up a
point’s weight by its coordinates, the `unordered_map` (C++) or
`HashMap` (Java) keyed on a pair is usually the most convenient.

If the point set is tiny (e.g. ≤ 10 000) or you just need a list, a
plain `vector`/`ArrayList` of structs/objects is often simpler.

---

### TL;DR

* **Point = triple (x, y, w)**  
* In **C++**: `struct Point{int x,y,w;}` + `vector<Point>` (fast)  
  or `unordered_map<pair<int,int>,int,hash>` for O(1) lookup.  
* In **Java**: `class Point{int x,y,w;}` + `ArrayList<Point>`  
  or `HashMap<Coord,Integer>` for lookup.  
* In **Python**: `list` of `tuple`s or a `dict` keyed on `(x,y)`.

Pick the container that matches your performance/memory needs and
keeps the code readable. Happy coding!