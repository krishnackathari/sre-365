# LeetCode Palindrome Number

## Problem
Given an integer `x`, return `true` if `x` is a palindrome, and `false` otherwise.

An integer is a palindrome when it reads the same backward as forward.

---

## Approach
- Negative numbers are **not** palindromes, since the minus sign makes the reversed value different.
- Convert the integer to a string.
- Compare the string with its reversed version.
- If both are equal, the number is a palindrome.

---

## Complexity Analysis
- **Time Complexity:** O(n), where `n` is the number of digits in `x`.
- **Space Complexity:** O(n), due to string conversion.

---

## Python Solution

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0:
            return False

        y = str(x)
        return y == y[::-1]

