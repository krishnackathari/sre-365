# 5. Longest Palindromic Substring

## Problem (Simple Words)

Given a string `s`, return the **longest substring** that is a **palindrome**.

A palindrome reads the same forward and backward.

---

## Examples

Input:
```
"babad"
```

Output:
```
"bab"   ( "aba" is also valid )
```

Input:
```
"cbbd"
```

Output:
```
"bb"
```

---

## Key Idea (Interview Explanation)

A palindrome always **grows from a center**.

There are **two types of centers**:

1. **Odd length palindromes**  
   Example: `"aba"`, `"racecar"`  
   → center is a single character

2. **Even length palindromes**  
   Example: `"bb"`, `"abba"`  
   → center is between two characters

To find the longest palindrome:
- Try every index as a center
- Expand outward while characters match
- Do this for both odd and even centers
- Keep the longest substring found

---

## Code (Expand Around Center)

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if not s:
            return ""

        best = ""

        def expand(left, right):
            while left >= 0 and right < len(s) and s[left] == s[right]:
                left -= 1
                right += 1
            return s[left + 1:right]

        for i in range(len(s)):
            # Odd-length palindrome (center at i)
            p1 = expand(i, i)

            # Even-length palindrome (center between i and i+1)
            p2 = expand(i, i + 1)

            if len(p1) > len(best):
                best = p1
            if len(p2) > len(best):
                best = p2

        return best
```

---

## Example Walkthrough

For:
```
s = "cbbd"
```

- Odd centers find: `"c"`, `"b"`, `"b"`, `"d"`
- Even center between `b` and `b` finds: `"bb"`

Longest palindrome:
```
"bb"
```

---

## Why `expand(i, i)` and `expand(i, i+1)`?

- `expand(i, i)` catches **odd-length palindromes** like `"aba"`
- `expand(i, i+1)` catches **even-length palindromes** like `"bb"`

We need **both** to catch all cases.

---

## Time & Space Complexity

- Time: **O(n²)**  
  (Each center expands outward)
- Space: **O(1)**  
  (Only pointers and variables used)

---

## One-Line Memory Trick

**Try every center, expand outward, keep the longest palindrome.**

