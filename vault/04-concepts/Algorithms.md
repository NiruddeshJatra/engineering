---
tags:
  - medium
source: "[[crash-course-cs-ep08]]"
---

## Definition
The steps a program use to solve a problem

## Details
- **The Black Box Model:** Computer Science is essentially the study of problem-solving. It uses a model where **Input** (the problem) enters a **Black Box** (the algorithm), resulting in **Output** (the solution).
- - **Linear vs. Binary Search:**
    - **Linear Search:** Checking one by one; it is slow (takes _n_ steps for _n_ items).
    - **Binary Search:** Tearing the problem in half repeatedly. This is **logarithmic**, meaning even if the data doubles, it only adds one extra step to the search.
 
## Why it matters to me as a dev
For a developer, the most important takeaway is **Abstraction**. We use high-level tools—like the OpenAI API or Python—to solve complex problems without needing to manage the zeros and ones ourselves. For that, we must use efficient algorithms and handle corner cases.

## In my words
In a [[Programs]], we use algorithms to perform what we want which eventually get translated by [[Compiler]] or [[Interpreter]] into machine code and gets executed by [[CPU]].