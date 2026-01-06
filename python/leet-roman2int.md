# LeetCode Roman to Integer

## Problem
Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

Given a Roman numeral string `s`, convert it to an integer.

Roman numerals are usually written from left to right in descending order.  
In some cases, a smaller numeral appears before a larger numeral to indicate subtraction (e.g., IV = 4, IX = 9).

---

## Approach
Use a dictionary to map Roman numerals to integer values.

Traverse the string from left to right:
- If the current numeral is smaller than the next numeral, subtract its value.
- Otherwise, add its value to the total.

This handles both normal addition and subtraction cases correctly.

---

## Complexity Analysis
- **Time Complexity:** O(n), where n is the length of the Roman numeral string.
- **Space Complexity:** O(1), since the mapping dictionary has a fixed size.

---

## Python Solution

```python
class Solution:
    def romanToInt(self, s: str) -> int:
        values = {
            'I': 1,
            'V': 5,
            'X': 10,
            'L': 50,
            'C': 100,
            'D': 500,
            'M': 1000
        }

        total = 0

        for i in range(len(s)):
            if i + 1 < len(s) and values[s[i]] < values[s[i + 1]]:
                total -= values[s[i]]
            else:
                total += values[s[i]]

        return total

