---
title: "Floating Point Encoding (IEEE 754)"
date: 2025-03-07
weight: 2
tags: ["float", "ieee754", "CS61C", "encoding"]
---

## 🔢 Floating Point Encoding: Single Precision

- Use **normalized** base-2 scientific notation:  
  `+1.xxxxx... × 2^y`
- IEEE 754 (32-bit float) splits into **3 fields**:  
  - **1 bit**: Sign (0 = positive, 1 = negative)  
  - **8 bits**: Exponent (with bias)  
  - **23 bits**: Significand / Mantissa  

> Value = (–1)^Sign × 1.Significand × 2^(Exponent – Bias)

---

## 📌 Bit Breakdown (32-bit float layout)

| Field | Size | Purpose |
|-------|------|---------|
| Sign | 1 bit | 1 = negative, 0 = positive |
| Exponent | 8 bits | Biased exponent |
| Significand | 23 bits | Fractional part (assumes leading 1) |

---

## 🧠 Why Use Biased Notation for Exponent?

If exponent were stored directly (signed int), comparison & sorting would be more complex.  
So IEEE 754 uses **bias notation**, shifting exponent range to all positive values.

### Biased Formula:

```
Actual exponent = Stored exponent – Bias
```

- Bias = 127 for float (32-bit)  
- Bias = 1023 for double (64-bit)  

**Example:**

```
Stored exponent = 10000001₂ = 129  
Actual exponent = 129 – 127 = 2
```

---

### 🧭 Exponent Value Table

| Actual Exponent | Biased (Stored) | Binary |
|-----------------|------------------|--------|
| +127 | 254 | 1111 1110 |
| 0    | 127 | 0111 1111 |
| –126 | 1   | 0000 0001 |
| Special: ∞ / NaN | 255 | 1111 1111 |

🌀 Negative exponents → closer to zero  
🧊 Large biased values → ∞ or NaN

---

## 🆚 Single vs Double Precision

| Type | Bit Length | Significand | Exponent | Bias |
|------|------------|-------------|----------|------|
| Float | 32-bit | 23 bits | 8 bits | 127 |
| Double | 64-bit | 52 bits | 11 bits | 1023 |

---

## ⚠️ Special Encoding Cases (IEEE 754)

| Case | Exponent | Significand | Meaning |
|------|----------|-------------|---------|
| Positive ∞ | all 1s | all 0s | `+∞` |
| Negative ∞ | all 1s | all 0s | `–∞` |
| NaN | all 1s | ≠ 0 | Not a Number |
| Zero | all 0s | all 0s | +0 or –0 |
| Denorm | all 0s | ≠ 0 | Very small numbers (no leading 1) |

---

📌 **Note**:  
Special encodings allow representation of edge cases like NaN, ∞, zero and subnormal numbers (denorms).  

You’ll need this logic later in `pipeline`, `FPU`, and `comparison instructions`.

---