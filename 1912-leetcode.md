---
title: LeetCode 1912. Design Movie Rental System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🎬 Design Movie Rental System – The Good, the Bad & the Ugly  
*(LeetCode #1912 – Java / Python / C++ implementations)*  

### 1️⃣ What the problem asks for  
You’re building a **Movie Rental System** that must answer four kinds of queries in an online store:

| method | description |
|--------|-------------|
| `search(movie)` | Return up to **5 cheapest unrented copies** of the given movie (by price, then shop, then movie id). |
| `rent(shop, movie)` | Mark the copy as *rented* (it can no longer be returned by `search`). |
| `drop(shop, movie)` | Return a rented copy back to the pool of available items. |
| `report()` | Return up to **5 cheapest rented copies** in the same order as `search`. |

The judge guarantees **10⁵** entries and **10⁵** calls, so an **O(log N)** solution per operation is expected.

> **Keywords** – *Movie Rental System, LeetCode 1912, design, interview, data structures, Java, Python, C++, TreeSet, HashMap, custom comparator, set, heap.*

---

## 🚀 The Good – A clean, O(log N) design

| Language | Data structure | Why it works |
|----------|----------------|--------------|
| **Java** | `TreeSet<Item>` with a *comparator* (price → shop → movie) | `search` is *O(1)*, `rent/drop` are *O(log N)*, `report` is *O(1)* |
| **Python** | Two `heapq`‑based priority queues with lazy‑deletion | `search` scans at most a handful of heap entries, still *amortised* O(log N) |
| **C++** | `std::set<Item, cmp>` and `std::map` + `std::unordered_map` | Same asymptotic guarantees as Java |

### 1.1 Java – TreeSet + HashMap

```java
import java.util.*;

public class MovieRentingSystem {
    /** A single copy of a movie in a shop */
    private static final class Item {
        final int shopId, movieId, price;
        Item(int shop, int movie, int price) { this.shopId = shop; this.movieId = movie; this.price = price; }
        @Override public int hashCode() { return Objects.hash(shopId, movieId, price); }
        @Override public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Item)) return false;
            Item i = (Item) o;
            return shopId == i.shopId && movieId == i.movieId && price == i.price;
        }
    }

    /* -------------   data   ------------- */
    private final Map<Integer, Map<Integer, Item>> shopMap = new HashMap<>();
    private final Map<Integer, TreeSet<Item>> unrented = new HashMap<>();
    private final TreeSet<Item> rented = new TreeSet<>(
        Comparator.comparingInt((Item i) -> i.price)
                  .thenComparingInt(i -> i.shopId)
                  .thenComparingInt(i -> i.movieId));

    /* -------------   ctor   ------------- */
    public MovieRentingSystem(int n, int[][] entries) {
        for (int[] e : entries) {
            int shop = e[0], movie = e[1], price = e[2];
            Item it = new Item(shop, movie, price);

            shopMap.computeIfAbsent(shop, k -> new HashMap<>()).put(movie, it);
            unrented.computeIfAbsent(movie, k -> new TreeSet<>(
                Comparator.comparingInt((Item i) -> i.price)
                          .thenComparingInt(i -> i.shopId)
                          .thenComparingInt(i -> i.movieId))).add(it);
        }
    }

    /* -------------   search   ------------- */
    public List<Integer> search(int movie) {
        TreeSet<Item> set = unrented.getOrDefault(movie, new TreeSet<>());
        List<Integer> res = new ArrayList<>(5);
        int cnt = 0;
        for (Item it : set) {
            if (cnt == 5) break;
            res.add(it.shopId);
            cnt++;
        }
        return res;
    }

    /* -------------   rent   ------------- */
    public void rent(int shop, int movie) {
        Item it = shopMap.get(shop).get(movie);
        unrented.get(movie).remove(it);
        rented.add(it);
    }

    /* -------------   drop   ------------- */
    public void drop(int shop, int movie) {
        Item it = shopMap.get(shop).get(movie);
        rented.remove(it);
        unrented.get(movie).add(it);
    }

    /* -------------   report   ------------- */
    public List<List<Integer>> report() {
        List<List<Integer>> res = new ArrayList<>(5);
        int cnt = 0;
        for (Item it : rented) {
            if (cnt == 5) break;
            res.add(Arrays.asList(it.shopId, it.movieId));
            cnt++;
        }
        return res;
    }
}
```

**Complexity**

| operation | time | space |
|-----------|------|-------|
| constructor | **O(N log N)** | **O(N)** |
| `search` | **O(1)** | – |
| `rent` / `drop` | **O(log N)** | – |
| `report` | **O(1)** | – |

*The Java `TreeSet` guarantees logarithmic insert/delete and sorted traversal, making every method efficient.*

---

### 📜 Python – Lazy‑Deletion Heaps (O(log N) amortised)

```python
import heapq
from collections import defaultdict

class MovieRentingSystem:
    """
    Python solution that uses two heaps with lazy‑deletion.
    """
    def __init__(self, n, entries):
        # shop -> movie -> price
        self.shop_map = defaultdict(dict)

        # movie -> heap of (price, shop)
        self.unrented = defaultdict(list)
        # (movie, shop) -> active flag for unrented heap
        self.unrented_active = defaultdict(lambda: False)

        # global heap of rented copies: (price, shop, movie)
        self.rented = []
        self.rented_active = defaultdict(lambda: False)

        for shop, movie, price in entries:
            self.shop_map[shop][movie] = price
            heapq.heappush(self.unrented[movie], [price, shop])
            self.unrented_active[(movie, shop)] = True

    # --------------------------------------------------
    def search(self, movie):
        heap = self.unrented.get(movie, [])
        result = []
        temp = []

        # take up to 5 active entries
        while heap and len(result) < 5:
            price, shop = heapq.heappop(heap)
            if self.unrented_active[(movie, shop)]:
                result.append(shop)
                temp.append([price, shop])          # keep a copy for reinsertion
            # else: stale entry – discard

        # push the collected entries back
        for item in temp:
            heapq.heappush(heap, item)
        return result

    # --------------------------------------------------
    def rent(self, shop, movie):
        price = self.shop_map[shop][movie]
        # deactivate in the unrented heap
        self.unrented_active[(movie, shop)] = False

        # push into the rented heap
        heapq.heappush(self.rented, [price, shop, movie])
        self.rented_active[(movie, shop)] = True

    # --------------------------------------------------
    def drop(self, shop, movie):
        # mark rented entry as inactive
        self.rented_active[(movie, shop)] = False
        # reactivate unrented entry
        self.unrented_active[(movie, shop)] = True

    # --------------------------------------------------
    def report(self):
        heap = self.rented
        result = []
        temp = []

        while heap and len(result) < 5:
            price, shop, movie = heapq.heappop(heap)
            if self.rented_active[(movie, shop)]:
                result.append([shop, movie])
                temp.append([price, shop, movie])
            # stale entry – skip

        for item in temp:
            heapq.heappush(heap, item)
        return result
```

**Why this works**

| method | How many heap pops do we touch? | Amortised time |
|--------|---------------------------------|----------------|
| `search` | At most *5* active entries + a few stale ones | **O(log N)** |
| `rent` | One `push` to `unrented` (log) + one to `rented` | **O(log N)** |
| `drop` | One flag toggle | **O(1)** |
| `report` | Same as `search` | **O(1)** |

*The lazy‑deletion pattern keeps the heaps small, and each active item is processed only a handful of times, giving good real‑world performance.*

---

### 🏗 C++ – `std::set` + `std::map` (fast, type‑safe)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Item {
    int shop, movie, price;
    Item(int s, int m, int p) : shop(s), movie(m), price(p) {}
};

