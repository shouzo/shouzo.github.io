---
layout: post
title:  "20170608 [學習筆記] Linux 系統程式 (14)"
date:   2017-06-08 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課堂講義
    * [Chapter 9: Virtual-Memory Management](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170608/ch09.pdf)


### (一) Background
* Code needs to be in memory to execute, but entire program rarely used
    * Error code, unusual routines, large data structures
* Entire program code not needed at same time
* Consider ability to execute partially-loaded program
    * Program no longer constrained by limits of physical memory
    * Program and programs could be larger than physical memory
* **Virtual memory** – separation of user logical memory from physical memory
    * Only part of the program needs to be in memory for execution
    * Logical address space can therefore be much larger than physical address space
    * Allows address spaces to be shared by several processes
    * Allows for more efficient process creation
    * More programs running concurrently
    * Less I/O needed to load or swap processes
* Virtual memory can be implemented via
    * Demand paging
    * Demand segmentation


### (二) Virtual Address Space
![](http://i.imgur.com/DkDhsAx.jpg)

* Enables **sparse** address spaces with holes left for growth, dynamically linked libraries, etc
* System libraries shared via mapping into virtual address space
* Shared memory by mapping pages read-write into virtual address space
* Pages can be shared during `fork()`, speeding process creation

![](http://i.imgur.com/3jsjqeH.png)


### (三) Demand Paging
* Could bring entire process into memory at load time
* Or bring a page into memory only when it is needed
    * Less I/O needed, no unnecessary I/O
    * Less memory needed
    * Faster response
    * More users
* Page is needed >> reference to it
    * invalid reference >> abort
    * not-in-memory >> bring to memory
* **Lazy swapper** – never swaps a page into memory unless page will be needed
    * Swapper that deals with pages is a **pager**

![](https://i.imgur.com/aLRTern.png)

#### 1. Valid-Invalid Bit
* With each page table entry a valid–invalid bit is associated (**v** >> in-memory – **memory resident**, **i** >> not-in-memory)
* Initially valid–invalid bit is set to **i** on all entries

![](https://i.imgur.com/0Fk929T.jpg)

#### 2. Example
* Memory access time = 200 nanoseconds
* Average page-fault service time = 8 milliseconds
> EAT = (1 – p) x 200 + p (8 milliseconds)
    > = (1 – p) x 200 + p x 8,000,000
    > = 200 + p x 7,999,800
* If one access out of 1,000 causes a page fault, then EAT = 8.2 microseconds. (This is a slowdown by a factor of 40)
* If want performance degradation < 10 percent
    * 220 > 200 + 7,999,800 x p
    * 20 > 7,999,800 x p
    * p < .0000025 (< one page fault in every 400,000 memory accesses)

#### 3. Optimizations
* Copy entire process image to swap space at process load time
    * Then page in and out of swap space
    * Used in older BSD Unix
* Demand page in from program binary on disk, but discard rather than paging out when freeing frame
    * Used in Solaris and current BSD


### (四) Page Fault
* If there is a reference to a page, first reference to that page will trap to operating system
    * **page fault**
* Operating system looks at another table to decide
    * Invalid reference >> abort
    * Just not in memory
* Get empty frame
* Swap page into frame via scheduled disk operation
* Reset tables to indicate page now in memory Set validation bit = **v**
* Restart the instruction that caused the page fault

![](https://i.imgur.com/98V6oNY.png)


#### 1. Aspects of Demand Paging
* Extreme case – start process with no pages in memory
    * OS sets instruction pointer to first instruction of process, non-memory-resident -> page fault
    * And for every other process pages on first access
    * **Pure demand paging**
* Actually, a given instruction could access multiple pages -> multiple page faults
    * Pain decreased because of **locality of reference**
* Hardware support needed for demand paging
    * Page table with valid / invalid bit
    * Secondary memory (swap device with **swap space**)
    * Instruction restart


#### 2. Instruction Restart
* Consider an instruction that could access several different locations
    * block move
    * auto increment/decrement location
    * Restart the whole operation?
        * What if source and destination overlap?

##### Performance of Demand Paging
* Stages in Demand Paging
> 1. Trap to the operating system
> 2. Save the user registers and process state
> 3. Determine that the interrupt was a page fault
> 4. Check that the page reference was legal and determine the location of the page on the disk
> 5. Issue a read from the disk to a free frame:
    > (1) Wait in a queue for this device until the read request is serviced
    > (2) Wait for the device seek and/or latency time
    > (3) Begin the transfer of the page to a free frame
> 6. While waiting, allocate the CPU to some other user
> 7. Receive an interrupt from the disk I/O subsystem (I/O completed)
> 8. Save the registers and process state for the other user
> 9. Determine that the interrupt was from the disk
> 10. Correct the page table and other tables to show page is now in memory
> 11. Wait for the CPU to be allocated to this process again
> 12. Restore the user registers, process state, and new page table, and then resume the interrupted instruction

![](https://i.imgur.com/HA817pb.png)


### (五) Copy-on-Write
* **Copy-on-Write** (COW) allows both parent and child processes to initially share the same pages in memory
    * If either process modifies a shared page, only then is the page copied
* COW allows more efficient process creation as only modified pages are copied
* In general, free pages are allocated from a **pool** of **zero-fill-on-demand** pages
    * Why zero-out a page before allocating it?
* `vfork()` variation on `fork()` system call has parent suspend and child using copy-on-write address space of parent
    * Designed to have child call `exec()`
    * Very efficient

![](http://i.imgur.com/LABMPPo.jpg)


### (六) Page Replacement
* Prevent over-allocation of memory by modifying page-fault service routine to include page replacement
* Use **modify (dirty) bit** to reduce overhead of page transfers – only modified pages are written to disk
* Page replacement completes separation between logical memory and physical memory – large virtual memory can be provided on a smaller physical memory

#### 1. What Happens if There is no Free Frame?
* Used up by process pages
* Also in demand from the kernel, I/O buffers, etc
* How much to allocate to each?
* Page replacement – find some page in memory, but not really in use, page it out
    * Algorithm – terminate? swap out? replace the page?
    * Performance – want an algorithm which will result in minimum number of page faults
* Same page may be brought into memory several times

![](https://i.imgur.com/eFpQyPv.png)

#### 2. Basic Page Replacement
> 1. Find the location of the desired page on disk
> 2. Find a free frame:
    > a. If there is a free frame, use it
    > b. If there is no free frame, use a page replacement algorithm to select a **victim frame**
    > c. Write victim frame to disk if dirty
> 3. Bring the desired page into the (newly) free frame; update the page and frame tables
> 4. Continue the process by restarting the instruction that caused the trap
* Note now potentially 2 page transfers for page fault – increasing EAT

![](https://i.imgur.com/Hm6MvuN.png)

#### 3. Page and Frame Replacement Algorithms
* **Frame-allocation algorithm** determines
    * How many frames to give each process
    * Which frames to replace
* **Page-replacement algorithm**
    * Want lowest page-fault rate on both first access and re-access
* Evaluate algorithm by running it on a particular string of memory references (reference string) and computing the number of page faults on that string
    * String is just page numbers, not full addresses
    * Repeated access to the same page does not cause a page fault

##### (1) First-In-First-Out (FIFO) Algorithm
![](https://i.imgur.com/cSezcCO.png)
![](https://i.imgur.com/NVa4BTe.png)
![](https://i.imgur.com/6VeXbGw.png)

##### (2) Optimal Algorithm
* Replace page that will not be used for longest period of time
    * 9 is optimal for the example on the next slide
* How do you know this?
    * Can’t read the future
* Used for measuring how well your algorithm performs

![](https://i.imgur.com/1jc2ngr.png)

##### (3) Least Recently Used (LRU) Algorithm
* Use past knowledge rather than future
* Replace page that has not been used in the most amount of time
* Associate time of last use with each page

![](https://i.imgur.com/RiLxhrF.png)

* Counter implementation
    * Every page entry has a counter; every time page is referenced through this entry, copy the clock into the counter
    * When a page needs to be changed, look at the counters to find smallest value
        * Search through table needed
* Stack implementation
    * Keep a stack of page numbers in a double link form:
    * Page referenced:
        * move it to the top
        * requires 6 pointers to be changed
    * But each update more expensive
    * No search for replacement
* LRU and OPT are cases of **stack algorithms** that don’t have Belady’s Anomaly

![](http://i.imgur.com/yvsGPvm.jpg)

##### (4) LRU Approximation Algorithms
* LRU needs special hardware and still slow
* **Reference bit**
    * With each page associate a bit, initially = 0
    * When page is referenced bit set to 1
    * Replace any with reference bit = 0 (if one exists)
        * We do not know the order, however
* **Second-chance algorithm**
    * Generally FIFO, plus hardware-provided reference bit
    * Clock replacement
    * If page to be replaced has
        * Reference bit = 0 -> replace it
        * reference bit = 1 then:
            * set reference bit 0, leave page in memory
            * replace next page, subject to same rules

![](http://i.imgur.com/jInNXqt.png)

##### (5) Counting Algorithms
* Keep a counter of the number of references that have been made to each page
    * Not common
* **LFU Algorithm**: replaces page with smallest count
* **MFU Algorithm**: based on the argument that the page with the smallest count was probably just brought in and has yet to be used

##### (6) Page-Buffering Algorithms
* Keep a pool of free frames, always
    * Then frame available when needed, not found at fault time
    * Read page into free frame and select victim to evict and add to free pool
    * When convenient, evict victim
* Possibly, keep list of modified pages
    * When backing store otherwise idle, write pages there and set to non-dirty
* Possibly, keep free frame contents intact and note what is in them
    * If referenced again before reused, no need to load contents again from disk
    * Generally useful to reduce penalty if wrong victim frame selected