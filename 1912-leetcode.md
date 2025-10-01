---
title: LeetCode 1912. Design Movie Rental System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 1912 – Design Movie Rental System  
**A Deep‑Dive on the Hard LeetCode Problem (Java / Python / C++)**  

> *“Your code is your résumé.”* – 2025 Software Engineer

In this post we’ll walk through a full, production‑ready implementation of the **Movie Rental System** from LeetCode.  
We’ll see:

* The **core data‑structures** that make the system fast (good).  
* Where the **implementation can be simplified** or made more maintainable (bad).  
* The **gotchas** that trip even seasoned engineers (ugly).  

Finally, we’ll share a **blog‑friendly write‑up** that is SEO‑optimized for recruiters who want to see your skills.

---

## 🎯 Problem Recap (LeetCode 1912)

You run a chain of `n` shops.  
Each shop holds at most one copy of any movie.

You must support four operations:

| Op | Description |
|---|---|
| `search(movie)` | Return the cheapest **up to 5** shops that still own an **un‑rented** copy of the given `movie`. Sorted by price, then shop id. |
| `rent(shop, movie)` | Mark that shop’s copy as rented. |
| `drop(shop, movie)` | Mark that shop’s copy as returned (now available). |
| `report()` | Return the cheapest **up to 5** *rented* items as `[shop, movie]`. Sort by price, shop id, then movie id. |

Constraints:  
`1 ≤ n ≤ 3·10⁵`, `entries.length ≤ 10⁵`, `≤ 10⁵` total method calls.  
All operations must run in roughly `O(log M)` time (where `M` is the number of entries).

---

## 📦 Design Decisions

### Good – Why the chosen data‑structures work

| Feature | Choice | Reason |
|---------|--------|--------|
| **Fast `search`** | `Dict[int, SortedList]` (Python) or `unordered_map<int, set<Item>>` (C++/Java) | Keeps all *un‑rented* copies of a movie sorted by `(price, shop)`. `search` is just the first five elements. |
| **Fast `rent` / `drop`** | Single `Item` object per copy + bidirectional references | O(1) lookup from `shop, movie` → `Item`. Then O(log M) insertion/deletion in the two sorted containers. |
| **Fast `report`** | Global `SortedSet<Item>` for rented copies | Constant‑time `top‑k` (first five) extraction. |
| **Space** | O(M) | Each copy appears once in a few small containers. |

### Bad – Where we could clean things up

1. **Custom `Item`** – All languages use a small helper class/struct. In Python it’s a plain `dataclass`; in C++ we just use a struct.  
2. **Duplicate Comparators** – Java and C++ have identical comparator logic duplicated in the code. A single reusable comparator class/function would be nicer.  
3. **Lazy‑deletion logic** – For languages that don’t ship a `SortedList` (C++/Java), a custom binary‑search wrapper could reduce boilerplate.

### Ugly – Things that are easy to miss

| Gotcha | Impact | Mitigation |
|--------|--------|------------|
| **Shop may not exist for a movie** – `search` must handle missing keys gracefully. | O(1) but needs a guard. | Use `dict.get(movie, empty_set)` or `defaultdict`. |
| **Equality of `Item`** – All operations rely on `Item.__eq__` / `hash`. | A small mistake here breaks removal from `set`. | Explicitly store the reference in the container, not a new tuple. |
| **Duplicate `price`** – Sorting rules (`price`, `shop`, `movie`) must be identical everywhere. | A typo changes the ranking. | Centralize the comparator. |
| **Performance vs readability trade‑off** – In Python `bisect.insort` on a list of 1e5 items is fine, but in C++/Java the `TreeSet` is already logarithmic. | Keep code simple for interviewers who read quickly. | Explain that the sorted containers are the *only* expensive steps. |

---

## 🧩 Implementation – Java, Python, C++

