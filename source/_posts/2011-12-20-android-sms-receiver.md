title: "在QQ通讯录、360之前拦截短信-Intent-Filter的优先权"
date: 2011-12-20
---

最近写一个应用（A），需要拦截短信分析。一般是这样实现的：注册一个接受短信Intent-Filter，获取短信广播，分析短信内容然后相应处理。对特定短信终止广播继续（abort方法），阻止其进入收件箱。大致就是这么一个过程。

<!-- more --> 

但上述方式，在QQ通讯录/360/飞信存在的情况下，拦截短信失败~也就是说它们抢先拿到了收短信的广播，并将其中断了。那么如何解决这个问题呢~

本来以为腾讯是拦截ril层的消息，然后阻止广播继续，但是这种方式要修改framework才能实现。

后来发现存在广播接收器（Receiver）的Intent-Filter的优先级（priority），SDK里说Prioruty的范围是-1000~1000，若设为一千以上好像跟1000的效果一样。然后我在A应用中的Manifest里将Receiver的Intent-Filter优先级设为1000，但测试结果还是被QQ通讯录抢先。

#### 进一步Google后，得到下面的结论：

反编译QQ通讯录/360手机卫士，发现些许奥秘。貌似这个涉及到Broadcast的分发机制，参考底层代码应该比较好解释~

广播分为2中，无序和有序。可以理解为散列和队列。

首先无序广播，不能中断，所有注册相应Intent-Filter的Reciver都可以接收到~

其次是有序广播，可以中断。它的消息是按优先级传送的，任何一个Receiver在接收后，可以使用abort将其停止，这样就导致了后续的Receiver不能收到广播。

下面是一个猜想，做了些测试，也基本符合~

> 假设广播接收器的优先级都设为最大整型2147483647，首先动态注册优先级最高，其次是静态注册。在动态注册中，最早注册的优先级最高。在静态注册中，最早安装的程序，优先级最高（注：安装apk会解析af.xml，把其加入队列）

在反编译360后，发现其静态注册的广播接收器里设置的优先级数值为2147483647，然后再广播中启动一个service，在service中注册一个优先级为2147483647的同样地广播接收器。也就是说，假设现在进程全杀，那么短信来了，360和QQ通讯录，谁先安装，谁的静态注册广播接收器就会先启动，然后把广播中断，而且它还启动了一个service又动态注册一个Receiver。这样，它的优先级就排在了所有静态接收器之前了。

#### 所以，我们做个试验，以同样的方式：

首先写一个应用，注册一个开机完成的Receiver

> <receiver android:name=”.MyBrocast” android:permission=”android.permission.BROADCAST_SMS”>
> 
> <intent-filter android:priority=”2147483647″>
> 
> <action android:name=”android.provider.Telephony.SMS_RECEIVED” />
> 
> </intent-filter>
> 
> <intent-filter android:priority=”2147483647″>
> 
> <action android:name=”android.intent.action.BOOT_COMPLETED” />
> 
> </intent-filter>
> 
> </receiver>

然后再这个Receiver接收到开机广播后，立即启动一个service

> public void onReceive(Context context, Intent intent) {
> 
> Log.v(“MyBrocast.onReceive”, “testtttttttttttt”);
> 
> if(intent.getAction().equals(Intent.ACTION\_BOOT\_COMPLETED)){
> 
> Intent service=new Intent(context, MyService.class);
> 
> context.startService(service);
> 
> }
> 
> }

然后在service中重新动态注册一个Receiver，优先级为2147483647

> IntentFilter localIntentFilter = new IntentFilter(“android.provider.Telephony.SMS_RECEIVED”);
> 
> localIntentFilter.setPriority(2147483647);
> 
> MyBrocast localMessageReceiver =new MyBrocast();
> 
> Log.v(“MyBrocast.onReceive”, “onCreate”);
> 
> Intent localIntent = registerReceiver(localMessageReceiver, localIntentFilter);

重启手机就OK了，抢先360、QQ拦截短信~因为360/QQ并没有在接受开机广播后，动态注册短信广播的接收器。这样重启后A应用就可以在它们之前拦截到短信了~

但上述抢先方式，付出的代价是，A应用需要一直有这么一个service后台运行。一旦被杀，优先权又回被360/QQ抢占，只有等到下次重启。除非A应用在QQ/360之前安装到手机上~

#### 总结一下：

具体的顺~ 代码动态注册的Intent-Filter高于manifest静态注册的Intent-Filter。动态注册中的Intent-Filter在相同优先级下（如整型的最大值），接受顺序是按照动态注册的时间顺序。静态注册中Intent-Filter在相同优先级下，接受顺序是apk的安装顺序。

参考帖子：[http://www.eoeandroid.com/forum.php?mod=viewthread&tid=148381<br>][1]

[][1]

 [1]: http://www.eoeandroid.com/forum.php?mod=viewthread&tid=148381
