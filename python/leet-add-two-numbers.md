# 2. Add Two Numbers

## Problem (Kid Version)

You are given **two linked lists**.
Each list stores a number **backwards**.

- First node = ones place
- Next node = tens place
- Next node = hundreds place

Your job:
👉 Add the two numbers  
👉 Return the sum as a **new linked list**, also backwards

---

## Example

Input:
```
l1 = [2,4,3]   → 342
l2 = [5,6,4]   → 465
```

Add:
```
342 + 465 = 807
```

Output:
```
[7,0,8]
```

---

## Simple Idea

We add numbers **digit by digit**, just like school math:

1. Add digits
2. Keep the **carry**
3. Write the last digit
4. Move to the next nodes
5. Repeat until everything is done

---

## Important Tools

- `carry` → stores extra value when sum ≥ 10
- `dummy` → fake head to easily build the result list
- `current` → pointer to the last node in result list

---

## Code (Your Solution)

```python
class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:

        # Dummy node to start the result list
        dummy = ListNode(0)
        current = dummy

        carry = 0

        # Continue while there are digits or carry left
        while l1 or l2 or carry:

            # Get values from nodes (0 if node is missing)
            val1 = l1.val if l1 else 0
            val2 = l2.val if l2 else 0

            # Add values and carry
            total = val1 + val2 + carry

            # Update carry and current digit
            carry = total // 10
            digit = total % 10

            # Create new node with the digit
            current.next = ListNode(digit)
            current = current.next

            # Move to next nodes
            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
            
        # Return the real head (skip dummy)
        return dummy.next
```

---

## Step-by-Step Example

Adding:
```
[2 → 4 → 3]
[5 → 6 → 4]
```

- 2 + 5 = 7 → write 7
- 4 + 6 = 10 → write 0, carry 1
- 3 + 4 + 1 = 8 → write 8

Result:
```
[7 → 0 → 8]
```

---

## Why This Works

- Lists are already reversed → easy addition
- Carry handled just like normal math
- Dummy node makes list creation simple
- Works even if one list is longer

---

## One-Line Memory Trick

**Add digits, keep carry, build the list**

