# RunLoop、Runtime、Method Swizzle、KVC、KVO

## Runloop

### 1、什么是 RunLoop

> 运行循环，内部就是 do-while 循环。在这个循环内部不断的处理各种任务。
>
> 一个线程对应一个 RunLoop，基本作用就是保持程序的持续运行，处理 app 中的各种事件。
>
> 节省 CPU 资源，提供程序性能。
>
> 
>
> RunLoop 通过 mode 来指定事件在运行循环中的优先级：
>
> * NSDefaultRunLoopMode：默认空闲状态
> * UITrackingRunLoopMode： scrollView 滑动时
> * UIInitializationRunLoopMode： 启动时
> * NSRunLoopCommonModes： mode 集合

### 2、RunLoop 滑动中定时器失效的原因

> 滑动时，主线程的 RunLoop 会转到 UITrackingRunLoopMode，这时 Timer 就不会运行。
>
> 有两种解决方案：
>
> 1. 设置 NSTimer 运行于 NSRunLoopCommonModes
> 2. 在另外一个线程执行和处理 Timer 事件，然后主线程更新 UI

## Runtime

### 1、什么是 Runtime

> Runtime 又叫运行时，是一套底层的 C 语言 API，其为 iOS 内部的核心之一。

### 2、Runtime 的实现机制

> 1. 系统首先找到消息的接收对象，然后通过对象的 isa 找到它的类；
> 2. 在它的类中查找 method_list ，是否有 selector 方法；
> 3. 没有则查找父类的 method_list ；
> 4. 找到对应的 method，执行它的 IMP；
> 5. 转发 IMP 的 return 值。

### 3、Runtime 的应用

> 1. 给分类增加属性；
> 2. 方法的添加和替换，KVO的实现；
> 3. 消息转发（热更新）；
> 4. 实现 NSCoding 的自动归档和自动解档；
> 5. 实现字典和模型的自动转换。

## Method Swizzle

### 1、什么是 Method Swizzle ？什么情况下会使用？

> 1. 在没有一个类的实现源码情况下，想改变其中一个方法的实现，除了继承它重写和借助类别重名之外，可以使用 Method Swizzle
> 2. Method Swizzle 指的是改变一个已存在的选择器对应的实现过程，OC 中方法的调用能够在运行时通过改变类的调度表中选择器到最终函数间的映射关系
> 3. 在 OC 中调用一个方法，其实是向一个对象发送消息，查找消息的唯一依据是 selector 名称。利用 OC 的动态特性，可以实现在运行时偷换 selector 对应的方法实现
> 4. 可以利用 method_exchangeImplementations 来交换两个方法中的 IMP
> 5. 可以利用 class_replaceMethod 来修改类
> 6. 可以利用 method_setImplementation 来直接设置某个方法的 IMP

### 2、_objc_msgForward 函数是做什么的？直接调用它将会发生什么？

> _objc_msgForward 是 IMP 类型，用于消息转发；
>
> 当向一个对象发送一条消息，但它并没有实现的时候，_objc_msgForward 会尝试做消息转发。

## KVC、KVO

### 1、KVO 与 KVC 的区别

> 1. KVC 是键值编码，通过字符串的名字来访问类属性，而不是通过调用 Setter、Getter 方法。即使这个属性没有 Set 和 Get 方法，也能访问；
> 2. KVO 是键值观察，指定观察的对象的属性被修改后，KVO 会自动通知相应的观察者，用完后需要在 dealloc 方法中移除观察者。ARC 中也可以调用 dealloc，只不过不能调用 [super dealloc]。

### 2、KVC 的底层实现

> 当通过 KVC 调用对象时，比如：[self valueForKey:"someKey"]
>
> 1. 检查是否存在相应的 key 的 set 方法，如果存在就调用 set 方法
> 2. 如果不存在 set 方法，会查找与 key 相同名称且带下划线的成员变量；如果有，直接给成员变量赋值
> 3. 如果没有找到带下划线的 key，会查找是否有相同名称的属性 key，如果有，直接赋值
> 4. 如果还是没有找到，则调用 valueForUndefinedKey： 和 setValue：forUndefinedKey 方法
> 5. 如果以上方法都抛出异常，可以根据需要重写它们