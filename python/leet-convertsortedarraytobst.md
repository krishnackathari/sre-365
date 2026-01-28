# 108. Convert Sorted Array to Binary Search Tree

## Problem (Simple Words)

You are given a list of numbers that is:
- **Already sorted**
- You must turn it into a **Binary Search Tree (BST)**
- The tree must be **balanced** (not leaning to one side)

---

## Key Rules (Kid Version)

1. **Middle number becomes the parent**
2. **Smaller numbers go to the left**
3. **Bigger numbers go to the right**
4. Do the same thing again for left and right parts
5. Stop when there are no numbers left

---

## Why the Middle?

Picking the middle number:
- Keeps the tree balanced
- Automatically follows BST rules
- Works for both odd and even lengths

---

## Step-by-Step Example

Input:
```
nums = [-10, -3, 0, 5, 9]
```

Middle → `0` (becomes root)

Left part:
```
[-10, -3]
```

Right part:
```
[5, 9]
```

Repeat the same steps for each part.

Final Tree:
```
        0
       / \
     -3   9
     /   /
  -10   5
```

---

## Code (Recursive Solution)

```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        # If there are no numbers, there is no tree
        if not nums:
            return None

        # Pick the middle index
        mid = len(nums) // 2

        # Middle number becomes the root
        root = TreeNode(nums[mid])

        # Build left subtree from left half
        root.left = self.sortedArrayToBST(nums[:mid])

        # Build right subtree from right half
        root.right = self.sortedArrayToBST(nums[mid + 1:])

        return root
```

---

## Important Understanding

- `sortedArrayToBST` is **NOT predefined**
- It is **your own function**
- It keeps calling itself to build smaller trees
- Left vs right is decided **only by smaller vs bigger values**

---

## One-Line Memory Trick

**Middle becomes parent, smaller left, bigger right**

