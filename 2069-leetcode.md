---
title: LeetCode 2069. Walking Robot Simulation II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem recap (LeetCode 2069 – *Walking Robot Simulation II*)

* A `width × height` grid is on the XY‑plane.  
  Bottom‑left cell = `(0, 0)`, top‑right cell = `(width‑1, height‑1)`.  
  The robot can face one of the four cardinal directions – “North”, “East”, “South”, “West”.
* The robot starts at `(0, 0)` facing **East**.  
* `step(num)` – the robot must take `num` single‑cell steps.  
  For each step:  
  1. Try to move one cell forward in the current direction.  
  2. If that cell would be outside the grid, **turn 90° counter‑clockwise** instead of moving.  
  3. After the turn, retry the same step (so a turn counts *as* a single step).  
* `getPos()` – return `[x, y]`.  
* `getDir()` – return the current direction string.

**Constraints**

```
2 ≤ width, height ≤ 100
1 ≤ num ≤ 10^5
≤ 10^4 total calls to step/getPos/getDir
```

---

## 2.  Core idea – reduce the number of simulated steps

The robot’s trajectory is *periodic*: after walking around the perimeter of the grid it will be back at the same position with the same direction.  

Perimeter length  
```
P = 2 * (width + height) – 4   // number of cells in the outer ring
```
For example, a 6×3 grid → `P = 2*(6+3)-4 = 14`.

If we take `step(num)`, only `num % P` “real” steps matter – the rest just repeat the same cycle.

Because `width, height ≤ 100`, the perimeter `P ≤ 396`.  
So after the modulo operation we can safely simulate the remaining at most 395 steps in a loop – this is O(1) per call and never exceeds a few hundred iterations.

---

## 3.  Reference solutions

### 3.1 Java (LeetCode‑style)

```java
// Java 17 (compatible with LeetCode)
public class Robot {
    private final int w, h;      // grid size
    private int x, y;            // current position
    private int dir;             // 0:E, 1:N, 2:W, 3:S

    private static final String[] DIRS = {"East", "North", "West", "South"};
    // movement vectors for E, N, W, S
    private static final int[] DX = {1, 0, -1, 0};
    private static final int[] DY = {0, 1, 0, -1};

    public Robot(int width, int height) {
        this.w = width;
        this.h = height;
        this.x = 0;
        this.y = 0;
        this.dir = 0;   // East
    }

    public void step(int num) {
        if (num == 0) return;

        int perimeter = 2 * (w + h) - 4;
        num %= perimeter;          // only the remainder matters
        if (num == 0) return;      // full cycle → nothing changes

        for (int i = 0; i < num; i++) {
            int nx = x + DX[dir];
            int ny = y + DY[dir];

            if (nx < 0 || nx >= w || ny < 0 || ny >= h) {
                // turn counter‑clockwise
                dir = (dir + 1) & 3;   // +1 mod 4
            } else {
                x = nx;
                y = ny;
            }
        }
    }

    public int[] getPos() {
        return new int[] {x, y};
    }

    public String getDir() {
        return DIRS[dir];
    }
}
```

> **Why it works**  
> * `num % P` guarantees at most 395 iterations.  
> * The modulo eliminates the heavy “repetition” part of the call.  
> * Turning is just an index change; moving is a simple coordinate addition.

### 3.2 Python 3

```python
# Python 3.9
class Robot:
    DIRS = ["East", "North", "West", "South"]
    DX   = [1, 0, -1, 0]
    DY   = [0, 1, 0, -1]

    def __init__(self, width: int, height: int):
        self.w, self.h = width, height
        self.x, self.y = 0, 0
        self.dir = 0            # 0:E, 1:N, 2:W, 3:S

    def step(self, num: int) -> None:
        if num == 0:
            return
        perim = 2 * (self.w + self.h) - 4
        num %= perim
        if num == 0:
            return

        for _ in range(num):
            nx = self.x + self.DX[self.dir]
            ny = self.y + self.DY[self.dir]
            if not (0 <= nx < self.w and 0 <= ny < self.h):
                # counter‑clockwise turn
                self.dir = (self.dir + 1) % 4
            else:
                self.x, self.y = nx, ny

    def getPos(self) -> list[int]:
        return [self.x, self.y]

    def getDir(self) -> str:
        return self.DIRS[self.dir]
```

### 3.3 C++ (GCC 17)

```cpp
// C++17 (LeetCode)
#include <bits/stdc++.h>
using namespace std;

class Robot {
private:
    const int w, h;     // grid dimensions
    int x, y;           // current position
    int dir;            // 0:E 1:N 2:W 3:S

    static const vector<string> DIRS;
    static const int DX[4];
    static const int DY[4];

public:
    Robot(int width, int height) : w(width), h(height), x(0), y(0), dir(0) {}

    void step(int num) {
        if (num == 0) return;

        int perimeter = 2 * (w + h) - 4;
        num %= perimeter;
        if (num == 0) return;

        for (int i = 0; i < num; ++i) {
            int nx = x + DX[dir];
            int ny = y + DY[dir];
            if (nx < 0 || nx >= w || ny < 0 || ny >= h) {
                dir = (dir + 1) & 3;  // turn counter‑clockwise
            } else {
                x = nx;
                y = ny;
            }
        }
    }

    vector<int> getPos() const {
        return {x, y};
    }

    string getDir() const {
        return DIRS[dir];
    }
};

// static members definition
const vector<string> Robot::DIRS = {"East", "North", "West", "South"};
const int Robot::DX[4] = {1, 0, -1, 0};
const int Robot::DY[4] = {0, 1, 0, -1};
```

