# LeetCode 83 – Remove Duplicates from Sorted List

## Problem
Given the `head` of a **sorted linked list**, delete all duplicates such that each element appears **only once**.  
Return the linked list **sorted**.

A linked list node contains:
- `val` – the value
- `next` – reference to the next node

---

## Important Clarification (Linked List vs Python List)

This problem uses a **linked list**, not a regular Python list.

- ❌ You cannot use indexing (`head[i]`)
- ❌ You cannot use `len(head)`
- ✅ You must traverse using the `.next` pointer

Example linked list:
```
1 → 1 → 2 → 3 → None
```

This is different from a Python list:
```
[1, 1, 2, 3]
```

---

## Approach
- Use a pointer called `current` to traverse the linked list.
- Initialize `current` to `head`.
- Because the list is **sorted**, duplicate values will always be **adjacent**.
- While traversing:
  - If `current.val == current.next.val`, skip the duplicate node by rewiring pointers.
  - Otherwise, move `current` to the next node.
- Modify the list **in place** (no extra memory).
- Return `head`, which still points to the first node of the updated list.

---

## How `head` and `current` Are Handled

- `head` points to the **first node** of the linked list.
- `current = head` means both variables reference the **same node in memory**.
- When we execute:
  ```
  current.next = current.next.next
  ```
  we change the **link between nodes**, effectively removing the duplicate node.
- Since `head` points to the same first node, the change is automatically reflected.
- `head` itself is never reassigned.

---

## Complexity Analysis
- **Time Complexity:** O(n), where n is the number of nodes.
- **Space Complexity:** O(1), as no extra data structures are used.

---

## Python Solution

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        current = head

        while current and current.next:
            if current.val == current.next.val:
                current.next = current.next.next
            else:
                current = current.next

        return head
```

---

## Example Walkthrough

Input:
```
1 → 1 → 1 → 2 → 3 → 3
```

Output:
```
1 → 2 → 3
```

---

## Key Takeaways
- Linked lists require **pointer manipulation**, not indexing.
- Updating `current.next` modifies the list seen by `head`.
- This solution works for **any number of duplicates**.
- This is the **expected interview approach**.

