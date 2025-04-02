---
title: "Memory"
date: 2025-03-31
tags: ["CS61C", "Computer Architecture", "Cache", "Memory"]
categories: ["Computer Science", "CS61C"]
draft: false
---

# Memory Hierarchy

Modern computer systems use a layered memory model to balance **speed**, **capacity**, and **cost**. This structure is known as the **memory hierarchy**.

| Level | Name               | Speed       | Cost      | Capacity      |
|-------|--------------------|-------------|-----------|----------------|
| L0    | Registers          | Fastest     | Highest   | Smallest       |
| L1    | L1 Cache           | Very Fast   | Very High | Tiny           |
| L2    | L2 Cache           | Fast        | High      | Small          |
| L3    | L3 Cache           | Medium      | Moderate  | Medium         |
| L4    | Main Memory (RAM)  | Slower      | Lower     | Large          |
| L5    | SSD/Disk           | Much Slower | Cheapest  | Very Large     |

As we go down the hierarchy:
- Speed **decreases**
- Capacity **increases**
- Cost per bit **decreases**

---

# Virtual Memory

Virtual memory creates the illusion of a large contiguous memory space. It maps program memory to physical memory and uses the **disk** as a backup when RAM is full.

This allows programs to run even when they need more memory than is physically available.

---

# Principle of Locality

To make memory access faster, hardware leverages **temporal** and **spatial locality**.

## Temporal Locality (Locality in Time)

> If a memory location is accessed once, it will likely be accessed again soon.

```c
int x = 42;
for (int i = 0; i < 1000000; i++) {
    sum += x; // frequent access to the same variable
}
```

Since `x` is accessed repeatedly, it stays in the cache, making access fast.

---

## Spatial Locality (Locality in Space)

> If a memory location is accessed, nearby memory locations will likely be accessed soon.

```c
int arr[10000];
for (int i = 0; i < 1000; i++) {
    arr[i] = i * 2;
}
```

Arrays are stored contiguously. When the CPU accesses `arr[i]`, it also loads `arr[i+1]`, `arr[i+2]`, etc., into the cache.

---

## A Bad Locality Example

```c
int arr[1000000];
for (int i = 0; i < 1000000; i += 1000) {
    arr[i] = i; // large stride
}
```

- Every access skips 1000 elements.
- Cache misses increase.
- Performance drops.

---

# Cache Levels and Naming

We often abbreviate cache names like this:

- `L1$` – Level 1 Cache
- `L2$` – Level 2 Cache
- `L3$` – Level 3 Cache
- `D$` – Data Cache
- `I$` – Instruction Cache

These labels are common in textbooks and lectures.

---

# Managing the Hierarchy

| Transition                  | Managed By                     |
|----------------------------|--------------------------------|
| Registers ↔ Memory         | Compiler / Assembly programmer |
| Cache ↔ Main Memory        | Cache controller (hardware)    |
| Main Memory ↔ Disk (VM)    | Operating system               |

---

# Final Goal

> Use small, fast caches to simulate large, fast memory.

Memory hierarchy and locality work together to let the CPU access data quickly **without knowing** it's stored in slow memory layers.

```text
"Temporal likes to reread, Spatial loves neighbors;
Cache's favorite kids: repetitive and cohesive."
```