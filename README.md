# Android Animator Guide

先记录下一些动画时候注意的点 到时候在整理

###几个基本原则
- 在做动画的时候不要尝试调用会导致view执行onMeasure onLayout的方法。 基本上来讲，只要执行了这两个方法，你的动画一定会卡，别跟我说什么眨眼补帧，调用到了这两个方法，一般都会牵连到parent view 如果你是viewGroup，则还会牵连到child View，如此操作，动画是到不了60帧的，用户体验上就会有掉帧的现象。 如果真的是涉及到了大小的变换，可以尝试使用view group里包只包含一个子view 通过设置padding的方式来改变大小，这样影响的view较少（该方法实际性能有待考证)
- 尽可能的使用view的平移、缩放、旋转方法 这些方法都只是修改的view的Matrix，系统开销较少，可以保证较高的流畅性
- 对于一些需要形变的view，由于不推荐使用cardview的设置round的方式，所以暂时也没想出来什么好的办法，目前能想到的是使用自定义view，自定义onDraw方法 实现形变
- 相同的元素之间使用动画过度，这里如果是跨界面的跳转 可以使用`ActivityOptions.makeSceneTransitionAnimation`

  其实这里不推荐大家使用形变这种动画方式，在分析动画的时候参考了目前公认动画效果最好的ios11，发现ios11的动画基本上是由缩放，平移 加上 Spring Physics 完成，只不过动画的bounce设置的比较高，给予了用户不错的回馈效果。 

  ios中由于系统实现了非常高效的高斯模糊引擎，所以在ios11的动画中也大量使用了高斯模糊，甚至有用高斯模糊来区分view的主次关系的用法。 在android2.3之前，android也提供有系统级的高斯模糊引擎，不过后来放弃了，MD现在更推荐用户使用setElevation的方式来来体现用户界面的层级关系

#### 使用SpringAnimation
android在com.android.support:support-dynamic-animation中提供了SpringAnimation方法，大大简化了动画的开发流程，以前使用ObjectAnimator实现Spring Physics的动画需要自定义差值器，而且google也没有提供Spring Physics的差值器，导致大家原来只是使用一些先行动画，在用户的观感上总是有那么一丝丝的变扭，而且开发也相对繁琐。

#### 关于侧滑返回
如果想要实现测滑返回这类的效果 可以使用SwipeBack这类的库 https://github.com/liuguangqiang/SwipeBack  不过这种库也有些小问题，貌似会导致onStop调用异常等问题。 ios中还有很多的页面切换，其实应用堆叠fragemt切换来做动画或许也能做到不错的效果（有待尝试）