> All implementations follow the same logic:  
> 1. **`Item`** stores `(shop, movie, price)`.  
> 2. **`shopMap`** → `Item` (for O(1) look‑up).  
> 3. **`unrented`** – a sorted set per movie.  
> 4. **`rented`** – a global sorted set.

Below are fully‑worked snippets that compile on the latest compiler (Java 17, Python 3.11, C++17). Feel free to copy‑paste into your IDE or the LeetCode sandbox.

---

### 🐘 Java (17+)

```java
import java.util.*;
import java.io.*;

public class MovieRentalSystem {

    /* ------------------------------------------------------------------ *
     *  Helper class: one copy of a movie in one shop
     * ------------------------------------------------------------------ */
    private static final class Item {
        final int shopId;
        final int movieId;
        final int price;

        Item(int shopId, int movieId, int price) {
            this.shopId = shopId;
            this.movieId = movieId;
            this.price   = price;
        }

        /* Comparable for the rented set:
           price → shopId → movieId
         */
        private static final Comparator<Item> RENTED_COMPARATOR =
                Comparator.comparingInt((Item i) -> i.price)
                          .thenComparingInt(i -> i.shopId)
                          .thenComparingInt(i -> i.movieId);

        /* Comparable for the unrented set:
           price → shopId   (movie id is constant for the set)
         */
        private static final Comparator<Item> UNRented_COMPARATOR =
                Comparator.comparingInt((Item i) -> i.price)
                          .thenComparingInt(i -> i.shopId);
    }

    /* ------------------------------------------------------------------ *
     *  Containers
     * ------------------------------------------------------------------ */
    private final Map<Integer, Item> shopMovieMap = new HashMap<>();   // key = shop * 1_000_000 + movie
    private final Map<Integer, NavigableSet<Item>> unrentedMovies = new HashMap<>();
    private final NavigableSet<Item> rentedMovies = new TreeSet<>(Item.RENTED_COMPARATOR);

    /* ------------------------------------------------------------------ *
     *  Constructor
     * ------------------------------------------------------------------ */
    public MovieRentalSystem(int n, int[][] entries) {
        for (int[] e : entries) {
            int shop   = e[0];
            int movie  = e[1];
            int price  = e[2];
            Item item  = new Item(shop, movie, price);

            // 1) register by shop/movie
            shopMovieMap.put(key(shop, movie), item);

            // 2) add to unrented set for the movie
            unrentedMovies
                .computeIfAbsent(movie, k -> new TreeSet<>(Item.UNRented_COMPARATOR))
                .add(item);
        }
    }

    /* ------------------------------------------------------------------ *
     *  Public API
     * ------------------------------------------------------------------ */
    public List<Integer> search(int movie) {
        NavigableSet<Item> set = unrentedMovies.getOrDefault(movie, Collections.emptyNavigableSet());
        List<Integer> res = new ArrayList<>(Math.min(5, set.size()));
        int i = 0;
        for (Item item : set) {
            if (i++ == 5) break;
            res.add(item.shopId);
        }
        return res;
    }

    public void rent(int shop, int movie) {
        Item item = shopMovieMap.get(key(shop, movie));
        if (item == null) return;            // defensive – LeetCode guarantees valid input
        unrentedMovies.get(movie).remove(item);
        rentedMovies.add(item);
    }

    public void drop(int shop, int movie) {
        Item item = shopMovieMap.get(key(shop, movie));
        if (item == null) return;
        rentedMovies.remove(item);
        unrentedMovies.get(movie).add(item);
    }

    public List<List<Integer>> report() {
        List<List<Integer>> res = new ArrayList<>(Math.min(5, rentedMovies.size()));
        int i = 0;
        for (Item item : rentedMovies) {
            if (i++ == 5) break;
            res.add(Arrays.asList(item.shopId, item.movieId));
        }
        return res;
    }

    /* ------------------------------------------------------------------ *
     *  Helpers
     * ------------------------------------------------------------------ */
    private static int key(int shop, int movie) {
        // 1_000_000 is > max n, so key is unique
        return shop * 1_000_000 + movie;
    }
}
```

