
# UI 相关

### 1、CALayer 和 UIView 的区别

> 1. UIView 和 CALayer 都是在 UI 操作的对象。两者都是 NSObject 的子类，发生在 UIView 上的操作本质上也发生在对应的 CALayer 上。
> 2. UIView 是 CALayer 用于交互的对象。UIView 是 UIResponder 的子类，其中提供了很多CALayer 所没有的交互上的接口，主要负责处理用户触发的各种操作。
> 3. CALayer 在图像和动画渲染上性能更好，因为 UIView 有冗余的交互接口，而且相比 CALayer，还有层级之分，CALayer 无需处理交互时进行渲染，可以节省大量时间。

### 2、frame 和 bounds 有什么不同

> frame：该 view 在父 view 坐标系中的位置和大小
>
> bounds：该 view 在本身坐标系中的位置和大小

### 3、layoutIfNeeded 和 setNeedsLayout 的区别

> * layoutIfNeeded: 如果有需要刷新的标记，立即调用 layoutSubViews 进行布局
> * setNeedsLayout: 标记为需要重新布局，异步调用 layoutIfNeeded 刷新布局，在下一轮 runloop 刷新

### 4、说明并比较关键词： Safe Area、SafeAreaLayoutGuide 和 SafeAreaInsets

> * Safe Area: 指 App 合理显示内容的区域，不包括 status bar、navigation bar、tab bar 和 tool bar，在 iPhone X 中，一般是指扣除了顶部 status bar 和 底部 home indicator 的区域。
> * SafeAreaLayoutGuide: 指 Safe Area 的区域范围和限制，在布局设置中，可以分别取得它的上下左右四个边界的位置进行相应的布局处理。
> * SafeAreaInset: 限定了 Safe Area 区域与整个屏幕之间的布局关系，一般用上下左右 4 个值来获取 SafeArea 与屏幕边缘之间的距离。

### 5、didMoveToSuperView、layoutSubviews、drawRect 都在什么时候调用？实际编码中用来做什么？

> didMoveToSuperView：当继承自 UIView 的子视图被贴到父视图时调用，可以在此方法中设置子视图自身的属性；
>
> 
>
> layoutSubviews：当添加到父视图或自己添加子视图时调用，当修改自己的 frame 或子视图的 frame 时也会调用，当自己调用 setNeedsLayout 方法时也会调用，父视图 UIScrollView 滚动时或者屏幕旋转时也会调用；可以在此方法中对子视图进行重新布局；
>
> 
>
> drawRect：贴到父视图时调用，设置它的 contentMode 属性值为 UIViewContentModeRedraw 时，每次更改 frame 时调用，直接调用 setNeedsDisplay 或 setNeedsDisplayRect 时也会调用；可以在此方法中绘制自己的内容。

### 6、说一下 UIScrollView 实现原理

> 在滚动过程中，其实是在修改圆点坐标。当手指触摸后，scrollView 会暂时拦截触摸事件，使用一个计时器，假如计时器到点后没有发生手指移动事件，那么 scrolView 发送 tracking events 到被点击的 subview。假如计时器到点前发生了移动事件，那么 scrollview 取消 tracking，自己发生滚动。

### 7、UIScrollView 及其子类中 contentView、contentInset、contentSize 和 contentOffset 说明

> * contentView：显示内容的区域，一般情况下，用户对 UIScrollView 的操作都是在这 contentView 中进行的。
>
> * contentInset：指 contentView 与 UIScrollVIew 的边界。分别指 contentView 的四条边到 UIScrollView 的对应边的距离。
> * contentSize：指contentView 的大小，是整个 UIScrollView 的实际内容大小。
> * contentOffSet：当前 contentView 浏览位置左上角的点的坐标。是相对于整个 UIScrollView 左上角为原点而言，默认是 CGPointZero。

### 8、描述一下事件的响应者链

> 当前触发事件 - 根视图上的子视图 - 视图控制器上的根视图 - viewController - window - UIApplication - AppDelegate

> iOS 中只有继承 UIResponse 的对象才能接受处理事件，常见的子类：UIView、UIViewController、UIApplication、UIApplocationDelegate。

> **UIView 不接受事件处理的情况**
>
> 1. hidden = YES； 隐藏视图
> 2. userInteractionEnable = NO；禁止用户操作视图
> 3. alpha < 0.01； 透明视图

### 9、CoreAnimation 和 CoreGraphic 的区别

> * CoreAnimation：核心动画，可以实现效果非常好的动画效果
>   * 支持 iOS、macOS
>   * 在后台执行动画，不会阻塞主线程
>   * 作用在 CALayer
>
> CoreGraphic：核心图形库，常用的 point、size、rect 等都定义在这个框架中，类名以 CG 开头饿都属于 CoreGraphic，提供的是 C 语言函数接口，支持 iOS、macOS。

> CoreAcimation 和 CoreGraphics 的关系：都是跨 iOS、macOS 平台，CoreAnimation 中大量使用到 CoreGraphics 中的类。

### 10、UIView 动画与核心动画的区别

> 核心动画只作用在 layer
>
> 核心动画不会去修改 view 的真实位置

> 什么时候使用 UIView 动画
>
> 如果需要与用户交互就使用 UIView 动画

> 什么时候使用核心动画
>
> 1. 不需要与用户交互
> 2. 需要根据路径做动画时，使用核心动画
> 3. 做转场动画时需要使用核心动画

### 11、如何兼容不同的屏幕

> 1. 在代码中写一个宏，判断当前设备的屏幕尺寸，根据不同的尺寸，动态调整图片及视图的尺寸；
> 2. 使用 main.storyboard 中的 size class 属性对不同的屏幕尺寸进行开分类处理
> 3. 使用自动布局（masonry）

### 12、添加约束时，如果产生了警告和冲突，如何解决

> 1. 非运行的约束冲突，去掉冲突中的一个约束；
> 2. 运行时的约束冲突：通过阅读日志，在运行时动态修改约束冲突

### 13、 xib 与 storyboard 的区别

> xib 是轻量级，用来描述局部的 UI 界面，一个工程中可以有多个 xib 文件，主要用于视图，一个 xib 可以在不同的视图控制器中使用。
>
> storyboard 是重量级，用来描述整个软件的多个界面，并且能展示多个界面之间的跳转关系，主要用于视图控制器。多个 storyboard 之间可以关联使用。

### 14、如何实现视图变形

> 修改 view 的 transform 属性

### 15、 高性能的给 UIImageView 增加圆角

> 使用图形上下文绘制

```objc
- (UIImage *)circleImage {
    // NO代表透明
    UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0);
    // 获得上下文
    CGContextRef ctx = UIGraphicsGetCurrentContext();
    // 添加一个圆
    CGRect rect = CGRectMake(0, 0, self.size.width, self.size.height);
    CGContextAddEllipseInRect(ctx, rect);
    // 裁剪
    CGContextClip(ctx);
    // 将图片画上去
    [self drawInRect:rect];
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    // 关闭上下文
    UIGraphicsEndImageContext();
    return image;
}
```

### 16、transform 改变的是 frame 还是 bounds

> transform 的改变，会影响 frame，bounds 不会受到影响
>
> transform 时一个矩阵，使用矩阵乘法，对 view 的frame 进行变换