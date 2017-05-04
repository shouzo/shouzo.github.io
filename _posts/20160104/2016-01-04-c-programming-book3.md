---
layout: post
title:  "20160104 [學習筆記] 由片語學習C程式設計(3) -- 運算"
date:   2016-01-04 19:06:05
categories: C語言
tags: C語言 程式設計
---

* content
{:toc}




# 整理自 [由片語學習C程式設計](https://sites.google.com/site/mycprogrammingbook/) - Ch03 : 運算

## [ 筆記內容 ]

    （一）指定
      1.  使用"等號( = )" 作"指定"的動作
        (1) "等號( = )" 的意義
          [ 範例 3-1 ] 設定 i 為 j
        (2) 交換兩個變數的值
          [ 範例 3-2 ] 交換變數 i 和 變數 j 的值
        (3) "指定"敘述本身也可以"作為算式"
          [ 範例 3-3 ] 使用指定當算式

    （二）算術運算
      [ 背景知識 ] 運算子 (operator) 及 運算元 (operand)
      1. 加減運算
        [ 範例 3-4 ] 設定 k 為 i 及 j 的和或差
      2. 乘除運算
        [ 範例 3-5 ] 設定 k 為 i 及 j 的商
        [ 範例 3-6 ] 設定 k 為 i 除以 j 的餘數
        [ 範例 3-7 ] 計算一個二位數的十位數及個位數
      3. 負數運算
        [ 範例 3-8 ] 計算一個數的負數
      4. 調整變數值
      5. 整數的範圍
        [ 範例 3-9 ] 用 "sizeof()" 計算 int 所占記憶體的位元組數
      6. 運算先後順序
        [ 範例 3-10 ] 數學運算的先後順序
        
    （三）比較運算
      1. 比較運算的結果
        [ 範例 3-11 ] 印出比較運算的結果

    （四）邏輯運算
      1. 運算順序及結果
        [ 範例 3-12 ] 整數使用邏輯運算
        [ 範例 3-13 ] 判斷閏年
      2. 快捷運算
        [ 範例 3-14 ] 快捷運算可能造成意料之外的執行結果
        [ 範例 3-15 ] "避免"快捷運算可能造成意料之外的執行結果 (繼範例 3-14)
        
    （五）作業
        [ 作業 3-16 ] 
        [ 作業 3-17 ]
        [ 作業 3-18 ] 
        [ 作業 3-19 ] 
        [ 作業 3-20 ] 
        [ 作業 3-21 ] 
        [ 作業 3-22 ] 
        [ 作業 3-23 ]
        [ 作業 3-24 ] 


### [ 範例程式碼 ] GitHub

