# LeetCode 70 – Climbing Stairs

## Problem
You are climbing a staircase. It takes `n` steps to reach the top.

Each time, you can either climb **1 step** or **2 steps**.  
Return the number of **distinct ways** you can climb to the top.

### Examples
- Input: `n = 2` → Output: `2`  
  Explanation:  
  - 1 step + 1 step  
  - 2 steps  

- Input: `n = 3` → Output: `3`  
  Explanation:  
  - 1 + 1 + 1  
  - 1 + 2  
  - 2 + 1  

---

## Approach
- This is a **dynamic programming** problem.
- To reach step `n`, the last move must be:
  - from step `n - 1` (1 step), or
  - from step `n - 2` (2 steps).
- Therefore:
  
  `ways(n) = ways(n - 1) + ways(n - 2)`

- Instead of storing all results, we only keep track of the **previous two steps** to save space.

---

## Complexity Analysis
- **Time Complexity:** O(n)  
  We iterate once from step 3 to step `n`.
- **Space Complexity:** O(1)  
  Only constant extra space is used.

---

## Python Solution

```python
class Solution:
    def climbStairs(self, n: int) -> int:

        if n <= 2:
            return n
        
        one_step = 2
        two_step = 1
        ways = 0

        for step in range(3, n + 1):
            ways = one_step + two_step
            two_step = one_step
            one_step = ways
            
        return ways

