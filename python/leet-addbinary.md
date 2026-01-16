# LeetCode 67 – Add Binary

## Problem
Given two binary strings `a` and `b`, return their sum as a binary string.

### Examples
- Input: `a = "11"`, `b = "1"` → Output: `"100"`
- Input: `a = "1010"`, `b = "1011"` → Output: `"10101"`

---

## Approach
- Convert each binary string into its integer value.
- Add the two integers.
- Convert the resulting integer back into a binary string.
- Handle the edge case where the sum is `0`.

---

## Complexity Analysis
- **Time Complexity:** O(n + m)  
  Where `n` and `m` are the lengths of the two binary strings.
- **Space Complexity:** O(1)  
  Only constant extra space is used for computation.

---

## Python Solution

```python
class Solution:
    def convertbinarytoint(self, d):
        ans = 0
        for i in d:
            ans = ans * 2 + int(i)
        return ans

    def addBinary(self, a: str, b: str) -> str:
        c = self.convertbinarytoint(a) + self.convertbinarytoint(b)
        final = ""

        if c == 0:
            return "0"

        while c > 0:
            remainder = c % 2
            final = str(remainder) + final
            c = c // 2

        return final

