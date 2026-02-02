# Python Interview Cheat Sheet (Strategy, Not Memorization)

## Goal
Recognize the approach instantly and explain it clearly in interviews,
without remembering solutions.

---

## STEP 1: Ask These 5 Questions (IN ORDER)

### 1️⃣ Am I allowed to create a new data structure?
Look for words like:
- “in-place”
- “constant space”
- “modify the input”

❌ If YES → overwrite input carefully  
✅ If NO → feel free to use set / dict / list

---

### 2️⃣ Am I scanning left → right only once?
If the problem:
- talks about substrings
- longest / shortest
- remove duplicates
- checking pairs while scanning

👉 You should NOT restart loops

Say:
> “I’ll scan once and maintain state.”

---

### 3️⃣ What do I need to REMEMBER while scanning?
This decides everything.

| Need to remember | Use |
|------------------|-----|
| seen values | set |
| value → index | dict |
| count / length | int |
| carry | int |
| structure | recursion |

Say:
> “While scanning, I’ll keep track of ___.”

---

### 4️⃣ Am I modifying the input itself?
If YES:
- One pointer reads
- One pointer overwrites valid data

Say:
> “I’ll overwrite the input while scanning.”

(No need to say slow/fast.)

---

### 5️⃣ Is this a tree problem?
Ask:
> “Can I solve left and right using the same logic?”

If YES → recursion.

Say:
> “This naturally breaks into left and right subtrees.”

---

## HOW THIS MAPS TO YOUR GIT REPO

### 🟢 Strings / Arrays (Scan Once)

**Files**
- leet-longest-substring-without-repeating
- leet-twosum
- leet-removeduplicates
- leet-search-insert

**What to say**
> “I’ll scan once and keep track of what I’ve seen.”

---

### 🟡 In-place Array Problems

**Files**
- leet-removeduplicates
- leet-search-insert

**What to say**
> “I’ll overwrite the array while scanning.”

---

### 🔵 Lookup Problems

**Files**
- leet-twosum
- leet-roman2int
- leet-firstindex

**What to say**
> “I’ll trade memory for speed using a hash map.”

---

### 🟣 Linked List Construction

**Files**
- leet-add-two-numbers

**What to say**
> “I’ll walk through both lists and build the result as I go.”

---

### 🔴 Tree Problems

**Files**
- leet-maxdepthbinarytree
- leet-sametree
- leet-symmetrictree
- leet-convertsortedarraytobst

**What to say**
> “I’ll solve this recursively using left and right subtrees.”

---

### 🟠 Math / Carry / Simulation

**Files**
- leet-plusone
- leet-addbinary
- leet-sqrtx
- leet-climbstairs

**What to say**
> “I’ll simulate step by step while carrying state forward.”

---

## THE ONLY 5 SENTENCES YOU NEED TO MEMORIZE

1️⃣ “I’ll scan once and keep track of state.”  
2️⃣ “I’ll overwrite the input while scanning.”  
3️⃣ “I’ll trade memory for speed using a map or set.”  
4️⃣ “This naturally breaks into left and right subtrees.”  
5️⃣ “I’ll process digit by digit while carrying over.”

That’s it.

---

## HOW TO SOUND SENIOR (VERY IMPORTANT)

❌ Don’t say:
- sliding window
- slow / fast pointers
- pattern names

✅ Say:
- “scan once”
- “maintain state”
- “overwrite input”
- “recursive structure”

Interviewers care about **thinking**, not buzzwords.

---

## FINAL REMINDER

You are NOT expected to remember solutions.
You are expected to:
- recognize the type
- explain a plan
- adjust calmly

This cheat sheet does exactly that.