struct CmpUnrented {
    bool operator()(const Item& a, const Item& b) const {
        if (a.price != b.price) return a.price < b.price;
        if (a.shop != b.shop)   return a.shop < b.shop;
        return a.movie < b.movie;
    }
};

struct CmpRented {
    bool operator()(const Item& a, const Item& b) const {
        if (a.price != b.price) return a.price < b.price;
        if (a.shop != b.shop)   return a.shop < b.shop;
        return a.movie < b.movie;
    }
};

class MovieRentingSystem {
public:
    // shop -> movie -> Item
    unordered_map<int, unordered_map<int, Item>> shopMap;
    unordered_map<int, set<Item, CmpUnrented>> unrented;
    set<Item, CmpRented> rented;

    MovieRentingSystem(int n, vector<vector<int>>& entries) {
        for (auto& e : entries) {
            int shop = e[0], movie = e[1], price = e[2];
            Item it(shop, movie, price);

            shopMap[shop][movie] = it;
            unrented[movie].insert(it);
        }
    }

    vector<int> search(int movie) {
        vector<int> ans;
        auto it = unrented.find(movie);
        if (it == unrented.end()) return ans;
        for (const auto& item : it->second) {
            if (ans.size() == 5) break;
            ans.push_back(item.shop);
        }
        return ans;
    }