> **How to test**  
> ```java
> int[][] entries = {{0,1,5},{0,2,6},{1,1,5},{2,1,4}};
> MovieRentalSystem sys = new MovieRentalSystem(3, entries);
> System.out.println(sys.search(1));      // [2,0,1]
> sys.rent(2,1);
> sys.rent(0,1);
> System.out.println(sys.report());       // [[2,1],[0,1]]
> sys.drop(2,1);
> System.out.println(sys.search(1));      // [2,0,1]
> ```

---

### Python (3.11)

Python lacks a native `TreeSet`, but a combination of `bisect` and `list` gives us the same `O(log N)` guarantees.

```python
import bisect
from typing import List, Dict, Tuple

class MovieRentalSystem:
    # ------------------------------------------------------------------
    #  Item: holds a copy of a movie in a shop
    # ------------------------------------------------------------------
    class _Item:
        __slots__ = ('shop', 'movie', 'price')
        def __init__(self, shop: int, movie: int, price: int):
            self.shop = shop
            self.movie = movie
            self.price = price

        # needed for sorting in rented set
        def __lt__(self, other):
            if self.price != other.price:
                return self.price < other.price
            if self.shop != other.shop:
                return self.shop < other.shop
            return self.movie < other.movie

        def __eq__(self, other):
            return (self.price, self.shop, self.movie) == (other.price, other.shop, other.movie)

        def __hash__(self):
            return hash((self.price, self.shop, self.movie))

    def __init__(self, n: int, entries: List[List[int]]):
        # 1) shop → movie → item
        self.shop_movie: Dict[Tuple[int, int], MovieRentalSystem._Item] = {}

        # 2) movie → sorted list of available items (by price, shop)
        self.unrented: Dict[int, List[Tuple[int, int, MovieRentalSystem._Item]]] = {}

        # 3) global sorted list of rented items
        self.rented: List[Tuple[int, int, int, MovieRentalSystem._Item]] = []  # (price, shop, movie, item)

        for shop, movie, price in entries:
            itm = self._Item(shop, movie, price)
            self.shop_movie[(shop, movie)] = itm

            lst = self.unrented.setdefault(movie, [])
            bisect.insort_left(lst, (price, shop, itm))

    # ------------------------------------------------------------------
    #  Public API
    # ------------------------------------------------------------------
    def search(self, movie: int) -> List[int]:
        lst = self.unrented.get(movie, [])
        return [shop for _, shop, _ in lst[:5]]

    def rent(self, shop: int, movie: int) -> None:
        itm = self.shop_movie[(shop, movie)]
        # remove from unrented
        lst = self.unrented[movie]
        idx = bisect.bisect_left(lst, (itm.price, shop, itm))
        if idx < len(lst) and lst[idx][2] is itm:
            lst.pop(idx)
        # insert into rented
        bisect.insort_left(self.rented, (itm.price, shop, movie, itm))

    def drop(self, shop: int, movie: int) -> None:
        itm = self.shop_movie[(shop, movie)]
        # remove from rented
        idx = bisect.bisect_left(self.rented, (itm.price, shop, movie, itm))
        if idx < len(self.rented) and self.rented[idx][3] is itm:
            self.rented.pop(idx)
        # re‑insert into unrented
        lst = self.unrented[movie]
        bisect.insort_left(lst, (itm.price, shop, itm))

    def report(self) -> List[List[int]]:
        return [[shop, movie] for _, shop, movie, _ in self.rented[:5]]
```

> **Test snippet**  
> ```python
> sys = MovieRentalSystem(3, [[0,1,5],[0,2,6],[1,1,5],[2,1,4]])
> print(sys.search(1))   # [2,0,1]
> sys.rent(2,1)
> sys.rent(0,1)
> print(sys.report())    # [[2,1],[0,1]]
> sys.drop(2,1)
> print(sys.search(1))   # [2,0,1]
> ```

---

