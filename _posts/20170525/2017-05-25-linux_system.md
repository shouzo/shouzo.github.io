---
layout: post
title:  "20170525 [學習筆記] Linux 系統程式 (12)"
date:   2017-05-25 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課堂講義
    * [Chapter 8: Memory-Management Strategies](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170525/ch08.pdf)


### (一) Contiguous Allocation
* Main memory must support both OS and user processes
* Limited resource, must allocate efficiently
* Contiguous allocation is one early method

* Main memory usually into two **partitions**
    * Resident operating system, usually held in low memory with interrupt vector
    * User processes then held in high memory
    * Each process contained in single contiguous section of memory

* Relocation registers used to protect user processes from each other, and from changing operating-system code and data
    * Base register contains value of smallest physical address
    * Limit register contains range of logical addresses – each logical address must be less than the limit register
    * MMU maps logical address dynamically
    * Can then allow actions such as kernel code being `transient` and kernel changing size

![](https://i.imgur.com/z3R2FCC.png)


* Multiple-partition allocation
    * Degree of multiprogramming limited by number of partitions
    * Variable-partition sizes for efficiency (sized to a given process’ needs)
    * Hole – block of available memory; holes of various size are scattered throughout memory
    * When a process arrives, it is allocated memory from a hole large enough to accommodate it
    * Process exiting frees its partition, adjacent free partitions combined
    * Operating system maintains information about
        * (a) allocated partitions
        * (b) free partitions (hole)

![](https://i.imgur.com/JQ1uTUe.png)


### (二) Dynamic Storage-Allocation Problem
* **First-fit**：Allocate the `first` hole that is big enough
* **Best-fit**：Allocate the `smallest` hole that is big enough; must search entire list, unless ordered by size
    * Produces the smallest leftover hole
* **Worst-fit**：Allocate the `largest` hole; must also search entire list
    * Produces the largest leftover hole

* First-fit and best-fit better than worst-fit in terms of speed and storage utilization


### (三) Fragmentation
* **External Fragmentation** – total memory space exists to satisfy a request, but it is not contiguous
* **Internal Fragmentation** – allocated memory may be slightly larger than requested memory; this size difference is memory internal to a partition, but not being used
* First fit analysis reveals that given N blocks allocated, 0.5 N blocks lost to fragmentation
    * 1/3 may be unusable -> **50-percent rule**
* Reduce external fragmentation by **compaction**
    * Shuffle memory contents to place all free memory together in one large block
    * Compaction is possible only if relocation is dynamic, and is done at execution time
    * I/O problem
        * Latch job in memory while it is involved in I/O
        * Do I/O only into OS buffers
* Now consider that backing store has same fragmentation problems


### (四) Segmentation
* Memory-management scheme that supports user view of memory

![](https://i.imgur.com/hiMN3YJ.png)

* A program is a collection of segments
    * A segment is a logical unit such as：`main program`、`procedure`、`function`、`method`、`object`、`local variables`、`global variables`、`common block`、`stack`、`symbol table`、`arrays`

![](https://i.imgur.com/PKuwv70.png)


#### Segmentation Architecture
* Logical address consists of a two tuple：`<segment-number, offset>`
* **Segment table** – maps two-dimensional physical addresses; each table entry has
    * **base** – contains the starting physical address where the segments reside in memory
    * **limit** – specifies the length of the segment
* **Segment-table base register (STBR)** points to the segment table’s location in memory
* **Segment-table length register (STLR)** indicates number of segments used by a program：`segment number s is legal if s < STLR`
* Protection
    * With each entry in segment table associate
        * validation bit = 0 >> illegal segment
        * read/write/execute privileges
* Protection bits associated with segments; code sharing occurs at segment level
* Since segments vary in length, memory allocation is a dynamic storage-allocation problem
* A segmentation example is shown in the following diagram

![](https://i.imgur.com/raq8eDi.png)
