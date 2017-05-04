---
layout: post
title:  "20160915 [R語言翻轉教室] 01-RBasic (2)"
date:   2016-09-15 23:59:59
categories: R語言
tags: R語言 Data-Science
---

* content
{:toc}


## 整理自 [R語言翻轉教室](http://datascienceandr.org/)
* 版本資訊 (sha-hash：4636e186)

### 課程進度：RBasic
* 01-RBasic-01-Introduction ...ok
* 01-RBasic-02-Data-Structure-Vectors ...ok
* [01-RBasic-03-Data-Structure-Object](http://datascienceandr.org/note/01-RBasic-03-Data-Structure-Object.html) (O)
* [01-RBasic-04-Factors](http://datascienceandr.org/note/01-RBasic-04-Factors.html) (O)
* [01-RBasic-05-Arrays-Matrices](http://datascienceandr.org/note/01-RBasic-05-Arrays-Matrices.html) (O)
* 01-RBasic-06-List-DataFrame
* 01-RBasic-07-Loading-Dataset




## (一) 03-Data-Structure-Object

### 物件結構
* 向量：最基礎的『物件』。

#### [屬性] mode()、length()
* mode()：查詢向量的型態。
* length()：查詢向量的值的個數。
	* 型態：`logical`、`integer`、`numeric`、`complex`、`character`和`raw`。

```r
> # 用`mode`和`length`函數來查詢向量的型態和長度。
> mode(x)
[1] "numeric"
>
> length(x)
[1] 5
```


#### [屬性] attributes()、names()、class()
* attributes()：查詢物件的屬性。
* names()：查詢物件的名字。
* class()：查詢物件的類別。

```r
> # 用`attributes`、`names`和`class`函數來查詢向量的屬性、名稱和類別。
> attributes(g)
$names
 [1] "coefficients"  "residuals"     "effects"      
 [4] "rank"          "fitted.values" "assign"       
 [7] "qr"            "df.residual"   "xlevels"      
[10] "call"          "terms"         "model"        

$class
[1] "lm"
>
> names(g)
 [1] "coefficients"  "residuals"     "effects"      
 [4] "rank"          "fitted.values" "assign"       
 [7] "qr"            "df.residual"   "xlevels"      
[10] "call"          "terms"         "model"        
> 
> class(g)
[1] "lm"
> 
```




## (二) 04-Factors
* 範例：血型分類

```r
> blood_type
 [1] "B"  "A"  "A"  "O"  "AB" "O"  "A"  "AB" "O"  "AB" "O" 
[12] "AB" "A"  "A"  "A"  "AB" "A"  "AB" "AB" "A"  "AB" "A" 
[23] "AB" "B"  "A"  "O"  "A"  "A"  "AB" "AB"
>
```

#### [屬性] factor()、levels()
* factor()：將資料轉為factor向量。
* levels()：取出向量中的類別。

```r
> factor(blood_type)
 [1] B  A  A  O  AB O  A  AB O  AB O  AB A  A  A  AB A  AB
[19] AB A  AB A  AB B  A  O  A  A  AB AB
Levels: A AB B O
>
> blood_type_factor <- factor(blood_type)
>
> levels(blood_type_factor)
[1] "A"  "AB" "B"  "O"
```


#### [屬性] str()
* str()：觀察factor向量的結構。

```r
> str(blood_type_factor)
 Factor w/ 4 levels "A","AB","B","O": 3 1 1 4 2 4 1 2 4 2 ...
```




## (三) 05-Arrays-Matrices
#### [屬性] matrix()


#### [屬性] dim()



#### [屬性] array()


#### [屬性] cbind()、rbind()



