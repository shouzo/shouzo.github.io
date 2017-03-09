---
layout: post
title:  "20170310 [學習筆記] Linux 系統程式 (2)"
date:   2017-03-10 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


### 一、作業系統
* [課程簡報 - Ch3：Process Concept](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170309/ch03.pdf)

### (一) Process in Memory
![](https://i.imgur.com/I9HnU1O.jpg)


### (二) Process State
![](https://i.imgur.com/mFSgwiJ.jpg)
    * **new**: The process is being created
    * **running**: Instructions are being executed.
    * **waiting**: The process is waiting for some event to occur.
    * **ready**: The process is waiting to be assigned to a processor.
    * **terminated**: The process has finished execution.


### (三) Process Control Block (PCB)
![](https://i.imgur.com/fOf7e2Q.jpg)


### (四) Process Switch
![](https://i.imgur.com/Bto5DiZ.jpg)


### (五) Process、Thread
* 參考資料
    * [Program / Process / Thread 的差別](https://goo.gl/whe9F)
    * ["Process 行程"、"Thread 執行緒"、"Multithreading 多執行緒" 簡介](https://goo.gl/5BnKWN)
![](https://i.imgur.com/DI9y5OS.jpg)


### (六) Process Scheduling
* Process scheduler selects among available processes for next execution on CPU
* Maintains scheduling queues of processes
    * Job queue – set of all processes in the system.
    * Ready queue – set of all processes residing in main memory, ready and waiting to
execute.
    * Device queues – set of processes waiting for an I/O device.
    * Processes migrate among the various queues.

### Schedulers
* **++Long-term scheduler++** (or job scheduler) – selects which processes should be brought into the
ready queue.
* **++Short-term scheduler++** (or CPU scheduler) – selects which process should be executed next and
allocates CPU.
    * Sometimes the only scheduler in a system.
* The long-term scheduler controls the **++degree of multiprogramming++**.
* **++Medium-term scheduler++** can be added if degree of multiple programming needs to decrease.
    * Remove process from memory, store on disk, bring back in from disk to continue execution:
swapping.

**[Degree of multiprogramming](http://enews.open2u.com.tw/~noupd/book_up/1746/8719.htm)**：多工程度、記憶體中行程的總數量

### Processes 
* **++I/O-bound process++** – spends more time doing I/O than computations, many short CPU
bursts.
    * 行程大部份的時間在做 I/O，只有少部份的時間在做計算。 
* **++CPU-bound process++** – spends more time doing computations; few very long CPU bursts.
    * 行程大部份的時間在做計算，只有少部份的時間在做  I/O。
    
![](https://i.imgur.com/Xnhn0Sa.jpg)


### (七) Process Creation
![](https://i.imgur.com/MDkGD81.jpg)
* UNIX examples
    * **fork()**：system call creates new process
    * **exec()**：system call used after a fork() to replace the process’ memory space with a new program

![](https://i.imgur.com/AxrT24a.jpg)


* 課程作業

```c
#include <sys/types.h>
#include <stdio.h>
#include <unistd.h>

int main(void) {
	pid_t pid;
	
/* fork a child process */
	pid = fork();

	
	if (pid < 0) {		// erro occurred
		fprintf(stderr, "Fork Failed");
		return 1;
	}
	else if (pid == 0) {	// child process
		execlp("/bin/ls", "ls", NULL);
	}
	else {			// parent process
		// parent will wait for the child to complete
		wait(NULL);
		printf("Child Complete\n");
	}
	
	return 0;
}
```

* 執行結果

![](https://i.imgur.com/CfTMr9N.jpg)