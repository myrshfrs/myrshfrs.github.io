---
layout: post
date: 2021-09-08
title: 使用feed43创建rss
author: zhangwf
---

想要通过RSS更方便地整合不同渠道的信息，但是有很多网站并不提供RSS订阅服务。我们可以通过[feed43](https://feed43.com)可以自己给喜欢的网站创建RSS订阅。类似feed43的服务挺多，对比下来我觉得feed43的可定制性比较强，免费版本更新间隔6小时，对于时效性要求不是特别高的我觉得足够了。甚至不用注册都可以创建RSS订阅，只要有人使用，RSS订阅就不会被删除，这一点还是非常良心的。feed43创建不了全文RSS，不过这并不是它的缺陷，其他类似的我看了看都不太行。所以现在我已经把获取全文的任务交给RSS阅读器或者浏览器了。

下面我介绍一下我用feed43给[cnBeta首页](https://www.cnbeta.com)创建RSS订阅的过程。

第一步，在网址栏中输入cnBeta首页的网址：https://www.cnbeta.com ，编码设置为utf-8，点击reload载入网页源码，同时建议在浏览器中打开网页作为对比。feed43免费版限制网页大小不超过100kB，所以只载入了一部分，不过影响不大。

![step1](/assets/img/creat-rss-with-feed43/feed43-1.PNG)

第二步，在源码中找到想要订阅的部分。可以直接CTRL+F搜索。比如我想订阅最新的新闻，第一条是“Cherry推出Stream Desktop Recharge可充电无线键鼠套装”，直接搜索“Cherry”，就可以在源码中找到这部分.然后需要找到这部分完整的源码，方法就是再找一两个同级的项目，比如下面两条新闻，在源码中看看这两条的源码都有哪些共同的，然后选一个复制粘贴到Item (repeatable) Search Pattern中。

![cnbeta](/assets/img/creat-rss-with-feed43/feed43-2.PNG)

![source](/assets/img/creat-rss-with-feed43/feed43-3.PNG)

这是我找到的一条新闻完整的源码。

```html
<div class="item" id="J_all_item_1176529">
<dl>
<dt><a href="https://www.cnbeta.com/articles/tech/1176529.htm" target="_blank">Cherry推出Stream Desktop Recharge可充电无线键鼠套装</a></dt>
<dd><p><strong>Cherry Americas 今日推出了全新的 Stream Desktop Recharge 无线键鼠套装，可知其采用了镍氢充电电池，续航可达数月，非常适合家用 / 办公环境。</strong>作为全球外设市场的领导者之一，新品不仅具有 Cherry 一以贯之的卓越品质和耐用性，还采用了符合人体工程学的舒适设计，可让用户在日常工作中获得轻松的打字与鼠标移动体验。</p></dd>
<a href="https://www.cnbeta.com/articles/tech/1176529.htm" target="_blank"><img class="lazy" src="https://static.cnbetacdn.com/thumb/mini/article/2021/0908/a984323feb2f3fc.png"></a>
</dl>
<div class="meta-data">
<label class="labels tech"><a href="https://www.cnbeta.com/category/tech" target="_blank">科技</a></label> <ul class="status">
<li>发布于2021-09-08 16:08&nbsp;&nbsp;|&nbsp;&nbsp;14次阅读&nbsp;&nbsp;|&nbsp;&nbsp;0个意见</li>
<li class="b"><a href="https://www.cnbeta.com/articles/tech/1176529.htm" target="_blank">详细内容</a></li>
</ul>
<div class="tks"><a target="_blank" href="http://m.cnbeta.com/"><b>cnBeta.COM 移动版</b></a></div> </div>
<div class="cnbeta-update-list-article">
<ul><li><a href="https://www.cnbeta.com/articles/tech/1175553.htm" title="台铁宣布其电子客票开始支持Apple Pay支付" target="_blank">台铁宣布其电子客票开始支持Apple Pay支付</a></li><li><a href="https://www.cnbeta.com/articles/tech/1175551.htm" title="为吸引三星电子建170亿美元芯片厂 美得州泰勒市拟大规模减免财产税" target="_blank">为吸引三星电子建170亿美元芯片厂 美得州泰勒市拟大规模减免财产税</a></li><li><a href="https://hot.cnbeta.com/articles/game/1175549.htm" title="游戏工委：已有63家单位响应防止未成年人沉迷通知" target="_blank">游戏工委：已有63家单位响应防止未成年人沉迷通知</a></li></ul> </div>
</div>
```
这是我把上面粘贴到Item (repeatable) Search Pattern中编辑出来的Search Pattern。`%`代表我需要的内容，`*`代表忽略的内容。注意每行后面必须都要加上`*`。

![pattern code](/assets/img/creat-rss-with-feed43/feed43-10.PNG)

![pattern](/assets/img/creat-rss-with-feed43/feed43-4.PNG)

编辑好Item (repeatable) Search Pattern之后，点击Extract。如果Pattern没问题的话，会提示“OK (XX items found)”，并且在下面能看到每条item的内容。如果没找到或者报错的话就再调整Pattern。下面是extracted出来的内容。每个item有10个参数（上面我们用`%`选择的内容）。

![extracted](/assets/img/creat-rss-with-feed43/feed43-5.PNG)

第三步，利用上一步中extract出来的参数，调整输出的格式。格式可以根据自己需要自己调整，下面是我弄的格式。

RSS feed properties：

![RSS feed properties](/assets/img/creat-rss-with-feed43/feed43-6.PNG)

RSS item properties：

![RSS item properties](/assets/img/creat-rss-with-feed43/feed43-7.PNG)

RSS feed输出效果：

![RSS feed output](/assets/img/creat-rss-with-feed43/feed43-8.PNG)

RSS item输出效果，由于是免费版，输出带有小尾巴：

![RSS item output](/assets/img/creat-rss-with-feed43/feed43-9.PNG)

下面是我用feed43生成的cnBeta RSS订阅链接，欢迎尝试：

[https://feed43.com/7823804171081114.xml](https://feed43.com/7823804171081114.xml)

feed43使用比较简单，但是功能很强大，目前我自己的很多订阅源都是通过feed43生成的，配合inoreader食用感觉很不错，推荐大家试试。


[back](./)
