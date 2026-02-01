# 3. Longest Substring Without Repeating Characters

## Problem (Simple Words)

You are given a string.

Your task:
- Find the **longest continuous part** (substring)
- That has **no repeated characters**
- Return the **length** of that substring

---

## Example

Input:
```
"abcabcbb"
```

Valid substrings without repeats:
- "a"
- "ab"
- "abc" ✅
- "bca"
- "cab"

Longest length:
```
3
```

---

## Key Idea (Kid Version)

- We use a **window** on the string
- The window moves from **left to right**
- Inside the window, **no letter is allowed to repeat**
- If a repeat appears → move the **left side** until it is fixed

---

## What Each Variable Means

- `seen` → letters currently inside the window
- `left` → left side of the window
- `right` → right side of the window
- `max_len` → longest window seen so far

---

## Code (Sliding Window with Set)

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        seen = set()     # letters in the current window
        left = 0         # left side of the window
        max_len = 0      # best answer so far

        for right in range(len(s)):
            # If character already exists, shrink window from left
            while s[right] in seen:
                seen.remove(s[left])
                left += 1

            # Add current character
            seen.add(s[right])

            # Update maximum length
            max_len = max(max_len, right - left + 1)

        return max_len
```

---

## Step-by-Step Example

For:
```
s = "abcb"
```

| Window | Action |
|------|--------|
| "a" | add |
| "ab" | add |
| "abc" | add |
| "abcb" | ❌ repeat → shrink |
| "cb" | fixed |

Longest window:
```
"abc"
```

Length:
```
3
```

---

## Why This Works

- Each character enters the window once
- Each character leaves the window once
- Time complexity is **O(n)**
- Window always stays valid (no duplicates)

---

## One-Line Memory Trick

**Grow right → shrink left on repeat → remember the biggest**

