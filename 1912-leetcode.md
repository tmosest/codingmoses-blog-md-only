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

# “The Good, The Bad, and The Ugly” – Solving LeetCode 1912 (Design Movie Rental System)

**Keywords**: LeetCode 1912, Design Movie Rental System, Java, Python, C++, interview, data structures, TreeSet, HashMap, SortedSet, job interview, algorithm, software engineer

---

## 1. Problem Recap

> *You run a chain of `n` movie‑shop outlets. Each shop may own **at most one copy** of a movie.  
> For a given movie you must be able to:*
> * **search** – return the *cheapest 5* shops that currently own an *unrented* copy  
> * **rent** – mark a specific copy as rented  
> * **drop** – return a rented copy  
> * **report** – return the *cheapest 5* rented copies in the whole system  

All operations must run fast enough for up to **100 000** calls.  
Typical constraints: `1 ≤ n ≤ 3·10⁵`, `1 ≤ entries.length ≤ 10⁵`, `1 ≤ price ≤ 10⁴`.

---

## 2. High‑Level Idea

| **Goal** | **Data structure** | **Why** |
|----------|---------------------|---------|
| Quickly find *cheapest 5 unrented copies* for a movie | **`TreeSet` / `std::set` / sorted list** of `(price, shop, movie)` | Sorted by price → shop → movie → we can just take first five |
| Quickly find *cheapest 5 rented copies* in the whole system | Another **`TreeSet` / `std::set`** of the *same key* | Same reasoning – sorted globally |
| Constant‑time lookup of the *exact item* in a shop | **HashMap shop → movie → Item** | Needed for rent/drop to find the item in O(1) before moving it between sets |

Thus we maintain **three** data structures:

1. **`shopMap`** – `Map<shop, Map<movie, Item>>` – fast pointer to a particular copy.
2. **`unrented`** – `Map<movie, TreeSet<Item>>` – all currently unrented copies of that movie.
3. **`rented`** – `TreeSet<Item>` – all rented copies sorted by the report order.

All operations (`search`, `rent`, `drop`, `report`) become `O(log k)` where `k` is the size of the relevant set (≤ 10⁵).  

With these structures we get:

* **search** – just iterate first 5 elements → `O(5 log k)` → essentially `O(1)`
* **rent / drop** – remove from one set, insert into the other → `O(log k)`
* **report** – iterate first 5 elements of the global rented set → `O(1)`

---

## 3. Detailed Design

### 3.1 Item

| Language | Representation |
|----------|----------------|
| **Java** | Inner class `Item` with `shopId`, `movieId`, `price`. |
| **Python** | Simple dataclass / tuple `(price, shop, movie)` – we keep the `shop` inside for report ordering. |
| **C++** | `struct Item` with `int shop, movie, price;` and a custom comparator. |

All three use the **same ordering**:  
`price` ➜ `shop` ➜ `movie`.  
This guarantees that the first element in a `TreeSet`/`set` is always the *best* copy for either `search` or `report`.

### 3.2 Constructors

During construction we simply insert every entry into:

* the shop’s local map (`shopMap`)
* the global rented set (`rented`) – *empty* at start
* the movie‑specific unrented set (`unrented.get(movie)`)

No operation is `O(n log n)`; we are doing `O(entries)` inserts, each costing at most `O(log k)` because we create a new `TreeSet` for each new movie only once.

---

## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| **Constructor** | `O(entries log entries)` | `O(entries)` |
| **search** | `O(1)` (just 5 pulls) | `O(1)` |
| **rent / drop** | `O(log k)` | `O(1)` extra for map look‑ups |
| **report** | `O(1)` (first five of the global set) | `O(1)` |

All limits are comfortably below the 100 000‑operation requirement.

---

## 5. Implementation

Below you will find a **ready‑to‑copy** implementation in **Java**, **Python**, and **C++**.  
All three follow the same logic, just adapted to the idiomatic containers of each language.

> **Tip for interviewers**:  
> *Explain the *why* behind each data‑structure choice – they want to see you understand “sorted collections” and “hash‑lookup” trade‑offs.*

---

### 5.1 Java (TreeSet + HashMap)

