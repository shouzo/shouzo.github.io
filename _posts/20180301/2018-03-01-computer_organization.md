---
layout: post
title:  "20180301 [學習筆記] 計算機結構 (1)"
date:   2018-03-01 00:00:00
categories: 計算機結構 作業系統
tags: 計算機結構 作業系統
---


* content
{:toc}


* 課程簡報
    * [Chapter-1-Computer-Abstractions-and-Technology](https://github.com/shouzo/Computer-Organization-And-Design_pages/blob/master/class-tutorial/20180301/Chapter-1-Computer-Abstractions-and-Technology.pdf)
* 參考資料
    * [計算機結構 - 速度的議題](http://ccckmit.wikidot.com/co:speed)
    * [GitBook - 組合語言與計算機組織](https://goo.gl/fW6rF6)
        * [Computer Abstractions and Technology](https://chi_gitbook.gitbooks.io/personal-note/content/computer_abstractions_and_technology.html)
        * [Performance](https://chi_gitbook.gitbooks.io/personal-note/content/performance.html)
        * [CPU Time](https://chi_gitbook.gitbooks.io/personal-note/content/cpu_time.html)


### 一、Classes of Computers
#### 1. Desktop computers
* General purpose, variety of software.
* Subject to ``cost`` / ``performance`` tradeoff.

#### 2. Server computers
* Network based.
* High capacity, performance, reliability.
* Range from small servers to building sized.

#### 3. Embedded computers
* Hidden as components of systems.
* Stringent ``power`` / ``performance`` / ``cost`` constraints.


### 二、Understanding Performance
#### 1. Algorithm
* Determine number of operations executed.

#### 2. Programming language, compiler, architecture
* Determine number of machine instructions executed per operation.

#### 3. Processor and memory system
* Determine how fast instructions are executed.

#### 4. I/O system (including OS)
* Determines how fast I/O operations are executed.


### 三、Below your Program
![](https://i.imgur.com/jbQSrpv.jpg)


#### 1. Application software
* Written in high-level language.

#### 2. System software
* Compiler: translates HLL code to machine code.

#### 3. Hardware
* Processor, memory, I/O controllers


### 四、Levels of Program Code
#### 1. High-level language
* Level of abstraction closer to problem domain.
* Provides for productivity and portability.

#### 2. Assembly language
* Textual representation of instrctions.

#### 3. Hardware representation
* Binary digits (bits).
* Encoded instructions and data.

### 五、Defining Performance
#### 1. Response time
* How long it takes to do a task.

#### 2. Throughput
* Total work done per unit time.
    * e.g. **tasks** / **transactions** / ... per hour


### 六、Relative Performance
![](https://i.imgur.com/QCzLpUf.jpg)

* X is n time faster than Y
![](https://i.imgur.com/mQHfgdK.jpg)


### 七、Measuring Execution Time
#### 1. Elapsed time
* Total response time, including all aspects
    * Processing, I/O, OS overhead, idle time.
* Determines system performance

#### 2. CPU time
![](https://i.imgur.com/ThOhi5q.jpg)

* CPU Time Example
    > ![](https://i.imgur.com/p0FUEbd.jpg)

* **Performance improved**
    > 1. Reducing number of clock cycles.
    > 2. Increasing clock rate.
    > 3. Hardware designer must often trade off clock rate against cycle count.

#### 3. Instruction Count and CPI
* `Instruction Count`：指令個數
* `CPI`：每一個指令需要執行多少個週期 (cycle per instruction)

![](https://i.imgur.com/7YA28w1.jpg)

* CPI Example
    > ![](https://i.imgur.com/6sLnWxw.jpg)


### 八、Performance Summary
* **Performance depends on**
    1. **Algorithm**: affects IC, possibly IC
    2. **Programming language**: affects IC, CPI
    3. **Compiler**: affects IC, CPI
    4. **Instruction set architecture**: affects IC, CPI, Tc