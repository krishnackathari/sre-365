# LeetCode Two Sum

## Problem
Given an array of integers `nums` and an integer `target`, return the indices of the two numbers such that they add up to the target.

Each input has exactly one solution, and the same element cannot be used twice.

---

## Approach
Use a hash map to store numbers we have already seen along with their indices, and for each element check if its complement (`target - num`) already exists.

---

#Complexity Analysis

Time Complexity: O(n) — the array is traversed once and dictionary lookups are constant time.
Space Complexity: O(n) — a hash map is used to store up to n elements.

---

## Python Solution

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:

        numbers = {}

        for i, num in enumerate(nums):
            diff = target - num

            if diff in numbers:
                return [numbers[diff], i]

            numbers[num] = i