```java
import java.util.*;

public class MovieRentingSystem {
    /** One copy of a movie in a shop */
    private static class Item {
        final int shopId;
        final int movieId;
        final int price;

        Item(int shopId, int movieId, int price) {
            this.shopId = shopId;
            this.movieId = movieId;
            this.price = price;
        }
    }

    // Comparator used for both unrented and rented sets
    private static final Comparator<Item> COMPARATOR =
            (a, b) -> a.price != b.price ? Integer.compare(a.price, b.price)
                                          : a.shopId != b.shopId ? Integer.compare(a.shopId, b.shopId)
                                                                : Integer.compare(a.movieId, b.movieId);

    private final Map<Integer, Map<Integer, Item>> shopMap = new HashMap<>();
    private final Map<Integer, TreeSet<Item>> unrented = new HashMap<>();
    private final TreeSet<Item> rented = new TreeSet<>(COMPARATOR);

    public MovieRentingSystem(int n, int[][] entries) {
        for (int[] e : entries) {
            int shop = e[0], movie = e[1], price = e[2];
            Item item = new Item(shop, movie, price);

            // 1. Map shop→movie→Item
            shopMap.computeIfAbsent(shop, k -> new HashMap<>()).put(movie, item);

            // 2. Unrented set for this movie
            unrented.computeIfAbsent(movie, k -> new TreeSet<>(COMPARATOR)).add(item);
        }
    }

    /** Return the shop IDs of the cheapest 5 unrented copies of the given movie. */
    public List<Integer> search(int movie) {
        TreeSet<Item> set = unrented.getOrDefault(movie, new TreeSet<>(COMPARATOR));
        List<Integer> ans = new ArrayList<>(5);
        int i = 0;
        for (Item it : set) {
            if (i++ == 5) break;
            ans.add(it.shopId);
        }
        return ans;
    }

    /** Rent a copy from a specific shop. */
    public void rent(int shop, int movie) {
        Item it = shopMap.get(shop).get(movie);
        unrented.get(movie).remove(it);
        rented.add(it);
    }

    /** Return a rented copy. */
    public void drop(int shop, int movie) {
        Item it = shopMap.get(shop).get(movie);
        rented.remove(it);
        unrented.get(movie).add(it);
    }

    /** Return the cheapest 5 rented copies (shop, movie). */
    public List<List<Integer>> report() {
        List<List<Integer>> res = new ArrayList<>(5);
        int i = 0;
        for (Item it : rented) {
            if (i++ == 5) break;
            res.add(Arrays.asList(it.shopId, it.movieId));
        }
        return res;
    }
}
```

> **Why it’s fast** – All mutating operations touch only two sorted containers, each insertion/deletion is `O(log k)`.

---

### 5.2 Python (Sorted List + `bisect`)

Python does not have a built‑in `TreeSet`, but the standard library `bisect` lets us keep a *sorted list* that behaves like a balanced tree for our constraints.

```python
import bisect
from typing import List

class Item:
    __slots__ = ("shop", "movie", "price")

    def __init__(self, shop: int, movie: int, price: int):
        self.shop = shop
        self.movie = movie
        self.price = price

    # key for sorting: (price, shop, movie)
    def key(self):
        return (self.price, self.shop, self.movie)

    def __lt__(self, other: 'Item'):
        return self.key() < other.key()


class MovieRentingSystem:
    def __init__(self, n: int, entries: List[List[int]]):
        # shop → movie → Item
        self.shop_map = {}

        # movie → sorted list of unrented items
        self.unrented = {}

        # global sorted list of rented items
        self.rented = []

        for shop, movie, price in entries:
            it = Item(shop, movie, price)

            self.shop_map.setdefault(shop, {})[movie] = it

            if movie not in self.unrented:
                self.unrented[movie] = []
            # insert while keeping the list sorted
            bisect.insort_left(self.unrented[movie], it)

    def search(self, movie: int) -> List[int]:
        lst = self.unrented.get(movie, [])
        return [it.shop for it in lst[:5]]

    def _remove_from_set(self, lst: List[Item], it: Item):
        idx = bisect.bisect_left(lst, it)
        # we trust the problem guarantees the item exists
        lst.pop(idx)

    def _add_to_set(self, lst: List[Item], it: Item):
        bisect.insort_left(lst, it)

    def rent(self, shop: int, movie: int) -> None:
        it = self.shop_map[shop][movie]
        self._remove_from_set(self.unrented[movie], it)
        self._add_to_set(self.rented, it)

    def drop(self, shop: int, movie: int) -> None:
        it = self.shop_map[shop][movie]
        self._remove_from_set(self.rented, it)
        self._add_to_set(self.unrented[movie], it)

    def report(self) -> List[List[int]]:
        return [[it.shop, it.movie] for it in self.rented[:5]]
```

