---
title: "Understanding IEEE 754 & Fraction Encoding"
date: 2025-03-07
weight: 3
tags: ["ieee754", "floating point", "fractions", "CS61C"]
---

## Representing Fractions (Binary Point)

### Key Idea: Fixed Binary Point

Use a fixed binary point to separate **negative** and **non-negative powers**:

- Example: `0b xx.xxxx` (6-bit format)  
- Each position represents:  
  `2^1 | 2^0 | . | 2^-1 | 2^-2 | ...`

> Example:  
> `10.101` = 1×2^1 + 0×2^0 + 1×2^-1 + 0×2^-2 + 1×2^-3 = `2.625`

### Scientific Notation

Use base-10 or base-2 scientific form:  
`2.625 = 2 × 10^0 + 6 × 10^-1 + 2 × 10^-2 + 5 × 10^-3`

So in binary:  
`2.625 = 10.101 = 1×2^1 + 0×2^0 + 1×2^-1 + 0×2^-2 + 1×2^-3`

---

## Why IEEE 754 Is So Important?

All CPUs, GPUs, and AI accelerators rely on **floating-point math**, especially IEEE 754:

- Used in languages: `float` / `double` in C, C++, Java, Python, Rust...  
- Widely applied in physics, graphics, AI, simulations...

### IEEE 754 Updates

- 2008: Added **Half-Precision (16-bit)**  
- Extended to **Quad Precision (128-bit)**  
- Focus on more flexible and accurate representations

---

## IEEE 754 Design Goals

### (1) Precision at Scale

Accuracy matters for compounding operations

- IEEE 754 supports both 32-bit and 64-bit FP  
- Results are deterministic and portable (for AI/ML)

---

### (2) Handling Floating-Point Errors

Robust error cases:

- **NaN (Not a Number)**: like 0.0 / 0.0  
- **Overflow**: exceeds max range  
- **Underflow**: value too small to represent  
- **+0 and -0**: IEEE allows signed zero for special edge cases

> These enhance **numerical stability** and prevent hidden bugs!

---

### (3) Compatibility with Two’s Complement

IEEE 754 works *in harmony* with integer arithmetic:

- +0 in IEEE 754 is binary `000...0`, same as integer +0  
- Ensures compatibility across int/float transitions

> Helps CPUs optimize under mixed integer/float workloads

---

Summary:  
IEEE 754 isn’t just a spec—it’s a **global standard for reliable math**, powering everything from Pixar rendering to neural nets to finance tools.

Next up, we’ll dive into instruction sets and how these formats are loaded into registers!

---

