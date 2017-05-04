---
layout: post
title:  "20170428 [學習筆記] Linux 系統程式 (8)"
date:   2017-04-28 00:00:00
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




## 二、Linux 程式設計
* 課程簡報
    * [Working with Files]()
    * [Low-Level I/O](http://advancedlinuxprogramming.com/alp-folder/alp-apB-low-level-io.pdf)


### (一) Low-level file access
Header：`#include <unistd.h>`

* Action
    * open
    * read
    * write
    * close
    * ioctl

* Status
    * 0：standard input
    * 1：standard output
    * 2：standard error

* Permission
    * S_IRUSR
    * S_IWUSR
    * S_IXUSR
    * S_IRGRP
    * S_IWGRP
    * S_IWGRP
    * S_IROTH
    * S_IWOTH
    * S_IXOTH

#### 1. open
* `int open(const char* path, int oflags);`
* `int open(const char* path, int oflags, mode_t mode);`
* Mode
    * O_RDONLY
    * O_WRONLY
    * O_RDWR
* Oflags
    * O_APPEND
    * O_TRUNC ( 放棄現有的內容、長度歸零)
    * O_CREAT
    * O_EXCL (確保呼叫者可以產生檔案)

#### 2. read
* `size_t read(int fildes, void* buf, size_t nbytes);`

```c
#include <unistd.h>

int main()
{
    char buffer[128];
    int nread;
    
    nread = read(0, buffer, 128);
    if (nread == -1)
        write(2, "A read error has occurred\n", 26);

    if ((write(1, buffer, nread)) != nread)
        write(2, "A write error has occurred\n", 27);
        
    exit(0);
}
```

#### 3. write
* `size_t write(int fildes, void* buf, size_t n
bytes);`

```c
#include <unistd.h>

int main()
{
    if ((write(1, "Here is some data\n", 18)) != 18
)
    write(2, "A write error has occurred on file descriptor 1\n",46);

    exit(0);
}
```

#### [Example] File copy
* Example 1

```c
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main()
{
    char c;
    int in, out;
    
    in = open("file.in", O_RDONLY);
    out = open("file.out", O_WRONLY|O_CREAT, S_IRUSR|S_IWUSR);

    while(read(in,&c,1) == 1)
        write(out,&c,1);
        
    exit(0);
}
```

* Example2

```c
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main()
{
    char block[1024];
    int in, out;
    int nread;
    
    in = open("file.in", O_RDONLY);
    out = open("file.out", O_WRONLY|O_CREAT, S_IRUSR|S_IWUSR);
    while((nread = read(in,block,sizeof(block))) > 0)
        write(out,block,nread);

    exit(0);
}
```


### (二) Standard I/O Library
Header：`#include <stdio.h>`

* `fopen`, `fclose`
    * int fclose(FILE* stream);

* `fread`, `fwrite`
    * size_t fread(void* ptr, size_t size, size_t nitems, FILE* stream);
    * size_t fwrite(void* ptr, size_t size, size_t nitems, FILE* stream);

* `fflush`
    * int fflush(FLIE* stream);

* `fseek`
    * int fseek(FILE* stream, long int offset, int whence);

* `fgetc`, `getc`, `getchar`
    * int fgetc(FILE* stream);
    * int getc(FILE* stream);
    * int getchar();

* `fputc`, `putc`, `putchar`
    * int fputc(int c, FILE* stream);
    * int putc(int c, FILE* stream);
    * int putchar(int c);

* `fgets`, `gets`
    * char* fgets(char*s, int n, FILE* stream);
    * char* gets(char*s);

* `printf`, `fprintf`, `sprintf`

* `scanf`, `fscanf`, `sscanf`

#### fopen
* `FILE *fopen(const char *filename, const
char* mode);`
    * `r` (唯讀模式)
    * `w` (寫入模式、長度歸零)
    * `a` (寫入模式、附加)

#### 2. printf, sprintf, fprintf
%d, %o, %x, %c ,%s ,%f (float) ,%e (double) ,%g (double)

#### 3. scanf, fscanf, sscanf
%d, %o, %x, %f, %e, %g, %c, %s, %[] (掃瞄特定字元), %% (掃瞄 % 的字元)