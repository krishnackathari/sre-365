# LeetCode 35 — Search Insert Position

## Problem
Given a sorted array of **distinct** integers `nums` and a target value `target`, return the index if the target is found.  
If not, return the index where it would be inserted in order.

You must write an algorithm with **O(log n)** runtime complexity.

---

## Approach
- The array is already sorted, so we can use **binary search**.
- We search for the **first index where the value is greater than or equal to the target**.
- If the target exists, that index will be returned.
- If it does not exist, that index is where the target should be inserted.

We maintain two pointers:
- `left` for the start of the search range
- `right` for the end of the search range

At each step:
- If `nums[mid] < target`, move the search to the right half.
- Otherwise, move the search to the left half.

When the search finishes, `left` will be at the correct insert position.

---

## Complexity Analysis
- **Time Complexity:** O(log n), because the search range is halved each time.
- **Space Complexity:** O(1), since no extra space is used.

---

## Python Solution

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid - 1

        return left

