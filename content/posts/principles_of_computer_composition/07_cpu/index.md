---
title: "Clock Cycle and Instruction Stages"
date: 2025-03-31
weight: 6
tags: ["Clock Cycle", "Pipeline", "CPU", "Instruction Stages", "RISC-V"]
---

## Clock Cycle

A **single-cycle CPU** completes **all stages** of an instruction within **one long clock cycle**.

Each instruction goes through the following five stages:

1. **Instruction Fetch**
2. **Decode / Register Read**
3. **Execute**
4. **Memory Access**
5. **Register Write Back**

> The clock cycle is long enough to complete all these stages without interruption.

---

## Instruction Execution Stages (RISC-V)

| Stage | Name                        | Description |
|-------|-----------------------------|-------------|
| IF    | **Instruction Fetch**       | Fetch the instruction from `IMEM`, increment `PC += 4` |
| ID    | **Instruction Decode**      | Decode the instruction, read `rs1` and `rs2` |
| EX    | **Execute**                 | Perform computation via ALU (arithmetic or address calculation) |
| MEM   | **Memory Access**           | `lw`: read from memory; `sw`: write to memory |
| WB    | **Write Back**              | Write result to `rd` |

> Every instruction passes through **IF, ID, EX, WB**.  
> Only `lw` and `sw` instructions go through the **MEM** stage.
