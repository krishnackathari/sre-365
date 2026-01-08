# LeetCode Valid Parentheses

## Problem
Given a string `s` containing only the characters `'('`, `')'`, `'{'`, `'}'`, `'['`, and `']'`, determine if the input string is valid.

An input string is valid if:
- Open brackets are closed by the **same type** of brackets.
- Open brackets are closed in the **correct order**.
- Every closing bracket has a corresponding opening bracket.

---

## Approach
- Use a **stack** to keep track of opening brackets.
- Traverse the string character by character:
  - If the character is an opening bracket, push it onto the stack.
  - If the character is a closing bracket:
    - Check that the stack is not empty.
    - Check that the top of the stack matches the corresponding opening bracket.
    - Pop the opening bracket from the stack.
- After processing the entire string, the stack must be empty for the string to be valid.

---

## Complexity Analysis
- **Time Complexity:** O(n), where n is the length of the string.
- **Space Complexity:** O(n), in the worst case when all characters are opening brackets.

---

## Python Solution

```python
class Solution:
    def isValid(self, s: str) -> bool:

        stack = []
        match = {')': '(', '}': '{', ']': '['}

        for i in s:
            if i in match.values():
                stack.append(i)
            else:
                if not stack or stack[-1] != match[i]:
                    return False
                stack.pop()

        return len(stack) == 0

