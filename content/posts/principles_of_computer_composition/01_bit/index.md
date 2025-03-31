---
title: "Hex & Binary Basics"
date: 2025-03-09
weight: 1
tags: ["bit", "binary", "CS61C", "fundamentals"]
---

## 🔍 Hexadecimal and bit/byte Relationship

Hexadecimal (Hex) is a base-16 system used to represent binary data. Its key relationships:

- 1 hex digit = 4 bits (also called a **nibble**)  
- 2 hex digits = 1 byte (8 bits)

In computer systems:

- `1 bit` = smallest binary unit, can be 0 or 1  
- `1 byte` = 8 bits  
- `1 nibble` = 4 bits  
- `1 word` = usually 4 bytes (32 bits)  
- `1 double word` = usually 8 bytes (64 bits)  

---

## 🌟 Example: Hex vs Binary Table

| Hex | Binary | Size |
|-----|--------|------|
| A   | 1010   | 4 bits (1 nibble) |
| 3F  | 0011 1111 | 8 bits (1 byte) |
| 7E2A | 0111 1110 0010 1010 | 16 bits (2 bytes) |
| FF AA 01 | 1111 1111 1010 1010 0000 0001 | 24 bits (3 bytes) |

---

## ✅ Summary

- 1 hex digit = 4 bits  
- 2 hex digits = 1 byte (8 bits)  
- 8 hex digits = 4 bytes = 1 word = 32 bits

---

## 🧠 Offset Bits and Block Size

The number of **offset bits** depends on the **block size**, using this formula:

\[
\text{Offset Bits} = \log_2(\text{Block Size})
\]

For example, if the block size is **8 bytes**, then:

\[
\log_2(8) = 3 \text{ bits}
\]

So 3 offset bits are needed to identify the exact byte inside the block:

| Offset Bits | Meaning |
|-------------|---------|
| 000 | Byte 0 |
| 001 | Byte 1 |
| 010 | Byte 2 |
| ... | ... |
| 111 | Byte 7 |

---

## 📎 Note

This concept is critical when dealing with cache addressing and memory hierarchy. Understanding how offset bits map into memory blocks helps later in cache tag/index/offset separation.

---

