# Assignment 8: Virtual Memory Management in xv6

**Student:** Parepally Aravind  
**Roll No:** 2401MC36  
**Course:** MA3105

## Overview

This assignment implements four virtual memory management extensions in xv6:

1. **Q1 (Virtual-to-Physical Address Translation):** Implements the `sys_va2pa` system call to expose page table walks and translate virtual addresses into physical addresses.
2. **Q2 (Memory Statistics):** Implements the `getmeminfo` system call to monitor the total and free physical memory pages.
3. **Q3 (Page Table Dump):** Implements the `vmprint` system call to display the hierarchical structure of page tables.
4. **Q4 (Lazy Allocation):** Implements demand paging by handling `T_PGFLT` page faults and allocating memory only when required.

## Consolidated Report

### Q1: Virtual-to-Physical Address Translation

The `sys_va2pa` system call is implemented using syscall number 22 and exposes the `walkpgdir` functionality to translate virtual addresses into their corresponding physical addresses. It displays the Page Directory Index (PDX), Page Table Index (PTX), page offset, and page entry flags. The implementation also demonstrates that `fork()` creates separate physical memory pages for the same virtual address in the parent and child processes.

### Q2: Physical Memory Statistics

The `kalloc.c` file is modified to maintain a `free_pages` counter that tracks the number of available physical memory pages. The `getmeminfo` system call provides information about total and free memory. Testing shows that `sbrk` decreases the available page count, and `fork()` reduces it further by allocating additional pages. After the child process terminates and the parent executes `wait`, the free page count returns to its original value, demonstrating proper memory reclamation without leaks.

### Q3: Page Table Dump

The `vmprint` functionality traverses the page directory entries (PDEs) and displays the page table hierarchy. User-space PDEs are expanded to show their corresponding page table entries (PTEs) with appropriate indentation, while kernel-space PDEs are summarized. The output also helps identify the stack guard page, where the `PTE_U` flag is cleared to prevent user-mode access.

### Q4: Lazy Allocation

Lazy allocation is implemented by modifying `sys_sbrk` so that it increases the process size (`sz`) without immediately allocating physical memory. When a page fault occurs, the `trap.c` handler detects `T_PGFLT` and invokes `lazyalloc` to allocate and map a zero-initialized physical memory frame. The `copyuvm` function is also modified to skip page table entries that are not present. Testing demonstrates that requesting 32 MB of memory through `sbrk` initially consumes no physical frames until the allocated memory is accessed.

## Directory Structure

- **`q1/`**: Contains `vatest.c`, code diffs, and screenshots for virtual-to-physical address translation.
- **`q2/`**: Contains `memtest.c`, `meminfo.h`, code diffs, and screenshots for physical memory statistics.
- **`q3/`**: Contains `vmprinttest.c`, code diffs, and screenshots for page table visualization.
- **`q4/`**: Contains `lazytest.c`, `meminfo.h`, code diffs, and screenshots for lazy allocation and demand paging.
