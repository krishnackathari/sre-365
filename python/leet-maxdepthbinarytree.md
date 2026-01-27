# 104. Maximum Depth of Binary Tree — Iterative Stack (Simple)

## Idea (very simple)

The **depth of a tree** is:
- How many nodes are there on the **longest path**
- From the **root** to the **deepest leaf**

We will:
- Walk through the tree using a **stack**
- Keep track of **how deep** we are at each node
- Remember the **maximum depth seen so far**

---

## How the stack works

The stack stores **two things together**:
- The node
- The depth at that node

Example stack item:
```
(node, depth)
```

---

## Steps

1. If the tree is empty → depth is `0`
2. Push `(root, 1)` into the stack
3. While the stack is not empty:
   - Pop one node and its depth
   - Update `max_depth`
   - Push left child with `depth + 1`
   - Push right child with `depth + 1`
4. When done → return `max_depth`

---

## Code (Iterative using Stack)

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        # Empty tree has depth 0
        if not root:
            return 0

        max_depth = 0
        stack = [(root, 1)]  # (node, current depth)

        while stack:
            node, depth = stack.pop()

            # Update maximum depth seen so far
            max_depth = max(max_depth, depth)

            # Push children with increased depth
            if node.left:
                stack.append((node.left, depth + 1))

            if node.right:
                stack.append((node.right, depth + 1))

        return max_depth
```

---

## Example walkthrough

For this tree:
```
    3
   / \
  9  20
     / \
    15  7
```

Longest path:
```
3 → 20 → 15
```

Depth = **3**

---

## One-line memory trick

**Depth = how many nodes you count while going down**

