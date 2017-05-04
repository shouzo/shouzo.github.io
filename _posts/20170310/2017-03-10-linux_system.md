---
layout: post
title:  "20170310 [學習筆記] Linux 系統程式 (2)"
date:   2017-03-10 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* [課程簡報 - Ch3：Process Concept](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170309/ch03.pdf)

### (一) Process in Memory
![](https://i.imgur.com/I9HnU1O.jpg)


### (二) Process State
![](https://i.imgur.com/mFSgwiJ.jpg)
   * **new**: The process is being created.
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
* Process scheduler selects among available processes for next execution on CPU.
* Maintains scheduling queues of processes.
    * Job queue – set of all processes in the system.
    * Ready queue – set of all processes residing in main memory, ready and waiting to
execute.
    * Device queues – set of processes waiting for an I/O device.
    * Processes migrate among the various queues.

#### Schedulers
* __Long-term scheduler__ (or job scheduler) – selects which processes should be brought into the
ready queue.
* __Short-term scheduler__ (or CPU scheduler) – selects which process should be executed next and
allocates CPU.
    * Sometimes the only scheduler in a system.
* The long-term scheduler controls the __degree of multiprogramming__.
* __Medium-term scheduler__ can be added if degree of multiple programming needs to decrease.
    * Remove process from memory, store on disk, bring back in from disk to continue execution:
swapping.

**[Degree of multiprogramming](http://enews.open2u.com.tw/~noupd/book_up/1746/8719.htm)**：多工程度、記憶體中行程的總數量

#### Processes 
* __I/O-bound process__ – spends more time doing I/O than computations, many short CPU
bursts.
    * 行程大部份的時間在做 I/O，只有少部份的時間在做計算。 
* __CPU-bound process__ – spends more time doing computations; few very long CPU bursts.
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




## 二、Linux 程式設計

* [課程簡報 - Creating and Executing Processes](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170310/Creating_and_Executing_Processes.pdf)
* [課程簡報 - Signal](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170310/Signals.pdf)


### (一) Unix system call

#### 1. execv()

* 課堂練習： `execv()` 的概念

```c
#include <stdio.h>
#include <stdlib.h>

char * argv[] = {"/bin/ls", "-1", 0};
int main(void) {

	int pid, status;

	if ((pid = fork()) < 0) {
		printf("Fork error \n");
		exit(1);
	}
	
	if (pid == 0) {	/* Child executes here */
		execv (argv[0], argv);
		printf("Exec error \n");
		exit(1);
	} else		/* Parent executes here */
		wait(&status);

	printf("Hello there! \n");
	return 0;
}
```


### (二) Signals

#### 1. UNIX Process Control
![](https://i.imgur.com/kFzherv.jpg)


#### 2. kill Command
> kill -signal pid

* Example
    * _kill –2 1234_
    * _kill -SIGINT 1234_
        * Same as pressing Ctrl-c if process 1234 is running in foreground.

    
#### 3. Function Call
* raise()

```c
int ret = raise(SIGINT); /* Process commits suicide. */
assert(ret != 0);	/* Shouldn't get here. */
```

* kill()

> int kill(pid_t iPid, int iSig);

```c
pid_t iPid = getpid(); /* Process gets its id.*/
kill(iPid, SIGINT);	/* Process sends itself a SIGINT signal (commits suicide?) */
```

#### 4. Linux Signal
* [Linux Signal 種類](http://www.comptechdoc.org/os/linux/programming/linux_pgsignals.html)
    * Ctrl-C (in older Unixes, DEL)
        * sends an INT signal (SIGINT)
        * by default, this causes the process to terminate.
    * Ctrl-Z
        * sends a TSTP signal (SIGTSTP)
        * by default, this causes the process to suspend execution.
    * Ctrl-\
        * sends a QUIT signal (SIGQUIT)
        * by default, this causes the process to terminate and dump core.

#### 5. 訊號處理 - signal.h
* 意義說明
    * SIGABORT    程序停止
    * SIGALRM     警示
    * SIGFPE      浮點數例外
    * SIGHUP      掛斷
    * SIGILL      非法指令
    * SIGINT      終端機插斷

##### (1) signal()
```c
#include <signal.h>
void(*signal(int sig, void (*func)(int)))(int);
```

* 攔截 Ctrl-C 訊號

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>

void ouch(int sig) {
	printf("OUCH! - I got signal %d\n", sig);
	(void) signal(SIGINT, SIG_DFL);
}

int main(void) {
	(void) signal(SIGINT, ouch);
	
	while(1) {
		printf("Hello World!\n");
		sleep(1);
	}
}
```


##### (2) sigaction()
```c
#include <signal.h>
int sigaction(int sig,const struct sigaction *act, struct sigaction *oact);
```
* 參數說明
    * sig: 要處理的訊號,若 act 指針非空, 則根據 act 修改該信號的處理動作；若 oact 指針非空, 則通過 oact 傳出該信號原來的處理動作。
    * void(*)(int) sa_handler
    * sigset_t sa_mask
    * int sa_flags
    * sa_handler 代表新的信號處理。
    * sa_mask 用來設置在處理該信號時暫時將 sa_mask 指定的信號集擱置。
    * sa_flags 用來設置信號處理的其他相關操作。


* 程式作業：`signal` 的處理
![](https://i.imgur.com/1Zbog12.jpg)

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>


// When the process get the "Ctrl+C" signal.
void show_time() {
    system("date"); // show the time.
}

// When the process gets the "Ctrl+\" signal.
void go_default() {
    signal(SIGINT, SIG_DFL);    // recover "Ctrl+C"
}


int main(void) {

    signal(SIGINT, show_time);  // triggering "Ctrl+C"
    signal(SIGQUIT, go_default);// triggering "Ctrl+\"

    while(1) {
        printf("What time is it？\n");  // Something interesting will happen if this line without "\n".
        sleep(1);
    }   
}
```

* 執行結果

![](https://i.imgur.com/TgkFPdi.jpg)
