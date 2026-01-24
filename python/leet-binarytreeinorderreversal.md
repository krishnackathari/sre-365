# LeetCode 94 – Binary Tree Inorder Traversal (Stack)

## What the Problem Asks
You are given the `root` of a **binary tree**.

Your task is to return the **inorder traversal** of the tree.

**Inorder rule:**
```
Left → Node → Right
```

You must visit every node **exactly once** and return the values in the order they are visited.

---

## Important Clarification (Tree vs List)

This is a **binary tree**, not a Python list.

- ❌ No indexing (`root[0]`)
- ❌ No length checks
- ✅ You move using `.left` and `.right`

Example tree:
```
    2
   / \
  1   3
```

---

## Why Use a Stack?

Recursion naturally follows inorder traversal, but here we **simulate recursion using a stack**.

The stack helps us:
- Remember nodes we must come back to
- Visit nodes in **Left → Node → Right** order
- Avoid recursion depth issues

---

## Core Idea (Plain English)

1. Go **left as far as possible**
   - While going left, **push nodes into the stack**
2. When you cannot go left:
   - Pop a node from the stack
   - Visit it (add its value to result)
   - Move to its right child
3. Repeat until:
   - There is no current node
   - AND the stack is empty

This guarantees inorder traversal.

---

## Python Solution (Stack)

```python
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        result = []
        stack = []
        current = root

        while current or stack:
            # Step 1: go left as far as possible
            while current:
                stack.append(current)
                current = current.left

            # Step 2: visit the node
            current = stack.pop()
            result.append(current.val)

            # Step 3: go right
            current = current.right

        return result
```

---

## Example Walkthrough

Tree:
```
      4
     / \
    2   5
   / \
  1   3
```

Traversal order:
```
[1, 2, 3, 4, 5]
```

---

## Complexity Analysis
- **Time Complexity:** O(n) — each node is visited once
- **Space Complexity:** O(n) — stack in the worst case

---

## Key Takeaways
- Stack replaces recursion
- Always **push while going left**
- **Pop → record → go right**
- This is the **expected interview solution**

