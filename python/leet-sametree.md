# LeetCode 100 – Same Tree

## Problem
Given the roots of two binary trees `p` and `q`, determine whether they are **the same**.

Two binary trees are considered the same if:
- They are **structurally identical**
- The corresponding nodes have the **same value**

---

## Approach
- If both nodes are `None`, the trees are the same at this position.
- If one node is `None` and the other is not, the trees are different.
- If both nodes exist but their values differ, the trees are different.
- Recursively compare:
  - the **left subtree** of `p` with the **left subtree** of `q`
  - the **right subtree** of `p` with the **right subtree** of `q`
- The trees are the same only if **all recursive checks return true**.

This approach directly follows the definition of a “same tree”.

---

## Complexity Analysis
- **Time Complexity:** O(n), where n is the number of nodes.
- **Space Complexity:** O(h), where h is the height of the tree due to recursion stack.

---

## Python Solution

```python
class Solution:
    def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
        # If both nodes are empty, they are the same
        if not p and not q:
            return True

        # If one is empty and the other is not, they are different
        if not p or not q:
            return False

        # If values differ, trees are different
        if p.val != q.val:
            return False

        # Recursively compare left and right subtrees
        return (
            self.isSameTree(p.left, q.left) and
            self.isSameTree(p.right, q.right)
        )
```

---

## Key Takeaway
> Two trees are the same **only if** their values match **and** their left and right subtrees match at every node.

