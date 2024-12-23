---
layout: post
title:  "20160908 [R語言翻轉教室] 01-RBasic (1)"
date:   2016-09-08 23:59:59
categories: R語言
tags: R語言 Data-Science
---

* content
{:toc}


## 整理自 [R語言翻轉教室](http://datascienceandr.org/)
* 版本資訊 (sha-hash：4636e186)

### 課程進度：RBasic
* [01-RBasic-01-Introduction](http://datascienceandr.org/note/01-RBasic-01-Introduction.html) (O)
* [01-RBasic-02-Data-Structure-Vectors](http://datascienceandr.org/note/01-RBasic-02-Data-Structure-Vectors.html) (O)
* 01-RBasic-03-Data-Structure-Object
* 01-RBasic-04-Factors
* 01-RBasic-05-Arrays-Matrices
* 01-RBasic-06-List-DataFrame
* 01-RBasic-07-Loading-Dataset


## (一) 01-Introduction

### 1. 查詢、說明

#### [用法] help()、?
* 獲得函數的細節說明。

#### [範例] 查詢 solve 函數的說明：

```r
> # 查詢 solve 函數
> help(solve)	# 用法 1
> ?solve		# 用法 2
```


### 2. expression、assignment
```r
> # assignment 動作 (不會顯示結果)
> a <- 1
> 
> # expression 動作 (會顯示結果)
> a + 1
[1] 2
>
```


### 3. 查詢物件

#### [用法] objects()、ls()
* 查詢所有環境的物件。

```r
> objects()	# 用法 1
[1] "a"                         "chat"                     
[3] "check_then_install"        "check_then_install_github"
[5] "issue"                     "source_by_l10n_info"      
> 
> ls()	# 用法 2
[1] "a"                         "chat"                     
[3] "check_then_install"        "check_then_install_github"
[5] "issue"                     "source_by_l10n_info"      
> 
```


### 4. 刪除物件

#### [用法] rm()
* 刪除指定的物件。

```r
> objects()	# 查詢所有環境的物件
[1] "chat"                      "check_then_install"       
[3] "check_then_install_github" "issue"                    
[5] "source_by_l10n_info"      
> 
> # 用 "rm()" 移除物件
> rm(chat)    # 移除"chat"
> rm(issue)   # 移除"issue"
> 
> objects()	# 再次查詢環境
[1] "check_then_install"        "check_then_install_github"
[3] "source_by_l10n_info"      
> 
```




## (二) 02-Data-Structure-Vectors

### 1. 數字向量
* 在數學上，輸入的 x 可能是整數、實數甚至是複數。R 的數學計算，會自動根據 x 的屬性做調整。

```r
> # 最簡單的向量
> x <- c(10.4, 5.6, 3.1, 6.4)	# assignment
> x
[1] 10.4  5.6  3.1  6.4
>
> # "c()"可以接受任意數量的的向量參數
> c(x, 2, 4, 6, 8)	# 在 x 後面追加"2, 4, 6, 8"
[1] 10.4  5.6  3.1  6.4  2.0  4.0  6.0  8.0
> c(x, x) 			# 建立一個在 x 後面接上 x 的向量
[1] 10.4  5.6  3.1  6.4 10.4  5.6  3.1  6.4
> 
> # 向量的運算
> c(1, 2, 3) + c(2, 4, 6)	# 相同長度的向量相加
[1] 3 6 9
> 2 + c(2, 4, 6)			 # 兩種不同長度的向量相加
[1] 4 6 8
>
```


### 2. 取最小值、最大值

#### [用法] min()、max()、range()

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> 
> c(min(x), max(x))	 # 取得 x 的最小值和最大值
[1]  3.1 10.4
> range(x)			  # 回傳向量的範圍（最小到最大）
[1]  3.1 10.4
> 
```


### 3. 取得向量值的加總、長度和平均值

#### [用法] sum()、length()、mean()

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> sum(x) 		# 回傳向量的加總
[1] 25.5
>
> length(x)	  # 回傳向量的長度
[1] 4
>
> sum(x) / length(x)	# 回傳向量的平均值
[1] 6.375
> mean(x)			   # 回傳向量的平均值
[1] 6.375
>
```


### 4. 樣本變異數
* 樣本變異數：sum((x - mean(x))^2) / (length(x) - 1)
	* 可以直接用 var(x) 取代

#### [用法] var()

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> sum((x - mean(x))^2) / (length(x) - 1)
[1] 9.175833
>
> var(x)
[1] 9.175833
> 
```


### 5. 樣本標準差

#### [用法] sd()

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> sd(x)
[1] 3.029164
>
```


### 6. 對向量進行排序

#### [用法] sort()

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> sort(x)	# 將向量裡的元素從小排到大
[1]  3.1  5.6  6.4 10.4
> 
```


### 7. 整數、實數、複數的計算

#### [範例] 開根號

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> sqrt(-17)			 # 對"負整數"開根號
[1] NaN
Warning message:
In sqrt(-17) : 產生了 NaNs
> 
> sqrt(-17 + 0i)		# 對"複數"開根號
[1] 0+4.123106i
> 
```


### 8. 產生序列

#### [用法1] 利用 `:` 產生序列
* 在 R 裡面，```:```的等級很高，會優先處理。

```r
> 1:20
 [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
>
> -17:15
 [1] -17 -16 -15 -14 -13 -12 -11 -10  -9  -8  -7  -6  -5  -4  -3  -2  -1   0   1
[20]   2   3   4   5   6   7   8   9  10  11  12  13  14  15
> 
> # 以下 expression，會優先處理 17:23，然後再把所有結果乘以 2
> 2 * 17:23
[1] 34 36 38 40 42 44 46
> 
```

#### [用法2] 利用 seq() 產生序列
* seq 的參數有：```from```、```to```、```by```、```length.out``` 和 ```along.with```
* 查看詳細用法︰```?seq```
* 查看使用範例︰```example(seq)```

```r
> # 如果不指定參數，而是依照順序填入：seq(1,10)，那就代表 from 是 1、to 是 10、而 by 等其他 argument 就使用預設參數
> seq(1, 10)
 [1]  1  2  3  4  5  6  7  8  9 10
> 
> # 指定參數
> seq(to = 10, from = 1)
 [1]  1  2  3  4  5  6  7  8  9 10
> 
> # 透過 by 參數控制間隔
> seq(1, 10, by = 0.5)
 [1]  1.0  1.5  2.0  2.5  3.0  3.5  4.0  4.5  5.0  5.5  6.0  6.5  7.0  7.5  8.0
[16]  8.5  9.0  9.5 10.0
> 
> # 產生一個從1開始，間隔2，長度為10的序列
> seq(from = 1, by = 2, length.out = 10)
 [1]  1  3  5  7  9 11 13 15 17 19
> 
```

#### [用法3] 利用 rep() 產生序列

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> 
> # 重複特定數字
> rep(2, times = 10)
 [1] 2 2 2 2 2 2 2 2 2 2
> 
> # 將整個序列重複times次，然後接起來
> rep(x, times = 2)
[1] 10.4  5.6  3.1  6.4 10.4  5.6  3.1  6.4
> 
> # 將向量的每一個值個別重複each遍之後，再接起來
> rep(x, each = 2)
[1] 10.4 10.4  5.6  5.6  3.1  3.1  6.4  6.4
> 
```


### 9. 邏輯向量
* 用於建立邏輯向量的「條件」：
	* 大於 ```>```
	* 大於等於 ```>=```
	* 小於 ```<```
	* 小於等於 ```<=```
	* 相等 ```==```
	* 不相等```!=```
* 常常表示資料中如：「是、否」、「男、女」之類"二選一"的選項。
* 常見的三種情況：
	* 第一種：「真」用 **TRUE** 或 **T** 代表。
	* 第二種：「假」用 **FALSE** 或 **F** 代表。
	* 第三種：「NA」（NotAvailable）

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> 
> # 利用「條件類型的expression」來建立邏輯向量
> x > 5	# 拿 5 和 c(10.4, 5.6, 3.1, 6.4) 比較，比較大的就是 TRUE ，而比較小的是 FALSE。
[1]  TRUE  TRUE FALSE  TRUE
> 
> # 篩選複雜條件
> x > 5 & x < 10	# 介於5和10之間的值為 TRUE，否則為 FALSE
[1] FALSE  TRUE FALSE  TRUE
> 
```


### 10. NA：缺失值、NaN：數學中沒有被定義的值
* 當資料的值無法取得的時候，R 就會給它一個特殊的記號：NA。
* 所有和NA做的運算結果，通常都會是NA。

#### [用法] is.na()

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> 
> is.na(x)	# 判斷向量 x 裡面有沒有NA
[1] FALSE FALSE FALSE FALSE
>
> # 判斷一個內含 NA 的向量
> is.na(c(1, 2, NA, 3))
[1] FALSE FALSE  TRUE FALSE
>
> # is.na() 會將 NA 和 NaN 都看成TRUE
> is.na(c(NA, NaN, 1))
[1]  TRUE  TRUE FALSE
> 
```


### 11. 字串的概念
* 透過單引號```'```或雙引號```"```來建立的資料，被稱為：「字串」。

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> x    # x 被視為變數
[1] 10.4  5.6  3.1  6.4
> 
> "x"  # x 被視為字串
[1] "x"
>
>
> # 輸入一個包含雙引號的字串，有兩種方法：一種是用單引號'來包覆雙引號，另外一種就是在雙引號之前插入斜線\
> "\""
[1] "\""
>
> "R is good!\""
[1] "R is good!\""
> 
```

#### [用法] paste()、paste0()
* 把輸入的argument以字串的形式，接成一個新的字串。

```r
> paste("a", "b")
[1] "a b"
>
>
> paste(c("X", "Y"), 1:10)	# 在串接字串時，中間會留空格。
 [1] "X 1"  "Y 2"  "X 3"  "Y 4"  "X 5"  "Y 6"  "X 7"  "Y 8"  "X 9"  "Y 10"
>
> paste0(c("X", "Y"), 1:10)	# 在串接字串時，中間不會留空格。
 [1] "X1"  "Y2"  "X3"  "Y4"  "X5"  "Y6"  "X7"  "Y8"  "X9"  "Y10"
> 
```


### 12. 在向量中挑選特定資料
* 利用 ```[]``` 挑選特定的資料。

```r
> x <- c(10.4, 5.6, 3.1, 6.4)
> x[c(1,3)]		# 挑選 x 的第一個和第三個位置的值
[1] 10.4  3.1
>
> x[x > 5]		# 挑選 x 之中大於 5 的值
[1] 10.4  5.6  6.4
>
> x[-2]			# 挑選 x 之中，除了第 2 個數字以外的值
[1] 10.4  3.1  6.4
> 
```


### 13. 為向量中的值進行命名
* 在 R 的向量中，每一個值是可以有名字的。

#### [用法] names()
```r
> x <- c(10.4, 5.6, 3.1, 6.4)
>
> # 為 x 裡的值進行命名
> names(x) <- c("a", "b", "c", "d")	
> 
> x		# 顯示 x 的值
   a    b    c    d 
10.4  5.6  3.1  6.4 
>
> # 選取名稱為"b"、"d"的值
> x[c("b", "d")]
  b   d 
5.6 6.4 
> 
```


### [範例] 整理台電的公開數據

#### <題目內容>

```r
# 社會服務業自民國87至民國91年的年度用電量（度）
year1 <- 87:91
power1 <- c(6097059332, 6425887925, 6982579022, 7323992602.53436, 7954239517) 
# 製造業自民國87至民國91年的年度用電量（度）
power2 <- c(59090445718, 61981666330, 67378329131, 66127460204.6482, 69696372914.6949) 
```


#### <標準解答>

```r
# 請同學選出年度(`year1`)中，社會服務業的的用電量超過`7e9` 的年份。
# （`7e9`是R 的科學符號，代表`7 * 10^9`）
year1.answer1 <- year1[power1 > 7e9]

# 接著請同學計算「社會服務業從民國87年到91年的平均用電量」
power1.mean <- mean(power1)

# 請計算「社會服務業從民國87年到91年用電量的標準差」
power1.sd <- sd(power1)

# 在統計中，我們常常會計算一筆數據的「標準分數」，也就是將數據減去平均數後除以標準差。
# 請同學計算「社會服務業從民國87年到91年用電量的標準分數」
power1.z <- (power1 - mean(power1)) / sd(power1)

# 同樣的道理，請同學算出「製造業自民國87年至民國91年用電量的平均數、標準差和標準分數」
power2.mean <- mean(power2)
power2.sd <- sd(power2)
power2.z <- (power2 - mean(power2)) / sd(power2)

# 最後，請同學找出年度中，「社會服務業的用電量」，超過「製造業的10%用電量」的年份
year1.answer2 <- year1[power1 > 0.1 * power2]
```
