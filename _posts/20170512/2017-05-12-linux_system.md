---
layout: post
title:  "20170512 [學習筆記] Linux 系統程式 (10)"
date:   2017-05-12 00:00:00
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


## 一、作業系統
* 課堂講義
    * [Chapter 7: Deadlocks](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170511/ch07.pdf)


### (一) Deadlock Characterization
* **Mutual exclusion**：only one process at a time can use a resource.
* **Hold and wait**：a process holding at least one resource is waiting to acquire additional resources held by other processes.
* **No preemption**：a resource can be released only voluntarily by the process holding it, after that process has completed its task.
* **Circular wait**：there exists a set {P0, P1, ..., Pn } of waiting processes such that P0 is waiting for a resource that is held by P1, P1 is waiting for a resource that is held by P2 , ..., Pn–1 is waiting for a resource that is held by Pn, and Pn is waiting for a resource that is held by P0.
* Deadlocks can occur via system calls, locking, etc


### (二) Resource-Allocation Graph
![](https://i.imgur.com/ruQSE03.png)
![](https://i.imgur.com/jSnZ1nO.png)

#### 1. Example
* Resource Allocation Graph
![](https://i.imgur.com/e10FFPp.png)

* Resource Allocation Graph With A Deadlock
![](https://i.imgur.com/t2UkKLi.png)

* Graph With A Cycle But No Deadlock
![](https://i.imgur.com/3mqHz5J.png)

#### 2. Basic Facts
* If graph contains no cycles >> no deadlock
* If graph contains a cycle
    * if only one instance per resource type, then deadlock
    * if several instances per resource type, possibility of deadlock

#### 3. Methods for Handling Deadlocks
* Ensure that the system will never enter a deadlock state
* Allow the system to enter a deadlock state and then recover
* Ignore the problem and pretend that deadlocks never occur in the system; used by most operating systems, including UNIX


### (三) Deadlock Prevention
* **Mutual Exclusion**：not required for sharable resources; must hold for nonsharable resources
* **Hold and Wait**：must guarantee that whenever a process requests a resource, it does not hold any other resources
    * Require process to request and be allocated all its resources before it begins execution, or allow process to request resources only when the process has none
    * Low resource utilization; starvation possible
* **No Preemption**：
    * If a process that is holding some resources requests another resource that cannot be immediately allocated to it, then all resources currently being held are released
    * Preempted resources are added to the list of resources for which the process is waiting
    * Process will be restarted only when it can regain its old resources, as well as the new ones that it is requesting
* **Circular Wait**：impose a total ordering of all resource types, and require that each process requests resources in an increasing order of enumeration

#### 1. Example
* Deadlock Example

```c
/* thread one runs in this function */
void *do work one(void *param)
{
    pthread mutex lock(&first mutex);
    pthread mutex lock(&second mutex);
    /** * Do some work */
    pthread mutex unlock(&second mutex);
    pthread mutex unlock(&first mutex);
    pthread exit(0);
}

/* thread two runs in this function */
void *do work two(void *param)
{
    pthread mutex lock(&second mutex);
    pthread mutex lock(&first mutex);
    /** * Do some work */
    pthread mutex unlock(&first mutex);
    pthread mutex unlock(&second mutex);
    pthread exit(0);
}
```

* Deadlock Example with Lock Ordering

```c
void transaction(Account from, Account to, double amount)
{
    mutex lock1, lock2;
    lock1 = get lock(from);
    lock2 = get lock(to);
    acquire(lock1);
    acquire(lock2);
    withdraw(from, amount);
    deposit(to, amount);
    release(lock2);
    release(lock1);
}
```

#### 2. Deadlock Avoidance
* Requires that the system has some additional a priori information available
    * Simplest and most useful model requires that each process declare the **maximum number** of resources of each type that it may need
    * The deadlock-avoidance algorithm dynamically examines the resource-allocation state to ensure that there can never be a circular-wait condition
    * Resource-allocation state is defined by the number of available and allocated resources, and the maximum demands of the processes

#### 3. Safe State
![](https://i.imgur.com/Qzeh5D8.png)

#### 4. Basic Facts
* If a system is in safe state >> no deadlocks
* If a system is in unsafe state >> possibility of deadlock
* Avoidance >> ensure that a system will never enter an unsafe state

#### 5. Safe, Unsafe, Deadlock State
![](https://i.imgur.com/Y7KEMVp.png)

#### 6. Avoidance algorithms
* Single instance of a resource type
    * Use a resource-allocation graph
* Multiple instances of a resource type
    * Use the banker’s algorithm

#### 7. Resource-Allocation Graph
![](https://i.imgur.com/aUkipvw.png)
![](http://i.imgur.com/RtDzuXQ.jpg)

#### 8. Algorithm
##### (1) Data Structures for the Banker’s Algorithm
![](http://i.imgur.com/Z9SQSCC.png)

* Example
![](http://i.imgur.com/IMSADSw.png)
![](http://i.imgur.com/CnQFX9s.png)

##### (2) Safety Algorithm
![](http://i.imgur.com/V6oA8hC.png)




## 二、Linux 程式設計
* 課程簡報
    * [Linux_shell](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170512/Linux_shell.pdf)
    * [Shell_practice](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170512/Shell_practice.pdf)
* 參考資料
    * [學習 Shell Scripts](http://linux.vbird.org/linux_basic/0340bashshell-scripts.php#script_why)


### (一) Shell 簡介
* Shell 是使用者與 Linux 系統的介面，可以輸入命令，交由作業系統去執行。
    * Shell 快速且簡單
    * Shell 一般稱為 script
    * 直譯式執行，容易除錯

![](https://i.imgur.com/xadWBBf.png)

### (二) 變數 (variables)
```shell
#!/bin/sh
salutation=Hello
echo $salutation

salutation="Yes Dear"
echo $salutation

salutation=7+5
echo $salutation
```

* 執行結果
![](https://i.imgur.com/jaY3g8G.png)


### (三) 引號 (quoting)
```shell
#!/bin/sh
myvar="Hi there"

echo $myvar
echo "$myvar"
echo '$myvar'
echo \$myvar

echo "Please enter something..."
read myvar
echo $myvar
```

* 執行結果
![](https://i.imgur.com/NobGb6l.png)


### (四) 條件判斷 (condition)
![](https://i.imgur.com/enjX7FA.png)
![](https://i.imgur.com/nVZTV1F.png)


### (五) 控制結構 - if
```shell
#!/bin/sh
echo "Is it morning ?"
read timeofday

if [ $timeofday = "yes" ]; then
    echo "Good morning"
else
    echo "Good afternoon"
fi
```

* 執行結果
![](https://i.imgur.com/lSzTfpI.png)


### (六) 控制結構 - elif
```shell
#!/bin/sh
echo "Is it morning ?"
read timeofday

if [ "$timeofday" = "yes" ]
then
    echo "Good morning"

elif [ "$timeofday" = "no" ]; then
    echo "Good afternoon"

else
    echo "Sorry, $timeofday not recognized"
fi
```

* 執行結果
![](https://i.imgur.com/6T6jDMe.png)


### (七) 控制結構 - for
##### Example 1
```shell
#!/bin/sh
for foo in bar fud 43
do
echo $foo
done
```

* 執行結果
![](https://i.imgur.com/rfV1Ra5.png)

##### Example 2
```shell
#!/bin/sh
for i in 1 2 3
do
echo $i
done
```

* 執行結果
![](https://i.imgur.com/0h1Rraa.png)


### (八) 控制結構 - while
##### Example 1
```shell
#!/bin/sh
echo "Enter password"
read trythis
    
while [ "$trythis" != "secret" ]; do
    echo "Sorry, try again"
    read trythis
done
```

* 執行結果
![](https://i.imgur.com/bLumx82.png)

##### Example 2
```shell
#!/bin/sh
foo=1

while [ "$foo" -le 20 ]
do
    echo "Here we go again"
    foo=$(($foo+1))
done
exit 0
```

* 執行結果
![](https://i.imgur.com/tr0Zi4C.png)

##### Example 3
```shell
#!/bin/sh
x=0

while [ "$x" -ne 10 ]; do
    echo $x
    x=$(($x+1))
done
exit 0
```

* 執行結果
![](https://i.imgur.com/eeuK6q3.png)


### (九) 控制結構 - case
##### Example 1
```shell
#!/bin/sh
echo "Is it morning? Please answer 'Yes'、'y'、'No'、'n'"
read timeofday

case "$timeofday" in
	Yes) echo "Good Morning" ;;
	No) echo "Good Aftrenoon" ;;
	y) echo "Good Morning" ;;
	n) echo "Good Aftrenoon" ;;
	*) echo "Sorry, answer not recognized" ;;
esac
```

* 執行結果
![](https://i.imgur.com/Hnaa66Y.png)

##### Example 2
```shell
#!/bin/sh
echo "Is it morning? Please answer 'Yes'、'Y...'、'y'、'No'、'N...'、'n'"
read timeofday

case "$timeofday" in
    Y* | y | Yes) echo "Good Morning" ;;
    N* | n | No) echo "Good Aftrenoon" ;;
    *) echo "Sorry, answer not recognized" ;;
esac
```

* 執行結果
![](https://i.imgur.com/Opd1dfC.png)


### (十) 控制結構 - AND
##### Example 1
```shell
#!/bin/sh
touch file_one
rm file_two

if [ -f file_one ] && echo "hello" && [ -f file_two ] && echo "there"
then 
    echo "in if"
else
    echo "in else"
fi

exit 0
```

* 執行結果
![](https://i.imgur.com/P3wITc9.png)


### (十一) 控制結構 - OR
##### Example 1
```shell
#!/bin/sh
rm –f file_one

if [ -f file_one ] || echo "hello" || echo "there"
then
    echo "in if"
else
    echo "in else"
fi

exit 0
```

* 執行結果
![](https://i.imgur.com/POqHcOx.png)


### 課堂作業
![](https://i.imgur.com/UYMl9ZM.png)

