---
title: Cocos2d iPhone 边缘触屏延迟现象
date: 2018-06-06 23:39:35
tags: [cocos2d, 想就做]
summary_img: /images/summary_image/touch_screen.jpg

---

Cocos2d iPhone 边缘触屏延迟现象
发现并解决问题全历程

<!-- more -->

<br/>

---

初衷:
这是在工作中遇到BUG，然后解决BUG的历程。
希望我的思路及方向能对你有所启发。

---

<br/>

# 现象
游戏在真机测试出现某些区域不响应的问题。

<br/>

# 查原因

## 找反馈者沟通
去了解具体发生的问题及有无任何规律等。
了解到只在**iPhone手机**上才出现。

## 自己测试

- 黑盒
方法：根据现象，进一步测试，打不同手机包等
经过各种测试，总结出的特征如下：
    - 只有iPhone手机有问题（测试过几款Android手机 及 模拟器）
    - 只有边缘触摸有问题（将触摸区域移动过不同的位置）
    - 只有iPhone 6s以上手机有问题
- 白盒
方法：打印触屏信息等。
发现 开始触摸时并没有打印，但是在结束触摸时，开始触摸喝结束触摸同时相应。
进一步打印，发现**触摸开始和结束在同一帧执行**。
- 总结
其实，在只有 iPhone 6s 以上手机有问题就比较好确定了。
经过调查发现 6s开始支持了 3D touch。

<br/>

# 解决
既然发现3D touch有关，那就关闭3D touch看看效果。
关闭以后发现，左右两边没有问题了，但是底边还是有问题。
又了解到苹果按住边缘滑动，会滑出任务管理器，这个好像没办法关掉...

然后，就去cocos2d的github的issues看有没有遇到同样的问题的。
发现也有人遇到了同样的问题，已经解决好了。 [ https://github.com/cocos2d/cocos2d-x/issues/18479 ]
解决方法就是将手势识别的触摸开始的延迟关掉。

在 AppController.mm 文件内，
        
```objectivec
        [window makeKeyAndVisible]; 
```

之后加入:

```objectivec
        for(UIGestureRecognizer* gesture in window.gestureRecognizers)
        {
            gesture.delaysTouchesBegan=NO;
        }
```

完美解决问题。
真是查问题5小时，解决问题5秒钟。


<br/>

# 扩展
一般到这里，解决完问题就结束了。
BUG是无穷尽的，但是它们是有共性的。
我们遇到一个问题，通过分析它能解决一类问题，这才是进步。

UIGestureRecognizer 是手势识别器的基类。
它用于识别触摸序列（或其他输入）的逻辑进行解耦，并对该识别进行操作，负责发送操作消息或转发触摸消息等。
它主要有以下的手势：

- UITapGestureRecognizer            [轻拍手势]
- UIPinchGestureRecognizer          [捏合(缩放)手势]
- UIRotationGestureRecognizer       [旋转手势]
- UISwipeGestureRecognizer          [轻扫手势]
- UIPanGestureRecognizer            [平移手势]
- UIScreenEdgePanGestureRecognizer  [屏幕边缘平移手势]
- UILongPressGestureRecognizer      [长按手势]

如果窗口绑定了手势识别器，触摸事件会先经过手势识别器处理，再传递给视图。
如果手势识别器识别了触摸，则传给视图的其余触摸事件将会被取消。
它有三个主要的属性:

- cancelsTouchesInView
如果手势识别器识别出手势，则从视图中解除该手势的其余触摸，从而使窗口不再传递它们。
- delaysTouchesBegan
只要手势识别器分析触摸事件没有失败，窗口就会将UITouchPhaseBegan阶段中的触摸对象传递给视图。如果随后识别手势，则视图不接收这些触摸对象。
- delaysTouchesEnded
只要手势识别器分析触摸事件没有失败，窗口就会将UITouchPhaseEnded阶段中的触摸对象传递给视图。如果随后识别手势，则触摸被取消。

更详细的可以看 参考资料3

<br/>

# 总结
遇到问题 -> 分析&总结特征 -> 解决问题 -> 扩展问题
针对同一个问题，学到的比其他人更多更广，长期以来，必将更进一步。



<br/>
<br/>
<br/>

---

参考资料:

- stackoverflow - TouchesBegan delay on left hand side of the display
[ https://stackoverflow.com/questions/39998489/touchesbegan-delay-on-left-hand-side-of-the-display ]
- stackoverflow - Swift SpriteKit 3D Touch and touches moved
 [ https://stackoverflow.com/questions/36060423/swift-spritekit-3d-touch-and-touches-moved ]
- 苹果开发者 UIGestureRecognizer [ https://developer.apple.com/documentation/uikit/uigesturerecognizer?changes=_4&language=objc ]