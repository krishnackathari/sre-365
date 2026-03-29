# Day 2 – Systems Engineer Notes
## Focus: Filtering Logs + Case Handling + Thinking Like Debugger

---

## 🧠 One-line takeaway
Not all logs matter — real systems extract signal (errors) from noise.

---

## 🔑 Core Concepts

### 1. Signal vs Noise
Logs contain:
- Noise → info/debug (ignore most of the time)
- Signal → errors, failures, warnings

👉 Your job = extract signal fast

---

### 2. Basic filtering

```python
if "error" in line:
```

👉 Problem:
- case sensitive
- misses: ERROR, Error, failed, FAIL

---

### 3. Case-insensitive filtering

```python
if "error" in line.lower():
```

👉 Normalize first → then compare

---

### 4. Multiple conditions (real-world)

```python
line_lower = line.lower()

if "error" in line_lower or "fail" in line_lower:
    print(line.strip())
```

---

### 5. Why this matters

In production:
- millions of logs
- you cannot read everything
- you must filter quickly

👉 This is how monitoring tools work

---

## 🛠️ Code

```python
with open("logs.txt", "r") as file:
    for line in file:
        line_lower = line.lower()

        if "error" in line_lower or "fail" in line_lower:
            print(f"Important: {line.strip()}")
```

---

## 🧪 Lab Summary

- Read logs using streaming
- Filtered only error/failure lines
- Handled case differences
- Printed meaningful output

---

## ⚠️ Limitations (IMPORTANT)

- What about:
  - "critical"
  - "warning"
  - "timeout"
- What about false positives?

👉 Filtering is imperfect → needs improvement

---

## 🧠 Deep Understanding

- Filtering = first step of observability
- Systems don’t process everything → they reduce data
- Good engineers:
  - reduce noise
  - highlight important signals

---

## 🔁 Reflection

- What keywords would YOU track?
- What if logs use different formats?
- How would you improve accuracy?

---

## 🚀 Next

👉 Day 3: Structuring logs into dictionaries (real power begins)