    void rent(int shop, int movie) {
        Item it = shopMap[shop][movie];
        unrented[movie].erase(it);
        rented.insert(it);
    }

    void drop(int shop, int movie) {
        Item it = shopMap[shop][movie];
        rented.erase(it);
        unrented[movie].insert(it);
    }

    vector<vector<int>> report() {
        vector<vector<int>> ans;
        for (const auto& it : rented) {
            if (ans.size() == 5) break;
            ans.push_back({it.shop, it.movie});
        }
        return ans;
    }
};
```

**Complexity**

Same as the Java version: *log‑time insert/delete in `std::set`, constant‑time traversal of the first five items.*

---

## 🔍 The Bad – What we still need to watch out for

| issue | consequence | remedy |
|-------|-------------|--------|
| **Large comparator chains** | If the comparator is not *stable*, `search`/`report` can return duplicates or miss an item. | Always use `Comparator.comparingInt(...).thenComparingInt(...)` (Java) or a single tuple `(price, shop, movie)` (C++ / Python). |
| **Memory overhead** | Every `Item` is stored in three places (`shopMap`, `unrented`, `rented`). | Keep the `Item` *single* object per copy and only store references; the Java version already does this. |
| **Stale heap entries (Python)** | A stale entry can linger indefinitely if `rent`/`drop` are called in a very unbalanced way. | Use a global *active* flag per copy or a `set` of active keys to skip stale entries. |

---

## ⚙️ The Ugly – Edge cases & maintenance pitfalls

1. **Duplicate entries**  
   The judge guarantees that each `(shop, movie)` pair is unique in the input, but if you change the constructor you must enforce this invariant.  
   *Solution*: `shopMap.computeIfAbsent(...).put(movie, it)` overwrites duplicates automatically.

2. **Calling `rent` on an already rented copy**  
   The problem statement guarantees that the call is legal, but defensive coding (e.g., checking `shopMap` existence) protects against accidental misuse.

3. **`report()` after many `drop()` operations**  
   In the heap‑based Python solution, the `rented` heap can contain many stale entries. If you repeatedly call `drop()`, the heap can grow in size.  
   *Solution*: Occasionally rebuild the heap from the active keys (e.g., after every 10 000 operations) – this keeps the memory footprint under control.

4. **Custom `Item` equality in Java**  
   `TreeSet` uses the comparator for ordering, but `hashCode()` and `equals()` are needed for the `HashMap` look‑ups.  
   *Tip*: Keep `equals()`/`hashCode()` **consistent** with the fields that uniquely identify a copy (`shopId`, `movieId`, `price`).

5. **Pointer aliasing in C++**  
   The `Item` stored in `std::set` is a value object; copying it into the `shopMap` would create separate instances that the set can’t delete.  
   *Fix*: Store *references* or *pointers* in the map and let the `set` own the sole instance.

---

## 🎯 Bottom‑Line Take‑aways

- **TreeSet** (Java) or **std::set** (C++) gives you sorted order *and* logarithmic updates in a single container – the “golden” choice for this problem.
- In **Python** you can mimic that behaviour with priority queues and lazy‑deletion – a common trick in interview settings.
- The key to *constant‑time* `search`/`report` is to never iterate over the whole data set; the containers give you the top‑five items in *O(1)* because the underlying structure keeps everything sorted for you.
- Always remember to keep your data structures in sync (`rent` moves an `Item` from one `TreeSet` to another, `drop` does the reverse).

Happy coding! 🚀