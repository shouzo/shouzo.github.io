---
layout: post
title:  "20170331 [學習筆記] Linux 系統程式 (5)"
date:   2017-03-31 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課程簡報
    * [Chapter 4: Mutithread Programming](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170330/ch04.pdf)
    * [Chapter 5: Process Scheduling](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170330/ch05.pdf)
    * [Linux_programming_pthread](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170330/Linux_programming_pthread.pdf)
* 參考資料
    * [Toward Concurrency](https://hackmd.io/s/Skh_AaVix)
    * [Using condition variables in pthreads](http://tuxthink.blogspot.tw/2013/01/using-condition-variables-in-pthreads.html)


### (一) Thread Synchronization
### 1. Semaphore
#### semaphore.h
```c
#include <semaphore.h>

int sem_init(sem_t *sem, int pshared, unsigned int value);
//create a semaphore


int sem_wait(sem_t *sem);
//lock a semaphore


int sem_post(sem_t *sem);
//unlock a semaphore


int sem_destroy(sem_t *sem);
//delete a semaphore
```

##### sem_init()
```c
int sem_init(sem_t *sem, int pshared, unsigned
int value);
```
* `sem_t *sem`：semaphore 識別字
* `int pshared`：設定為 0 表示僅供目前的 process 及其 thread 使用。非 0 表示此 semaphore 與其他 process 共用
* `unsigned int value`：semaphore 的初始值

##### sem_wait()
```c
int sem_wait(sem_t *sem);
```
* 若 semaphore 為非 0，則 semaphore 值減
1；若 semaphore 為 0，則呼叫此 function
的 thread 會被 block ，直到 semaphore 值不
為 0。

##### sem_post()
```c
int sem_post(sem_t *sem);
```
* 對 semaphore 值加 1 。

##### sem_destroy()
```c
int sem_destroy(sem_t *sem);
//delete a semaphore
```


### 2. Mutex
#### pthread.h
```c
#include <pthread.h>

int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *mutexattr);
//create a mutex


int pthread_mutex_lock(pthread_mutex_t *mutex);
//lock a mutex


int pthread_mutex_unlock(pthread_mutex_t *mutex);
//unlock a mutex


int pthread_mutex_destroy(pthread_mutex_t *mutex);
//delete a mutex
```

##### pthread_mutex_init()
```c
int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *mutexattr);
```
* `pthread_mutex_t *mutex`：mutex 識別字
* `const pthread_mutexattr_t *mutexattr`：mutex 的屬性。設定為 NULL 表示使用預設。

##### pthread_mutex_lock()
```c
int pthread_mutex_lock(pthread_mutex_t *mutex);
//lock a mutex
```

##### pthread_mutex_unlock()
```c
int pthread_mutex_unlock(pthread_mutex_t *mutex);
//unlock a mutex
```

##### pthread_mutex_destroy()
```c
int pthread_mutex_destroy(pthread_mutex_t *mutex);
//delete a mutex
```


### 3. Condition Variables
#### pthread_cond_init (condition, attr)
* [pthread_cond_init](https://computing.llnl.gov/tutorials/pthreads/man/pthread_cond_init.txt)

#### pthread_cond_destroy (condition)
* [pthread_cond_destroy](https://computing.llnl.gov/tutorials/pthreads/man/pthread_cond_destroy.txt)

#### pthread_condattr_init (attr)
* [pthread_condattr_init](https://computing.llnl.gov/tutorials/pthreads/man/pthread_condattr_init.txt)

#### pthread_condattr_destroy (attr)
* [pthread_condattr_destroy](https://computing.llnl.gov/tutorials/pthreads/man/pthread_condattr_destroy.txt)


### 4. Barrier
* [Using barriers in pthreads](http://angelonotes.blogspot.tw/2012/02/pthreadbarrier.html)
* [pthread︰barrier](http://angelonotes.blogspot.tw/2012/02/pthreadbarrier.html)


### (二) Producer-Consumer Problem
#### 1. Producer
```c
item next produced;
while (true) {
    /* produce an item in next produced */
    
    while (((in + 1) % BUFFER SIZE) == out)
    ; /* do nothing */
        
    buffer[in] = next produced;
    in = (in + 1) % BUFFER SIZE;
}
```


#### 2. Consumer
```c
item next consumed;
while (true) {
    while (in == out)
    ; /* do nothing */
    
    next consumed = buffer[out];
    out = (out + 1) % BUFFER SIZE;
    
    /* consume the item in next consumed */
}
```


### 課程作業
```c
/*
 *  Solution to Producer Consumer Problem
 *  Using Ptheads, a mutex and condition variables
 *  From Tanenbaum, Modern Operating Systems, 3rd Ed.
 */

/*
    In this version the buffer is a single number.
    The producer is putting numbers into the shared buffer
    (in this case sequentially)
    And the consumer is taking them out.
    If the buffer contains zero, that indicates that the buffer is empty.
    Any other value is valid.
*/

#include <stdio.h>
#include <pthread.h>

#define MAX 10//000000			/* Numbers to produce */
#define BUFFER_SIZE 5

pthread_mutex_t the_mutex;
pthread_cond_t condc, condp;
int buffer[BUFFER_SIZE];
int in = 0, out = 0;

void *producer(void *ptr) {
    int i;

    for (i = 0; i < MAX; i++) {
        pthread_mutex_lock(&the_mutex);	/* protect buffer */
        
        while (((in + 1) % BUFFER_SIZE) == out)		       /* If there is something in the buffer then wait */
            pthread_cond_wait(&condp, &the_mutex);

        buffer[in] = i;
        printf("ProBuffer[%d]：%2d\n", in, buffer[in]);
        in = (in + 1) % BUFFER_SIZE;
   
        pthread_cond_signal(&condc);	/* wake up consumer */
        pthread_mutex_unlock(&the_mutex);	/* release the buffer */
    }

    pthread_exit(0);
}


void *consumer(void *ptr) {
    int i;

    for (i = 0; i < MAX; i++) {
        pthread_mutex_lock(&the_mutex);	/* protect buffer */
    
        while (in == out)			/* If there is nothing in the buffer then wait */
            pthread_cond_wait(&condc, &the_mutex);
    
        printf("ConBuffer[%d]：%2d\n", out, buffer[out]);
        out = (out + 1) % BUFFER_SIZE;
        buffer[out] = 0;
    
        pthread_cond_signal(&condp);	/* wake up consumer */
        pthread_mutex_unlock(&the_mutex);	/* release the buffer */
    }
    pthread_exit(0);
}

int main(int argc, char **argv) {
    pthread_t pro, con;

    // Initialize the mutex and condition variables
    /* What's the NULL for ??? */
    pthread_mutex_init(&the_mutex, NULL);	
    pthread_cond_init(&condc, NULL);		/* Initialize consumer condition variable */
    pthread_cond_init(&condp, NULL);		/* Initialize producer condition variable */

    // Create the threads
    pthread_create(&con, NULL, consumer, NULL);
    pthread_create(&pro, NULL, producer, NULL);

    // Wait for the threads to finish
    // Otherwise main might run to the end
    // and kill the entire process when it exits.
    pthread_join(con, NULL);
    pthread_join(pro, NULL);

    // Cleanup -- would happen automatically at end of program
    pthread_mutex_destroy(&the_mutex);	/* Free up the_mutex */
    pthread_cond_destroy(&condc);		/* Free up consumer condition variable */
    pthread_cond_destroy(&condp);		/* Free up producer condition variable */
}
```

* 執行結果
![](https://i.imgur.com/6miNt1O.jpg)




## 二、Linux 程式設計
* 課程簡報
    * [Socket_Programming](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170331/Socket_Programming.pdf)
    * [Linux-Programming_Socket_1](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170331/Linux-Programming_Socket_1.pdf)
    * [Linux-Programming_Socket_2](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170331/Linux-Programming_Socket_2.pdf)


### Socket Programming
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


#### Example
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
    server_address.sin_port = 9734;
    server_len = sizeof(server_address);
    bind(server_sockfd, (struct sockaddr *)&server_address, server_len);

/*  Create a connection queue and wait for clients.  */

    listen(server_sockfd, 5);
    while(1) {
        char ch;

        printf("server waiting\n");

/*  Accept a connection.  */

        client_len = sizeof(client_address);
        client_sockfd = accept(server_sockfd, 
            (struct sockaddr *)&client_address, &client_len);

/*  We can now read/write to client on client_sockfd.  */

        read(client_sockfd, &ch, 1);
        printf("receive from client = %c\n", ch);
        ch++;
        write(client_sockfd, &ch, 1);
        close(client_sockfd);
    }
}
```

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
    int result;
    char ch = 'A';

/*  Create a socket for the client.  */

    sockfd = socket(AF_INET, SOCK_STREAM, 0);

/*  Name the socket, as agreed with the server.  */

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = inet_addr("127.0.0.1");
    address.sin_port = 9734;
    len = sizeof(address);

/*  Now connect our socket to the server's socket.  */

    result = connect(sockfd, (struct sockaddr *)&address, len);

    if(result == -1) {
        perror("oops: client2");
        exit(1);
    }

/*  We can now read/write via sockfd.  */

    write(sockfd, &ch, 1);
    read(sockfd, &ch, 1);
    printf("char from server = %c\n", ch);
    close(sockfd);
    exit(0);
}
```


### 課程作業
* 利用Socket Programming，將 client 端的整數陣列傳送給 server 端排序，並將排序後的結果回傳給 client 端顯示。

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
    server_address.sin_port = 9734;
    server_len = sizeof(server_address);
    bind(server_sockfd, (struct sockaddr *)&server_address, server_len);

/*  Create a connection queue and wait for clients.  */

    listen(server_sockfd, 5);    
    
    while(1) {
        printf("\nserver waiting\n");

/*  Accept a connection.  */

        client_len = sizeof(client_address);
        client_sockfd = accept(server_sockfd, (struct sockaddr *)&client_address, &client_len);

/*  We can now read/write to client on client_sockfd.  */

        int i, j, tmp = 0, get_n;
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
        
        write(client_sockfd, buf, get_n * sizeof(buf));
        close(client_sockfd);
    }
}
```


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
![](https://i.imgur.com/AhJZRLN.jpg)