> **Why the loop is safe** – after `num %= perimeter` the loop runs ≤ 395 iterations (worst case `w=h=100` → `perimeter=396`).  
> The overhead of the modulo itself is negligible, so the overall complexity is **O(1)** per `step` call.

---

## 4.  How to talk about this problem in an interview

| **What you’re asked** | **What the interviewer expects** |
|-----------------------|----------------------------------|
| **Explain the periodicity** | Show you can identify a repeating pattern early on. |
| **Show a fast solution** | A naive loop over `num` could blow up to `10^9` operations → time‑out. |
| **Mention constraints** | The small perimeter (≤ 396) guarantees the loop is constant‑time. |
| **Talk about edge cases** | Full cycles (`num % P == 0`) → no change; turning at corners; initial direction. |
| **Optional: direct math** | Compute distance to edge and skip entire turns in a single step, but not necessary for the limits. |

> **Takeaway** – *Always check for hidden repetitions* before blindly simulating.  
> That trick turns a potentially 1 billion‑step simulation into a handful of operations.

---

## 5.  Blog article (SEO‑ready)

> **Title**  
> **Walking Robot Simulation II – The Good, The Bad, and The Ugly of a LeetCode Interview Problem**

> **Meta description**  
> Learn how to solve LeetCode 2069 “Walking Robot Simulation II” efficiently, what pitfalls to avoid, and how mastering this problem can help you ace coding interviews and land a software engineering role.

> **Keywords**  
> “LeetCode 2069”, “Walking Robot Simulation II solution”, “robot simulation coding interview”, “O(1) robot steps”, “job interview coding problems”, “software engineer interview tips”.

---

### 5.1 The Good – Why this problem is a *classic* interview test

1. **Clear statement** – The rules are unambiguous, which means you can focus on the algorithm, not on parsing the problem.  
2. **Hidden pattern** – A periodic trajectory is a beautiful “aha” moment. Recognizing it shows pattern‑recognition skills, a prized interview ability.  
3. **Small constraints** – The grid is tiny, which lets you prototype with a simple loop and then optimize only if necessary.  
4. **Multiple languages** – Demonstrating the solution in Java, Python, and C++ shows language‑agnostic design skills.  

### 5.2 The Bad – Common pitfalls that will cost you time

| Mistake | Why it fails |
|---------|--------------|
| Simulating every single step (even after the modulo) | With `num = 10^5` and 10^4 calls → 10^9 operations → TLE. |
| Forgetting that a *turn* counts as a step | You may skip a step after a turn and end up with the wrong position. |
| Using the wrong perimeter formula | `2*(w+h)` counts the outer cells *twice*; you need to subtract 4 to avoid double‑counting the corner cells. |
| Off‑by‑one errors when turning at the borders | A robot at `(w‑1, 0)` facing East will turn to North – check your direction order (E→N→W→S→E). |

### 5.3 The Ugly – When you write a “quick and dirty” solution

> **Quick dirty implementation**  
> ```python
> def step(self, num):
>     for _ in range(num):
>         # single step logic
> ```
> This passes the tiny test cases but will time‑out on the hidden stress tests.  

> **Why it’s ugly**  
> * O(`num`) complexity is linear and unbounded.  
> * It ignores the obvious cycle.  
> * It risks a 10‑second runtime on LeetCode, which will show up in your interview log.  

> **Avoid it** – Always look for a cycle or a shortcut before jumping into a full simulation.

---

## 6.  Quick‑start – Running the solutions locally

### 6.1 Java

```bash
# compile & run a quick test
javac Robot.java
java Robot  // (LeetCode will invoke the Robot class; use a small test harness if you want)
```

### 6.2 Python

```bash
# python3 robot.py
python3 - <<'PY'
from robot import Robot

r = Robot(6, 3)
r.step(10)
print(r.getPos(), r.getDir())
r.step(20)
print(r.getPos(), r.getDir())
PY
```

### 6.3 C++

```bash
g++ -std=c++17 -O2 -pipe -static -s robot.cpp -o robot
./robot   # run your own test harness
```

---

## 7.  Take‑away for your next interview

1. **Spot the pattern first** – a grid robot on a rectangle will always move along the perimeter.  
2. **Reduce the problem size with a modulo** – after that only a handful of real steps remain.  
3. **Implement a clean, O(1) loop** – simulation after the modulo is trivial and safe.  
4. **Test edge cases** – full‑cycle step (`num % P == 0`), turning on a corner, large `num` values, etc.  
5. **Explain your thought process** – interviewers love to hear *why* you used a modulo vs. a full simulation.

With the solutions above in hand (Java, Python, C++), you’ll be ready to flash this problem during any coding interview and show that you can think both fast and deeply.

--- 

**Happy coding – and may your robot always find the right direction!**