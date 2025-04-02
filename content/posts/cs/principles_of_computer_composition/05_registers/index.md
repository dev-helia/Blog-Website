---
title: "Registers File reg[]"
date: 2025-03-31
weight: 5
tags: ["Registers", "RISC-V", "x0-x31", "Zero Register"]
---



### 1. How Many Registers in RISC-V?

- RISC-V defines **32 general-purpose registers**:  
  `x0` ~ `x31` (each 32-bit, holds a **word**)

- Trade-off:
  - More registers = more variables can be stored  
  - But → harder to access quickly → slower hardware

---

### 2. Register Classification

| Type         | Alias      | Range      | Usage Hint                      |
|--------------|------------|------------|----------------------------------|
| Saved regs   | `s0`~`s1`  | `x8`~`x9`   | Values preserved across function calls |
|              | `s2`~`s11` | `x18`~`x27` |                                  |
| Temp regs    | `t0`~`t2`  | `x5`~`x7`   | Temporary variables              |
|              | `t3`~`t6`  | `x28`~`x31` |                                  |

> Note: Registers have **no inherent type**, unlike variables in C.  
> Their role depends on how you use them.

---

### 3. Special Register: x0 (Zero Register)

- **x0** is hardwired to `0`  
  - Always contains `0`  
  - Any attempt to write → has no effect

- Why useful?
  - Used in constant comparisons, clearing values, etc.  
  - Saves instruction space by avoiding explicit constants

---

### Summary

- Total: 32 registers (x0 to x31)  
- Key takeaway: understand **naming convention + classification + behavior**
