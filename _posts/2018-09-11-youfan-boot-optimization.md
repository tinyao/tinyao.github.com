---
title: 有饭的启动速度优化
date: 2018-09-11 20:30
tag: 有饭,技术,Android
excerpt: 给有饭做次小手术，提升 50% 的启动速度
cover: https://zico.oss-cn-beijing.aliyuncs.com/blog/5_viewpager_onlayout.png
---


最近在看 Android 性能优化的相关材料，这方面的最佳资源莫过于 [官方文档](https://developer.android.com/topic/performance/) 和 [Youtube 课程](https://developer.android.com/topic/performance/)了。提到这个系列视频，16 年的时候也跟过，但一直没怎么实践过。

这两天工作稍有空，就拿半年未更新的「有饭」来做个试验。先从观感上看下优化前后的对比 (放慢一倍速度)。

<video id="video" class="wrap" controls="" controlsList="nodownload" preload="none" poster="https://zico.oss-cn-beijing.aliyuncs.com/blog/youfan-opt-cover.png" type="video/mp4" style="display: block;width: 100%;"><source src="https://zico.oss-cn-beijing.aliyuncs.com/blog/youfan-opt.mp4"></video>

以 ActivityManager 统计的第一个 Activity Displayed 时间为标准。在我服役多年的 OnePlus 3T 上，优化前在 820ms 左右，优化后在 468ms 左右，加速 40%；

## 应用的启动过程

应用启动慢，常见的几个问题有饭肯定也存在，比如：Application、Activity 的 onCreate 耗时过长、启动瞬间线程跑太多、Activity 的布局过复杂。不过，这只是定性的认识，这次我希望定量的去发现与解决问题。

首先来回顾下应用冷启动的过程（这里先不谈 framework 层细节）：

![-w922](https://zico.oss-cn-beijing.aliyuncs.com/blog/0_app_startup.jpg)

1. 加载并启动 App
2. 系统立即展示出一个空白窗口
3. 创建 App 进程

系统创建完 App 进程后，进入下一步：

1. 创建 App 实例
2. 启动 Main Thread
3. 创建 Main Activity 对象
4. 加载、布局、首次绘制视图

首次渲染完成后，系统进程就用 Main Activity 替换前面的空白窗口，启动冷过程完毕。

而热启动(Hot start)就是系统将 Activity 带回到前台显示。如果 Activity 还存在于内容中，即避免了重复初始化和布局渲染。而如果已经被回收内存，则 Acitivity 被再次创建。与冷启动相同的，都经历了空白窗口。

温启动(Warm Start)则存在很多种情况，比如：用户退出应用，然后立即再启动，进程还在，但需要完全重建 Activity 并执行 onCreate()；或者系统从内存中清掉了应用，然后用户启动应用，这时候进程和activity需要重新开始，但可以从保存状态里恢复一些信息。

因此，要优化应用的启动速度，Google 官方给出了几个方向：

> 1. 为 MainActivity 的主题设置 windowBackground，以快速显示给用户
> 2. 避免繁重的初始化
> 3. 定位耗时问题：IO 问题、反序列化、布局复杂等。

当然 1 只属于假象，但总比的白屏要强。2、3 是重点要揪出来优化的。

## 启动加速 - 启动图

这一步很简单，为 Main Activity 设置一个带 windowBackground 的主题，然后在 onCreate 里把正确的主题设置回去即可。这里有两点建议的：

1. 图片可以选择首页空数据状态下的截图。这样可以“欺骗”用户，应用已经进入到了首页，只是在加载数据。
2. 全屏的尺寸较大，在无特殊需要的情况下，可以将大尺寸转用 xml 实现。这样在 xml 里通过 layer-list 可以引用小图片(矢量图或位图)。直接用全屏图片还有一个问题就是，在不同长宽比的设备上会被拉伸变形。

## 启动加速 - 发现定位问题

有饭因为业务比较简单，并没有太多初始化工作。前面提到我们要定量分析，也就是说这些不多的初始化，究竟谁费时最多最少。

我们先用 Android Studio 的 Profiler 来抓取一下有饭的启动过程。

![1. 启动过程](https://zico.oss-cn-beijing.aliyuncs.com/blog/1_youfan_startup.png)

从图中主线程的执行情况，我们可以看到几个耗时大户，下面我们逐个分析：

### Application onCreate

![2. application oncreate](https://zico.oss-cn-beijing.aliyuncs.com/blog/2_application%20oncreate.png)

可以看出 Applicaitioin 的 onCreate 存在几个比较耗时的任务：

1. Hawk 初始化 (一个第三方 KV 存储库)
2. Fabric (Twitter 的统计分析服务)
3. createAppShortcuts 方法：因为其中涉及了加载当前用户`磁盘 IO 操作`，所以耗时。

对于 1，因为有后续 app 强依赖，暂时没有做改造。2 和 3 可以丢掉异步线程上去，比如 IntentService、AsyncTask、RxJava、线程池 等，如果自开线程，注意设置线程优先级，否则同主线程级别的话，还是会竞争资源。另外不要开过多的线程，可以用线程池，或者多个任务共用一个线程。

### Activity onCreate

![3. activity_oncreate](https://zico.oss-cn-beijing.aliyuncs.com/blog/3_activity_oncreate.png)

从图中可以看到，Activity 的 onCreate 过程的耗时主要是：

1. Okhttp 的实例创建：由于 Dagger 注入，使得一连串的实例初始化集中到了这一步，其中源头 okhttp 耗时最多；
2. setMiuiLightStatusBar：为了兼容 MIUI 和 Flyme 的状态栏颜色设置，但其中太多的反射调用拖慢了速度。
3. setupPager：首页 ViewPager 的配置设置

问题 1，可以将 dagger 注入延迟到网络请求调用时，让页面先出来
问题 2，预先判断 MIUI/Flyme 系统，避免在其他机型上执行
问题 3，暂时没做优化

### 布局 onMeasure 和 onLayout 过程

![4. viewpager_onmeasure](https://zico.oss-cn-beijing.aliyuncs.com/blog/4_viewpager_onmeasure.png)

因为 ViewPager 设置了 offsetlimit 为 2，所以一次性要 measure 三个 Fragment 的布局，并且详细查看后第三个 Fragment 还格外耗时。

解决的办法就是 ViewPager 的布局懒加载。ViewPager 同时加载三个 Fragment，但实际不需要三个的页面的布局都完整测算渲染出来。对此，可以用 ViewStub 将 Fragment 的主体布局用 ViewStub 收纳起来。等到 ViewPager 滑动到该页时，再 inflate 出完整的 view，然后加载数据。

![5. viewpager_onlayout](https://zico.oss-cn-beijing.aliyuncs.com/blog/5_viewpager_onlayout.png)

onLayout 的过程则可以看出 Html.fromHtml 函数占掉了近一般的耗时。 这个函数用于在将 N 条 html 文本显示在列表的 TextView 上。此前只大概了解这个方法的耗时，没想到这么严重。

我的解决方案是：直接把 fromHtml 的处理前置到 API 层，及网络请求到消息列表(StatusList)后，就立即处理 html 文本。这样后续的 memory cache 和 disk cache 就存储了处理后的文本，视图显示就无需关心了。

### 首页数据加载

首页 Feeds 流的加载逻辑是：先 db 后网络的策略。网络取回数据后，会做 memory cache 和 db cache。

所以只要本地有缓存，其加载出的速度有没问题。db 存储用的是 room 方案，并且结合前述的 feed 文本的 fromHtml 操作前置到了网络请求阶段，所以本地 cache 直接读取后就能用于显示。

可以优化的点在于：

1. 网络请求后，Gson 反序列化相对耗时。对比一番后，决定用 moshi 替换 gson
2. 网络取回数据后，db cache 能否异步掉。

## 验收测试

好了，验收效果的时间到了。首先，我们以系统定义的 Main Activity Displayed 时间为标准，对优化前后的 Release 包各自测了 40 组数据。

```
// 优化前
09-10 11:21:01.534  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +874ms
09-10 11:21:03.949  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +952ms
09-10 11:21:06.095  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +747ms
09-10 11:21:08.610  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +858ms
09-10 11:21:10.979  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +1s49ms
09-10 11:21:13.349  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +894ms
09-10 11:21:15.659  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +970ms
09-10 11:21:18.438  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +794ms
09-10 11:21:21.798  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +770ms

// 优化后
09-10 11:17:10.605  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +431ms
09-10 11:17:13.862  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +540ms
09-10 11:17:16.352  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +413ms
09-10 11:17:19.088  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +428ms
09-10 11:17:21.566  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +480ms
09-10 11:17:24.126  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +423ms
09-10 11:17:26.553  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +431ms
09-10 11:17:28.877  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +414ms
09-10 11:17:31.502  1288  1510 I ActivityManager: Displayed im.zico.fancy/.biz.MainActivity: +427ms
```

再测算下用户感知的「启动时间」(即首页加载显示数据完成)。即在 Main Activity 完成 measure 和 layout 后主动 reportFullyDrawn() 获得时间。

```
// 优化前
09-10 12:37:44.102  1311  2879 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +1025ms
09-10 12:37:47.523  1311  9499 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +1012ms
09-10 12:37:49.329  1311  1777 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +934ms
09-10 12:37:52.924  1311  1777 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +954ms
09-10 12:37:56.342  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +979ms
09-10 12:37:59.642  1311  2122 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +943ms
09-10 12:38:04.454  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +992ms
09-10 12:38:07.705  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +926ms
09-10 12:38:10.253  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +969ms

// 优化后
09-10 12:34:54.057  1311  2879 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +785ms
09-10 12:34:56.492  1311  9499 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +700ms
09-10 12:34:58.263  1311  1777 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +674ms
09-10 12:35:00.829  1311  1777 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +684ms
09-10 12:35:02.724  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +676ms
09-10 12:35:04.564  1311  2122 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +733ms
09-10 12:35:06.304  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +685ms
09-10 12:35:07.887  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +683ms
09-10 12:35:09.533  1311  1334 I ActivityManager: Fully drawn im.zico.fancy/.biz.MainActivity: +687ms
```

均值比较


|   | 优化前 | 优化后 | 加速比 |
| --- | --- | --- | --- |
| Displayed Time | 864 | 438 | 49% |
| Full Draw | 970 | 700 | 27%  |

## 总结

总结下，启动优化过程中最重要的就是初始化任务的管理。尤其是对于企业应用来说，众多业务处于自己的产品或技术需求，都会在启动时初始化，那问题的复杂性就来了。除了从技术角度，捋出来众多初始化的依赖关系，统一初始化逻辑。另一方面，需要大家站在全局统筹的角度安排好优先级，以此来达到比较好的结果。

如今太多国产应用，已经把 Splash screen 给用烂了，从最早先为了繁重初始化迫不得已等待一两秒，到现在为了品牌或广告的诉求，不顾需求的必要性，一股脑都开始给 Splash screen 加上了倒计时。甚至连暖启动也得闪这么一下。

对于大多数产品，「**来了就用，用完就走**」才该是最好的体验。