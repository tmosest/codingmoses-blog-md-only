---
title: LeetCode 1758. Minimum Changes To Make Alternating Binary String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **JavaScript (Node.js) – “Minimum Changes to Make Alternating Binary String”**  
*Time O(n) Space O(1)*  

```js
/*  Minimum Changes to Make Alternating Binary String
    --------------------------------------------------
    Reads a binary string from the command line (e.g.:
       node solution.js 10010100
    and prints the minimum number of character flips needed
    to transform it into a perfectly alternating string
    ("1010..." or "0101...").

    The algorithm counts mismatches when we try to force the
    pattern that starts with '1' (i.e. 1,0,1,0,...).
    For any position i the expected digit is:
          i%2===0 → '1'   (because pattern starts with '1')
          i%2===1 → '0'
    If the current digit equals the expected one, we need to flip it,
    so we increment the counter.  The number of flips needed for the
    opposite pattern ("0101...") is simply
          length - flipsForPatternStartingWith1
    The answer is the minimum of the two.

    The script contains no functions or classes – just top‑level
    code that runs in a single file.
*/

const s = process.argv[2] || '';   // read binary string from args

let flipsStartingWith1 = 0;

// Count mismatches for the pattern 1,0,1,0,...
for (let i = 0; i < s.length; i++) {
  if (parseInt(s[i], 10) === i % 2) {
    flipsStartingWith1++;
  }
}

// Minimum flips between the two possible alternating patterns
const result = Math.min(flipsStartingWith1, s.length - flipsStartingWith1);

console.log(result);
```

**How to run**

```bash
# Example
node solution.js 10010100
# Output: 3
```

The script uses only built‑in JavaScript features, runs in linear time, and uses constant additional space.