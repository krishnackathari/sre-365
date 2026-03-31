# Day 3 – Systems Engineer Notes
## Focus: Structuring Logs into Dictionaries

---

## 🧠 One-line takeaway
Raw logs become powerful only when converted into structured data (dictionaries).

---

## 🔑 Core Concepts

### 1. From text → structure

Raw log:
```
Mar 26 10:15:23 kubelet[1234]: Error: Failed to pull image
```

Structured:
```python
{
  "timestamp": "Mar 26 10:15:23",
  "service": "kubelet",
  "level": "error",
  "message": "Failed to pull image"
}
```

👉 Structure = usable data

---

### 2. Dictionary (core data structure)

```python
log = {
    "service": "kubelet",
    "message": "failed"
}
```

- key → value
- flexible
- used everywhere (APIs, AI, systems)

---

### 3. Extracting parts from a string

Basic idea:

```python
parts = line.split(" ")
```

👉 Splits text into pieces

---

### 4. Building structured data

```python
log_entry = {
    "timestamp": timestamp,
    "service": service,
    "message": message
}
```

👉 You define meaning

---

### 5. Why this matters

Without structure:
- hard to search ❌  
- hard to analyze ❌  

With structure:
- easy to filter ✅  
- easy to count ✅  
- easy for AI ✅  

---

## 🛠️ Code (simple version)

```python
with open("logs.txt", "r") as file:
    for line in file:
        parts = line.split()

        timestamp = " ".join(parts[0:3])
        service = parts[3].split("[")[0]
        message = " ".join(parts[4:])

        log_entry = {
            "timestamp": timestamp,
            "service": service,
            "message": message
        }

        print(log_entry)
```

---

## 🧪 Lab Summary

- Split log line into parts
- Extracted timestamp, service, message
- Created dictionary for each log
- Printed structured output

---

## ⚠️ Limitations

- What if format changes?
- What if parts are missing?
- What if log is malformed?

👉 Parsing must be resilient

---

## 🧠 Deep Understanding

- Systems don’t work with raw text
- They convert → structure → process
- This is foundation of:
  - APIs
  - databases
  - AI pipelines

---

## 🔁 Reflection

- Which field is most important for debugging?
- What happens if parsing fails?
- How would you handle bad logs?

---

## 🚀 Next

👉 Day 4: JSON (saving + loading structured data)
