---
layout: post
title:  "20160419 [學習筆記] OpenCV - 程式設計介紹(2)：視訊鏡頭的使用"
date:   2016-04-19 19:06:05
categories: OpenCV C語言 
tags: C語言 程式設計 OpenCV
---




## 內文整理自 [OpenCV程式設計介紹(2)](http://yester-place.blogspot.tw/2008/06/opencv2.html)

#### [ 基本資料結構 ]

* （一）IplImage

* （二）CvCapture


#### [ 範例程式 ] 視訊鏡頭的使用
* 程式碼(GitHub)：[https://github.com/shouzo/OpenCV_Programming_pages/tree/master/Yester-place/Introduce/2](https://github.com/shouzo/OpenCV_Programming_pages/tree/master/Yester-place/Introduce/2)

```c
// Introduce_2.cpp
#include <cv.h>
#include <highgui.h>
#include <stdio.h>

int main()
{
        CvCapture *capture;
        IplImage *frame;
        capture = cvCaptureFromCAM(0) ;     // "cvCaptureFromCAM()"：選定視訊裝置(Webcam)，"0" 代表自動偵測視訊裝置
        cvNamedWindow("Webcam",0);  // 命名顯示在螢幕上的視窗名稱
        

        while(true)     // 用"while"無窮迴圈來捕捉連續影像的圖形畫面
        {
                frame = cvQueryFrame(capture);      // "cvQueryFrame()"：擷取每秒顯示出來的畫面
                cvShowImage("Webcam",frame);      // "cvShowImage()"：顯示圖片
            
                if(cvWaitKey(10) >= 0)      // "cvWaitKey(10)"：每延遲10毫秒就捕捉一次視訊畫面，當鍵盤事件發生時則會跳出"while"無窮迴圈
                {
                        break;
                }
        }  


        /* 進行記憶體回收的動作 */
        cvDestroyWindow("Webcam");   // 銷毀視窗
        cvReleaseCapture(&capture);     // 釋放記憶體    
        
        return 0;
}
```

**< 執行結果 >**
![](/assets/20160419/002.png)
