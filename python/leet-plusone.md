# LeetCode 66 – Plus One

## Problem
You are given a large integer represented as an integer array `digits`, where each `digits[i]` is the ith digit of the integer.  
The digits are ordered from most significant to least significant in left-to-right order, and the integer does not contain any leading zeros.

Increment the large integer by one and return the resulting array of digits.

---

## Approach
- Convert the list of digits into a string.
- Convert the string into an integer and add `1`.
- Convert the updated integer back into a string.
- Convert each character of the string back into an integer and return it as a list.

This approach leverages Python’s built-in support for large integers to keep the solution simple and readable.

---

## Complexity Analysis
- **Time Complexity:** O(n), where `n` is the number of digits.  
  The digits are processed during conversion and reconstruction.
- **Space Complexity:** O(n), for creating the new list of digits.

---

## Python Solution

```python
class Solution:
    def plusOne(self, digits: List[int]) -> List[int]:
        number1 = int("".join(map(str, digits))) + 1
        return list(map(int, str(number1)))

