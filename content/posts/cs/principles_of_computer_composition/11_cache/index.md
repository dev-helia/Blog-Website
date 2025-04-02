---
title: "Cache Structure & Strategies"
date: 2025-03-31
tags: ["CS61C", "Computer Architecture", "Cache", "Memory"]
categories: ["Computer Science", "CS61C"]
draft: false
---

# Cache Structure: Tag | Index | Offset | Data

A memory address is divided into:

- **Tag**: Identifies which block the data belongs to.
- **Index**: Points to a specific cache line (set).
- **Offset**: Locates the exact byte inside a block.
- **Data**: The actual value stored.

For example, in a **4KB Direct-Mapped Cache** with **64B block size**:

| Tag   | Index | Offset | Data Block (64B)         |
|--------|--------|--------|--------------------------|
| 0x1A3 | 010    | 000000 | 64 bytes of data         |
| 0x2B4 | 101    | 000000 | 64 bytes of data         |

---

# Cache Lookup Process

When the CPU sends a memory request:

1. Use the **Index** to locate a cache line.
2. Compare the stored **Tag** with the address tag.
3. If matched → **Hit**, use **Offset** to extract the byte.
4. If not matched → **Miss**, load data from main memory and update cache.

---

# Address Breakdown

When the CPU accesses memory (e.g., a 32-bit address), the cache splits the address into:

- **Offset** (for block position)
- **Index** (to find the set)
- **Tag** (to verify data identity)

Steps:
1. CPU issues an address.
2. Cache splits it: Tag + Index + Offset.
3. Locate set using Index.
4. Match Tag.
5. On Hit → Read directly.
6. On Miss → Fetch from memory, update cache.

---

# Write Policy

When writing data, cache uses one of two strategies:

### Write-Through

- Write to both **Cache** and **Memory** immediately.
- Simple to manage, consistent.
- Slower due to double writes.

### Write-Back

- Write only to **Cache**.
- Mark block as dirty.
- Update memory **only when block is replaced**.
- Faster, but more complex (requires dirty bit tracking).

---

# Write Miss Policies

What happens if the data to be written is **not in cache**?

- **Write Allocate**: Load into cache, then write.
- **No Write Allocate**: Write directly to memory without loading cache.

| Write Hit Policy | Write Miss Policy   | Name                         |
|------------------|---------------------|------------------------------|
| Write-Through    | No Write Allocate   | Simple, but slower           |
| Write-Back       | Write Allocate      | Fast, used in modern CPUs    |

---
# What is Cache Miss?

## Cache Hit:
When the data you want is already in cache:
> CPU: "Perfect, data is right here!"

## Cache Miss:
When the data is not in cache:
> CPU: "Ugh, I have to go all the way to memory to get it!"

---

## Sources of Cache Misses: The 3Cs

### 1. Compulsory Miss (cold start, process switch, first access)
- Caused by: First-time access
- Unavoidable initially
- Effect diminishes in long-running programs

### 2. Capacity Miss
- Caused by: Cache is too small to hold all needed blocks
- Even full associativity won't help if total size is insufficient

### 3. Conflict Miss
- Caused by: Multiple addresses map to the same cache index
- Happens when associativity is low (e.g., direct-mapped cache)

---

## Table Summary of 3Cs

| Miss Type       | Cause                                                    | Optimization                                   |
|------------------|-----------------------------------------------------------|------------------------------------------------|
| Compulsory Miss  | First-time access, never stored in cache                 | Unavoidable, but large-block prefetch can help |
| Capacity Miss    | Cache too small to hold all needed data                  | Increase cache size or use better replacement  |
| Conflict Miss    | Different addresses map to same index, overwrite happens | Increase associativity or optimize access      |

---

## Mnemonic Table (Human Interpretation)

