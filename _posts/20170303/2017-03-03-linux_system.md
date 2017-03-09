---
layout: post
title:  "20170303 [學習筆記] Linux 系統程式 (1)"
date:   2017-03-03 23:59:59
categories: Linux 作業系統 程式設計
tags: Linux 作業系統 程式設計
---


* content
{:toc}


### (一) 作業系統
* [課程簡報 - Ch1：Introduction](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170302/ch01.pdf)




### (二) Linux 程式設計
* [課程簡報 - Creating and Executing Processes](https://github.com/shouzo/Operating-System_pages/blob/master/class-tutorial/20170303/Creating_and_Executing_Processes.pdf)

* 程式作業： `fork()` 的概念

```c
#include <stdlib.h>
#include <stdio.h>

int main(void) {
/* Press any Integer */
	int n;
	scanf("%d", &n);

/* Use "fork()" to create Child's and Parent's process */
	int ret = fork();
	int result = n;	// Set the "result" initial value

	if (ret == 0) {	// Child's entry
		printf("\n***** Child's pid = %d *****\n", getpid());
		while (n --> 0)
			result += n;
		printf("1 + 2 + ... + n = %d\n", result);
		exit(0);
	
	} else {	// Parent's entry
		printf("***** Parent's pid = %d *****\n", getpid());
		printf("n 的因數：%d", n);
		while (n --> 1)
			if (result % n == 0)
				printf("\t%d", n);
		exit(0);
	}
}
```
* **執行結果**

![](https://i.imgur.com/TKyt2rB.jpg)