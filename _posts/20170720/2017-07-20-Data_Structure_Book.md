---
layout: post
title:  "20170720 [學習筆記] 細談資料結構_6th (1)"
date:   2017-07-20 00:00:00
categories: 資料結構 演算法 C語言 程式設計
tags: 資料結構 演算法 C語言 程式設計
---


* content
{:toc}


# 20170720 - Ch1_導論
* 進度摘要
    * Ch1_導論 (100%)
* 完整專案
    * [https://github.com/shouzo/Data-Structure-Learning_pages/tree/master/Data_Structures_6th_Edition/20170720](https://github.com/shouzo/Data-Structure-Learning_pages/tree/master/Data_Structures_6th_Edition/20170720)
* 參考資料
    * [DIKW 體系 - 維基百科](https://zh.wikipedia.org/wiki/DIKW%E4%BD%93%E7%B3%BB)
    * [演算法的表示](https://market.cloud.edu.tw/content/senior/computer/ks_ks/book/algodata/algorithm/algo3.htm)
    * [虛擬碼 - 維基百科](https://zh.wikipedia.org/wiki/%E4%BC%AA%E4%BB%A3%E7%A0%81)
    * [時間複雜度 - 維基百科](https://zh.wikipedia.org/wiki/%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6)
    * [Algorithm Analysis](http://www.csie.ntnu.edu.tw/~u91029/AlgorithmAnalysis.html)
    * 資料結構 - 鍾宜玲
        * [資料結構教學講義](http://imil.au.edu.tw/~hsichcl/DataStructureHandout.htm)
        * [第1章 - 導論 (Introduction)](http://ds.klab.tw/pdf/chapter1_1.pdf)
        * [程式設計範例](http://ds.klab.tw/pdf/chapter1_2.pdf)
        * [時間複雜度、費氏數列](http://ds.klab.tw/pdf/chapter1_3.pdf)



## 一、DIKW 模型
* 資料 (Data) - **D**
    * 具體的符號或文數字。
* 資訊 (Information) - **I**
    * 資料所呈現出來，可以透過分析而理解的訊息。
* 知識 (Knowledge) - **K**
    * 從資訊中學習、推導或歸納後，經由理解所得到的結果。
* 智慧 (Wisdom or Intelligence) - **W**
    * 知識的系統化成果，是對於知識的整體洞察。

![](https://i.imgur.com/Ia8Tc7E.jpg)


## 二、定義與表示
![](https://i.imgur.com/KvTLhQo.jpg)

---

* **資料結構 (Data Structures)**
    * 將資料加以計算或處理時所建立而成的結構。
* **演算法 (Algorithms)**
    * 建立並運用資料結構的方法。
    * **在有限步驟內，解決數學問題的程序**。


![](https://i.imgur.com/QOe1t5m.jpg)


### [範例] 歐幾里得演算法
* 計算兩個自然數的「最大公因數 (Greatest Common Divisor, GCD)」

#### **(1) 描述過程 (3個敘述)**
1. 輸入兩個自然數。
2. 作輾轉相除直到餘數為零。
	* 註：此敘述不符合明確性
3. 除數即為 GCD。

#### **(2) 將原先的 3個敘述 改為 5個敘述**
1. 輸入兩個自然數 A, B。
2. A 除以 B 餘數為 R。
3. 如果 R 為零，則跳至敘述 5。
4. A ← B (B的值給A)，B ← R (R的值給B)，跳至敘述 2。
5. B 即為 GCD。

#### **(3) 以 逐步追蹤法 (Stepwise trace) 驗證敘述過程**
* 假設 `A = 18`, `B = 12`
	1. A = 18, B = 12
    2. R = (18 MOD 12) = 6
    3. R ≠ 0
	4. A = 12, B = 6
	5. R = (12 MOD 6) = 0
	6. R = 0
	7. B 即為 GCD (GCD = 6)
* 由過程可以得知這些敘述符合明確性、有限性、正確性和具有結果的輸出。


### (一) 流程圖 (flow chart)
![](https://i.imgur.com/MBTLY3p.png)

* 流程圖說明
    * [http://www2.lssh.tp.edu.tw/~hlf/class-1/lang-c/flow/flow-chat.htm](http://www2.lssh.tp.edu.tw/~hlf/class-1/lang-c/flow/flow-chat.htm)

**[範例] 求兩實數相減之絕對值**
1. 輸入兩個實數 a 與 b 的值。
2. 設定實數 c 值為 `a - b`。
3. 如果 (c < 0) 成立，則實數 c 設定為 `-c`。
4. 輸出實數 c 的值。

![](https://i.imgur.com/LU4WcQ5.png)


### (二) 虛擬碼 (pseudo code)
* 以比較口語的描述法加上「迴圈」及「條件判斷」等結構敘述來表達演算法的執行過程，藉以增加可讀性。

**[範例] 從 1 加到 10 演算法的虛擬程式碼** (如下所示)

```
counter = 1
total = 0
while counter <= 10
{
   total = total + counter
   add 1 to counter
}
output total
```


## 三、程式的分析
* 一個好的程式必須滿足以下條件：
    1. **執行結果正確**
    2. **記憶體需求低**
    3. **可維護性高**
        * 檢驗項目一：**程式是否模組化？**
        * 檢驗項目二：**命名是否有意義？**
            * 常數、變數及函式
        * 檢驗項目三：**定義是否明確？**
            * 輸入、輸出及功能
        * 檢驗項目四：**註解是否恰當？**
        * 檢驗項目五：**說明文件是否完備？**
    4. **執行效率高**
        * 執行時間 (run time) 的長短：簡略以程式執行的敘述多寡 (頻率計數) 來測量。
        * 檢驗項目：**「頻率計數」(frequency count)**
            * 計算程式敘述被執行的總次數；當次數越高，執行時間越長。
            * 用來評估程式的執行時間，以判斷演算法的優劣。

        ![](https://i.imgur.com/rNifrSw.jpg)
                
        1. **循序敘述**
            * 將敘述的行數進行加總。
        2. **決策分支敘述**
            * 取各個條件分支中總行數的最大值，再加上比較敘述的次數。
        3. **迴圈敘述**
            * 計算迴圈重複的次數，再乘上迴圈內的行數。


## 四、Big-O 符號
* **Big-O：時間複雜度 (time complexity)**
    * 程式效率以頻率計數函數的 **級數 (order)** 進行分級。
    * 一般皆以 O 符號來表示時間複雜度，讀作 **Big-oh**。
    * 可以看出執行時間相對於問題大小的「成長速度」、「成長趨勢」。

### (一) 時間複雜度等級
![](https://i.imgur.com/d5Gw2H2.png)

### (二) 大小排序
![](https://i.imgur.com/4BfZu1g.png)
![](https://i.imgur.com/9tz4yMA.png)

### (三) 範例
![](https://i.imgur.com/eWmEGTs.png)
