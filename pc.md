# Understanding Program Counter, Cache Memory, and Memory Hierarchy

> **Computer Architecture Notes**

## Table of Contents
- [Introduction: Where Does PC Point?](#introduction-where-does-pc-point)
- [Why Memory Hierarchy Matters](#why-memory-hierarchy-matters)
- [Cache Hit/Miss Detection](#cache-hitmiss-detection)
- [PC Behavior on Cache Miss](#pc-behavior-on-cache-miss)
- [Complete Example: Real Assembly Code](#complete-example-real-assembly-code)
- [Virtual vs Physical Addresses](#virtual-vs-physical-addresses)
- [Summary of Key Concepts](#summary-of-key-concepts)
- [Common Misconceptions](#common-misconceptions)

---

## Introduction: Where Does PC Point?

### The Fundamental Question

Textbooks often state: *"The Program Counter (PC) points to the next instruction to be executed."*

**But where exactly is that instruction?**
- Main memory?
- L1 instruction cache (L1i)?
- L2 or L3 cache?

### The Answer

The PC contains a **virtual memory address** that conceptually points to a location in the **main memory address space**. However, in practice:

1. The PC holds a **virtual address**
2. The MMU (Memory Management Unit) translates it to a **physical address**
3. The instruction is fetched from **L1i cache** (if present)
4. On cache miss, it's fetched from lower levels: L2 → L3 → Main Memory
5. **The CPU's execution core can only consume instructions from L1i**. On a cache miss, the memory controller brings the data into L1i, and the core then reads it from there. While the core itself never accesses main memory directly, the hardware memory subsystem may use techniques like direct data placement for prefetching or streaming that can bypass some cache levels.

---

## Why Memory Hierarchy Matters

### The Critical Principle

**The CPU's execution core always interfaces with L1 cache.** It never directly executes instructions from main memory or lower-level caches.

### Cache Miss Flow

```
Main Memory (~200+ cycles)
     ↓
L3 Cache (~40 cycles)
     ↓
L2 Cache (~12 cycles)
     ↓
L1 Cache (~4 cycles)
     ↓
   CPU
```

**Key insight:** If the CPU could bypass caches and execute directly from main memory, the entire memory hierarchy would be pointless!

### Performance Impact

| Level | Access Time |
|-------|-------------|
| L1 hit | ~4 cycles |
| L2 hit | ~12 cycles |
| L3 hit | ~40 cycles |
| Main memory | ~200+ cycles |

---

## Cache Hit/Miss Detection

### Cache Line Structure

Each cache line contains:
- **Tag bits**: Upper portion of memory address
- **Valid bit**: Indicates if line contains valid data
- **Data/Instruction**: The actual cached content
- **Metadata**: Dirty bit, replacement policy bits, etc.

### Address Decomposition

A memory address is split into three parts:

| Tag | Index | Offset |
|-----|-------|--------|
| Identifies memory block | Selects cache line | Byte within line |

### Hit/Miss Determination Algorithm

1. Use **Index** bits to select cache line
2. Check **Valid bit** (must be 1)
3. Compare **Tag** from address with stored tag
4. **Hit** if: Valid = 1 AND Tags match
5. **Miss** if: Valid = 0 OR Tags don't match

---

## PC Behavior on Cache Miss

### What Happens to PC on Instruction Cache Miss?

When L1i cache miss occurs:

1. Fetch stage attempts to read from L1i using current PC value
2. Cache miss detected
3. **Pipeline stalls** — PC holds its current value, doesn't increment
4. Miss handling begins — request propagates: L2 → L3 → Main Memory
5. CPU waits (entire pipeline typically stalls in simple in-order cores)
6. Instruction arrives and is loaded into L1i
7. PC advances — fetch completes, pipeline resumes

**Critical point:** The PC doesn't move to the next instruction until the current instruction is successfully fetched into L1i.

### Advanced Note: Out-of-Order Execution

**Important:** The complete pipeline stall described above applies to **simple in-order execution cores**. Modern **out-of-order (OoO) processors** are more sophisticated:

- OoO CPUs may execute independent instructions from later in the code stream that are already cached
- Simultaneous Multithreading (SMT) allows execution from a separate thread while waiting
- The fetch stage still stalls for the missing instruction, but execution units may remain busy
- However, forward progress on the thread with the I-cache miss is still blocked until the instruction arrives

---

## Complete Example: Real Assembly Code

### Sample Program

```asm
Address        Machine Code      Instruction
0x00000000:    66 b8 01 00      mov $0x1,%ax
0x00000004:    66 83 c0 01      add $0x1,%ax
```

### Cache Configuration

- **Cache size**: 16 KB (16,384 bytes)
- **Line size**: 64 bytes
- **Associativity**: Direct-mapped
- **Number of lines**: 16,384 / 64 = 256 lines

### Address Bit Allocation

For 32-bit addresses:
- **Offset bits**: log₂(64) = 6 bits (address bytes within line)
- **Index bits**: log₂(256) = 8 bits (select cache line)
- **Tag bits**: 32 - 8 - 6 = 18 bits (identify memory block)

---

### Step-by-Step Execution

#### Step 1: PC = 0x00000000 (First Instruction)

**Address Breakdown:**
```
Address = 0x00000000
        = 0000 0000 0000 0000 0000 0000 0000 0000 (binary)
```

| Tag (18 bits) | Index (8 bits) | Offset (6 bits) |
|---------------|----------------|-----------------|
| `000000000000000000` | `00000000` | `000000` |
| `0x00000` | `0x00` | `0x00` |

**Cache Lookup:**
- Check cache line at index = 0
- Check if tag = `0x00000`
- Check valid bit

**Assumption:** Cold start (empty cache)
- Valid bit = 0
- **Result: CACHE MISS** ❌

**Miss Handling:**

1. Pipeline stalls, PC remains at `0x00000000`
2. Fetch 64-byte block starting from physical address `0x00000000`
3. Block contains addresses `0x00000000` through `0x0000003F`
4. Load into L1i at cache line index 0
5. Set tag = `0x00000`, valid = 1
6. Extract bytes at offset 0: `66 b8 01 00`
7. Decode instruction: `mov $0x1,%ax`
8. Execute: Load value 1 into register AX
9. PC advances to `0x00000004`

**Important:** The instruction bytes `66 b8 01 00` must be in L1i cache before the CPU can decode and execute them.

---

#### Step 2: PC = 0x00000004 (Second Instruction)

**Address Breakdown:**
```
Address = 0x00000004
        = 0000 0000 0000 0000 0000 0000 0000 0100 (binary)
```

| Tag (18 bits) | Index (8 bits) | Offset (6 bits) |
|---------------|----------------|-----------------|
| `000000000000000000` | `00000000` | `000100` |
| `0x00000` | `0x00` | `0x04` |

**Cache Lookup:**
- Check cache line at index = 0
- Stored tag = `0x00000` ✓ (matches!)
- Valid bit = 1 ✓
- **Result: CACHE HIT** ✅

**Hit Handling:**

1. No pipeline stall! Instruction already in cache
2. Extract bytes at offset 4: `66 83 c0 01`
3. Decode instruction: `add $0x1,%ax`
4. Execute: Add 1 to register AX
5. PC advances to `0x00000008`

---

### Key Observations

1. **First instruction:** MISS (cold start)
2. **Second instruction:** HIT (spatial locality!)
3. Both instructions fit in the same 64-byte cache line
4. We fetched 64 bytes but only needed 8 bytes total for both instructions
5. **This "waste" is intentional!** It's the fundamental trade-off that makes caches effective — fetching larger blocks exploits spatial locality
6. After the initial miss, we achieve 100% hit rate for subsequent instructions in the same block
7. This demonstrates the power of **spatial locality**

---

## Virtual vs Physical Addresses

### The Complete Picture

The PC holds a **virtual address**, not a physical address (in systems with virtual memory).

**Complete flow:**

1. PC contains **virtual address** of next instruction
2. Virtual address sent to **MMU (Memory Management Unit)**
3. MMU uses **TLB (Translation Lookaside Buffer)** to translate
4. **Physical address** is produced
5. Physical address used to check **L1i cache**
6. On cache miss, physical address propagates through memory hierarchy

### TLB (Translation Lookaside Buffer)

- **TLB Hit** (common case): Translation happens in ~1 cycle
- **TLB Miss** (rare): Must walk page table in memory (expensive!)

---

## Summary of Key Concepts

1. **PC holds virtual address** pointing to main memory address space
2. **Instructions are fetched from L1i**, not main memory directly
3. **CPU core always interfaces with L1**, never bypasses it
4. **Cache miss ⇒ pipeline stall** until instruction arrives in L1i (in simple in-order cores)
5. **Hit/miss detection** uses tag bits, index bits, and valid bit
6. **Virtual-to-physical translation** happens via MMU/TLB before cache lookup
7. **Memory hierarchy works** because CPU always goes through L1
8. **Spatial locality** means fetching larger blocks improves hit rates

---

## Common Misconceptions

| ❌ Misconception | ✅ Reality |
|------------------|-----------|
| PC points directly to L1i cache | PC holds a virtual address that maps to a location in the process's virtual address space |
| CPU core executes from main memory on cache miss | CPU core always waits for instructions to arrive in L1i; it never directly accesses main memory |
| Offset alone determines hit/miss | Tag + Valid bit determine hit/miss; offset selects byte within the cache line |
| PC holds physical address | PC holds virtual address (translated by MMU/TLB to physical) |
| Pipeline always continues during instruction cache miss | Simple in-order pipelines stall completely; OoO processors may execute independent instructions |

---

## Additional Resources

- [What Every Programmer Should Know About Memory](https://people.freebsd.org/~lstewart/articles/cpumemory.pdf)
- [Computer Architecture: A Quantitative Approach](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy & Patterson
- [Intel 64 and IA-32 Architectures Optimization Reference Manual](https://www.intel.com/content/www/us/en/developer/articles/technical/intel-sdm.html)

---

## License

This document is licensed under [Creative Commons CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

---

**Questions or corrections?** Feel free to open an issue or submit a pull request!
