---
layout: post
title:  "20170414 [學習筆記] Linux 系統程式 (7)"
date:   2017-04-14 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課程簡報
    * [Chapter 3: Process Concept](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170413/ch03.pdf)


### (一) Direct Communication
* Processes must name each other explicitly：
    * send(P, message) – send a message to process P
    * receive(Q, message) – receive a message from process Q

* Properties of communication link
    * Links are established automatically
    * A link is associated with exactly one pair of communicating processes
    * Between each pair there exists exactly one link
    * The link may be unidirectional, but is usually bi-directional


### (二) Indirect Communication
* Messages are directed and received from mailboxes (also referred to as ports)
    * Each mailbox has a unique id
    * Processes can communicate only if they share a mailbox

* Properties of communication link
    * Link established only if processes share a common mailbox
    * A link may be associated with many processes
    * Each pair of processes may share several communication links
    * Link may be unidirectional or bi-directional

* Operations
    * create a new mailbox
    * send and receive messages through mailbox
    * destroy a mailbox

* Primitives are defined as：
    * send(A, message) – send a message to mailbox A
    * receive(A, message) – receive a message from mailbox A

* Mailbox sharing
    * Who gets the message？
        * P 1 , P 2 , and P 3 share mailbox A
        * P 1 , sends; P 2 and P 3 receive

* Solutions
    * Allow a link to be associated with at most two processes.
    * Allow only one process at a time to execute a receive operation.
    * Allow the system to select arbitrarily the receiver. Sender is notified who the receiver was.


### (三) Synchronization
* Message passing may be either blocking or non-blocking

* **Blocking** is considered **synchronous**
    * `Blocking send` has the sender block until the message is received
    * `Blocking receive` has the receiver block until a message is available

* **Non-blocking** is considered **asynchronous**
    * `Non-blocking send` has the sender send the message and continue
    * `Non-blocking receive` has the receiver receive a valid message or null

* Different combinations possible
    * If both send and receive are blocking, we have a rendezvous
* Producer-consumer becomes trivial


### (四) Buffering
* Queue of messages attached to the link; implemented in one of three ways
    1. Zero capacity – 0 messages
    Sender must wait for receiver (rendezvous)
    2. Bounded capacity – finite length of n messages
    Sender must wait if link full
    3. Unbounded capacity – infinite length
    Sender never waits


### Examples of IPC Systems

#### 1. POSIX
* POSIX Shared Memory
    * Process first creates shared memory segment：`shm_fd = shm_open(name, O CREAT | O RDRW, 0666);`
    * Also used to open an existing segment to share it
    * Set the size of the object：`ftruncate(shm fd, 4096);`
    * Now the process could write to the shared memory：`sprintf(shared memory, "Writing to shared memory");`

#### 2. Mach
* Mach communication is message based
    * Even system calls are messages
    * Each task gets two mailboxes at creation- Kernel and Notify
    * Only three system calls needed for message transfer：`msg_send()`, `msg_receive()`, `msg_rpc()`
    * Mailboxes needed for commuication, created via：`port_allocate()`
    * Send and receive are flexible, for example four options if mailbox full：
        * Wait indefinitely
        * Wait at most n milliseconds
        * Return immediately
        * Temporarily cache a message

#### 3. Windows
* Message-passing centric via `advanced local procedure call (LPC)` facility
    * Only works between processes on the same system
    * Uses ports (like mailboxes) to establish and maintain communication channels
    * Communication works as follows：
        * The client opens a handle to the subsystem’s `connection port` object.
        * The client sends a connection request.
        * The server creates two private `communication ports` and returns the handle to one of them to the client.
        * The client and server use the corresponding port handle to send messages or callbacks and to listen for replies.


### (五) Communications in Client-Server Systems

#### 1. Sockets
* A socket is defined as an endpoint for communication
* Concatenation of `IP address` and `port` – a number included at start of message packet to differentiate network services on a host
    * The socket 161.25.19.8:1625 refers to port 1625 on host 161.25.19.8
* Communication consists between a pair of sockets
* All ports below 1024 are well known, used for standard services
* Special IP address `127.0.0.1 (loopback)` to refer to system on which process is running

#### 2. Remote Procedure Calls
* Remote procedure call (RPC) abstracts procedure calls between processes on networked systems
    * Again uses ports for service differentiation
