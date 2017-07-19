---
layout: post
title:  "20170615 [學習筆記] Linux 系統程式 (15)"
date:   2017-06-15 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課堂講義
    * [Chapter 9: Virtual-Memory Management](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170615/ch09.pdf)


### (一) Allocation of Frames
* Each process needs minimum number of frames
* Example: IBM 370 – 6 pages to handle SS MOVE instruction:
    * instruction is 6 bytes, might span 2 pages
    * 2 pages to handle from
    * 2 pages to handle to
* Maximum of course is total frames in the system
* Two major allocation schemes
    * fixed allocation
    * priority allocation
* Many variations

#### 1. Fixed Allocation
* Equal allocation – For example, if there are 100 frames (after allocating frames for the OS) and 5 processes, give each process 20 frames
    * Keep some as free frame buffer pool
* Proportional allocation – Allocate according to the size of process
    * Dynamic as degree of multiprogramming, process sizes change

![](https://i.imgur.com/EwupsLy.png)


#### 2. Priority Allocation
* Use a proportional allocation scheme using priorities rather than size
* If process P i generates a page fault,
    * select for replacement one of its frames
    * select for replacement a frame from a process with lower priority number


#### 3. Global vs. Local Allocation
* **Global replacement** – process selects a replacement frame from the set of all frames; one process can take a frame from another
    * But then process execution time can vary greatly
    * But greater throughput so more common
* **Local replacement** – each process selects from only its own set of allocated frames
    * More consistent per-process performance
    * But possibly underutilized memory


#### 4. Non-Uniform Memory Access
* So far all memory accessed equally
* Many systems are NUMA – speed of access to memory varies
    * Consider system boards containing CPUs and memory, interconnected over a system bus
* Optimal performance comes from allocating memory “close to” the CPU on which the thread is scheduled
    * And modifying the scheduler to schedule the thread on the same system board when possible
    * Solved by Solaris by creating **lgroups**
        * Structure to track CPU / Memory low latency groups
        * Used my schedule and pager
        * When possible schedule all threads of a process and allocate all memory for that process within the lgroup


#### 5. Thrashing
* If a process does not have “enough” pages, the page-fault rate is very high
    * Page fault to get page
    * Replace existing frame
    * But quickly need replaced frame back
    * This leads to:
        * Low CPU utilization
        * Operating system thinking that it needs to increase the degree of multiprogramming
        * Another process added to the system
* **Thrashing** - a process is busy swapping pages in and out

![](https://i.imgur.com/jBcF8pC.png)


#### 6. Demand Paging and Thrashing
* Why does demand paging work? **Locality model**
    * Process migrates from one locality to another
    * Localities may overlap
* Why does thrashing occur? **size of locality > total memory size**
    * Limit effects by using local or priority page replacement


#### 7. Working-Set Model
![](https://i.imgur.com/Nsrm3Lw.png)
![](https://i.imgur.com/Xy48qM2.png)

* **Keeping Track of the Working Set**
![](https://i.imgur.com/QHoALdo.png)


#### 8. Page-Fault Frequency
* More direct approach than WSS
* Establish “acceptable” **page-fault frequency** rate and use local replacement policy
    * If actual rate too low, process loses frame
    * If actual rate too high, process gains frame

![](https://i.imgur.com/ws4FmRA.png)
![](https://i.imgur.com/ujHWuKv.png)


### (二) Memory-Mapped Files
* Memory-mapped file I/O allows file I/O to be treated as routine memory access by **mapping** a disk block to a page in memory
* A file is initially read using demand paging
    * A page-sized portion of the file is read from the file system into a physical page
    * Subsequent reads/writes to/from the file are treated as ordinary memory accesses
* Simplifies and speeds file access by driving file I/O through memory rather than `read()` and `write()` system calls
* Also allows several processes to map the same file allowing the pages in memory to be shared
* But when does written data make it to disk?
    * Periodically and / or at file `close()` time
    * For example, when the pager scans for dirty pages

![](https://i.imgur.com/zWof78r.png)


### (三) Other Considerations -- Prepaging
* To reduce the large number of page faults that occurs at process startup
* Prepage all or some of the pages a process will need, before they are referenced
* But if prepaged pages are unused, I/O and memory was wasted
* Assume s pages are prepaged and α of the pages is used
    * Is cost of `s * α` save pages faults `>` or `<` than the cost of prepaging `s * (1-α)` unnecessary pages?
    * α near zero >> prepaging loses


### (四) Other Issues
![](https://i.imgur.com/289vIx7.png)
---
![](https://i.imgur.com/AzOr7n2.png)
---
![](https://i.imgur.com/9ZuU18h.png)
---
![](https://i.imgur.com/cHTpyGG.png)
