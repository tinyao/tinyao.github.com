date: 2015-05-10 14:52
title: 'RxJava 备忘'
---

这一篇文章作为最近几个月接触 RxJava 的总结，自己在 Android 项目中，运用 RxJava 还不顺手，因为零星的概念，以及其可运用场景的多样性。目前还只是在 App 的一部分开发，主要是在数据层方面使用 Rx。

## 什么是 RxJava

> RxJava is a Java VM implementation of ReactiveX (Reactive Extensions): a library for composing asynchronous and event-based programs by using observable sequences.

简单来说，RxJava 就是 ReactiveX 的一个 Java 实现，这个类库可以基于观察序列进行异步和事件驱动的编程。

其核心包括了：Observable 和 Subscriber，即被观察者和订阅者。

那么为什么要 Rx ？有几个方面：

* 多线程并发的需要，而其必然带来复杂性；
* 异步编程的回调问题，常显得代码不简洁，可读性差
* 多个异步数据源
* 没有标准统一的错误/异常处理模型

所以需要引入一个统一的模型，用于并发、组织事件和错误异常处理。Rx 恰能做到这样。

## RxJava 的组成

在 RxJava 中有这么几个概念：

* Observable：做主体任务的
* Subscriber：接收 Observable 的数据，Subscriber可以取消订阅Observable，可以设置Observable使得只有被订阅时才生产
* Subscription：在 Observable 和 Subscriber 间起连接作用
* Schedulers：可以指定 Observable 或者 Subscriber 在特定的线程上执行
* Operator：用于操作 Observable 产生的数据。比如 map, flatmap, concatmap, filter, take, doOnNext
* Error Handling：将异常抛出，交由 onError() 处理

## RxJava 的使用场景

* 使用 Schedulers 多线程

Observable 在后台线程获取数据，然后经 Operator 处理，交给 Subscriber

* 缓冲调用(buffer)

当点击按钮一次，响应一次；快速点击五次，将积攒响应一次

* 实时搜索(subject + debounce)

在输入搜索关键字时，在输入停顿超过阈值时，触发搜索

* 配合 Retrofit (zip + flatmap)

将 Retrofit 的 API 返回转换成相应的 Observable

* 集合多个网络请求

将多个并行的网络请求结果Observable，组装zip成一个 Observable

* 自动更新 View (publish subject)

View 「检测」数据模型，并在其变化时，自动更新状态

* 使用 Schedulers 轮询

* RxBus，使用 RxJava 和 DebouncedBuffer 形成一个 EventBus

  * Implementing an event bus with RxJava
  * DebouncedBuffer used for the fancier variant of the demo
  * share/publish/refcount

* 表单验证 (combineLatest)

* 使用 concat 先后从 cache 和 network 取数据

* 使用 timer/delay/interval 进行简单定时操作

* Exponential backoff ？

* 旋转保持

在旋转屏幕时，保持数据任务的一致性


---

Reference

* [Learning RxJava for Android by example](https://github.com/kaushikgopal/RxJava-Android-Samples)
* [The Mayans Lost Guide to RxJava on Android](https://speakerdeck.com/android10/the-mayans-lost-guide-to-rxjava-on-android)
* [Reactive SoundCloud - Tackling complexity in large applications](https://speakerdeck.com/mttkay/reactive-soundcloud-tackling-complexity-in-large-applications)
* [DanLew's articles on RxJava](blog.danlew.net/2014/10/08/grokking-rxjava-part-1/)
* [Fragmented Podcast on RxJava](http://fragmentedpodcast.com/)



















