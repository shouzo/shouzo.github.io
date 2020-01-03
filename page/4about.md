---
layout: page
title: 關於 About
permalink: /about/
icon: commenting
---

* content
{:toc}


## 一、這裡是哪裡？

由生活和思考堆砌而成的屋子。
屋裡空無一物，只放著裝滿水的瓶子。

## 二、守備範圍 - 電腦科學 / 運動專項 / 自治組織
1. 電腦科學：立志當一名駭客 (暫時擱置)
2. 運動專項：武術為輸入，跑酷為輸出
3. 自治組織：學生社團狂熱份子


## 三、我做過什麼？


### 學生社團經歷
* 臺北市立大安高級工業職業學校
	* 武術研究社 - 萬用雜工 (2010 - 2012)

* 國立高雄科技大學 (原 "國立高雄應用科技大學")
    * 國術社社長 (2013 - 2015)
    * 跑酷社創社社長 (2015 - 2017)
    * 畢聯會活動部長 (2016 - 2017)

現任職務
* 讓狂人飛 - 泛型瑞士刀 (2017 - )
* 中華民國跑酷協會 - 第2屆理事 (2017 - )
* 台灣摩猴跑酷團隊 - 專案管理 / 運動員 / 教練 (2017 - )
* 國立交通大學跑酷社 - 社團指導老師 (2019 - )
* 國立清華大學跑酷社 - 社團指導老師 (2019 - )




<!-- Comments -->

{% if site.disqus_shortname %}
<div id="disqus_thread"></div>
<script>
/**
* RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
* LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
*/

var disqus_config = function () {
this.page.url = '{{ site.url }}{{ page.url }}'; // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = '{{ site.url }}{{ page.url }}'; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};

(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');

s.src = '//{{site.disqus_shortname}}.disqus.com/embed.js';

s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
{% endif %}


<script>
/**
 * target _blank
 */
(function() {
    var aTags = document.querySelectorAll('.left a')
    for (var i = 0; i < aTags.length; i++) {
        aTags[i].setAttribute('target', '_blank')
    }

	}());
</script>