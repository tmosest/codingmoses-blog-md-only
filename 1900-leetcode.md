---
title: LeetCode 1900. The Earliest and Latest Rounds Where Players Compete - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every test case we are given

* `N` – number of participants in the current round  
* `P1 , P2` – positions of the two players in the current round  
  (positions are `1 … N`, the leftmost player has position `1`)

In one round the following happens

```
player 1  vs player N
player 2  vs player N-1
player 3  vs player N-2
                …
```

The winner of every pair (or the single middle player when `N` is odd)
continues to the next round.  
The winners are placed in one line **sorted by their positions** (left
to right) – the order is completely deterministic, it does not depend on
who wins inside a pair.

The task is to determine after how many rounds the two players meet
(first time).  
The answer is the same for the best possible and for the worst possible
situation – it is uniquely defined by the initial data.



--------------------------------------------------------------------

#### Observation 1  
In a round the two players meet **iff**

```
P1 + P2 = N + 1
```

because the player at position `i` meets the player at position `N-i+1`.

--------------------------------------------------------------------

#### Observation 2  
After a round the positions of the two players in the next round can
be computed **without knowing who won**.

Let `M = (N + 1) / 2` – number of winners (integer division).

For any player at position `x` in the current round the new position in
the next round is

```
newPos = ceil(x / 2)
```

`ceil` can be expressed with integer arithmetic as

```
newPos = (x + 1) / 2            // integer division
```

Proof sketch:

* For the left part of the field (`x ≤ N/2`) the winner is the
  `x`‑th winner, hence his new index is `ceil(x/2)`.
* For the right part of the field (`x > N/2`) the winner is the
  `N-x+1`‑st winner, but after sorting the winners the relative order
  of the two winners coming from the same pair is preserved,
  therefore the new index is also `ceil(x/2)`.
* For the middle player when `N` is odd (`x = (N+1)/2`) the same
  formula gives the correct new index – it ends up in the middle of
  the next round.

Thus the new positions are completely determined by the current
positions and the current number of participants.

--------------------------------------------------------------------

#### Algorithm
For every test case

```
round = 1
while true
        if P1 + P2 == N + 1          // the two players meet
                answer = round
                break
        // otherwise they both survive to the next round
        P1 = (P1 + 1) / 2
        P2 = (P2 + 1) / 2
        N  = (N + 1) / 2
        round++
output answer
```

The loop runs at most `log₂(N)` times, so the complexity is
`O(log N)` per test case.

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the round in which the two players
first meet.

---

##### Lemma 1  
During each iteration of the loop the variables `P1` and `P2`
are the true positions of the two players in the current round.

**Proof.**

* Initially they are the given positions – true.  
* Assume they are correct before an iteration.  
  If they did not meet, both survive to the next round.
  By Observation&nbsp;2 we update them to  
  `P1 = (P1 + 1) / 2` and `P2 = (P2 + 1) / 2`.  
  By the observation these are exactly their positions in the next
  round.  
  Therefore the invariant holds for the next iteration. ∎



##### Lemma 2  
When the loop terminates, the value `answer` equals the first round in
which the two players meet.

**Proof.**

The loop stops only when the condition `P1 + P2 == N + 1` holds.
By Observation&nbsp;1 this condition is equivalent to the two players
meeting in that round.  
Because the loop runs in increasing order of `round`,
the first time the condition becomes true is the first meeting round.
Thus `answer` is the desired value. ∎



##### Theorem  
For every test case the algorithm outputs the number of rounds after
which the two given players meet for the first time.

**Proof.**

By Lemma&nbsp;1 the loop stops exactly when the two players meet.  
By Lemma&nbsp;2 the recorded round number equals the first meeting
round.  
Therefore the printed number is correct. ∎



--------------------------------------------------------------------

#### Complexity Analysis
Let `N` be the initial number of participants.

* Each iteration reduces `N` at least by a factor of two
  (`N ← (N+1)/2`), so the loop runs `⌈log₂ N⌉` times.
* All operations inside the loop are `O(1)`.

Hence

```
Time   :  O(log N)   per test case
Memory :  O(1)
```

--------------------------------------------------------------------

#### Reference Implementation  (Go 1.17)

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	in := bufio.NewReader(os.Stdin)
	var T int
	if _, err := fmt.Fscan(in, &T); err != nil {
		return
	}
	writer := bufio.NewWriter(os.Stdout)
	for ; T > 0; T-- {
		var N, P1, P2 int64
		if _, err := fmt.Fscan(in, &N, &P1, &P2); err != nil {
			return
		}
		round := int64(1)
		for {
			if P1+P2 == N+1 {
				fmt.Fprintln(writer, round)
				break
			}
			// advance to next round
			P1 = (P1 + 1) / 2
			P2 = (P2 + 1) / 2
			N = (N + 1) / 2
			round++
		}
	}
	writer.Flush()
}
```

The program follows exactly the algorithm proven correct above and
conforms to Go 1.17.