* **Stubs** – client-side proxy for the actual procedure on the server
* The client-side stub locates the server and **marshalls** the parameters
* The server-side stub receives this message, unpacks the marshalled parameters, and performs the procedure on the server
* On Windows, stub code compile from specification written in **Microsoft Interface Definition Language (MIDL)**
* Data representation handled via **External Data Representation (XDL)** format to account for different architectures
    * `Big-endian` and `little-endian`
* Remote communication has more failure scenarios than local
    * Messages can be delivered `exactly once` rather than `at most once`
* OS typically provides a rendezvous (or **matchmaker**) service to connect client and server

![](https://i.imgur.com/fvjj3rR.jpg)

#### 3. Pipes
* Acts as a conduit allowing two processes to communicate
* **Issues**
    * Is communication unidirectional or bidirectional?
    * In the case of two-way communication, is it half or full-duplex?
    * Must there exist a relationship (i.e. parent-child) between the communicating processes?
    * Can the pipes be used over a network?

![](https://i.imgur.com/W3b5HkN.jpg)
![](https://i.imgur.com/c18pKq0.jpg)


### 課堂作業
* `client` 部份

```c
/* vim: ts=4 sw=4 et
*/
/* The second program is the producer and allows us to enter data for consumers.
 It's very similar to shm1.c and looks like this. */

#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

#include <sys/shm.h>

#include "shm_com.h"

int main()
{
    int running = 1;
    void *shared_memory = (void *)0;
    struct shared_use_st *shared_stuff;
    char buffer[BUFSIZ];
    int shmid;
    int rand_arr[10];

    shmid = shmget((key_t)1234, sizeof(struct shared_use_st), 0666 | IPC_CREAT);
	
    if (shmid == -1) {
        fprintf(stderr, "shmget failed\n");
        exit(EXIT_FAILURE);
    }

    shared_memory = shmat(shmid, (void *)0, 0);
    if (shared_memory == (void *)-1) {
        fprintf(stderr, "shmat failed\n");
        exit(EXIT_FAILURE);
    }

    printf("Memory attached at %X\n", (unsigned int)(long)shared_memory);

    shared_stuff = (struct shared_use_st *)shared_memory;

    while (1) {
        while (shared_stuff->written_by_you == 1) {
            sleep(1);            
            printf("waiting for client...\n");
        }
        
        /*
        printf("\nPress Enter to continue...");
        while (getchar() != '\n');
        // fgets(buffer, BUFSIZ, stdin);
        */

        srand((unsigned)time(NULL));
        for (int i = 0; i < 10; i++) {
            rand_arr[i] = rand() % 100 + 1;
            shared_stuff->some_text[i] = rand_arr[i];
            printf("[%d]%d \t", i, rand_arr[i]);
        }
        printf("\n");

        //  strncpy(shared_stuff->some_text, &rand_arr, TEXT_SZ);
        shared_stuff->written_by_you = 1;
        break;
    }

    if (shmdt(shared_memory) == -1) {
        fprintf(stderr, "shmdt failed\n");
        exit(EXIT_FAILURE);
    }
    exit(EXIT_SUCCESS);
}
```

* `server` 部份

```c
/* vim: ts=4 sw=4 et
*/
/* Our first program is a consumer. After the headers the shared memory segment
 (the size of our shared memory structure) is created with a call to shmget,
 with the IPC_CREAT bit specified. */

#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

#include <sys/shm.h>

#include "shm_com.h"

int main()
{
    int running = 1;
    void *shared_memory = (void *)0;
    struct shared_use_st *shared_stuff;
    int shmid;

    srand((unsigned int)getpid());    

    shmid = shmget((key_t)1234, sizeof(struct shared_use_st), 0666 | IPC_CREAT);
    
    if (shmid == -1) {
        fprintf(stderr, "shmget failed\n");
        exit(EXIT_FAILURE);
    }

/* We now make the shared memory accessible to the program. */

    shared_memory = shmat(shmid, (void *)0, 0);
    if (shared_memory == (void *)-1) {
        fprintf(stderr, "shmat failed\n");
        exit(EXIT_FAILURE);
    }

    printf("Memory attached at %X\n", (unsigned int)(long)shared_memory);

/* The next portion of the program assigns the shared_memory segment to shared_stuff,
 which then prints out any text in written_by_you. The loop continues until end is found
 in written_by_you. The call to sleep forces the consumer to sit in its critical section,
 which makes the producer wait. */

    shared_stuff = (struct shared_use_st *)shared_memory;
    shared_stuff->written_by_you = 0;
    
    while (1) {
        if (shared_stuff->written_by_you) {
            printf("\nYou wrote:\n");
            
            for (int i = 0; i < 10; i++) {
                printf("[%d]%d \t", i, shared_stuff->some_text[i]);
            }
            printf("\n");

            sleep(rand() % 4); /* make the other process wait for us ! */
            // shared_stuff->written_by_you = 0;
            
        shared_stuff->written_by_you = 0;
        }
    }
/* Lastly, the shared memory is detached and then deleted. */


    if (shmdt(shared_memory) == -1) {
        fprintf(stderr, "shmdt failed\n");
        exit(EXIT_FAILURE);
    }

    if (shmctl(shmid, IPC_RMID, 0) == -1) {
        fprintf(stderr, "shmctl(IPC_RMID) failed\n");
        exit(EXIT_FAILURE);
    }

    exit(EXIT_SUCCESS);
}
```

* 執行結果
![](https://i.imgur.com/rPMcs2j.jpg)




## 二、Linux 程式設計
* 課程簡報
    * [Linux-Programming-IPC-2](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170414/Linux.Programming.Programming.IPC-2.pdf)


### (一) Shared Memory Concept
* 共享記憶體是由 IPC 為一程序所建立的特殊記憶體位址，其他的程序可以將此相同的 shared memory 區段納入自己的位址空間中，所有的程序皆可存取這些記憶體位址，就像是由自己定址一樣。
![](https://i.imgur.com/ctobLbA.jpg)


### (二) Shared Memory Function
```c
#include <sys/sem.h>
#include <sys/type.h>
#include <sys/ipc.h>


int shmget(key_t key, size_t size, int shmflg);
    // 建立 shared memory 。


void *shmat(int shm_id, const void *shm_addr, int shmflg);
    // 允許程序對 shared memory 存取。


int shmdt(const void *shm_addr);
    // 讓目前的程序從 shared memory 脫離出來。


int shmctl( int shm_id, int cmd, struct shmid_ds *buf );
    // 用來改變 shared memory 。
```


#### shmget()
```c
int shmget(key_t key, size_t size, int shmflg);
```
* key: 用來為 shared memory 命名。
* size: 需要的 shared memory 大小，以 byte 為單位。
* shmflg: shared memory 的權限。在建立新的 shared memory 時要加上 IPC_CREATE。


#### *shmat()
```c
void *shmat(int shm_id, const void *shm_addr, int shmflg);
```
* shm_id: Shared memory id.
* shm_addr: Shared memory 加到目前程序中的位址，通常為一 null 指標。
* shmflg: 一般設為 0 即可。


#### shmdt()
```c
int shmdt(const void *shm_addr);
```
* shm_addr: shmat 傳回的位址。


#### shmctl()
```c
int shmctl(int shm_id, int cmd, struct shmid_ds *buf);
```
* shm_id: Shared memory id.
* cmd:IPC_RMID: 刪除 shared memory 區段。
* struct shmid_ds:

```c
struct shmid_ds {
    uid_t shm_perm.uid;
    uid_t shm_perm.gid;
    mode_t shm_perm.mode;
}
```


### 課堂作業
* `client` 部份

```c
/* vim: ts=4 sw=4 et
*/
/* The second program is the producer and allows us to enter data for consumers.
 It's very similar to shm1.c and looks like this. */

#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <time.h>

#include <sys/shm.h>

#include "shm_com.h"

int main()
{
    void *shared_memory = (void *)0;
    struct shared_use_st *shared_stuff;
    char buffer[BUFSIZ];
    int shmid;
    int rand_arr[10];

    shmid = shmget((key_t)1234, sizeof(struct shared_use_st), 0666 | IPC_CREAT);
	
    if (shmid == -1) {
        fprintf(stderr, "shmget failed\n");
        exit(EXIT_FAILURE);
    }

    shared_memory = shmat(shmid, (void *)0, 0);
    if (shared_memory == (void *)-1) {
        fprintf(stderr, "shmat failed\n");
        exit(EXIT_FAILURE);
    }

    printf("Memory attached at %X\n", (unsigned int)(long)shared_memory);

    shared_stuff = (struct shared_use_st *)shared_memory;


    /* Initial the array */
    for (int i = 0; i < 10; i++)
        shared_stuff->some_text[i] = 0;
 

    shared_stuff->written_by_you = 1;
    shared_stuff->answer_by_you = 0;


    while (1) {
        while (shared_stuff->written_by_you == 1) {
            sleep(1);            
            printf("Waiting for server...\n");
        }
      

        printf("\n【Server connected】\n");

        /* Generate random number */
        printf("Generate random number\n");
        srand((unsigned)time(NULL));
        for (int i = 0; i < 10; i++) {
            rand_arr[i] = rand() % 100 + 1;
            shared_stuff->some_text[i] = rand_arr[i];
        }
        shared_stuff->written_by_you = 2;


        /* Wait for the sorted array */
        printf("Wait for the sorted array...\n");
        while (shared_stuff->answer_by_you == 0);

        printf("The array (After Sorted)\n");
        for (int i = 0; i < 10; i++) {
            printf("[%d]%d \t", i, shared_stuff->some_text[i]);
        }

        printf("\n");


        shared_stuff->written_by_you = 1;
        shared_stuff->answer_by_you = 0;
        break;
    }

    if (shmdt(shared_memory) == -1) {
        fprintf(stderr, "shmdt failed\n");
        exit(EXIT_FAILURE);
    }
    exit(EXIT_SUCCESS);
}
```

* `server` 部份

```c
/* vim: ts=4 sw=4 et
*/
/* Our first program is a consumer. After the headers the shared memory segment
 (the size of our shared memory structure) is created with a call to shmget,
 with the IPC_CREAT bit specified. */

#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

#include <sys/shm.h>

#include "shm_com.h"

int main()
{
    void *shared_memory = (void *)0;
    struct shared_use_st *shared_stuff;
    int shmid;

    srand((unsigned int)getpid());    

    shmid = shmget((key_t)1234, sizeof(struct shared_use_st), 0666 | IPC_CREAT);
    
    if (shmid == -1) {
        fprintf(stderr, "shmget failed\n");
        exit(EXIT_FAILURE);
    }

/* We now make the shared memory accessible to the program. */

    shared_memory = shmat(shmid, (void *)0, 0);
    if (shared_memory == (void *)-1) {
        fprintf(stderr, "shmat failed\n");
        exit(EXIT_FAILURE);
    }

    printf("Memory attached at %X\n", (unsigned int)(long)shared_memory);

/* The next portion of the program assigns the shared_memory segment to shared_stuff,
 which then prints out any text in written_by_you. The loop continues until end is found
 in written_by_you. The call to sleep forces the consumer to sit in its critical section,
 which makes the producer wait. */

    shared_stuff = (struct shared_use_st *)shared_memory;


    shared_stuff->written_by_you = 1;

    while (1) {
        /* Wait for the client */
        if (shared_stuff->written_by_you != 2)
            shared_stuff->written_by_you = 0;

        if (shared_stuff->written_by_you == 2) {
            printf("\n【Get the client's array】\n");
            printf("The array (Before Sorted)\n");
            for (int i = 0; i < 10; i++) {
                printf("[%d]%d \t", i, shared_stuff->some_text[i]);
            }
            printf("\n");


            /* Sort the array */
            shared_stuff->answer_by_you = 0;
            int tmp = 0;
            for (int i = 0; i < 10; i++) {
                for (int j = 0; j < 10 ; j++) {
                    if (shared_stuff->some_text[i] < shared_stuff->some_text[j]) {
                        tmp = shared_stuff->some_text[i];
                        shared_stuff->some_text[i] = shared_stuff->some_text[j];
                        shared_stuff->some_text[j] = tmp;
                    }
                }
            }
            shared_stuff->answer_by_you = 1;
            sleep(rand() % 4); /* make the other process wait for us ! */ 
            shared_stuff->written_by_you = 1;
        }
        sleep(1);
    }


/* Lastly, the shared memory is detached and then deleted. */
    if (shmdt(shared_memory) == -1) {
        fprintf(stderr, "shmdt failed\n");
        exit(EXIT_FAILURE);
    }

    if (shmctl(shmid, IPC_RMID, 0) == -1) {
        fprintf(stderr, "shmctl(IPC_RMID) failed\n");
        exit(EXIT_FAILURE);
    }

    exit(EXIT_SUCCESS);
}

```

* 執行結果
![](https://i.imgur.com/6VYmXyg.jpg)