| Miss Type       | Cause                             | Interpretation                | Optimization Direction           |
|------------------|------------------------------------|--------------------------------|----------------------------------|
| Compulsory Miss  | First-time access                  | “Cold start” problem           | Prefetch or wait it out          |
| Capacity Miss    | Cache too small                    | “Not enough space for all”     | Increase size or code locality   |
| Conflict Miss    | Multiple addresses to same index   | “Collision, eviction occurs”   | Higher associativity              |

---

## Mnemonic Summary:

> Compulsory is the first-timer,  
> Capacity runs out of room,  
> Conflict causes a fight.
> 
# Cache Mapping Strategies

### 1. Direct-Mapped (DM)
- Each address maps to **one set**.
- Simple and fast, but high conflict rate.

### 2. Set-Associative (SA)
- Each set holds **multiple blocks**.
- Reduces conflicts, balances speed and flexibility.
- Common: 2-Way, 4-Way, 8-Way SA

### 3. Fully Associative (FA)
- Any block can go **anywhere** in cache.
- Maximum flexibility, no index needed.
- Expensive, slower.

| Mode | Flexibility     | Conflict Rate | Cost     |
|------|------------------|---------------|----------|
| DM   | Least Flexible   | High          | Cheapest |
| SA   | Balanced         | Medium        | Common   |
| FA   | Most Flexible    | Low           | Costly   |

---
# Cache Associativity

The full name is: **Cache Associativity**

At its core, it answers this question:

> "How many possible locations in cache can a memory address be placed into?"

---

## Analogy:

You are a lazy college student. Cache is your library seat.

You’re going to study at a specific table. The rules for picking a seat differ depending on associativity:

| Associativity Type | Seat Selection Rule            | Analogy Explanation                                      |
|---------------------|-------------------------------|-----------------------------------------------------------|
| DM (Direct Mapped)  | You can only sit at table 7. If someone else is there, you must wait. | Each memory address can only go to **one** location → severe conflict |
| 2-Way SA             | You can choose between table 7 and table 8       | Each set has 2 candidate spots → fewer conflicts          |
| FA (Fully Associative) | You can sit anywhere in the room                | Any cache block can go anywhere → minimal conflict        |

---

## So, what does “increasing associativity” mean?

It means:

> Instead of allowing **1 block per set**, we allow **2 blocks**, **4 blocks**, or **8 blocks**.

In other words:

- Higher associativity
- More flexibility
- Fewer mapping collisions

---

## Example:

You have an array: arr[0], arr[64], arr[128], ...

Every 64 bytes, the address maps to the **same cache index**.

- If you use DM (Direct Mapped) Cache:
  - One index can only hold one block
  - So the later blocks will evict the earlier ones → **Conflict Miss**

- If you use a 4-Way SA (4-Way Set Associative):
  - The same set can store 4 blocks
  - Then arr[0], arr[64], arr[128], arr[192] can all coexist
  - Conflict Misses are greatly reduced

---

## Summary Table

| Associativity     | Analogy            | Conflict Rate | Search Speed | Cost        |
|------------------|--------------------|---------------|--------------|-------------|
| 1-Way (DM)       | Fixed seat         | High          | Fast         | Cheapest    |
| N-Way (Set Assoc)| A few seat options | Medium        | Medium       | Common      |
| Fully Associative| Sit anywhere       | Lowest        | Slowest      | Most costly |

---

# Replacement Policy

When a set is full, we must evict one block to insert new data.

### Common Policies

| Policy | Description               | Notes             |
|--------|---------------------------|-------------------|
| LRU    | Least Recently Used       | Most common       |
| FIFO   | First In First Out        | Simple but naive  |
| Random | Random block              | Easy, less optimal|
| LFU    | Least Frequently Used     | Precise but costly|

### LRU is preferred
- Matches **temporal locality**
- Frequently accessed data stays in cache
- Implemented with timestamps or stack structures

---

# Final Summary

- **Tag** identifies the data
- **Index** locates the cache line
- **Offset** finds the byte inside the block
- Write and replacement policies impact performance
- More **associativity** means fewer conflict misses
