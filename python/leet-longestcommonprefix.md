# LeetCode Longest Common Prefix

## Problem
Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

---

## Approach
- Assume the first string is the common prefix.
- Compare characters index by index with all other strings.
- If a mismatch occurs or a string ends, return the prefix up to that index.
- If no mismatch occurs, the entire first string is the common prefix.

---

## Complexity Analysis
- **Time Complexity:** O(n × m), where n is the number of strings and m is the length of the shortest string.
- **Space Complexity:** O(1).

---

## Python Solution

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ""

        start = strs[0]

        for i in range(len(start)):
            for word in strs[1:]:
                if i >= len(word) or word[i] != start[i]:
                    return start[:i]

        return start

