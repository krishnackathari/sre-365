# LeetCode 88 – Merge Sorted Array

## Problem
You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers `m` and `n`, representing the number of valid elements in `nums1` and `nums2`.

Merge `nums2` into `nums1` so that `nums1` becomes a single array sorted in non-decreasing order.

- `nums1` has a length of `m + n`
- The last `n` elements of `nums1` are set to `0` and should be ignored
- The result must be stored **in-place** inside `nums1`
- Do **not** return anything

---

## Approach
- Use three pointers:
  - `i` pointing to the last valid element in `nums1`
  - `j` pointing to the last element in `nums2`
  - `k` pointing to the last position in `nums1`
- Compare elements from the end of both arrays
- Place the larger element at position `k`
- Move pointers accordingly
- If elements remain in `nums2`, copy them into `nums1`

This works because `nums1` already has enough empty space at the end.

---

## Complexity Analysis
- **Time Complexity:** O(m + n)
- **Space Complexity:** O(1)

---

## Python Solution

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        i = m - 1
        j = n - 1
        k = m + n - 1

        while i >= 0 and j >= 0:
            if nums1[i] > nums2[j]:
                nums1[k] = nums1[i]
                i -= 1
            else:
                nums1[k] = nums2[j]
                j -= 1
            k -= 1

        while j >= 0:
            nums1[k] = nums2[j]
            j -= 1
            k -= 1
```

