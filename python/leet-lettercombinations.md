# 📱 LeetCode 17: Letter Combinations of a Phone Number

## 🧠 Problem
Given a string of digits (2–9), return all possible letter combinations based on phone keypad mapping.

---

## 📥 Example

**Input:**
```
23
```

**Output:**
```
['ad', 'ae', 'af', 'bd', 'be', 'bf', 'cd', 'ce', 'cf']
```

---

## ⚙️ Approach (My Solution)

- Create a mapping of digits → letters
- Convert input digits into corresponding letter lists
- Start with `['']` as base
- Iteratively build combinations using nested list comprehension

---

## 🧾 Code

```python
a = {
    2: ['a','b','c'], 
    3: ['d','e','f'], 
    4: ['g','h','i'], 
    5: ['j','k','l'],
    6: ['m','n','o'], 
    7: ['p','q','r','s'], 
    8: ['t','u','v'], 
    9: ['w','x','y','z']
}

b = input("Enter numbers: ").strip()

c = [a[int(i)] for i in b]

print(c)

result = ['']
for first in c:
    result = [third + second for third in result for second in first]

print(result)
```

---

## 🔍 How It Works

Example: `"23"`

Step 1:
```
[['a','b','c'], ['d','e','f']]
```

Step 2:
```
Start: ['']
```

Step 3:
```
After '2' → ['a','b','c']
After '3' → ['ad','ae','af','bd','be','bf','cd','ce','cf']
```

---

## ⚡ Time Complexity
- **O(4ⁿ)** (worst case when digits include 7 or 9)

---

## 💡 Notes
- Clean iterative solution (no recursion)
- Uses Python list comprehension effectively
- Similar to **Cartesian product**

---

## 🧑‍💻 Takeaway
Good example of:
- Building combinations iteratively
- Using nested loops in a Pythonic way