*Why `bisect` works*:  
The list is always sorted by `(price, shop, movie)`. Inserting or deleting a single element costs `O(log k)` plus a list shift (`O(k)`) – but with 10⁵ elements it’s still well below the 100 000‑operation limit.

---

### 5.3 C++ (std::set + unordered_map)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Item {
    int shop, movie, price;
    Item(int s, int m, int p) : shop(s), movie(m), price(p) {}
    // key for sorting: price → shop → movie
    bool operator<(const Item& other) const {
        if (price != other.price) return price < other.price;
        if (shop != other.shop)   return shop < other.shop;
        return movie < other.movie;
    }
};

class MovieRentingSystem {
    // shop -> (movie -> pointer to Item)
    unordered_map<int, unordered_map<int, Item*>> shopMap;
    // movie -> set of unrented items
    unordered_map<int, set<Item*>> unrented;
    // global set of rented items
    set<Item*> rented;

public:
    MovieRentingSystem(int n, vector<vector<int>>& entries) {
        vector<Item*> allItems;
        allItems.reserve(entries.size());

        for (auto& e : entries) {
            int shop = e[0], movie = e[1], price = e[2];
            allItems.push_back(new Item(shop, movie, price));
            Item* it = allItems.back();

            shopMap[shop][movie] = it;
            unrented[movie].insert(it);
        }
    }

    vector<int> search(int movie) {
        vector<int> res;
        auto it = unrented.find(movie);
        if (it == unrented.end()) return res;
        int cnt = 0;
        for (Item* ip : it->second) {
            if (cnt++ == 5) break;
            res.push_back(ip->shop);
        }
        return res;
    }

    void rent(int shop, int movie) {
        Item* ip = shopMap[shop][movie];
        unrented[movie].erase(ip);
        rented.insert(ip);
    }

    void drop(int shop, int movie) {
        Item* ip = shopMap[shop][movie];
        rented.erase(ip);
        unrented[movie].insert(ip);
    }

    vector<vector<int>> report() {
        vector<vector<int>> res;
        int cnt = 0;
        for (Item* ip : rented) {
            if (cnt++ == 5) break;
            res.push_back({ip->shop, ip->movie});
        }
        return res;
    }
};
```

> **Memory cleanup** – For brevity the destructor does not free `Item*`. In a real interview you’d mention it or use `unique_ptr`.

---

## 6. “What Went Wrong?” – Common Pitfalls

1. **Choosing the wrong sorted container**  
   *Solution*: Use `TreeSet`/`set` to keep ordering; avoid hash‑only solutions because you lose “best” element retrieval.

2. **Not sharing the comparator**  
   *Result*: `search` and `report` would give different “best” items.  
   *Fix*: Keep a single comparator for all sorted sets.

3. **Storing only price in the global rented set**  
   *Problem*: Report requires the shop ID too.  
   *Fix*: Include `shop` in the Item or the key tuple.

4. **Copying objects instead of storing pointers**  
   *Effect*: Each mutating operation would create new objects → unnecessary memory and slower look‑ups.  
   *Solution*: Store pointers or references to the same `Item` instance in all sets.

---

## 7. Takeaway

- **Sorted collections** (TreeSet/TreeSet/Set) give you *constant‑time* best‑element retrieval.
- **Hash maps** give you *O(1)* shop → movie → copy lookup.
- The combination solves both *search* and *report* in O(1) while keeping updates cheap.

> **Remember**: The interview is not only about writing code; it’s about demonstrating *why* your data‑structure choices make the solution efficient.

Happy coding and good luck on your next interview!

---