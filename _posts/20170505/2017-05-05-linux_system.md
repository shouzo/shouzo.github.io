---
layout: post
title:  "20170505 [學習筆記] Linux 系統程式 (9)"
date:   2017-05-05 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課程簡報
    * [Chapter 6: Process Scheduling](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170427/ch06.pdf)

### Race Condition
* Consumer
![](https://i.imgur.com/07kx1WD.jpg)

* Producer
![](https://i.imgur.com/eBPWPyb.jpg)

* Race Condition
![](https://i.imgur.com/Q5qf9Xi.jpg)


### (一) Critical-Section Problem
* General Structure：
![](https://i.imgur.com/yVVaplo.jpg)
![](https://i.imgur.com/dWa1ujd.jpg)


### (二) Solution to Critical-Section Problem
1. **Mutual Exclusion** - If process Pi is executing in its critical section, then no other processes can be executing in their critical sections.

2. **Progress** - If no process is executing in its critical section and there exist some processes that wish to enter their critical section, then the selection of the processes that will enter the critical section next cannot be postponed indefinitely.

3. **Bounded Waiting** - A bound must exist on the number of times that other processes are allowed to enter their critical sections.
    * Assume that each process executes at a nonzero speed.
    * No assumption concerning relative speed of the n processes.

4. Two approaches depending on if kernel is preemptive or non-preemptive.
    * **Preemptive** – allows preemption of process when running in kernel mode.
    * **Non-preemptive** – runs until exits kernel mode, blocks, or voluntarily yields CPU.
        * Essentially free of race conditions in kernel mode.


### (三) Peterson's Solution
* Good algorithmic description of solving the problem.
* Two process solution
* Assume that the load and store instructions are atomic; that is, cannot be interrupted
* The two processes share two variables：
    * int turn;
    * Boolean flag[2]
* The variable turn indicates whose turn it is to enter the critical section
* The flag array is used to indicate if a process is ready to enter the critical section. 
* `flag[i] = true` implies that process Pi is ready!

![](https://i.imgur.com/VjV8Owl.jpg)
* Provable that
    1. Mutual exclusion is preserved.
    2. Progress requirement is satisfied.
    3. Bounded-waiting requirement is met.


### (四) Synchronization Hardware
* Many systems provide hardware support for critical section code.
* All solutions below based on idea of **locking**.
    * Protecting critical regions via locks.
* Uniprocessors – could disable interrupts
    * Currently running code would execute without preemption.
    * Generally too inefficient on multiprocessor systems.
        * Operating systems using this not broadly scalable.
* Modern machines provide special atomic hardware instructions.
    * **Atomic** = non-interruptible
        * Either test memory word and set value.
        * Or swap contents of two memory words.

#### 1. test_and_set Instruction
![](https://i.imgur.com/HP1PMeH.jpg)
* Solution using test_and_set()
    ![](https://i.imgur.com/5SMn8ix.jpg)
* Bounded-waiting Mutual Exclusion with test_and_set
    ![](https://i.imgur.com/wGOMFdV.jpg)

#### 2. compare_and_swap Instruction
![](https://i.imgur.com/7neEbLe.jpg)
* Solution using compare_and_swap
    ![](https://i.imgur.com/37TZRjA.jpg)


### (五) Mutex Locks
* Previous solutions are complicated and generally inaccessible to application programmers.
* OS designers build software tools to solve critical section problem.
* Simplest is mutex lock
* Product critical regions with it by first **acquire()** a lock then **release()** it.
    * Boolean variable indicating if lock is available or not
* Calls to **acquire()** and **release()** must be atomic.
    * Usually implemented via hardware atomic instructions
* But this solution requires **busy waiting**.
    * This lock therefore called a **spinlock**

#### acquire() and release()
![](https://i.imgur.com/stNKbbR.jpg)




## 20170504
* 課程簡報
    * [Chapter 6: Process Scheduling](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170504/ch06.pdf)

### (一) Semaphore
* Synchronization tool that does not require busy waiting.
* Semaphore S – integer variable
* Two standard operations modify S: `wait()` and `signal()`
* Less complicated
* Can only be accessed via two indivisible (atomic) operations

```c
wait(S)
    while (S <= 0)
        ; //busy wait
    S--;
}
signal(S) {
    S++;
}
```

#### 1. Usage
* **Counting semaphore** – integer value can range over an unrestricted domain
* **Binary semaphore** – integer value can range only between 0 and 1
    * Then a `mutex lock`
* Can implement a counting semaphore S as a binary semaphore
* Can solve various synchronization problems
* Consider P1 and P2 that require S1 to happen before S2

```c
P1:
    S1;
    signal(synch);
P2:
    wait(synch);
    S2;
```

#### 2. Implementation
##### (1) Busy waiting
* Must guarantee that no two processes can execute `wait()` and `signal()` on the same semaphore at the same time

* Thus, implementation becomes the critical section problem where the wait and signal code are placed in the critical section
* Could now have **busy waiting** in critical section implementation
    * But implementation code is short
    * Little busy waiting if critical section rarely occupied
* Note that applications may spend lots of time in critical sections and therefore this is not a good solution

##### (2) with no Busy waiting
* With each semaphore there is an associated waiting queue
* Each entry in a waiting queue has two data items: 
    * value (of type integer)
    * pointer to next record in the list
* Two operations:
    * `block` – place the process invoking the operation on the appropriate waiting queue
    * `wakeup` – remove one of processes in the waiting queue and place it in the ready queue

```c
typedef struct{
    int value;
    struct process *list;
} semaphore;

wait(semaphore *S) {
    S->value--;
    if (S->value < 0) {
        // add this process to S->list;
        block();
    }
}

signal(semaphore *S) {
    S->value++;
    if (S->value <= 0) {
        // remove a process P from S->list;
        wakeup(P);
    }
}
```


### (二) Deadlock and Starvation
* **Deadlock** – two or more processes are waiting indefinitely for an event that can be caused by only one of the waiting processes
* Let S and Q be two semaphores initialized to 1
![](https://i.imgur.com/9cUghpD.png)

* **Starvation** – indefinite blocking
    * A process may never be removed from the semaphore queue in which it is suspended
* **Priority Inversion**
    * Scheduling problem when lower-priority process holds a lock needed by higher-priority process
    * Solved via **priority-inheritance protocol**


### (三) Classical Problems of Synchronization
#### 1. Bounded-Buffer Problem
* n buffers, each can hold one item
    * Semaphore `mutex` initialized to the value 1
    * Semaphore `full` initialized to the value 0
    * Semaphore `empty` initialized to the value n

##### (1) The structure of the producer process

```c
do {
    ...
    /* produce an item in next_produced */
    ...
    
    wait(empty);
    wait(mutex);
    
    ...
    /* add next produced to the buffer */
    ...

    signal(mutex);
    signal(full);
} while (true);
```

##### (2) The structure of the consumer process

```c
do {
    wait(full);
    wait(mutex);

    ...
    /* remove an item from buffer to next_consumed */
    ...

    signal(mutex);
    signal(empty);

    ...
    /* consume the item in next consumed */
    ...
} while (true);
```


#### 2. Readers-Writers Problem
* A data set is shared among a number of concurrent processes
    * **Readers** - only read the data set; they do not perform any updates
    * **Writers** - can both read and write
* **Problem** - allow multiple readers to read at the same time
* Several variations of how readers and writers are treated – all involve priorities
* Shared Data
    * Data set
    * Semaphore `rw_mutex` initialized to 1
    * Semaphore `mutex` initialized to 1
    * Integer `read_count` initialized to 0

##### (1) The structure of a writer process

```c
do {
    wait(rw_mutex);
    
    ...
    /* writing is performed */
    ...
    
    signal(rw_mutex);
} while (true);
```

##### (2) The structure of a reader process

```c
do {
    wait(mutex);
    read_count++;
    
    
    if (read_count == 1)    // only for the first reader entering
        wait(rw_mutex);
        
    signal(mutex);    // if the first reader wait for rw_mutex, other readers will be blocked on wait(mutex)
        
    ...
    /* reading is performed */
    ...
        
    wait(mutex);
    read_count--;
        
    if (read_count == 0)    // only for the last reader leaving
        signal(rw_mutex);
    
    signal(mutex);
} while (true)
```

##### (3) Problem
* **First variation** – no reader kept waiting unless writer has permission to use shared object
* **Second variation** – once writer is ready, it performs write asap
* Both may have starvation leading to even more variations
* Problem is solved on some systems by kernel providing `reader-writer locks`


#### 3. Dining-Philosophers Problem
* Philosophers spend their lives thinking and eating
* Don’t interact with their neighbors, occasionally try to pick up 2 chopsticks (one at a time) to eat from bowl
    * Need both to eat, then release both when done
![](https://i.imgur.com/9W4qi3z.png)

##### (1) In the case of 5 philosophers
* Shared data
    * Bowl of rice (data set)
    * Semaphore chopstick [5] initialized to 1
* The structure of Philosopher i:

```c
do {
    wait ( chopstick[i] );
    wait ( chopStick[ (i+1) % 5] );
    
    // eat

    signal ( chopstick[i] );
    signal (chopstick[ (i+1) % 5] );
    
    // think
    
} while (TRUE);
```
* Incorrect use of semaphore operations:
    * signal (mutex) .... wait (mutex)
    * wait (mutex) ... wait (mutex)
    * Omitting of wait (mutex) or signal (mutex) (or both)

* Deadlock and starvation

##### (2) Monitors
![](https://i.imgur.com/Et1mwXN.png)

* A high-level abstraction that provides a convenient and effective mechanism for process synchronization
* Abstract data type, internal variables only accessible by code within the procedure
* Only one process may be active within the monitor at a time
* But not powerful enough to model some synchronization schemes

##### Monitors Implementation
###### 1. Using Semaphores
* Variables

```c
semaphore mutex; // (initially = 1)
semaphore next; // (initially = 0)
int next_count = 0;
```

* Each procedure F will be replaced by

```c
wait(mutex);

...
body of F;
...

if (next_count > 0)
    signal(next)
else
    signal(mutex);
```

* Mutual exclusion within a monitor is ensured

###### 2. Condition Variables

* For each condition variable x, we have:

```c
semaphore x_sem; // (initially = 0)
int x_count = 0;
```

* The operation `x.wait` can be implemented as:

```c
x-count++;
if (next_count > 0)
    signal(next);
else
    signal(mutex);
wait(x_sem);
x-count--;
```

###### Resuming Processes
* If several processes queued on condition x, and x.signal() executed, which should be resumed?
* FCFS frequently not adequate
* **conditional-wait** construct of the form x.wait(c)
    * Where c is **priority number**
    * Process with lowest number (highest priority) is scheduled next

###### Allocate Single Resource 
```c
monitor ResourceAllocator
{
    boolean busy;
    condition x;
    
    void acquire(int time) {
        if (busy)
            x.wait(time);
        busy = TRUE;
    }
    
    void release() {
        busy = FALSE;
        x.signal();
    }
    
    initialization code() {
        busy = FALSE;
    }
}
```

##### (3) Solution
* Each philosopher i invokes the operations `pickup()` and `putdown()` in the following sequence:
    * No deadlock, but starvation is possible.

```c
DiningPhilosophers.pickup(i);
    EAT
DiningPhilosophers.putdown(i);
```

```c
monitor DiningPhilosophers
{
    enum { THINKING; HUNGRY, EATING) state [5] ;
    condition self [5];

    void pickup (int i) {
        state[i] = HUNGRY;
        test(i);
        if (state[i] != EATING) self [i].wait;
    }
    
    void putdown (int i) {
        state[i] = THINKING;
    
        /* test left and right neighbors */
        test((i + 4) % 5);
        test((i + 1) % 5);
    }

    void test (int i) {
        if ((state[(i + 4) % 5] != EATING) &&
(state[i] == HUNGRY) &&
(state[(i + 1) % 5] != EATING)) {
            state[i] = EATING;
            self[i].signal();
    }
}

    initialization_code() {
        for (int i = 0; i < 5; i++)
            state[i] = THINKING;
    }
}
```




## 二、Linux 程式設計
* 課程簡報
    * [Working-with-files](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170505/Working-with-files.pdf)

### (一) mmap
* `void *mmap(void *addr, size_t len, int port, int flags, int fildes, off_t off);`
    * PORT_READ
    * PORT_WRITE
    * PORT_EXEC
    * PORT_NONE
    * MAP_PRIVATE
    * MAP_SHARED
    * MAP_FIXED


### (二) msync
* `int msync(void* addr, size_t len, int flags);`
    * MS_AYSNC (非同步寫入)
    * MS_SYNC (同步寫入)
    * MS_INVALIDATE (再從檔案讀)


### (三) munmap
* `int munmap(void * addr, size_t len);`

* `fprintf.c`
```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#define SIZE 100
int main() {

	FILE *fptr, *fptr2;
	int i, j;
	float a, b;
    srand(time(NULL));

	fptr = fopen("ans.txt", "w");

	if (!fptr){
		printf("open error \n");
		exit(1);
	} else {
        printf("open success, start writing into file\n");
    }

	for (i = 1; i <= SIZE; i++) {
        a = ((float)rand() / (float)(RAND_MAX)) * 100;
		printf("[%3d] = %3.3f\n", i, a);
		fprintf(fptr, "%3.3f\n", a);
	}

	fclose(fptr);
	/*---------------------------------------------------------- */
	fptr2 = fopen("ans.txt", "r");

	if(!fptr2) {
		printf("open error\n");
		exit(1);
	} else {
	    printf("open success, start reading from file\n");
	}

	i = 1;
	while (fscanf(fptr2, "%6f" ,&b) == 1)
		printf("[%3d] = %3.3f\n", i++, b);
	
	fclose(fptr2);
	return 0;
}
```
* 執行結果

![](https://i.imgur.com/WkwKXZJ.png)

![](https://i.imgur.com/vFysQAi.png)


### 課堂作業
* 給予一個資料檔，計算其平均值與標準差。

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#define SIZE 100
int main() {

	FILE *fptr1, *fptr2;
	int i;
	float a = .0, total = .0, avg = .0;
    float sigma = .0, sd = .0;


    /* Use fptr1 to calculate total and average */
    fptr1 = fopen("ans.txt", "r");
	if (!fptr1) {
		printf("open error\n");
		exit(1);
	} 
    
    else {
	    printf("\nopen success, fptr1 starts reading from file\n");
	}


    i = 1;
    while (fscanf(fptr1, "%6f" ,&a) == 1) {
		printf("[%3d] = %3.3f\n", i++, a);
        total += a;
    }
	avg = (total / i);
    fclose(fptr1);




    /* Use fptr2 to calculate standard deviation */
    fptr2 = fopen("ans.txt", "r");
    if (!fptr2) {
		printf("open error\n");
		exit(1);
	} 
    
    else {
	    printf("\nopen success, fptr2 starts reading from file\n");
	}


    i = 1;
    while (fscanf(fptr2, "%6f" ,&a) == 1) {
        sigma += (a - avg) * (a - avg);
        i++;
    }
    sd = sqrt(sigma / i); 
    fclose(fptr2);




    /* Output */
    printf("\n\nThe average = %f\n", avg);
    printf("The standard deviation = %f\n", sd);


	return 0;
}
```

* 執行結果
![](https://i.imgur.com/W17ixQD.png)
