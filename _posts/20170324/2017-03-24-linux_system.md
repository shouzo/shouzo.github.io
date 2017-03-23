---
layout: post
title:  "20170324 [學習筆記] Linux 系統程式 (4)"
date:   2017-03-24 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課程簡報
    * [Chapter 4: Mutithread Programming](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170323/ch04.pdf)
    * [Linux_programming_pthread](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170323/Linux_programming_pthread.pdf)
* 參考資料
    * [Toward Concurrency](https://hackmd.io/s/Skh_AaVix)
    * [Amdahl's Law](https://chi_gitbook.gitbooks.io/personal-note/content/amdahls_law.html)

### Multithread Architecture
![](https://i.imgur.com/8ICdvBz.jpg)
* **Responsiveness** – may allow continued execution if part of process is blocked, especially important for user interfaces.
* **Resource Sharing** – threads share resources of process, easier than shared memory or message passing.
* **Economy** – cheaper than process creation, thread switching lower overhead than context switching.
* **Scalability** – process can take advantage of multiprocessor architectures.


### 一、Multicore Programming
* **Multicore** or **multiprocessor** systems putting pressure on programmers, challenges include:
    * **Dividing activities**
    * **Balance**
    * **Data splitting**
    * **Data dependency**
    * **Testing and debugging**
    
* **Parallelism** implies a system can perform more than one task simultaneously.
* **Concurrency** supports more than one task making progress.
    * Single processor / core, scheduler providing concurrency
* Types of parallelism
    * **Data parallelism** – distributes subsets of the same data across multiple cores, same operation on each.
    * **Task parallelism** – distributing threads across cores, each thread performing unique operation.
* As # of threads grows, so does architectural support for threading.
    * CPUs have cores as well as **hardware threads**.
    * Consider Oracle SPARC T4 with 8 cores, and 8 hardware threads per core.


### 二、Concurrency vs Parallelism
* 引用資料：[Toward Concurrency](https://hackmd.io/s/Skh_AaVix)
* [Concurrency is not Parallelism](https://blog.golang.org/concurrency-is-not-parallelism), by Rob Pike
    * [投影片](https://talks.golang.org/2012/waza.slide#1)
    * [錄影](https://www.youtube.com/watch?v=cN_DpYBzKso)
    * [後續 stackoverflow 討論](http://stackoverflow.com/questions/11700953/concurrency-is-not-parallelism)
* Concurrency 對軟體設計的影響
  * 想要充分使用到 CPU 的資源
  * 程式越來越有機會造成 CPU-bound。雖然主要還是 IO-bound 等，但如果 CPU 時脈無法增加，而其他存取方式速度變快，最後會發生 CPU-bound
  * 軟體效能優化將會越來越重要
  * 程式語言必須好好處理 concurrency

#### 1. Concurrency
* 是指程式架構，將程式拆開成多個可獨立運作的工作。eg：drivers，都可以獨立運作，但不需要平行化。
	* 拆開多個的工作不一定要同時運行
	* 多個工作在單核心 CPU 上運行

#### 2. Parallelism
* 是指程式執行，同時執行多個程式。Concurrency 可能會用到 parallelism，但不一定要用 parallelism 才能實現 concurrency。eg：Vector dot product
	* 程式會同時執行 (例如：分支後，同時執行，再收集結果)
	* 一個工作在多核心 CPU 上運行

#### 3. Concurrency vs Parallelism
* Rob Pike 用地鼠燒書做例子:
![](https://hackpad-attachments.s3.amazonaws.com/embedded2016.hackpad.com_K6DJ0ZtiecH_p.537916_1460613009716_task.jpg)

* 如果今天增加多一只地鼠，一個推車或多一個焚燒盧，這樣有機會作到更好的資源使用率，但我們不能保證兩只或更多地鼠會同時進行 (可能只有有限的火爐)。在單核系統中只能允許一次進行一次的燒書工作，那樣就沒有效率了。
![](https://i.imgur.com/XTGAK98.jpg)
以 Concurrency 的方式去作業，能夠以不同的解構方式去進行，可以是三個地鼠分別負責一部分的工作 (decomposition)
![](https://i.imgur.com/w5Eugs7.jpg)
其中也可以 Parallelism:
![](https://i.imgur.com/3IHX5BT.jpg)
或
![](https://i.imgur.com/duEVvNK.jpg)

**Concurrency:** 是指程式架構，將程式拆開成多個可獨立運作的工作，像是驅動程式都可獨立運作，但不需要平行化
* 拆開多個的工作不一定要同時運行
* 多個工作在單核心 CPU 上運行

**Parallelism:** 是指程式執行，同時執行多個程式。Concurrency 可能會用到 parallelism，但不一定要用 parallelism 才能實現 concurrency。eg：Vector dot product
* 程式會同時執行 (例如：fork 後，同時執行，再收集結果 [join])
* 一個工作在多核心 CPU 上運行

#### 4. 相關整理
線上教材 [Introduction to OpenMP](https://www.youtube.com/watch?v=6jFkNjhJ-Z4) 做了以下整理：

(1) Concurrent (並行)
* 工作可拆分成「獨立執行」的部份，這樣「可以」讓很多事情一起做，但是「不一定」要真的同時做。下方情境：
![](https://i.imgur.com/rweOyiD.png)
    * 展示具有並行性，但不去同時執行。
    * 並行性是種「架構程式」的概念。寫下一段程式之前，思考問題架構時就決定好的。 

(2) Parallel (平行)
* 把規劃好、能夠並行的程式，分配給不同執行緒，並讓他們同時執行。
![](https://i.imgur.com/Oom3wM5.png)
   * 「平行」是一種選擇。


### 三、Single and Multithreaded Processes
![](https://i.imgur.com/KLVunhJ.jpg)

* **Amdahl's Law**
    * 針對系統裡面某一個特定的元件予以最佳化，對於整體系統有多少的效能改變。
    ![](https://i.imgur.com/E1L6ODN.jpg)
    * 分成兩部份
        * **有辦法改進的部份**
        * **沒有辦法改進的部份**
    * 因為有無法改進的部份，所以不可能無限提升系統的某一個特定部分的效率。


### 四、User Threads and Kernel Threads
* **User threads**
    * Management done by user-level threads library.
* **Kernel threads**
    * Supported by the Kernel.


### 五、Multithreading Models
1. **Many-to-One**
    * Many user-level threads mapped to single kernel thread.
    * **One thread blocking causes all to block.**
    * Multiple threads may not run in parallel on muticore system because only one may be in kernel at a time.
    * Examples：`Solaris Green Threads`、`GNU Portable Threads`
![](https://i.imgur.com/dwfuxrT.jpg)
    
2. **One-to-One**
    * Each user-level thread maps to kernel thread.
    * **Creating a user-level thread creates a kernel thread.**
    * More concurrency than many-to-one.
    * Number of threads per process sometimes restricted due to overhead.
    * Examples：`Windows NT/XP/2000`、`Linux`、`Solaris 9 and later`
![](https://i.imgur.com/Fh9QH0S.jpg)

3. **Many-to-Many**
* Allows many user level threads to be mapped to many kernel threads.
* Allows the operating system to create a sufficient number of kernel threads.
* Solaris prior to version 9.
* Example：`Windows NT/2000 with the ThreadFiber package`
![](https://i.imgur.com/hWCuKOk.jpg)

4. **Two-level**
* Similar to M:M, except that it allows a user thread to be bound to kernel thread.
* Examples：`IRIX`、`HP-UX`、`Tru64 UNIX`、`Solaris 8 and earlier`
![](https://i.imgur.com/iRt3Xj2.jpg)


### 六、Pthreads
* May be provided either as user-level or kernel-level.
* A POSIX standard (IEEE 1003.1c) API for thread creation and synchronization.
* **Specification, not implementation.**
* API specifies behavior of the thread library, implementation is up to development of the library.
* Common in UNIX operating systems (Solaris, Linux, Mac OS X).

#### pthread.h
```c
#include <pthread.h>

int pthread_create(pthread_t *thread, pthread_attr_t
*attr, void *(*start_routine)(void *), void *arg);
//create a thread


void pthread_exit(void *retval);
//terminate a thread


int pthread_join(pthread_t th, void **thread_return);
//wait for thread termination

```

##### pthread_create()
```c
int pthread_create(pthread_t *thread,
pthread_attr_t *attr, void *(*start_routine)(void
*), void *arg);
```
* `pthread_t *thread`：thread 的識別字
* `pthread_attr_t *attr`：thread 的屬性，設定為 NULL 表示使用預設值
* `void *(*start_routine)(void*)`：thread 要執行的 function
* `void *arg`：傳遞給 thread 的參數

##### pthread_exit()
```c
void pthread_exit(void *retval);
```
* `void *retval`：thread 結束時回傳的變數

##### pthread_join()
```c
int pthread_join(pthread_t th, void **thread_return);
```
* `pthread_t th`：thread 識別字
* `void **thread_return`：接收 pthread_exit 傳回的變數


### 課程作業
* 從 1 - 10000 之間取出所有的質數，利用 threads 來分配計算質數的範圍。

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <pthread.h>
#include <semaphore.h> 
#include <time.h>

#define NUM_THREADS 10
#define MSIZE 10000

// 找出 1 - 10000 的所有質數

static double getDoubleTime();
void *thread_function(void *arg);
pthread_mutex_t work_mutex;

// 宣告 prime_array 陣列
int prime_array[NUM_THREADS][(MSIZE / NUM_THREADS)];


int main(void) {
	int res;
	pthread_t a_thread[NUM_THREADS];
	void *thread_result;
	int lots_of_threads;
    int print_prime = 0;


	// start to measure time...
	double start_time = getDoubleTime();
	
	// initialize mutex...
	res = pthread_mutex_init(&work_mutex, NULL);
	if (res != 0) {
       	perror("Mutex initialization failed");
	    exit(EXIT_FAILURE);
    }

	// pthread_create...
	for (lots_of_threads = 0; lots_of_threads < NUM_THREADS; lots_of_threads ++) {
		res = pthread_create(&(a_thread[lots_of_threads]), NULL, thread_function, (void*)(long)lots_of_threads);

        if (res != 0) {
			perror("Thread creation failed");
        		exit(EXIT_FAILURE);
		}
	}

	// pthread_join...
	for (lots_of_threads = NUM_THREADS - 1; lots_of_threads >= 0; lots_of_threads--) {
        	res = pthread_join(a_thread[lots_of_threads], &thread_result); 

            if (res != 0) {
	            perror("pthread_join failed");
        	}
    }	
    
    int i = 0;  // 設定計數器
    for (lots_of_threads = 0; lots_of_threads < NUM_THREADS; lots_of_threads ++) { 
        printf("\n\nThe thread[%d]'s numbers：\n", lots_of_threads);

        for (i = 0; i < (MSIZE / NUM_THREADS); i++) {
            if (prime_array[lots_of_threads][i] != 0)
                printf("%d\t", prime_array[lots_of_threads][i]);                        }
    }


	printf("\nThread joined\n");

	// stop measuring time...
	double finish_time = getDoubleTime();
	printf("Execute Time:  %.3lf ms\n", (finish_time - start_time));
    exit(EXIT_SUCCESS);
}


void *thread_function(void *arg) {
    // pthread_mutex_lock(&work_mutex);
	
    int my_num = (long)arg;
	// if (MSIZE % NUM_THREADS != 0){    printf("error");   pthread_exit(-1);    }

    int start_num = (MSIZE / NUM_THREADS) * my_num + 1;
	int end_num = (MSIZE / NUM_THREADS) * (my_num + 1);

    int i = 0, j = 0, k = 0;   // Set the loop
    int count = 0;      // Set the counter
    int result = 0;     // result

    printf("I'm thread[%d], start_num:%d, end_num:%d\n", my_num, start_num, end_num);

    /* find the prime number */
    for (i = start_num; i <= end_num; i++) {
        count = 0;      // Reset counter

        for (j = 1; j <= i; j++) {
            if (i % j == 0)
                count += 1;
        }
        
        if (count == 2) { 
            prime_array[my_num][k] = i;
            k++;
        }
    }


    // pthread_mutex_unlock(&work_mutex);
	pthread_exit(0);
}


static double getDoubleTime() {
        struct timeval tm_tv;
        gettimeofday(&tm_tv,0);
        return (double)(((double)tm_tv.tv_sec * (double)1000. + (double)(tm_tv.tv_usec)) * (double)0.001);
}

```
* 執行結果
![](https://i.imgur.com/jw2pSxj.jpg)




## 二、Linux 程式設計
