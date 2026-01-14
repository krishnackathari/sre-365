# LeetCode Length of Last Word

## Problem
Given a string `s` consisting of words and spaces, return the length of the **last word** in the string.

A word is defined as a maximal substring consisting of **non-space characters only**.

---

## Approach
- Traverse the string character by character.
- Build words manually instead of using `split()` to demonstrate string parsing.
- When a space is encountered:
  - If a word has been formed, append it to a list.
  - Reset the current word.
- After traversal, append the last word if it exists.
- Return the length of the last word in the list.

This approach correctly handles:
- Leading spaces
- Trailing spaces
- Multiple spaces between words

---

## Complexity Analysis
- **Time Complexity:** O(n), where n is the length of the string.
- **Space Complexity:** O(n), for storing the words list.

---

## Python Solution

```python
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        s_list = []
        s_word = ""

        for i in s:
            if i == " ":
                if s_word:
                    s_list.append(s_word)
                    s_word = ""
            else:
                s_word += i

        if s_word:
            s_list.append(s_word)

        return len(s_list[-1])

