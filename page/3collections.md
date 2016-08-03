---
layout: page
title: 收藏 Collections
permalink: /collection/
icon: bookmark
---

* content
{:toc}


# (一) GitBook 電子書
* 這裡儲存所有學習到的知識和內容，將會分門別類的存放在這裡：
	* [https://www.gitbook.com/book/shouzo/shouzo-notebook/](https://www.gitbook.com/book/shouzo/shouzo-notebook/)

---

# (二) Hackpad 線上筆記
* 這裡整理著之前在 Hackpad 撰寫的筆記：
	* [20160601 [學習筆記] Hackpad - 線上筆記彙整(1)](/2016/05/31/hackpad-notes/)

---

# (三) 學習簡報
* 這裡整理著自己製作的學習簡報：

## 資料科學 Data Science 系列
1. [20160523 [資料科學系列] 資料探勘 Data Mining (1)](/collections/data-science/20160523-Data-Mining-1.html)
2. [20160530 [資料科學系列] 資料探勘 Data Mining (2)](/collections/data-science/20160530-Data-Mining-2.html)
3. [20160606 [資料科學系列] 資料探勘 Data Mining (3)](/collections/data-science/20160606-Data-Mining-3.html)
4. [20160617 [資料科學系列] 專題製作 - 視覺化電動機車運行能量地圖 (1)](/collections/data-science/20160617-MapProject-1.html)

---

# (四) 社團筆記
1. [20160802 那些年，關於大學的那些事...](/collections/clubs/20160802-about-university.html)
2. [20160803 社團創社經驗分享與交流](/collections/clubs/20160803-clubs-share.html)







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
