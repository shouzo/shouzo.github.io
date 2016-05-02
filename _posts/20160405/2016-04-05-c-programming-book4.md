---
layout: post
title:  "20160405 [學習筆記] 由片語學習C程式設計(4) -- 判斷"
date:   2016-04-05 19:06:05
categories: C語言
tags: C語言 程式設計
---

* content
{:toc}




# 整理自 [由片語學習C程式設計](https://sites.google.com/site/mycprogrammingbook/) - Ch04 : 判斷

## [ 筆記內容 ]

    （一）if 判斷
      1.  if then
        [ 範例 4-1 ] 只顯示正數
        [ 範例 4-2 ] 檢查 i 及 j 的乘積是否為正數
      
      2.  if then else
        [ 範例 4-3 ] 顯示絕對值
        [ 範例 4-4 ] 決定三個數中的最大值
        [ 範例 4-5 ] 讀入兩個整數並由小到大印出
      
      3.  if then else if
        [ 範例 4-6 ] 使用 else if 判定閏年
        [ 範例 4-7 ] 使用多層 if 判定閏年

      4.  判斷式值
        [ 範例 4-8 ] 顯示絕對值(利用判斷式值精簡化)
        [ 範例 4-9 ] 決定三個數中的最大值(利用判斷式值精簡化)
        [ 範例 4-10 ] 判斷平閏年(利用判斷式值精簡化)


    （二）switch 判斷
        [ 範例 4-11 ] 使用 switch 依據 power 值做不同的事
        [ 範例 4-12 ] 使用 switch 依據 power 值做不同的事(少寫一個 break)
        [ 範例 4-13 ] 決定一個月的天數


    （三）作業
        [ 作業 4-14 ]
        [ 作業 4-15 ]
        [ 作業 4-16 ]
        [ 作業 4-17 ]
        [ 作業 4-18 ]
        [ 作業 4-19 ]
        [ 作業 4-20 ]
        [ 作業 4-21 ]
    
    