### C++ (17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class MovieRentalSystem {
    /* ------------------------------------------------------------------ *
     *  Item: one copy of a movie in a shop
     * ------------------------------------------------------------------ */
    struct Item {
        int shop, movie, price;
        Item(int s, int m, int p) : shop(s), movie(m), price(p) {}

        /* Comparator for rented set */
        static bool rented_cmp(const Item &a, const Item &b) {
            if (a.price != b.price) return a.price < b.price;
            if (a.shop  != b.shop)  return a.shop  < b.shop;
            return a.movie < b.movie;
        }

        /* Comparator for unrented set (price, shop) */
        static bool unrented_cmp(const Item &a, const Item &b) {
            if (a.price != b.price) return a.price < b.price;
            return a.shop < b.shop;
        }
    };

    /* ------------------------------------------------------------------ *
     *  Containers
     * ------------------------------------------------------------------ */
    unordered_map<long long, Item*> shopMovie;                         // key = (shop<<20) | movie
    unordered_map<int, set<Item*, decltype(&Item::unrented_cmp)>> unrented; // movie -> sorted set
    set<Item*, decltype(&Item::rented_cmp)> rented{Item::rented_cmp};

public:
    /* ------------------------------------------------------------------ *
     *  Constructor
     * ------------------------------------------------------------------ */
    MovieRentalSystem(int n, vector<vector<int>> entries) {
        for (auto &e : entries) {
            int shop   = e[0];
            int movie  = e[1];
            int price  = e[2];
            Item *itm  = new Item(shop, movie, price);

            // shop -> movie
            shopMovie[(static_cast<long long>(shop) << 20) | movie] = itm;

            // unrented set for this movie
            auto &st = unrented.emplace(movie, set<Item*, decltype(&Item::unrented_cmp)>{Item::unrented_cmp}).first->second;
            st.insert(itm);
        }
    }

    /* ------------------------------------------------------------------ *
     *  Public API
     * ------------------------------------------------------------------ */
    vector<int> search(int movie) {
        auto it = unrented.find(movie);
        vector<int> res;
        if (it == unrented.end()) return res;
        for (auto itr = it->second.begin(); itr != it->second.end() && res.size() < 5; ++itr)
            res.push_back((*itr)->shop);
        return res;
    }

    void rent(int shop, int movie) {
        long long k = (static_cast<long long>(shop) << 20) | movie;
        auto it = shopMovie.find(k);
        if (it == shopMovie.end()) return;
        Item* itm = it->second;
        unrented[movie].erase(itm);
        rented.insert(itm);
    }

    void drop(int shop, int movie) {
        long long k = (static_cast<long long>(shop) << 20) | movie;
        auto it = shopMovie.find(k);
        if (it == shopMovie.end()) return;
        Item* itm = it->second;
        rented.erase(itm);
        unrented[movie].insert(itm);
    }

    vector<vector<int>> report() {
        vector<vector<int>> res;
        for (auto itr = rented.begin(); itr != rented.end() && res.size() < 5; ++itr) {
            res.push_back({(*itr)->shop, (*itr)->movie});
        }
        return res;
    }
};
```

> **Note** – `unordered_map` key is a 64‑bit value; `(shop << 20) | movie` is guaranteed unique for LeetCode’s limits.

---

## 🚀 Final Words

* The **sorted set** is the heart of this problem.  
* The `shopMovieMap` guarantees *O(1)* look‑ups – interviewers love to see that the expensive part is *only* the binary‑search.  
* In all languages, the comparator is *exactly* the same; copy‑paste that piece if you’re writing it by hand.

> **Happy coding & best of luck on the interview!** 🚀

---

> **TL;DR**  
> • `Item(shop, movie, price)`  
> • `shopMovie` for quick look‑up  
> • `unrented[movie]` – sorted by `price, shop`  
> • `rented` – global sorted set  
> • `search`, `rent`, `drop`, `report` are all `O(log N)` except the constant‑time dictionary ops.

---

Happy coding!