* [https://github.com/shouzo/C-Programming/tree/master/C_Programming-Book/Ch03](https://github.com/shouzo/C-Programming/tree/master/C_Programming-Book/Ch03)


---


### （一）指定

#### 1.  使用"等號( = )" 作"指定"的動作

##### (1) "等號( = )" 的意義

* 將 "=" 右邊的值指定給 "=" 左邊的值。
* 代表指定而非相等。

```c
// 使用"等號(=)"進行指定的動作
i = 0;    // 將"變數 i"的內容指定為 0
i = j;    // 將"變數 i"的內容設定為另一個"變數 j"
```

##### [ 範例 3-1 ] 設定 i 為 j
  
```c
// "3-1.c"
#include <stdio.h>
main()
{
    int i;
    int j;
    scanf("%d", &i);    
    scanf("%d", &j);
    printf("%d\n", i);
    i = j;
    printf("%d\n", i);
}
```
**< 執行結果 >**
![](/assets/20160104/3-1.png)

##### (2) 交換兩個變數的值

* 建立一個變數作為暫存之用。

```c
// 利用暫存變數"temp"來交換兩個變數("i"和"j")的值
    temp = i;    
    i = j;
    j = temp;
```

##### [ 範例 3-2 ] 交換變數 i 和 變數 j 的值
  
```c
// "3-2.c"
#include <stdio.h>
main()
{
    int i;
    int j;
    int temp;   // 宣告暫存變數"temp"
    scanf("%d", &i);
    scanf("%d", &j);

  // **** 開始交換變數 i 和 變數 j ****
    temp = i;
    i = j;
    j = temp;

  // **** 交換結束 ****
    printf("%d\n", i);
    printf("%d\n", j);
}
```
**< 執行結果 >**
![](/assets/20160104/3-2.png)

##### (3) "指定"敘述本身也可以"作為算式"

* 使用指定當算式

```c
// 使用指定當算式
i = j = 0;
// 由於"="右邊會優先運算，此時可視為"i = (j = 0)": 0 先給 j 作為新值，再將 j 的新值指定給 i
``` 
    
##### [ 範例 3-3 ] 使用指定當算式
    
```c
// "3-3.c"
#include <stdio.h>
main()
{
    int i = 1;
    int j = 2;
    int k = 3;
    
    scanf("%d", &i);
    k = j = i;
    
    printf("%d\n", i);
    printf("%d\n", j);
    printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160104/3-3.png)


---


### （二）算術運算

#### [ 背景知識 ] 運算子(operator) 及 運算元(operand)

* 算術運算是由 "運算子 (operator)" 及 "運算元 (operand)" 所構成：
  * 運算子 (operator)
    1. 和 ( + )、差 ( - ) 、積 ( * )、商 ( / )、餘數 ( % ) 等運算子稱為 "二元運算子 (binary operator)"：需要兩個運算元。
    * 取負數 稱為 "一元運算子 (unary operator)"：只需要一個運算元。

![](/assets/20160104/op.png)

圖片來源(C 語言運算子) - [https://zh.wikipedia.org/wiki/C%E8%AF%AD%E8%A8%80](https://zh.wikipedia.org/wiki/C%E8%AF%AD%E8%A8%80) 

  * 運算元 (operand)
    * 進行算術運算中所使用到的"變數或數字" (運算子以外的字元)。        

* 運算子與運算元的差異

```c
/* 認識運算子與運算元 */
    a = b + 7;    // 運算式一定在"等號(=)"右邊，在此"b + 7"為運算式
    // "a" 及 "b" 為運算元；
    // "=" 及 "+" 為運算子        
/* 計算和、差、積、商、或是餘數 */
  // **** 包含 "二元運算子" ****
    k = i + j;
    k = i - j;
    k = i * j;
    k = i / j;
    k = i % j;
  // **** 包含 "一元運算子" ****
    k = -i;
```
  
  
#### 1.  加減運算

* 基本的加減運算

##### [ 範例 3-4 ] 設定 k 為 i 及 j 的和或差
  
```c
// "3-4.c"
#include <stdio.h>
main()
{
    int i, j, k;
    scanf("%d", &i);
    scanf("%d", &j);
          
    k = i + j;
    printf("%d\n", k);

    k = i - j;
    printf("%d\n", k);
} 
```
**< 執行結果 >**
![](/assets/20160104/3-4.png)


#### 2.  乘除運算
* 基本的乘除運算

##### [ 範例 3-5 ] 設定 k 為 i 及 j 的商

```c
// "3-5.c"
#include <stdio.h>
main()
{
    int i, j, k;          
    
    // 第一次輸入
    scanf("%d", &i);
    scanf("%d", &j);
    k = i / j;
    printf("%d\n", k);
    
    // 第二次輸入
    scanf("%d", &i);
    scanf("%d", &j);
    k = i / j;
    printf("%d\n", k);
}      
```
**< 執行結果 >**
![](/assets/20160104/3-5.png)


##### [ 範例 3-6 ] 設定 k 為 i 除以 j 的餘數

```c
// "3-6.c"
#include <stdio.h>
main()
{
    int i, j, k;

    // 第一次輸入
    scanf("%d", &i);
    scanf("%d", &j);
    k = i % j;
    printf("%d\n", k);
     
    // 第二次輸入
    scanf("%d", &i);
    scanf("%d", &j);
    k = i % j;
    printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160104/3-6.png)

##### [ 範例 3-7 ] 計算一個二位數的十位數及個位數

```c
// "3-7.c"
#include <stdio.h>
main()
{
      int i;
      int k;
      
      scanf("%d", &i);
      k = i / 10;
      printf("%d\n", k);

      k = i % 10;
      printf("%d\n", k);      
}
```
**< 執行結果 >**
![](/assets/20160104/3-7.png)


#### 3.  負數運算

##### [ 範例 3-8 ] 計算一個數的負數

```c
// "3-8.c"
#include <stdio.h>
main()
{
      int i;
      int j;
      
      scanf("%d", &i);
      j = -i;
      printf("%d\n", j);      
}
```
**< 執行結果 >**
![](/assets/20160104/3-8.png)


#### 4.  調整變數值

* 運算的簡潔表示

```c
/* 簡潔表示 */
i += j;    // 相當於 i = i + j
i -= j;    // 相當於 i = i - j
i *= j;    // 相當於 i = i * j
i /= j;    // 相當於 i = i / j
i %= j;    // 相當於 i = i % j
/* 把 i 的值加、減 "1" */
i++;       // 相當於 i = i + 1;    
i--;       // 相當於 i = i - 1;
```


#### 5.  變數的範圍
* 用 "sizeof()" 印出變數所占記憶體的位元組數

```c
// "sizeof()"的用法
printf("%d\n", sizeof(variable));
```
* 如果計算的結果超過一個變數所能表示的範圍就稱為 "溢位"，溢位會使計算結果不正確。

##### [ 範例 3-9 ] 用 "sizeof()" 計算 int 所占記憶體的位元組數
  * 一個 "int" 所能表示的範圍：2^31 - 1 到 -2^31，若輸入的整數超過 "int" 的範圍將會產生 "整數溢位"。

```c
// "3-9.c"
#include <stdio.h>
main()
{
      int i;

      printf("%d\n", sizeof(i));     
      scanf("%d", &i);
      printf("%d\n", i);
      i++;
      printf("%d\n", i);
}
```
**< 執行結果 >**
![](/assets/20160104/3-9.png)


#### 6.  運算先後順序
* 在C語言中，先做 "乘法"、"除法"、"求餘數"；後做 "加法"、"減法"。
* 可以用 "小括號" 進行強制優先運算。

##### [ 範例 3-10 ] 數學運算的先後順序

```c
// "3-10.c"
#include <stdio.h>
main()
{
      int i, j, k;

      scanf("%d", &i);
      scanf("%d", &j);
      k = i + 4 * j;
      
      printf("%d\n", k);
      k = (i + 4) * j;
      printf("%d\n", k);
}

```
**< 執行結果 >**
![](/assets/20160104/3-10.png)


---


### （三）比較運算
* 相關運算符號

| 符號 | 意義 |
| -- | -- |
| >= | 大於等於 |
| > | 大於 |
| <= | 小於等於 |
| < | 小於 |
| == | 等於 |
| != | 不等於 |

* 常見用法
  * 註：算術運算先做，比較運算後做。
  
| 比較"常數" | 比較"整數變數" | 比較"整數算術算式" |
| -- | -- | -- |
| i >= 0 | i >= j | i >= (j+n) |
| i > 0 | i > j | i > (j+n) |
| i <= 0 | i <= j | i <= (j+n) |
| i < 0 | i < j | i < (j+n) |
| i == 0 | i == j | i == (j+n) |
| i != 0 | i != j | i != (j+n) |

* 特殊用法

| 偶數判斷 | 直角三角形判斷 |
| -- | -- |
| (i % 2) == 0 | (a)(a) + (b)(b) == (c)(c) |


#### 1.  比較運算的結果
* 在C語言裡面，"0" 代表 "偽"、"非零的整數" 代表 "真"。

##### [ 範例 3-11 ] 印出比較運算的結果

```c
#include <stdio.h>
main()
{
      int i, j, k;

      scanf("%d", &i);
      scanf("%d", &j);
      k = (i == 3);
      printf("%d\n", k);
      
      k = (i == j);
      printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160104/3-11.png)


---


### （四）邏輯運算

#### [ 邏輯運算符號 ]

* 且(and)：
  * 兩者都要為 "真"，答案才為 "真"。

  ```c
  // "且"(and)的運算
  i && j
  ```
  

* 或(or)：
  * 兩者間只要有一個答案為 "真"，答案即為 "真"。

  ```c
  // "或"(or)的運算
  i || j
  ```


* 反(not)：!
  * 變數為 "真" 時，答案為 "偽"；變數為 "偽" 時，答案為 "真"。

  ```c
  // "反"(not)的運算
  !i
  ```
    

#### 1.  運算順序及結果

    一元運算子 >> 算術運算 >> 比較運算 >> 邏輯運算 >> 調整運算

| 優先順序 | C 語言表示法 |
| -- | -- |
| 1 | ++ (加1)、- - (減1) |
| 2 | - (求負數) |
| 3 | ! (非) |
| 4 | * (乘)、/ (除)、% (求餘數) |
| 5 | + (加)、- (減) |
| 6 | == (等於)、!= (不等於)、> (大於)、< (小於)、>= (大於等於)、<= (小於等於) |
| 7 | && (且)、|| (或) |
| 8 | 指定及調整變數值：+=、- =、*=、/=、%= |


##### [ 範例 3-12 ] 整數使用邏輯運算
```c
#include <stdio.h>
main()
{
      int i = 3;
      int j = 0;
      int k;
      
      k = (i && j);
      printf("%d\n", k);
      
      k = (i || j);
      printf("%d\n", k);
      
      k = !i;
      printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160104/3-12.png)


##### [ 範例 3-13 ] 判斷閏年
* 一個閏年必須是 400 的倍數，或是 4 的倍數但非 100 的倍數。

```c
#include <stdio.h>
main()
{
      int year;
      int k;
      
      scanf("%d", &year);
      k = (year % 400 == 0) || ((year % 4 == 0) && (year % 100 != 0));
      printf("%d\n", k);
}
```
**< 執行結果 >**
![](/assets/20160104/3-13.png)


#### 2.  快捷運算 (short circuit evalutaion)
* 適用在 "不更改任何變數值" 的邏輯算式：如果一個邏輯算式經由部份算式已經可以確認真偽，則剩下的部分不會執行。

##### [ 範例 3-14 ] 快捷運算可能造成意料之外的執行結果

```c
#include <stdio.h>
main()
{
      int i, j, k = 3, l = 4;
      
      scanf("%d", &i);
      scanf("%d", &j);
      if ((k = i) > 0 || (l = j) > 0)
      {
           printf("%d\n", k);
           printf("%d\n", l);  
      }
}
```
**< 執行結果 >**
![](/assets/20160104/3-14.png)

##### [ 範例 3-15 ] "避免"快捷運算可能造成意料之外的執行結果 (繼範例 3-14)

```c
#include <stdio.h>
main()
{
      int i, j, k = 3, l = 4;
      
      scanf("%d", &i);
      scanf("%d", &j);
      k = i;
      l = j;
      if (k > 0 || l > 0)
      {
           printf("%d\n", k);
           printf("%d\n", l);  
      }
}
```
**< 執行結果 >**
![](/assets/20160104/3-15.png)


---


### （五）作業

#### [ 作業 3-16 ] 
* 寫一個程式讀入三個˙整數，計算並輸出他們的連乘積。

```c
#include <stdio.h>
main()
{
      int i1, i2, i3, total;
      
      scanf("%d %d %d", &i1, &i2, &i3);
      total = i1 * i2 * i3;
      
      printf("%d * %d * %d = %d", i1, i2, i3, total);
}
```

**< 執行結果 >**

![](/assets/20160104/3-16.png)


#### [ 作業 3-17 ] 
* 寫一個程式讀入一個整數，如果該整數為偶數則輸出 1 ，否則輸出 0。

```c
#include <stdio.h>
main()
{
      int i;
      
      scanf("%d", &i);
      
      i = ((i % 2 == 0)) && (!(i % 2 == 1));
      
      printf("%d", i);
}
```
**< 執行結果 >**
![](/assets/20160104/3-17.png)


#### [ 作業 3-18 ] 
* 寫一個程式讀入一個四位正整數並計算四個位數的和。如果輸入為 1234，則輸出為 10。

```c
#include <stdio.h>
main()
{
      int i;
      int i1, i2, i3, i4;
      int total;
      
      scanf("%d", &i);
      
      i1 = i % 10;                          // 计 
      i2 = (i % 100 - i % 10) / 10;         // 计
      i3 = ((i % 1000) - (i % 100)) / 100;  // κ计
      i4 = i / 1000;                        // 计
      
      total = i1 + i2 + i3 + i4;
      
      printf("%d", total); 
}
```
**< 執行結果 >**
![](/assets/20160104/3-18.png)


#### [ 作業 3-19 ] 
* 寫一個程式讀入一個正整數。如果該整數為 3 的倍數但不為 5 的倍數則輸出 1 ，或是為 5 的倍數但不為 3 的倍數則輸出 1；其他狀況均輸出 0。例如輸入為 36 或 20，則輸出為 1；如果輸入為 30 或 7，則輸出為 0。

```c
#include <stdio.h>
main()
{
      int i = 0;
      
      scanf("%d", &i);
      
      i = (!(i % 3) && (i % 5)) || (!(i % 5) && (i % 3));
      
      printf("%d", i); 
}
```
**< 執行結果 >**
![](/assets/20160104/3-19.png)


#### [ 作業 3-20 ] 
* 寫一個程式找零錢。程式首先讀入一介於 1 與 100 之間的整數，然後使用最少數目的 50、10、5、1 硬幣湊足給定的整數。

```c
#include <stdio.h>
main()
{
      int i;
      int i1, i2, i3, i4;
      
      scanf("%d", &i);    // 輸入的整數須介於"1 ~ 100"之間 
      
      i1 = i / 50;    // 取得"50元"的數量                         
      i2 = (i - (50 * i1)) / 10;                     // 取得"10元"的數量
      i3 = (i - (50 * i1 + 10 * i2 )) / 5;           // 取得"5元"的數量
      i4 = (i - (50 * i1 + 10 * i2 + 5 * i3)) / 1;   // 取得"1元"的數量
      
      printf("%d\n", i1);
      printf("%d\n", i2);
      printf("%d\n", i3);
      printf("%d\n", i4);
}
```
**< 執行結果 >**
![](/assets/20160104/3-20.png)


#### [ 作業 3-21 ] 
* 寫一個程式讀入四個正整數 a、b、c、d 並保證點 (a, b) 及 (c, d) 為不同點。如果連接點 (a, b) 及 (c, d) 的直線通過原點則輸出 1，其他狀況均輸出 0。

```c
#include <stdio.h>
main()
{
      int a, b, c, d;
      int A, B, A1, B1;
      int result = 0;
      
      printf("請分別輸入正整數\n"); 
      
      printf("a = ");
      scanf("%d", &a);
      
      printf("b = ");
      scanf("%d", &b);
      
      printf("c = ");
      scanf("%d", &c);
      
      printf("d = ");
      scanf("%d", &d);      
      
      
      // 判斷"商" 
      A = (d-b)/(c-a);
      B = (b-0)/(a-0);
      
      // 判斷"餘數" 
      A1 = (d-b)%(c-a);
      B1 = (b-0)%(a-0);
      
      result = ((A == B) && (A1 == B1));    // 利用"商"跟"餘數"來判斷斜率是否相同
      printf("%d", result);
}
```
**< 執行結果 >**
![](/assets/20160104/3-21.png)


#### [ 作業 3-22 ] 
* 寫一個程式讀入四個整數 a、b、c、d 並且保證 c > a 且 d > b 。計算以 (a, b) 為左下角，(c, d) 為右上角的長方形面積及周長。

```c
#include <stdio.h>
main()
{
      int a, b, c, d;
      int area, length;
      
      printf("請分別輸入四個整數\n");
      printf("必須要保證 c > a 和 d > b\n"); 
      
      printf("a = ");
      scanf("%d", &a);
      
      printf("b = ");
      scanf("%d", &b);
      
      printf("c = ");
      scanf("%d", &c);
      
      printf("d = ");
      scanf("%d", &d);
      
      
      area = (c-a) * (d-b);            // 計算面積
      length = 2 * ((c-a) + (d-b));    // 計算周長 

      printf("面積為：%d\n", area);
      printf("周長為：%d", length); 
}
```

**< 執行結果 >**

![](/assets/20160104/3-22.png)


#### [ 作業 3-23 ] 
* 寫一個程式讀入三個整數 a、b、c，其中 a 不等於 0。如果方程式 f(x) = ax^2 + bx + c = 0 有重根則輸出 1，其他狀況均輸出 0。

```c
#include <stdio.h>
main()
{
      int a, b, c;
      int result;
      
      printf("請分別輸入三個整數\n");
      printf(" 'a' 不能為0\n"); 
      
      printf("a = ");
      scanf("%d", &a);
      
      printf("b = ");
      scanf("%d", &b);
      
      printf("c = ");
      scanf("%d", &c);
      
      
      // 利用 b*b - 4*a*c 公式來判別方程式是否為重根 
      result = (b * b - 4 * a * c) == 0;


      printf("%d", result); 
}
```

**< 執行結果 >**

![](/assets/20160104/3-23.png)


#### [ 作業 3-24 ] 
* 寫一個程式讀入三個整數，分別為一個長方體的高度、寬度和深度。計算並輸出其表面積及體積。

```c
#include <stdio.h>
main()
{
      int height, width, depth;
      int area, volume;
      
      printf("請分別輸入高度、寬度、深度\n");
      
      printf("高度(height) = ");
      scanf("%d", &height);
      
      printf("寬度(width) = ");
      scanf("%d", &width);
      
      printf("深度(depth) = ");
      scanf("%d", &depth);
      
      
      // 計算表面積 
      area = 4 * (height * width) + 2 * (height * depth);
      
      // 計算體積
      volume = height * width * depth;
      
      
      printf("表面積為：%d\n", area);
      printf("體積為：%d", volume);
      
}
```

**< 執行結果 >**

![](/assets/20160104/3-24.png)
