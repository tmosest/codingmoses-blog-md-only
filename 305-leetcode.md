---
title: LeetCode 305. Number of Islands II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

For every new land cell we have to know

* whether this cell is a duplicate of a previous one,
* how many *different* islands touch it,
* and if any of them merge into a single island.

A classic way to keep track of “disjoint sets” (here: islands) is **Union‑Find** (Disjoint Set Union, DSU).  
With path compression and union by size/rank the operations are almost constant time  
(α – the inverse Ackermann function).

We represent the 2‑D grid as a 1‑D slice of length `m*n`.  
For a cell `(x,y)` its index is `x*n + y`.  
A value of `-1` means “water”; otherwise the index of its root in the DSU.

--------------------------------------------------------------------

#### Algorithm
```
parent[i]  : parent pointer of DSU element i, -1 for water
size[i]    : size of the component whose root is i
islandCnt  : current number of islands
ans        : result slice

for each position (x,y) in positions:
    idx = x*n + y
    if parent[idx] != -1:          // duplicate land
        append islandCnt to ans
        continue

    // new land → new island for now
    parent[idx] = idx
    size[idx]   = 1
    islandCnt++

    // look at the four neighbours
    for (dx,dy) in {(1,0),(-1,0),(0,1),(0,-1)}:
        nx, ny = x+dx , y+dy
        if out of bounds or parent[nx*n+ny]==-1:  continue

        rootIdx   = find(idx)
        rootNeigh = find(nx*n+ny)
        if rootIdx != rootNeigh:
            union(rootIdx, rootNeigh)
            islandCnt--          // two islands merged

    append islandCnt to ans
return ans
```

`find` uses path compression, `union` uses size‑based union.

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the correct number of islands after each
addition.

---

##### Lemma 1  
`parent` and `size` always maintain a correct Union‑Find structure for all
currently land cells.

**Proof.**  
When a new land cell `(x,y)` appears we initialise `parent[idx]=idx` and
`size[idx]=1`.  
No union is performed before this, so the DSU contains a single element.  
During the loop over neighbours we only call `find` and `union` on indices
that are already land (`parent[nei] != -1`), thus they are valid DSU nodes.  
`union` merges two disjoint components by linking the smaller one to the
larger, updating the component size, and decreasing the component counter.
No other DSU structure is modified.  
Therefore after each iteration the DSU invariants hold. ∎



##### Lemma 2  
After processing a position `(x,y)` the set of DSU components
exactly corresponds to the set of distinct islands on the grid.

**Proof.**  
We proceed by induction over the processed positions.

*Base*: Before any position the grid is empty, no component exists – true.

*Induction step*: Assume the statement holds after processing the first
`k-1` positions.  
Consider position `k` at cell `c = (x,y)`.

* If `c` is already land, we do nothing and the set of islands is unchanged,
  thus the statement still holds.

* Otherwise we create a new DSU node for `c`.  
  For each of the four neighbours that are land we merge the
  corresponding DSU components.  
  Every union merges exactly two previously distinct islands that become one
  after adding `c`, which is exactly what happens in the grid.
  No other islands are affected.  
  Hence after all unions the DSU components are in bijection with the real
  islands.

Thus the lemma holds for all processed positions. ∎



##### Lemma 3  
`islandCnt` maintained by the algorithm equals the number of DSU components.

**Proof.**  
`islandCnt` is increased by one when a new land cell is added.
Each time `union` merges two components, `islandCnt` is decreased by one.
Because, by Lemma&nbsp;1, the DSU components are precisely the components
of the Union‑Find structure, the counter always equals the number of
components. ∎



##### Theorem  
After each operation the algorithm appends the correct number of islands to
the answer list.

**Proof.**  
By Lemma&nbsp;2 the DSU components after the operation are exactly the real
islands on the grid.  
By Lemma&nbsp;3 `islandCnt` equals the number of these components.  
The algorithm appends `islandCnt` to the answer, therefore the value is
correct. ∎



--------------------------------------------------------------------

#### Complexity Analysis  

Let `k` be the number of positions.

* `find` and `union` run in `α(mn)` time (inverse Ackermann),
  practically constant.
* For each position we inspect at most 4 neighbours.

```
Time   :  O(k · α(mn))          ≈ O(k)
Memory :  O(m·n)   (parent, size slices)
```

The implementation uses only arrays, no hash maps, thus it works
efficiently even for large grids.

--------------------------------------------------------------------

#### Reference Implementation  (Go 1.17)

```go
package main

import (
	"fmt"
)

// DSU for a 2‑D grid
type DSU struct {
	parent []int // parent pointer, -1 for water
	size   []int // component size
	cnt    int   // number of components
}

func NewDSU(m, n int) *DSU {
	total := m * n
	p := make([]int, total)
	s := make([]int, total)
	for i := range p {
		p[i] = -1 // water
	}
	return &DSU{parent: p, size: s}
}

// find with path compression
func (d *DSU) find(i int) int {
	if d.parent[i] == i {
		return i
	}
	d.parent[i] = d.find(d.parent[i])
	return d.parent[i]
}

// union by size, decreases component counter
func (d *DSU) union(a, b int) {
	ra := d.find(a)
	rb := d.find(b)
	if ra == rb {
		return
	}
	if d.size[ra] < d.size[rb] {
		ra, rb = rb, ra
	}
	d.parent[rb] = ra
	d.size[ra] += d.size[rb]
	d.cnt--
}

// add a new land cell at idx (already knows that this cell was water)
func (d *DSU) add(idx int) {
	d.parent[idx] = idx
	d.size[idx] = 1
	d.cnt++
}

func numIslands2(m, n int, positions [][]int) []int {
	dsu := NewDSU(m, n)
	dirX := []int{1, -1, 0, 0}
	dirY := []int{0, 0, 1, -1}

	ans := make([]int, 0, len(positions))
	for _, pos := range positions {
		x, y := pos[0], pos[1]
		idx := x*n + y

		// duplicate land
		if dsu.parent[idx] != -1 {
			ans = append(ans, dsu.cnt)
			continue
		}

		// new land
		dsu.add(idx)

		// neighbours
		for d := 0; d < 4; d++ {
			nx, ny := x+dirX[d], y+dirY[d]
			if nx < 0 || nx >= m || ny < 0 || ny >= n {
				continue
			}
			neiIdx := nx*n + ny
			if dsu.parent[neiIdx] == -1 {
				continue
			}
			// merge components if they are different
			if dsu.find(idx) != dsu.find(neiIdx) {
				dsu.union(idx, neiIdx)
			}
		}

		ans = append(ans, dsu.cnt)
	}
	return ans
}

func main() {
	m := 3
	n := 3
	positions := [][]int{{0, 0}, {0, 1}, {1, 2}, {2, 1}}
	fmt.Println(numIslands2(m, n, positions)) // [1 1 2 3]

	// another test with duplicates
	positions2 := [][]int{{0, 0}, {0, 0}, {1, 1}, {1, 1}}
	fmt.Println(numIslands2(2, 2, positions2)) // [1 1 2 2]
}
```

The program implements exactly the algorithm proven correct above.
It uses only built‑in types and works with Go 1.17.