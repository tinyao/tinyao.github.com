---
title: Material Design in Action - Wing for Twitter
updated: 2015-09-06 15:56
excerpt: 使用 Material Design 设计一个 Twitter 客户端。
cover: https://zico.oss-cn-beijing.aliyuncs.com/blog/posts/2017-03-26-wing-cover.png
---

![wing-cover]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-cover.png)

说来惭愧，本来打算连带设计加开发的，可是到现在设计迭代了几个版本。开发远远没跟上呐，我承认我是个不合格的开发。好吧，说回正题，这篇文章就只谈谈以  Twitter 为案例的一次 Material Design in Action。

### 导航结构

首先来梳理下，有哪些主要功能是需要导航的：“首页时间轴”、“提及”、“私信”、“个人主页”、“搜索”、“话题”、“列表”、“收藏”、“草稿”、“设置”、“账号切换”，可以看到最初罗列的这些功能点很多。于是第一次设计我就使用了 [Navigation Drawer](https://www.google.com/design/spec/patterns/navigation-drawer.html#) 来容纳这些页面的导航。但又不想 Drawer 里列表过长，就将时间轴、提及、私信并列放在了首页，以 Toolbar 上三个 Tab 形式呈现。

![wing-home-with-drawer]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-home-with-drawer.png)

Drawer 则包含了 “首页”、“收藏”、“列表”、“话题”、“搜索”、“草稿”、“设置”，以及顶部的“个人主页”入口和“账号切换”。

后来由于，个人情感上开始排斥 Navigation Drawer，觉得 Drawer 应用过于泛滥，而且实际使用单手划出 Drawer 还常有失败的时候。另外一方面，对于手机带壳的用户来说，壳很可能使得边缘略高于屏幕，这样划出 Drawer 就更加困难了。所以最终还是决定放弃官方 Drawer 的方案，中间还尝试过 [SlidingMenu](https://github.com/jfeinstein10/SlidingMenu) 的方案，也就是滑动手势不用从屏幕最左边开始划，在屏幕中间右划即可。但因为本质上还是 Drawer，以及 Google 官方不推荐这么来了，也就放弃了。

最终导航方式是，保持首页三个 Tab，分别是 “Home Timeline”、“Notification”、“Discover”, HomeTimeline 就是时间轴，Notification 包含了提及和私信，Discover 则将搜索、话题、用户列表放在了一起。Tab 并不是标准的 [Guideline](https://www.google.com/design/spec/components/tabs.html#tabs-usage) 做法，这里参考了 [Fuubo](http://www.wandoujia.com/apps/me.imid.fuubo)、[网易云音乐](http://www.wandoujia.com/apps/com.netease.cloudmusic)、[Capitaine Train](https://play.google.com/store/apps/details?id=com.capitainetrain.android)等的导航设计。

![wing-home-1]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-home-1.png)

然后 Toolbar 左侧，采用了跟 [Google+](https://play.google.com/store/apps/details?id=com.google.android.apps.plus) 类似的设计方案，用于显示当前用户以及切换用户。展开 Spinner，会显示多账号和“夜间模式”开关、“设置”。

写 Tweet 则是使用了 [Fab](https://www.google.com/design/spec/components/buttons-floating-action-button.html)，点击即跳转到撰写页面。

### Home Timeline

![wing-home-with-drawer]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-home-with-drawer.png)

时间轴的设计，最初也是使用 [卡片](https://www.google.com/design/spec/components/cards.html#cards-usage) 的，但由于一条 Tweet 的长度一般比较小，尤其是英文情况下（140个英文字符比140个汉字短好多的），这就使得一个屏幕上可能显示三个卡片，卡片的阴影和间隔就会显得很多，一定程度上会影响阅读注意力。所以 Card 可能适合微博，适合 Google Plus，但 Twitter 并怎么合适。最终放弃分离的 Card，使用没有阴影的平面。

其实按照 Material Design 的逻辑来看，这也算是 Card 的了，只是因为每个 Card 是紧挨着的，所以不存在 Card 与更底部背景的层次关系，也就没有了阴影。而当从时间轴到 Tweet 详情时，阴影就出来了，因为每一条 Tweet 实质上还是一张卡片，在详情页面，Tweet 跟底部的灰色背景不是同一个平面，有层级关系，就会有阴影。

然后，从 Timeline 到 Detail 转场动画，运用类似 Shared Element Transition，这个动画灵感应该是来源于 Keynote 的 Magic Move。之前的设计版本实现了这个效果，可以看 [这里]({{ site.cdn_url }}/blog/posts/assets/video/wingtwitter.mp4)。

长按 Tweet 可以唤出操作的Bar，回复、转推、收藏、更多。（这里有在考虑是否取消长按功能，将操作栏一直显示在每条 Tweet 下方，毕竟觉得长按是一个比较吃力的动作）

### Notification Center

![wing-home-notification]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-home-notification.png)

这里设计将“提及”和“私信”放在了一个 Tab 下面，主要是因为需要导航的东西太多，只能充分去把一些信息归类。后面的 Discover 也是相同的。消息中心里，“提及”和“私信”也是以类 Tab 形式呈现，只是这里 Tab 不可滑动。要查看私信，则点击“Message”。当然这里 Tab 也可以做成可滑动的，这样相当于隐式地整个是有了 4 个 Tab。考虑到私信的频次可能相对较低，4个 Tab 时滑动到最右边的 Discover 较远，遂将消息中心里“提及”和“私信” tab 设为了 Fix，只能通过点击切换。这也是为什么将切换 Tab 放在底部的原因。

为了突出重点，这里将最近的消息单与长时间以前的消息做了区分，定义了两个 Section：“Recent”、“Days Ago”。

### Discover

![wing-discover]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-discover.png)

Discover 融合了搜索、热门话题、用户列表这三者。热门话题默认显示前三条，用户列表显示自己关注的。

### Profile Page

![wing-profile]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-profile.png)

个人主页依旧采用了较为常规的大 Header 设计，Header 背景来源于用户自己在 Twitter 上的设置。由于原图一般是长条的，而这里的 Header 是竖长条的，所以背景图片是显示不完整的，所以这里设计了背景图缓慢移动的效果。底部是滑动的 Tab 展示用户的 Tweets、Followings、Followers、Favs。

页面向上滚动时，背景图与前置视图会以视察滚动的形式向上移动，当到达 ToolBar + Tabbar 的高度时锁住 Toolbar和Tabbar。

### Compose Tweet

![wing-timeline-tweet]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-timeline-tweet.png)

没太多说的，看图即可 ==，哦，草稿箱放在了底部的第四个图标那里。

### Direct Message Conversation

![wing-conversation]({{ site.cdn_url }}/blog/posts/2017-03-26-wing-conversation.png)

---

嗯，主体页面就这么多。欢迎设计师、程序猿、推友前来拍砖 ~

好了，该滚去继续写代码了 ==

