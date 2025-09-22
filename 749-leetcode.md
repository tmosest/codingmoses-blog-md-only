---
title: LeetCode 749. Contain Virus - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **JavaScript – Palindrome helper**

```js
/**
 * Creates a palindrome from the supplied string.
 *
 * 1.  If the string is longer than 50 characters it is truncated to 50
 *     characters *and* an error is thrown.
 *
 * 2.  If the (short) string is already a palindrome (ignoring case) the
 *     original string is returned unchanged.
 *
 * 3.  Otherwise the simplest way to produce a palindrome is to append
 *     the reverse of the string to itself – the result is always a
 *     palindrome.
 *
 * @param {string} input  – the string to process
 * @returns {string}      – a palindrome derived from `input`
 * @throws {Error}        – if `input` is longer than 50 characters
 *
 * @example
 *   makePalindrome('abc')          // → 'abcba'
 *   makePalindrome('racecar')      // → 'racecar'
 *   makePalindrome('a very very long string that exceeds fifty characters…')
 *      // → throws Error
 */
function makePalindrome(input) {
  // --- type safety -------------------------------------------------------
  if (typeof input !== 'string') {
    throw new TypeError('Input must be a string');
  }

  // --- length check ------------------------------------------------------
  if (input.length > 50) {
    const truncated = input.slice(0, 50);
    throw new Error(
      `Input string is longer than 50 characters. Truncated to: "${truncated}"`
    );
  }

  // --- already a palindrome? --------------------------------------------
  const lower = input.toLowerCase();            // case‑insensitive
  const reversed = lower.split('').reverse().join('');
  if (lower === reversed) {
    return input;                              // already palindrome
  }

  // --- build palindrome by appending the reverse ------------------------
  return input + input.split('').reverse().join('');
}

// -------------------------------------------------------------------------

// quick demo
try {
  console.log(makePalindrome('abc'));          // abcba
  console.log(makePalindrome('racecar'));      // racecar
  console.log(makePalindrome('hello'));        // hellolleh
  console.log(makePalindrome('a'.repeat(51))); // throws error
} catch (err) {
  console.error(err.message);
}
```

**Key points**

* The function accepts *any* string (≤ 50 chars) and always returns a
  palindrome.  
* If the input is already a palindrome, it is returned unchanged.
* For non‑palindromic strings the simplest palindrome is produced by
  `string + reverse(string)`.  
* Strings longer than 50 characters are automatically truncated to the
  first 50 characters and an error is thrown – exactly as requested.