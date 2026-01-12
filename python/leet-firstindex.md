# LeetCode 28 – Find the Index of the First Occurrence in a String

## Problem
Given two strings `haystack` and `needle`, return the index of the first occurrence of `needle` in `haystack`.  
If `needle` is not part of `haystack`, return `-1`.

---

## Approach
- Get the length of `needle`.
- Slide a window of size `needle` across `haystack`.
- At each position, compare the substring with `needle`.
- If a match is found, return the current index.
- If no match is found after checking all positions, return `-1`.

---

## Complexity Analysis
- **Time Complexity:** O(n × m), where  
  `n` = length of `haystack` and `m` = length of `needle`.  
  In the worst case, the substring comparison of length `m` happens at each of the `n` positions.
- **Space Complexity:** O(1), since no extra data structures are used.

---

## Python Solution

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        length = len(needle)

        for i in range(len(haystack)):
            if haystack[i] == needle[0]:
                if needle == haystack[i : i + length]:
                    return i

        return -1