### [ 範例程式碼 ] GitHub
* [https://github.com/shouzo/C-Programming_pages/tree/master/C_Programming-Book/Ch04](https://github.com/shouzo/C-Programming_pages/tree/master/C_Programming-Book/Ch04)


---


### （一）if 判斷

#### 1.  if then

* 根據條件(condition)決定是否執行敘述(statement)。如果條件為真（非零），則會執行敘述；如果條件為偽（零），則不會執行敘述。

```c
// if then 判斷
if (condition)
    statement;
```

##### [ 範例 4-1 ] 只顯示正數

```c
// "4-1.c"
#include <stdio.h>
main()
{
    int i;
    scanf("%d", &i);
    
    if (i > 0)
        printf("%d\n", i);
}
```
**< 執行結果 >**
![](/assets/20160405/4-1.png)


* 複合敘述：將一組敘述合在一起當成一個敘述使用。兩個以上的敘述要用"大括號{}"包起來，而且句尾都有分號。

```c
// if 複合敘述
if (condition) {
    staement1;
    staement2;
    staement3;
}
```


##### [ 範例 4-2 ] 檢查 i 及 j 的乘積是否為正數
```c
// "4-2.c"
#include <stdio.h>
main()
{
    int i;
    int j;
        
    scanf("%d", &i);
    scanf("%d", &j);

    if(i * j > 0) {
        printf("%d\n", i);
        printf("%d\n", j);
    }
}
```
**< 執行結果 >**
![](/assets/20160405/4-2.png)


#### 2.  if then else

  * 如果條件為真（非零），則會執行敘述1；如果條件為偽（零），則會執行敘述2。

```c
// if then else 判斷
if (condition)
    statement1;
else
    statement2;
```

##### [ 範例 4-3 ] 顯示絕對值

```c
// "4-3.c"
#include <stdio.h>
main()
{
    int i;
    int k;
    scanf("%d", &i);
    
    if(i > 0)
        k = i;
    else
        k = -i;

    printf("%d\n", k);
}
```


**< 執行結果 >**
![](/assets/20160405/4-3.png)


  * 將較大值設為 max 

```c
// 將 i 及 j 的較大值設為 max
if (i > j)
    max = i;
    else
    max = j;    
// 將 i 及 max 的較大值設為 max
if (i > max)
    max = i;
```

##### [ 範例 4-4 ] 決定三個數中的最大值

```c
// "4-4.c"
#include <stdio.h>
main()
{
	int i, j;
	scanf("%d", &i);
	scanf("%d", &j);

	if(i > j) {
		printf("%d\n", j);
		printf("%d\n", i);
	} else {
		printf("%d\n", i);
		printf("%d\n", j);
	}
}
```

**< 執行結果 >**
![](/assets/20160405/4-4.png)

* 在 then 和 else 的部份使用複合敘述

```c
// 在 then 和 else 的部份使用複合敘述
if (condition) {
    statement1;   
    statement2;
    statement3;
} 
else {
    statement4;
    statement5;
    statement6;
}
```

##### [ 範例 4-5 ] 讀入兩個整數並由小到大印出

```c
// "4-5.c"
#include <stdio.h>
main()
{
	int i, j;
	scanf("%d", &i);
	scanf("%d", &j);

	if(i > j) {
		printf("%d\n", j);
		printf("%d\n", i);
	} else {
		printf("%d\n", i);
		printf("%d\n", j);
	}
}
```
**< 執行結果 >**
![](/assets/20160405/4-5.png)


#### 3.  if then else if
* "多層次的 if-then-else" 結構也稱為 "巢狀 if-then-else"

```c   
// else if
if (condition1)
    statement1;
else if (condition2)
    statement2;
else
    statement3;   
```

##### [ 用法說明 ]
  * 如果條件 1 為真，則只執行敘述 1 並結束。
  * 如果條件 1 為偽，進入下列判斷：
    * 如果條件 2 為真，只執行敘述 2 並結束。
    * 如果條件 3 為偽，只執行敘述 3 並結束。


##### [ 範例 4-6 ] 使用 else if 判定閏年
* 使用 else if 判定閏年，把判定的結果存在 k 中。如果 k 為 1，則 year 為閏年；如果 k 為 0，則 year 為平年。

```c
// "4-6.c"
#include <stdio.h>
main()
{
	int year, k;
	scanf("%d", &year);
	
	if (year % 400 == 0)
		k = 1;
	else if ((year % 4 == 0) && (year % 100 != 0))
		k = 1;
	else
		k = 0;

	printf("%d\n", k);	
}
```
**< 執行結果 >**
![](/assets/20160405/4-6.png)


##### [ 範例 4-7 ] 使用多層 if 判定閏年
* 一個 else 會對應到往上最近的一個尚未對應的 if。

```c
// "4-7.c"
#include <stdio.h>
main()
{
	int year, k;
	scanf("%d", &year);
	
	if (year % 400 != 0)
		if ((year % 4 == 0) && (year % 100 != 0))
			k = 1;
		else
			k = 0;
	else
		k = 1;
	
	printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160405/4-7.png)


#### 4.  判斷式值
* 如果"條件(condition)"為真，則"變數(variable)"的值設為"算式1(expression1)，否則設為"算式2(expression2)。

```c
// 判斷式值
(condition)?expression1:expression2
```

##### [ 範例 4-8 ] 顯示絕對值(利用判斷式值精簡化)

```c
// "4-8.c"
#include <stdio.h>
main()
{
	int i, k;
	scanf("%d", &i);
	
	k = (i > 0)? i : -i;
	printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160405/4-8.png)


##### [ 範例 4-9 ] 決定三個數中的最大值(利用判斷式值精簡化)

```c
// "4-9.c"
#include <stdio.h>
main()
{
	int i, j, k, max;
	scanf("%d", &i);
	scanf("%d", &j);
	scanf("%d", &k);

	max = (i > j)? i : j;
	if (k > max)
		max = k;
	printf("%d\n", max);
}
```
**< 執行結果 >**
![](/assets/20160405/4-9.png)


##### [ 範例 4-10 ] 判斷平閏年(利用判斷式值精簡化)

```c
// "4-10.c"
#include <stdio.h>
main()
{
	int year, k;
	scanf("%d", &year);
	k = (year % 400 == 0) ? 1 : (((year % 4 == 0) && (year % 100 != 0)) ? 1 : 0);
	printf("%d\n", k);
	
	scanf("%d", &year);
	k = (year % 400 == 0) || (((year % 4 == 0) && (year % 100 != 0)) ? 1 : 0);
	printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160405/4-10.png)


---


### （二）switch 判斷
* switch 判斷會根據一個旗標變數的"值"，在許多個可能中選擇一個來執行。

```c
// 使用 switch 判斷
switch (flag) {
    case 1 :
        statement1;
        break;
        
    case 2 :
        statement2;
        break;
    ...
    
    case n :
        statementn;
        break;

    default :
        default_statement;
    }
```

#### [ 用法說明 ]
1. flag 必須只能是一個變數，而非一個算式。(所以不能使用 i + j)
2. 接在 case 之後的必須是常數。
3. 接在敘述之後的 break 不可省略。break 在這裡的意思是跳出整個 switch 判斷，執行下一個敘述。
4. 如果旗標變數 flag 的值不在這些列舉的 case 中，則會執行預設敘述(default statement)。雖然 switch 可以不寫 default ，但為了程式的正確性，必須加上 default 用以處理例外的狀況。
    

##### [ 範例 4-11 ] 使用 switch 依據 power 值做不同的事

```c
// "4-11.c"
#include <stdio.h>
main()
{
	int i, j, power;
	scanf("%d", &power);
	scanf("%d", &i);
	
	switch (power) {
		case 1 :
			j = i;
			break;
		case 2 :
			j = i * i;
			break;
		case 3 :
			j = i * i * i;
			break;
		default :
			j = 0;
	}
	printf("%d\n", j);
}
```
**< 執行結果 >**
![](/assets/20160405/4-11.png)


##### [ 範例 4-12 ] 使用 switch 依據 power 值做不同的事(少寫一個 break)

```c
// "4-12.c"
#include <stdio.h>
main()
{
	int i, j, power;
	scanf("%d", &power);
	scanf("%d", &i);
	
	switch (power) {
		case 1 :
			j = i;
			break;
		case 2 :
			j = i * i;
		case 3 :
			j = i * i * i;
			break;
		default :
			j = 0;
	}
	printf("%d\n", j);
}
```
**< 執行結果 >**
![](/assets/20160405/4-12.png)


##### [ 範例 4-13 ] 決定一個月的天數

```c
// "4-13.c"
#include <stdio.h>
main()
{
	int year, month, days;
	scanf("%d", &year);
	scanf("%d", &month);

	switch (month) {
		case 1 : case 3 : case 5 : case 7 : case 8 :
		case 10 : case 12 :
			days = 31;
			break;

		case 4 : case 6 : case 9 : case 11 :
			days = 30;
			break;

		case 2 :
			if ((year % 4 == 0 && (year % 100 != 0)))
				days = 29;
			else
				days = 28;
			break;
		default :
			days = 0;
	}
	printf("%d\n", days);
}
```
**< 執行結果 >**
![](/assets/20160405/4-13.png)


---


### （三）作業
* 本章所有作業中的數字均為整數，且可用 int 代表，計算中也不會有溢位的問題。輸入均為一行一整數，行末有換行字元。輸出也均為一行一整數，行末有換行字元。

##### [ 作業 4-14 ]
* 寫一個程式讀入三個正整數 a、b、c。計算並印出其中的最小值。

```c
#include <stdio.h>
main()
{
	int i, j, k;
	int min = 0;
	scanf("%d%d%d", &i, &j, &k);

	if (i < j && i <k)
		min = i;
	else if (j < i && j < k)
		min = j;
	else if (k < i && k < j)
		min = k;
	
	printf("min = %d\n", min);
}
```
**< 執行結果 >**
![](/assets/20160405/4-14.png)


##### [ 作業 4-15 ]
* 寫一個程式讀入三個平面上的點座標，然後印出其中距離原點最遠的點。如果兩個或多個點有相同的距離，輸出最先出現的點座標。

```c
#include <stdio.h>
main()
{
	int a1, a2, la;	// 點 a(a1, a2)、與原點距離 la
	int b1, b2, lb;	// 點 b(b1, b2)、與原點距離 lb
	int c1, c2, lc;	// 點 c(c1, c2)、與原點距離 lc

	printf("輸入a點的座標 : ");	scanf("%d%d", &a1, &a2);
	printf("輸入b點的座標 : ");	scanf("%d%d", &b1, &b2);
	printf("輸入c點的座標 : ");	scanf("%d%d", &c1, &c2);

	la = (a1 * a1 + a2 * a2);		// 判斷 a 的距離
	lb = (b1 * b1 + b2 * b2);	// 判斷 b 的距離
	lc = (c1 * c1 + c2 * c2);		// 判斷 c 的距離

	if (la <= lb && la <= lc)
	{
		printf("min = a(%d, %d)\n", a1, a2);
	}
	else if (lb <= la && lb <= lc)
	{
		printf("min = b(%d, %d)\n", b1, b2);
	}
	else if (lc <= la && lc <= lb)
	{
		printf("min = c(%d, %d)\n", c1, c2);
	}		
}
```
**< 執行結果 >**
![](/assets/20160405/4-15.png)


##### [ 作業 4-16 ]
* 寫一個程式讀入四個整數a、b、c、d。計算以 (a，b) 及 (c，d) 為兩對角頂點的長方形面積。注意 (a，b) 及 (c，d) 若是水平或是垂直共線則面積為 0 。

```c
// "4-16.c"
#include <stdio.h>
main()
{
	int a = 0, b = 0;		// 點 (a, b) 
	int c = 0, d = 0;		// 點 (c, d)
	int area = 0;	// 由 (a, b) 和 (c, d) 兩對角所組成的長方形面積

	printf("請輸入 '點 (a, b)' 的資料：");
	scanf("%d%d", &a, &b);
	printf("請輸入 '點 (c, d)' 的資料：");
	scanf("%d%d", &c, &d);

	if ((a == c) || (b == d))	// 判斷是否為"垂直共線"(x軸) 或 "水平共線"(y軸)
		area = 0;
	else if ( (c - a) * (d - b) < 0)	// 判斷面積是否為負
		area = - (c - a) * (d - b);
	else
		area = (c - a) * (d - b);

	printf("面積為：%d\n", area);
}
```
**< 執行結果 >**
![](/assets/20160405/4-16.png)


##### [ 作業 4-17 ]
* 寫一個程式讀入四個整數a、b、c、d。本題保證 (a，b) 及 (c，d) 不會水平或是垂直共線。請計算以 (a，b) 及 (c，d) 為兩對角頂點的長方形周長。

```c
// "4-17.c"
#include <stdio.h>
main()
{
	int a = 0, b = 0;		// 點 (a, b) 
	int c = 0, d = 0;		// 點 (c, d)
	int length = 0;	// 由 (a, b) 和 (c, d) 兩對角所組成的長方形周長

	printf("請輸入 '點 (a, b)' 的資料：");
	scanf("%d%d", &a, &b);
	printf("請輸入 '點 (c, d)' 的資料：");
	scanf("%d%d", &c, &d);

	if ((a == c) || (b == d))	// 判斷是否為"垂直共線"(x軸) 或 "水平共線"(y軸)
	{
		length = 0;
		printf("點 (a, b) 和 點 (c, d) 互為'垂直'或'水平'共線");
	}

	else if ((c - a) < 0 || (d - b) < 0)	// 判斷兩邊邊長是否為負
	{
		if ((c - a) < 0 && (d - b) > 0)	// 如果 x 座標相減為負
			length = 2 * ((-1) * (c - a) + (d - b));

		else if ((c - a) > 0 && (d - b) < 0)	// 如果 y 座標相減為負
			length = 2 * ((c - a) + (-1) * (d - b));

		else if ((c - a) * (d - b) > 0)	// 如果 x、y 座標相減皆為負
			length = - 2 * ((c - a) + (d - b));

		printf("周長為：%d\n", length);
	}
		
	else	// 如果 x、y 座標相減皆為正
	{
		length = (c - a) * (d - b);		
		printf("周長為：%d\n", length);
	}
}
```
**< 執行結果 >**
![](/assets/20160405/4-17.png)


##### [ 作業 4-18 ]
* 寫一個程式讀入三個非零整數 a、b、c。如果 f(x) = ax^2 + bx + c = 0 有重根則輸出 0 ，有兩相異實根輸出 1 ，有兩相異虛根則輸出 -1。

```c
// "4-18.c"
#include <stdio.h>
main()
{
	int a, b, c;	// 三個非零的整數
	scanf("%d%d%d", &a, &b, &c);

	   /* 利用判別式： "b * b - 4 * a * c"  */
	if (b * b - 4 * a * c < 0)	// 判斷是否為兩相異虛根
		printf("-1");

	else if (b * b - 4 * a * c = 0)	// 判斷是否為重根
		printf("0");

	else if (b * b - 4 * a * c > 0)	// 判斷是否為兩相異實根
		printf("1");
}
```
**< 執行結果 >**
![](/assets/20160405/4-18.png)


##### [ 作業 4-19 ]
* 寫一個程式讀入三個正整數 a、b、c。如果邊長為 a、b、c 的三角形不存在則輸出 0，否則輸出 1。

```c
// "4-19.c"
#include <stdio.h>
main()
{
	int a, b, c;	// 三個非零的整數
	scanf("%d%d%d", &a, &b, &c);

	   /* 利用三角形特性：任兩邊邊長相加必定大於另一邊 之長度 */
	if ((a + b > c) && (b + c > a) && (a + c > b))	
		printf("1\n");

	else
		printf("0\n");
}
```
**< 執行結果 >**
![](/assets/20160405/4-19.png)


##### [ 作業 4-20 ]
* 寫一個程式讀入兩個整數 a、b 並計算由 a 加到 b 的和。

```c
// "4-20.c"
#include <stdio.h>
main()
{
	int a, b, sum = 0;
	scanf("%d%d", &a, &b);

	if ( a > b)
		for ( ; b <= a; b++)
			sum += b;

	else if ( b > a)
		for ( ; a <= b; a++)
			sum += a;

	printf("%d\n", sum);
}
```
**< 執行結果 >**
![](/assets/20160405/4-20.png)


##### [ 作業 4-21 ]
* 寫一個程式讀入三個正整數 a、b、c 並判定由 a、b、c 為邊長的三角形為銳角、鈍角或是直角三角形。如為銳角三角形則輸出 1，鈍角三角形則輸出 2，直角三角形則輸出 3。題目保證 a、b、c 可形成一個三角形。

```c
// "4-21.c"
#include <stdio.h>
main()
{
	int a, b, c;	// 三個非零的整數 (3個三角形的邊長)
	int i, tmp = 0;
	scanf("%d%d%d", &a, &b, &c);

	   // 利用三角形勾股定理特性來判別三角形的種類 (三角形最長邊，預設為 "c > b" 同時 "c > a")
	
	if (b > c) {
		tmp = b;  b = c;  	c = tmp;
		if (a > c) {
			tmp = a;  a = c;  c = tmp;
		}
	}

	if (a > c) {
		tmp = a;  a = c;  c = tmp;
		if (b > c) {
			tmp = b;  b = c;  	c = tmp;
		}
	}
	
	if (a * a + b * b > c * c)	// 判定是否為銳角三角形
		printf("此為銳角三角形：1\n");

	else if (a * a + b * b < c * c)	// 判定是否為鈍角三角形
		printf("此為鈍角三角形：2\n");

	else if (a * a + b * b == c * c)	// 判定是否為直角三角形
		printf("此為直角三角形：3\n");
}
```
**< 執行結果 >**
![](/assets/20160405/4-21.png)
