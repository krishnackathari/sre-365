# 101. Symmetric Tree — Iterative Stack Solution (Very Simple)

## Idea (plain English)

A tree is **symmetric** if:
- Left side looks like the **mirror** of the right side 🪞

We check this using a **stack** instead of recursion.

The stack always stores **pairs of nodes** that should be mirrors.

---

## Rules to remember

1. If **both nodes are None** → OK, continue
2. If **one is None and the other isn’t** → Not symmetric
3. If **values are different** → Not symmetric
4. Otherwise → push their children in **mirror order**

Mirror order means:
- left.left  ↔ right.right
- left.right ↔ right.left

---

## Code (Iterative using Stack)

```python
class Solution:
    def isSymmetric(self, root: Optional[TreeNode]) -> bool:
        # Empty tree is symmetric
        if not root:
            return True

        # Stack holds pairs of nodes that must mirror each other
        stack = [(root.left, root.right)]

        while stack:
            left, right = stack.pop()

            # Both nodes missing → mirror matches
            if not left and not right:
                continue

            # One missing → mirror breaks
            if not left or not right:
                return False

            # Values must be equal
            if left.val != right.val:
                return False

            # Push children in mirror order
            stack.append((left.left, right.right))  # outer children
            stack.append((left.right, right.left))  # inner children

        # All checks passed
        return True
```

---

## One-line memory trick

**Outside with outside, inside with inside**

(left.left ↔ right.right)  
(left.right ↔ right.left)

