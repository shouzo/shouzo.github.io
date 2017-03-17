---
layout: post
title:  "20170317 [學習筆記] Linux 系統程式 (3)"
date:   2017-03-17 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課程簡報
    * [Chapter 5: Process Scheduling](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170316/ch05.pdf)

* 參考資料
    * [Preemptive Process Scheduling 的觀念](http://www.jollen.org/blog/2006/11/_scheduler_running_process.html)
    * [各種 CPU 排程演算法之探討](http://moon.cse.yzu.edu.tw/~s981502/7/Chap4.pdf)
    * [行程排程演算法](http://publish.get.com.tw/BookPre_pdf/51MG060707-2.PDF)
    * [Operating System Scheduling algorithms](https://www.tutorialspoint.com/operating_system/os_process_scheduling_algorithms.htm)
  

### Basic Concepts
* Maximum CPU utilization obtained with multiprogramming.
* CPU–I/O Burst Cycle – Process execution consists of a cycle of CPU execution and I/O wait.
* CPU burst followed by I/O burst.
* CPU burst distribution is of main concern.


### CPU Scheduler
* Short-term scheduler selects from among the processes in ready queue, and allocates the CPU to one of them.
    * Queue may be ordered in various ways.
* CPU scheduling decisions may take place when a process:
    1. Switches from running to waiting state
    2. Switches from running to ready state
    3. Switches from waiting to ready
    4. Terminates
* Scheduling under 1 and 4 is nonpreemptive.
* All other scheduling is preemptive.
    * Consider access to shared data.
    * Consider preemption while in kernel mode.
    * Consider interrupts occurring during crucial OS activities.


### Dispatcher
* Dispatcher module gives control of the CPU to the process selected by the short-term scheduler; this involves：
    1. switching context
    2. switching to user mode
    3. jumping to the proper location in the user program to restart that program
* Dispatch latency：time it takes for the dispatcher to stop one process and start another running.


### Scheduling Algorithm Optimization Criteria
![](https://i.imgur.com/LuBGjEu.jpg)
1. CPU utilization – keep the CPU as busy as possible.
2. Throughput – # of processes that complete their execution per time unit.
3. Turnaround time – amount of time to execute a particular process.
4. Waiting time – amount of time a process has been waiting in the ready queue.
5. Response time – amount of time it takes from when a request was submitted until the first response is produced, not output (for time-sharing environment).

* Length of Next CPU Burst
    * Determining
        ![](https://i.imgur.com/1GaCgCb.jpg)
    * Prediction
        ![](https://i.imgur.com/HWSLIdj.jpg)


### Scheduling Algorithm
#### (一) First-Come, First-Served (FCFS)：先到先服務法
![](https://i.imgur.com/xWKL0Z3.jpg)

---

![](https://i.imgur.com/6MIKb3Q.jpg)

* Convoy effect：很多短時間的 process，都在等一個
長時間的 process 時，所產生的效應 (因為等待時間很長)。


#### (二) Shortest-Job-First (SJF)：最短優先法
* Associate with each process the length of its next CPU burst.
    * Use these lengths to schedule the process with the shortest time.
* SJF is optimal – gives minimum average waiting time for a given set of processes.
    * The difficulty is knowing the length of the next CPU request.
    * Could ask the user.

![](https://i.imgur.com/Wf8bEgG.jpg)


#### (三) Priority Scheduling (PS)：優先權排班法
* A priority number (integer) is associated with each process.
* The CPU is allocated to the process with the highest priority (smallest integer >> highest priority).
    * Preemptive
    * Nonpreemptive
* SJF is priority scheduling where priority is the inverse of predicted next CPU burst time.
* Problem：Starvation – low priority processes may never execute.
* Solution：Aging – as time progresses increase the priority of the process.

![](https://i.imgur.com/ZeyVmU1.jpg)


#### (四) Round Robin (RR)：輪流法
* Each process gets a small unit of CPU time (time quantum q), usually 10-100 milliseconds. After this time has elapsed, the process is preempted and added to the end of the ready queue.
* If there are n processes in the ready queue and the time quantum is q, then each process gets 1/n of the CPU time in chunks of at most q time units at once. No process waits more than (n-1)q time units.
* Timer interrupts every quantum to schedule next process.
* Performance.
    * q large >> FIFO.
    * q small >> q must be large with respect to context switch, otherwise overhead is too high.

![](https://i.imgur.com/uu9W1cI.jpg)

##### 1. Time Quantum and Context Switch Time
![](http://i.imgur.com/7SMF7rd.jpg)

##### 2. Turnaround Time Varies With The Time Quantum
![](http://i.imgur.com/EtNCekP.jpg)


#### (五) Multilevel
##### 1. Queue
* Ready queue is partitioned into separate queues, eg:
	* foreground (interactive)
	* background (batch)
* Process permanently in a given queue.
Each queue has its own scheduling algorithm:
	* foreground – RR
	* background – FCFS
* Scheduling must be done between the queues:
	* Fixed priority scheduling; (i.e., serve all from foreground then from background). Possibility of starvation.
	* Time slice – each queue gets a certain amount of CPU time which it can schedule amongst its processes; i.e., 80% to foreground in RR.
	* 20% to background in FCFS.

![](https://i.imgur.com/rW9GXTr.jpg)

##### 2. Feedback Queue
* A process can move between the various queues; aging can be implemented this way.
* Multilevel-feedback-queue scheduler. defined by the following parameters:
	* number of queues.
	* scheduling algorithms for each queue.
	* method used to determine when to upgrade a process.
	* method used to determine when to demote a process.
	* method used to determine which queue a process will enter when that process needs service.

![](https://i.imgur.com/Nc2SsIQ.jpg)


* 課程作業




## 二、Linux 程式設計
* 課程簡報
    * [Linux_programming_pthread-1](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170317/Linux_programming_pthread-1.pdf)
    * [Linux_Programming_pthread-2](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170317/Linux_programming_pthread-2.pdf)

* 參考資料
    * [POSIX Threads Programming](https://computing.llnl.gov/tutorials/pthreads/)


### 1. What is a Thread？
#### (1) OS view
* A thread is an independent stream of instructions that can be scheduled to run by
the OS.

#### (2) Software developer view
* a thread can be considered as a “procedure” that runs independently from the main program.
    * Sequential program: a single stream of instructions in
a program.
    * Multi-threaded program: a program with multiple streams.
        * Multiple threads are needed to use multiple cores/CPUs.

#### [Example]
1. Computer games
    * each thread controls the movement of an object.
2. Scientific simulations
    * Hurricane movement simulation: each thread simulates the hurricane in a small domain.
    * Molecular dynamic: each thread simulates a subset of particulars.
3. Web server
    * Each thread handles a connection.


### 2. Process and Thread
#### (1) Process context
* Two parts in the context: `self-contained domain (protection)` and `execution of instructions`.
    * Process ID, process group ID, user ID, and group ID
    * Environment
    * Working directory.
    * Program instructions
    * Registers (including PC)
    * Stack
    * Heap
    * File descriptors
    * Signal actions
    * Shared libraries
    * Inter-process communication tools
* What are absolutely needed to support a stream of instructions, given the process context?
    * Registers (including PC)
    * Stack

![](https://i.imgur.com/6kN1sIR.jpg)

#### (2) Threads
* **Advantages**
    * Light-weight
        * Lower overhead for thread creation.
        * Lower Context Switching Overhead.
            * Fewer OS resources
    * Shared State
        * Don’t need IPC-like mechanism to communicate between threads of same process.
* **Disadvantages**
    * Shared State!
        * Global variables are shared between threads. Accidental changes can be fatal.
    * Many library functions are not thread-safe
        * Library Functions that return pointers to static internal memory. E.g. gethostbyname()
    * Lack of robustness
        * Crash in one thread will crash the entire process.


### 3. Pthreads
* Hardware vendors used to implement
proprietary versions of threads
    * Thread programs are not portable
* Pthreads = POSIX threads, specified in IEEE POSIX 1003.1c (1995)

#### (1) The Pthreads API
* Three types of routines:
    1. `Thread management`: create, terminate, join, and detach
    2. `Mutexes`: mutual exclusion, creating, destroying, locking, and unlocking mutexes
    3. `Condition variables`: event driven synchronizaiton.
    * Mutexes and condition variables are concerned about synchronization.
    * Why not anything related to inter-thread communication?
* The concept of opaque objects pervades the
design of the API.
* API naming convention
![](https://i.imgur.com/euq9YUQ.jpg)

#### (2) Thread management
* Pthread header file <pthread.h>
* Compiling pthread programs：
    > gcc aaa.c -o aaa -lpthread

1. `Creation`
    * pthread_create
    ![](https://i.imgur.com/KZ0yaCh.jpg)
2. `Termination`
    * Return
    * Pthread_exit
    * Can we still use exit？
    ![](https://i.imgur.com/O8QpEBi.jpg)
3. `Wait (parent/child synchronization)`
    * pthread_join
    ![](https://i.imgur.com/bacALSV.jpg)


* 課程作業