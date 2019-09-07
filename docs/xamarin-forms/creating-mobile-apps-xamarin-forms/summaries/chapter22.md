---
title: 第 22 章的摘要。 动画
description: 用 Xamarin 创建移动应用程序：第 22 章的摘要。 动画
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770996"
---
# <a name="summary-of-chapter-22-animation"></a>第 22 章的摘要。 动画

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

您已了解你可以创建自己的动画使用 Xamarin.Forms 计时器或`Task.Delay`，但通常更易于使用的 Xamarin.Forms 提供的动画功能。 三个类实现这些动画：

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)高级方法
- [`Animation`](xref:Xamarin.Forms.Animation)但更难更灵活
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions)最多用途、 最低级别方法

通常情况下，所支持的可绑定属性的属性为目标的动画。 这不是一项要求，但这些是动态地对更改做出反应的唯一属性。

没有这些动画的 XAML 界面，但您可以将动画集成到 XAML 使用中所述的技术[**第 23 章。触发器和行为**](chapter23.md)。

## <a name="exploring-basic-animations"></a>探索基本动画

基本动画函数是扩展方法中找到[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类。 这些方法应用于派生的任何对象`VisualElement`。 最简单的动画目标属性中所述的转换[ `Chapter 21. Transforms` ](chapter21.md)。

[ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout)演示了如何`Clicked`事件处理程序`Button`可以调用[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))扩展方法以启动中圆形按钮。

`RotateTo`方法更改`Rotation`属性的`Button`从 0 到 360 在过去的四分之一秒 （默认）。 如果`Button`点击同样，但是，它不执行任何操作因为`Rotation`属性已 360 度。

### <a name="setting-the-animation-duration"></a>设置动画持续时间

第二个参数`RotateTo`持续时间以毫秒为单位。 如果设置为较大的值，点击`Button`在动画期间启动新动画开始的当前角度。

### <a name="relative-animations"></a>相对动画

`RelRotateTo`方法通过将指定的值添加到现有值执行相对旋转。 此方法允许`Button`多次，而每台要分流的时间会增加`Rotation`360 度的属性。

### <a name="awaiting-animations"></a>正在等待动画

中的所有动画方法`ViewExtensions`返回`Task<bool>`对象。 这意味着您可以定义一系列的使用顺序动画`ContinueWith`或`await`。 `bool`完成的返回值是`false`如果在动画完成而不发生中断或`true`如果它已被取消[ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法，这将取消所有由启动的动画中的其他方法`ViewExtensions`同一元素上设置。

### <a name="composite-animations"></a>复合动画

可以混合使用等待和非等待创建复合动画的动画。 这些是中的动画`ViewExtensions`面向`TranslationX`， `TranslationY`，和`Scale`转换属性：

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

请注意，`TranslateTo`可能会同时影响`TranslationX`和`TranslationY`属性。

### <a name="taskwhenall-and-taskwhenany"></a>Task.WhenAll 和 Task.WhenAny

还有可能要管理使用的同时进行动画[ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*)，这表示当多个任务具有所有得出的结论是，和[ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*)，这表示当多个第一个任务已结束。

### <a name="rotation-and-anchors"></a>旋转和定位点

调用时`ScaleTo`， `RelScaleTo`， `RotateTo`，和`RelRotateTo`方法，可以设置[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)并[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)属性来指示缩放和旋转的中心。

[ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton)演示此技术通过旋转`Button`围绕页的中心。

### <a name="easing-functions"></a>缓动函数

通常，动画是线性从起始值到结束值。 缓动函数可能会导致动画以加快或减慢其过程中。 类型的动画方法的最后一个可选参数是[ `Easing` ](xref:Xamarin.Forms.Easing)，用于定义类型的 11 静态只读字段类`Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear)默认值
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)[ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut)，和 [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)[ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut)，和 [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) 和 [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 和 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

`In`后缀表示的效果是动画，开头`Out`意味着在结束时，和`InOut`意味着它位于的开头和结尾的动画。

[ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton)示例演示如何使用缓动函数。

### <a name="your-own-easing-functions"></a>缓动函数

您还可以定义您自己的缓动函数通过传递[ `Func<double, double>` ](xref:System.Func`2)到[`Easing`构造函数](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double}))。 `Easing` 此外定义了隐式转换`Func<double, double>`到`Easing`。 缓动函数的参数在 0 到 1 范围内是始终，随着该动画从开头到末尾以线性方式进行。 该函数*通常*返回的 0 到 1 范围内的一个值，但可以负或大于 1 简要是 (使用这种情况`SpringIn`和`SpringOut`函数) 或可能违反规则，如果您知道自己在做什么。

[ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale)示例演示如何自定义缓动函数，并且[ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase)演示另一个。

[ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton)示例还演示了自定义缓动函数，以及更改的一种技术`AnchorX`和`AnchorY`旋转动画在序列中的属性。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库有[ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs)类，该类使用自定义缓动函数问题时单击一个按钮。 [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo)示例说明了这一点。

### <a name="entrance-animations"></a>进入动画

页首次出现时，会发生一种常用类型的动画。 可在启动此类动画[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)页面的重写。 对于这些动画，其最大努力来设置要显示的页面的 XAML*后*动画，然后初始化并进行动画处理代码中的布局。

[ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance)的示例使用[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))扩展方法，以在页面内容淡入。

[ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance)的示例使用[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))扩展方法，以从边滑动中页的内容。

[ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance)的示例使用[ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))扩展方法进行动画处理`RotationY`属性。 一个[ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法也是可用。

### <a name="forever-animations"></a>不限次数的动画

另一种极端情况是，"forever"动画之前运行程序将会终止。 这些通常被为了出于演示目的。

[ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation)的示例使用[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))动画以淡入淡出的两段文本。

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation)显示 palindrome，然后按顺序的单个字母来旋转 180 度使其所有颠倒。 然后将整个字符串被翻转 180 度读取的原始字符串相同。

[ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation)示例旋转一个简单`BoxView`直升机时围绕在屏幕的中心旋转。

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes)需要考虑的是`BoxView`分支围绕在屏幕的中心服务器，然后旋转每个辐射本身来创建有趣的模式：

[![旋转分支服务器的三个屏幕截图](images/ch22fg21-small.png "旋转分支服务器")](images/ch22fg21-large.png#lightbox "旋转分支服务器")

但是，逐渐提高`Rotation`元素的属性可能无法工作长远来看，作为[ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)示例演示。

[ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage)的示例使用[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))， [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，和[ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))以使其看起来像在三维空间中旋转位图。

### <a name="animating-the-bounds-property"></a>边界属性进行动画处理

中唯一的扩展方法`ViewExtensions`尚未演示是[ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))，这有效地之间进行动画处理只读[ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds)属性通过调用[`Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。 通常情况下调用此方法`Layout`派生类中将讨论[**第 26 章。CustomLayouts**](chapter26.md)。

`LayoutTo`方法应被限制为特殊用途。 [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox)程序使用它来压缩和展开`BoxView`如弹回的页边。

[ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)的示例使用`LayoutTo`移动中的磁贴的经典实现显示经过编码的图像而不是带编号的磁贴的 15 到 16 测验题：

[![Xamarin Xuzzle 的三个屏幕截图](images/ch22fg26-small.png "Xuzzle 益智游戏")](images/ch22fg26-large.png#lightbox "Xuzzle 益智游戏")

### <a name="your-own-awaitable-animations"></a>可等待的动画

[ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation)示例创建的可等待的动画。 可以返回的关键类`Task`方法并在动画完成时的信号中的对象是[ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1)。

## <a name="deeper-into-animations"></a>深入到动画

Xamarin.Forms 动画系统可以是有点令人困惑。 除了`Easing`类中，动画系统包含`ViewExtensions`， `Animation`，和`AnimationExtension`类。

### <a name="viewextensions-class"></a>ViewExtensions 类

您所见[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)。 它定义了九个方法返回`Task<bool>`并[ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))。 七个九个方法目标转换属性。 其他两个[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，哪些目标[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)属性，以及[ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))，它调用[ `Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。

### <a name="animation-class"></a>动画类

[ `Animation` ](xref:Xamarin.Forms.AnimationExtensions)类有[构造函数](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action))具有五个参数来定义回调和已完成的方法，以及动画的参数。

可以通过添加子动画[ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))， [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))， [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))，并重载[ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

动画对象然后通过调用启动[ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法。

### <a name="animationextensions-class"></a>AnimationExtensions 类

[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions)类包含大部分的扩展方法。 有几个版本的`Animate`方法和泛型[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法是各种不同场合，它实际上是所需要的唯一动画函数。

## <a name="working-with-the-animation-class"></a>使用动画类

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例演示[ `Animation` ](xref:Xamarin.Forms.Animation)具有多个不同的动画类。

### <a name="child-animations"></a>子动画

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例还演示了动画，请使用的 （非常类似） 的子[ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))和[ `Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation))方法。

### <a name="beyond-the-high-level-animation-methods"></a>超出高级动画方法

[ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations)示例还演示了如何执行超出面向的属性的动画`ViewExtensions`方法。 在一个示例中，一系列句点的变长;在另一个示例中，`BackgroundColor`属性进行动画处理。

### <a name="more-of-your-own-awaitable-methods"></a>多个你自己的可等待方法

[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法`ViewExtensions`不适用于[ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut)函数。 它将停止时的缓动输出获取高于 1。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)类[ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12)和[ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49)扩展方法不具有此问题，以及[ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44)并[ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71)取消那些方法动画。

[ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance)演示了`TranslateXTo`方法。

`MoreExtensions`类还包含[ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76)扩展方法，它将 X 和 Y 转换时，合并和一个[ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113)方法。

### <a name="implementing-a-bezier-animation"></a>实现贝塞尔动画

还有可能要开发移动的贝塞尔样条路径的某个元素的动画。 [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含[ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs)封装的贝塞尔样条的结构和一个[ `BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs)控件方向的枚举。

[ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)类包含[ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118)扩展方法和一个[ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161)方法。

[ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop)示例演示如何对 Beizer 路径的某个元素进行动画处理。

## <a name="working-with-animationextensions"></a>使用 AnimationExtensions

一种类型的标准集合中缺少是动画的颜色动画。 问题是无法正确执行两个内插`Color`值。 可以插入单个的 RGB 值，但只需为有效在插值 HSL 值。

出于此原因， [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含两个`Color`动画方法： [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)并[ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188)。 (此外，还有两个取消方法： [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183)并[ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206))。

这两种方法使利用[ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)，通过调用广泛泛型执行动画[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)中的方法[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions)。

[ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations)示例演示如何使用这两种颜色动画类型。

## <a name="structuring-your-animations"></a>构建您的动画

有时它可用于 express 中 XAML 的动画和使用它们与 MVVM 结合使用。 在下一章中，对此进行了[**第 23 章。触发器和行为**](chapter23.md)。

## <a name="related-links"></a>相关链接

- [第 22 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [第 22 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [动画](~/xamarin-forms/user-interface/animation/index.md)
