# Day 1 – Systems Engineer Notes
## Focus: Logs as Data + File Cursor + Python Basics

---

## 🧠 One-line takeaway
Logs are structured data, and file reading is cursor-based (stateful).

---

## 🔑 Core Concepts

### 1. Logs = structured data (not plain text)
```
Mar 26 10:15:23 kubelet[1234]: Error: Failed to pull image
```
Fields:
- timestamp
- service
- level
- message

👉 Logs = data you can extract

---

### 2. File cursor (VERY IMPORTANT)

- File has a pointer (cursor)
- Reading moves it forward
- End reached → no more data

```python
file.readlines()  # moves cursor to end
for line in file: # nothing left to read
```

👉 Fix:
```python
file.seek(0)
```

---

### 3. Streaming vs loading

```python
for line in file:     # ✅ streaming (good for large files)
file.readlines()      # ❌ loads entire file into memory
```

---

### 4. Python fundamentals (must know)

#### Module
👉 A file with Python code (e.g., `json`, `os`)

```python
import json
```

---

#### Function
👉 Reusable block of code

```python
len(lines)
```

---

#### Method
👉 Function tied to an object

```python
line.strip()
file.readlines()
```

---

#### Object
👉 Data + methods

```python
file = open("logs.txt")
```

---

#### Variable
👉 Stores value

```python
lines = file.readlines()
```

---

### 5. f-strings (clean output)

```python
print(f"Total logs: {len(lines)}")
```

---

## 🛠️ Code

```python
with open("logs.txt", "r") as file:
    lines = file.readlines()

print(f"Total logs: {len(lines)}")

for line in lines:
    print(f"Log: {line.strip()}")
```

---

## ⚠️ Key Insight

👉 Reading = consuming  
👉 Cursor position controls behavior  
👉 Large systems = streaming, not loading

---

## 🔁 Reflection

- Why did `readlines()` + loop fail?
- When should you use streaming?
- What part of logs matters most?

---

## 🚀 Next

👉 Day 2: Filtering logs (error detection)
