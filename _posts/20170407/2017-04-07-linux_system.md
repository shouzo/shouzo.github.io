---
layout: post
title:  "20170407 [學習筆記] Linux 系統程式 (6)"
date:   2017-04-07 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課程簡報
    * [Chapter 4: Mutithread Programming](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170406/ch04.pdf)
    * [Chapter 5: Process Scheduling](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170406/ch05.pdf)
    * [Socket_Programming](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170406/Socket_Programming.pdf)
    * [Linux-Programming_Socket_1](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170406/Linux-Programming_Socket_1.pdf)
    * [Linux-Programming_Socket_2](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170406/Linux-Programming_Socket_2.pdf)

* 參考資料
    * [簡易的程式平行化方法－OpenMP（一）簡介](https://goo.gl/Rs9JgE)
    * [Toward Concurrency](https://hackmd.io/s/Skh_AaVix)


### (一) Thread Pools
* Create a number of threads in a pool where they await work.
* Advantages:
    * Usually slightly faster to service a request with an existing thread than create a new thread.
    * Allows the number of threads in the application(s) to be bound to the size of the pool.
    * Separating task to be performed from mechanics of creating task allows different strategies for running task.
        * Tasks could be scheduled to run periodically.


### (二) OpenMP
* Set of compiler directives and an API for C, C++, FORTRAN.
* Provides support for parallel programming in shared-memory environments.
* Identifies `parallel regions` – blocks of code that can run in parallel.

#### `#pragma omp parallel`
* Create as many threads as there are cores.

```c
#pragma omp parallel for
for(i = 0; i < N; i++) {
    c[i] = a[i] + b[i];
}
// Run for loop in parallel
```

#### Example

```c
#include <omp.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    /* sequential code */
    
    #pragma omp parallel
    {
        printf("I am a parallel region.");
    }

    /* sequential code */
    
    return 0;
}
```


### (三) Threading Issues

#### 1. Semantics of fork() and exec()

* Does fork() duplicate only the calling thread or all threads?
    * Some UNIXes have two versions of fork
* Exec() usually works as normal – replace the running process including all threads.


#### 2. Signal Handling
* Signals are used in UNIX systems to notify a process that a particular event has occurred.
* A `signal handler` is used to process signals.
    1. Signal is generated by particular event
    2. Signal is delivered to a process
    3. Signal is handled by one of two signal handlers:
        1. default
        2. user-defined
* Every signal has `default handler` that kernel runs when handling signal.
    * User-defined signal handler can override default.
    * For single-threaded, signal delivered to process.
* Where should a signal be delivered for multi-threaded?
    * Deliver the signal to the thread to which the signal applies.
    * Deliver the signal to every thread in the process.
    * Deliver the signal to certain threads in the process.
    * Assign a specific thread to receive all signals for the process.


#### 3. Thread Cancellation
* Terminating a thread before it has finished.
* Thread to be canceled is target thread.
* Two general approaches:
    * `Asynchronous cancellation` terminates the target thread immediately.
    * `Deferred cancellation` allows the target thread to periodically check if it should be cancelled.

* Pthread code to create and cancel a thread:

```c
pthread_t tid;

/* Create the thread */
pthread_create(&tid, 0, worker, NULL);

...

/* Cancel the thread */
pthread_cancel(tid);
```


#### 4. Thread-Local Storage
* `Thread-local storage (TLS)` allows each thread to have its own copy of data.
* Useful when you do not have control over the thread creation process (i.e., when using a thread).
* Different from local variables
    * Local variables visible only during single function invocation.
    * TLS visible across function invocations
* Similar to static data
    * TLS is unique to each thread.


#### 5. Scheduler Activations
* Both M:M and Two-level models require communication to maintain the appropriate number of kernel threads allocated to the application.
* Typically use an intermediate data structure between user and kernel threads – `lightweight process (LWP)`
![](http://i.imgur.com/N1XOvDn.jpg)
    * Appears to be a virtual processor on which process can schedule user thread to run
    * Each LWP attached to kernel thread.
    * How many LWPs to create?
* Scheduler activations provide `upcalls` - a communication mechanism from the kernel to the `upcall handler` in the thread library.
* This communication allows an application to maintain the correct number kernel threads.


### (四) Thread Scheduling
* Distinction between user-level and kernel-level threads.
* When threads supported, threads scheduled, not processes.
* Many-to-one and many-to-many models, thread library schedules user-level threads to run on LWP.
    * Known as `process-contention scope (PCS)` since scheduling competition is within the process.
    * Typically done via priority set by programmer.
* Kernel thread scheduled onto available CPU is `system-contention scope (SCS)` – competition among all threads in system.


### (五) Pthread Scheduling
* API allows specifying either PCS or SCS during thread creation.
    * `PTHREAD_SCOPE_PROCESS` schedules threads using PCS scheduling.
    * `PTHREAD_SCOPE_SYSTEM` schedules threads using SCS scheduling.
* Can be limited by OS – Linux and Mac OS X only allow `PTHREAD_SCOPE_SYSTEM`.


### (六) Multiple-Processor Scheduling
![](https://i.imgur.com/0nuvCJs.jpg)

* CPU scheduling more complex when multiple CPUs are available.
* `Homogeneous processors` within a multiprocessor.
* `Asymmetric multiprocessing` – only one processor accesses the system data structures, alleviating the need for data sharing.
* `Symmetric multiprocessing (SMP)` – each processor is self-scheduling, all processes in common ready queue, or each has its own private queue of ready processes.
    * Currently, most common
* `Processor affinity` – process has affinity for processor on which it is currently running.
    * soft affinity
    * hard affinity
    * Variations including `processor sets`

* **Load Balancing** - attempts to keep workload evenly distributed.
    * `Push migration` – periodic task checks load on each processor, and if found pushes task from overloaded CPU to other CPUs.
    * `Pull migration` – idle processors pulls waiting task from busy processor.


### (七) Socket - Message Passing

#### 1. What is a socket？

* An interface between application and network.
    * The application creates a socket.
    * The socket type dictates the style of communication.
        * `reliable` vs. `best effort`
        * `connection-oriented` vs. `connectionless`

* Once configured the application can
    * pass data to the socket for network transmission
    * receive data from the socket (transmitted through the network by some other host)


#### 2. Two essential types of sockets
![](https://i.imgur.com/HMu29e3.jpg)

#### (1) SOCK_STREAM
* a.k.a. TCP
* reliable delivery
* in-order guaranteed
* connection-oriented
* bidirectional

#### (2) SOCK_DGRAM
* a.k.a. UDP
* unreliable delivery
* no order guarantees
* no notion of “connection” – app indicates dest. for each packet
* can send or receive


#### 3. Connection setup
![](https://i.imgur.com/Je3VTRz.png)


### 課程作業
* 自 Client 端輸入一整數，將數字傳至 Server 端判斷是否為質數，再將結果回傳自 Client。

* `client` 部份

```c
/*  Make the necessary includes and set up the variables.  */

#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>

int main()
{
    int sockfd;
    int len;
    struct sockaddr_in address;
    int in;     // integer
    int result;
    // char ch = 'A';
  

/*  Create a socket for the client.  */

    sockfd = socket(AF_INET, SOCK_STREAM, 0);

/*  Name the socket, as agreed with the server.  */

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = inet_addr("127.0.0.1");
    address.sin_port = 9453;
    len = sizeof(address);

/*  Now connect our socket to the server's socket.  */

    result = connect(sockfd, (struct sockaddr *)&address, len);

    if(result == -1) {
        perror("oops: Client");
        exit(1);
    }

/*  We can now read/write via sockfd.  */

    printf("Please key in an integer：");
    scanf("%d", &in);

    write(sockfd, &in, sizeof(int));
    read(sockfd, &in, sizeof(int));

    if (in == 1)
        printf("Result from Server：數字'是質數'\n");
    else
        printf("result from server：數字'不是質數'\n");

    close(sockfd);
    exit(0);
}
```


* `server` 部份

```c
/*  Make the necessary includes and set up the variables.  */

#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>

int main()
{
    int server_sockfd, client_sockfd;
    int server_len, client_len;
    struct sockaddr_in server_address;
    struct sockaddr_in client_address;

/*  Create an unnamed socket for the server.  */

    server_sockfd = socket(AF_INET, SOCK_STREAM, 0);

/*  Name the socket.  */

    server_address.sin_family = AF_INET;
    server_address.sin_addr.s_addr = inet_addr("127.0.0.1");
    server_address.sin_port = 9453;
    server_len = sizeof(server_address);
    bind(server_sockfd, (struct sockaddr *)&server_address, server_len);

/*  Create a connection queue and wait for clients.  */

    listen(server_sockfd, 5);
    while(1) {
        int i = 0, count = 0;   // Set loop
        char in;

        printf("Server waiting...\n");

/*  Accept a connection.  */

        client_len = sizeof(client_address);
        client_sockfd = accept(server_sockfd, 
            (struct sockaddr *)&client_address, &client_len);

/*  We can now read/write to client on client_sockfd.  */

        read(client_sockfd, &in, sizeof(int));
        printf("Input from Client = %d\n",in);

        for (i = in; i > 0; i--)
            if (in % i == 0)
                count++;
        
        if (count > 2)
            in = 0;
        else if (count <= 2)
            in = 1;
     
        write(client_sockfd, &in, sizeof(int));
        close(client_sockfd);
    }
}
```

* 執行結果
![](https://i.imgur.com/vCZaRoc.jpg)




## 二、Linux 程式設計

### 課堂練習 (一)

* 將 Socket 以 multithread 的方式執行：

* `Server` 部份

```c
/*  Make the necessary includes and set up the variables.  */

#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>
#include <pthread.h>

void *thread_function(void *arg);

int main()
{
    int server_sockfd, client_sockfd;
    int server_len, client_len;
    struct sockaddr_in server_address;
    struct sockaddr_in client_address;
    
    pthread_t a_thread;
    void *thread_result;
    int res;

/*  Create an unnamed socket for the server.  */

    server_sockfd = socket(AF_INET, SOCK_STREAM, 0);

/*  Name the socket.  */

    server_address.sin_family = AF_INET;
    server_address.sin_addr.s_addr = inet_addr("127.0.0.1");
    server_address.sin_port = 9734;
    server_len = sizeof(server_address);
    bind(server_sockfd, (struct sockaddr *)&server_address, server_len);

/*  Create a connection queue and wait for clients.  */

    listen(server_sockfd, 5);    
    
    while(1) {
        printf("\nServer waiting...\n");

/*  Accept a connection.  */

        client_len = sizeof(client_address);
        client_sockfd = accept(server_sockfd, (struct sockaddr *)&client_address, &client_len);

        res = pthread_create(&a_thread, NULL, thread_function, (void *)(long)client_sockfd);
        
        if (res != 0) {
            perror("Thread creation failed");
            exit(EXIT_FAILURE);
        }
    }
}


/*  We can now read/write to client on client_sockfd.  */
void *thread_function(void *arg) {
    int i, j, tmp = 0, get_n;
    int client_sockfd = (long)arg;
    int *buf;
        
    read(client_sockfd, &get_n, sizeof(get_n));
    buf = (int *) malloc(get_n * sizeof(int));
    read(client_sockfd, buf, get_n * sizeof(int));
    printf("sort arr[%d] from client:\n", get_n);
     
    for (i = 0; i < get_n; i++) {
        for (j = 0; j < get_n; j++) {
            if (buf[i] < buf[j]) {
                tmp = buf[i];
                buf[i] = buf[j];
                buf[j] = tmp;
            }
        }
    }


    for (i = 0; i < get_n; i++)
        printf("arr[%d] = %d\n", i, buf[i]);

    // sleep(3);
    write(client_sockfd, buf, get_n * sizeof(buf));
    close(client_sockfd);
    pthread_detach(pthread_self());
    pthread_exit(0);
}
```

* `Client` 部份

```c
/*  Make the necessary includes and set up the variables.  */

#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
#include <stdlib.h>

#define SIZE 5

int main()
{
    int sockfd;
    int len;
    struct sockaddr_in address;
    int result, n;

/*  Create a socket for the client.  */

    sockfd = socket(AF_INET, SOCK_STREAM, 0);

/*  Name the socket, as agreed with the server.  */

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = inet_addr("127.0.0.1");
    address.sin_port = 9734;
    len = sizeof(address);

/*  Now connect our socket to the server's socket.  */

    result = connect(sockfd, (struct sockaddr *)&address, len);

    if (result == -1) {
        perror("oops: client!");
        exit(1);
    }

/*  We can now read/write via sockfd.  */

    int i, j, tmp = 0, s = SIZE;
    int arr[SIZE] = {4, 7, 1, 8, 3};
    int rec[SIZE] = {0, 0, 0, 0, 0};
    printf("%d\n", s);

    write(sockfd, &s, sizeof(int));
    printf("\noriginal from client - arr[]\n"); 
    for (i = 0; i < SIZE; i++)
    	printf("arr[%d] = %d\n", i, arr[i]);

    write(sockfd, arr, sizeof(arr));
   
   
    read(sockfd, rec, SIZE * sizeof(rec));    
    printf("\nresult from server - rec[]\n");

    for (i = 0; i < SIZE; i++)
        printf("rec[%d] = %d\n", i, rec[i]); 
    

    close(sockfd);
    exit(0);
}
```

* 執行結果
![](https://i.imgur.com/PaWoNZx.jpg)


### 課堂練習 (二)
* 矩陣相乘

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <pthread.h>
#include <time.h>
#define NUM_THREADS 5
#define MSIZE 10

static double getDoubleTime();
// void check();

void *thread_function(void *arg);
// srand(time(NULL));
// int a = (rand() % 10) + 1;
int A[MSIZE][MSIZE];
int B[MSIZE][MSIZE];
int C[MSIZE][MSIZE];
int D[MSIZE][MSIZE];

int main(void) {
	int res;
	int i, j;

	srand(time(NULL));
	for (i = 0; i < MSIZE; i++) {
		for (j = 0; j < MSIZE; j++) {
			A[i][j] = 1;    // (rand() % 10) + 1;
			B[i][j] = 2;    // (rand() % 10) + 1;
		}
	}

	pthread_t a_thread[NUM_THREADS];
	void *thread_result;
	int lots_of_threads;

	double start_time = getDoubleTime();

	for (lots_of_threads = 0; lots_of_threads < NUM_THREADS; lots_of_threads++) {
        res = pthread_create(&(a_thread[lots_of_threads]), NULL, thread_function, (void *)(long)lots_of_threads);
		if (res != 0) {
			perror("Thread creation failed");
			exit(EXIT_FAILURE);
		}
		// sleep(1);
	}

	printf("Waiting for threads to finish...\n");
	
    for (lots_of_threads = NUM_THREADS - 1; lots_of_threads >= 0; lots_of_threads--) {
		res = pthread_join(a_thread[lots_of_threads], &thread_result);
		if (res == 0) {
			printf("Picked up a thread[%d]\n", lots_of_threads);
		}
		else {
			perror("pthread_join failed");
		}
	}

	double finish_time = getDoubleTime();
	printf("All done\n");

	// check();

	printf("Execute Time:  %.3lf ms\n", (finish_time - start_time));
	exit(EXIT_SUCCESS);
}


void *thread_function(void *arg) {
	int my_number = (long)arg;
	int i, j, k;
	printf("Thread Number[%d]\n", my_number); 
	for (i = (MSIZE / NUM_THREADS) * my_number; i < ((MSIZE / NUM_THREADS) * my_number) + (MSIZE / NUM_THREADS); i++) {
		for (j = 0; j < MSIZE; j++) {
            for (k = 0; k < MSIZE; k++)
			    C[i][j] += A[i][k] * B[k][j];
			
            printf("C[%d][%d]:{ %d } = A[%d][%d]:{ %d } * B[%d][%d]:{ %d }\n", i, j, C[i][j], i, j, A[i][j], i, j, B[i][j]);
		}
	}

	pthread_exit(NULL);
}


static double getDoubleTime() {
	struct timeval tm_tv;
	gettimeofday(&tm_tv, 0);
	return (double)(((double)tm_tv.tv_sec * (double)1000. + (double)(tm_tv.tv_usec)) * (double)0.001);
}

/*
void check() {
	int i, j, k;
	int count = 0;

	for (i = 0; i < MSIZE; i++) {
		for (j = 0; j < MSIZE; j++) {
            for (k = 0; k < MSIZE; j++)
			    D[i][j] += A[i][k] * B[k][j];
		}
	}

	for (i = 0; i < MSIZE; i++) {
		for (j = 0; j < MSIZE; j++) {
			if ((D[i][j] - C[i][j]) != 0) {
				count++;
				printf("Error[%d][%d] = [%d]\n", i, j, D[i][j] - C[i][j]);
			}
		}
	}

	printf("Different = [%d]\n", count);
}
```

* 執行結果
![](https://i.imgur.com/g61ccDI.jpg)