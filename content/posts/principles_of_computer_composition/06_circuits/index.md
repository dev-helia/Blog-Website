---
title: "Switches in Circuits"
date: 2025-03-06
weight: 6
tags: ["Circuits", "Switch", "Logic"]
---

# Switches

- The basic element of physical implementations  
- **Convention**: if input is a `1`, the switch is **asserted**

```
   A         Z
   o——o      💡
    |         |
   ===        ===
```
- **Open** switch if A is `0` (unasserted) and turn **OFF** light bulb (Z)  
- **Close** switch if A is `1` (asserted) and turn **ON** light bulb (Z)  


# Maximum Clock Frequency

What is the max frequency of this circuit?

- Limited by how much time needed to get correct **Next State to Register** (`t_setup` constraint)

**Equation:**

```
Max Delay = CLK-to-Q Delay  
          + CL Delay  
          + Setup Time

Min Period = Max Delay  
Max Freq = 1 / Min Period
```

> Assumes: Max Delay > Hold Time

# Arithmetic Logic Unit (ALU)


- Most processors contain a special logic block called the **Arithmetic Logic Unit (ALU)**
  - We'll show you an easy one that does ADD, SUB, bitwise AND, and bitwise OR

**Schematic:**

```
     A────┐
         │32
     B────┘
         ┌──────┐
         │ ALU  │────── R
         └──────┘
             │
            32
```

**Behavior Table:**

- when `S=00`, `R = A + B`
- when `S=01`, `R = A - B`
- when `S=10`, `R = A AND B`
- when `S=11`, `R = A OR B`

---