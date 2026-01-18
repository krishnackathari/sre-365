## Problem
Given a non-negative integer `x`, return the square root of `x` rounded **down** to the nearest integer.

The returned integer must be non-negative.

⚠️ You **must not** use built-in exponent functions such as:
- `pow(x, 0.5)`
- `x ** 0.5`

---

## Examples
- Input: `x = 4` → Output: `2`
- Input: `x = 8` → Output: `2`

Explanation:
- √4 = 2
- √8 ≈ 2.828 → rounded down → 2

---

## Approach
- Start with a counter `i = 1`.
- Loop infinitely.
- Square `i` and compare it with `x`.
- If `i * i` becomes greater than `x`, then the correct integer square root is `i - 1`.
- Return `i - 1` immediately.

This works because the moment we cross `x`, the previous number was the largest integer whose square was less than or equal to `x`.

---

## Complexity Analysis
- **Time Complexity:** O(√x)  
  We iterate until `i * i` exceeds `x`.
- **Space Complexity:** O(1)  
  No extra space is used.

---

## Python Solution

```python
class Solution:
    def mySqrt(self, x: int) -> int:
        i = 1
        while True:
            if i * i > x:
                return i - 1
            i += 1

