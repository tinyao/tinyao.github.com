title: "Android图片旋转与仪表盘"
date: 2012-07-08 14:10
---

最近跟同学在做一个英飞凌的比赛，简单的说就是移动终端控制灯组，完成多项控制与状态查询，中间采用了Dali协议完成指令操作。同时，将硬件反馈的信息显示在手机屏幕上。为了让显示效果更为逼真，这里使用到了仪表盘，仪表盘的关键是指针的旋转动画。

<!-- more --> 

初步的显示效果入下图，四个SeekBar来控制表盘指针的偏转，每个seekBar最大占比25%

![](http://i.imgur.com/J9eD1.png)

贴代码：

    int sum_angle = (seek1.getProgress()
         + seek2.getProgress()
         + seek3.getProgress()
         + seek4.getProgress()) / 4;
    
     // 动画设定(指定旋转动画) (startAngle, endAngle, rotateX, rotateY) 88 17
     am = new RotateAnimation(200*(sum_angle-0.25f)/100,
         200*sum_angle/100,
         dip2px(DemoActivity.this, 53), dip2px(DemoActivity.this, 11));
     // 保持最后的状态
     am.setFillAfter(true);
    
     pointer.startAnimation(am);
    
     per1.setText("" + sum_angle/100);
     per2.setText("" + sum_angle%100/10);
     per3.setText("" + sum_angle%100%10);
