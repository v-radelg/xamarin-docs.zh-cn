---
title: 第 27 章的摘要。 自定义呈现器
description: 使用 Xamarin.Forms 创建移动应用：第 27 章的摘要。 自定义呈现器
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 96d06626fe0a8a4bb5aca59de454f707d4dfc731
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331390"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>第 27 章的摘要。 自定义呈现器

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

如的 Xamarin.Forms 元素`Button`呈现并封装在一个名为类的特定于平台的按钮`ButtonRenderer`。  下面是[iOS 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)，则[Android 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)，并[的 UWP 版本`ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs)。

本章讨论了如何编写您自己的呈现器创建映射到特定于平台的对象的自定义视图。

## <a name="the-complete-class-hierarchy"></a>完整的类层次结构

有四个包含 Xamarin.Forms 特定于平台的代码的程序集。
您可以使用下面的链接的 GitHub 上查看的源代码：

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) （非常小）
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> `WinRT`一书中提到的程序集不再是此解决方案的一部分。 

[ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy)示例将显示为正在执行平台有效的程序集的类层次结构。

您会注意到一个名为的重要类`ViewRenderer`。 这是创建特定于平台的呈现器时，在从派生的类。 它存在于三个不同的版本，因为它绑定到视图系统的目标平台：

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25)具有泛型参数：

- `TView` 约束为 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` 约束为 [`UIKit.UIView`](xref:UIKit.UIView)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17)具有泛型参数：

- `TView` 约束为 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` 约束为 [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

UWP [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6)具有以不同方式命名的泛型自变量：

- `TElement` 约束为 [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` 约束为 [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

在编写将呈现器时，将派生的类从`View`，然后编写多个`ViewRenderer`类、 一个针对每个受支持的平台。 每个特定于平台的实现将引用从为指定的类型派生的本机类`TNativeView`或`TNativeElement`参数。

## <a name="hello-custom-renderers"></a>Hello，自定义呈现器 ！

[ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers)程序引用名为的自定义视图`HelloView`中其[ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs)类。

[ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs)类包括在**HelloRenderers**项目，然后只需派生`View`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs)类**HelloRenderers.iOS**项目派生`ViewRenderer<HelloView, UILabel>`。 在中`OnElementChanged`重写时，它会创建本机 iOS`UILabel`并调用`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs)类**HelloRenderers.Droid**项目派生`ViewRenderer<HelloView, TextView>`。 在中`OnElementChanged`重写时，它会创建 Android`TextView`并调用`SetNativeControl`。

[ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs)类**HelloRenderers.UWP**和其他 Windows 项目派生`ViewRenderer<HelloView, TextBlock>`。 在中`OnElementChanged`重写时，它会创建 Windows`TextBlock`并调用`SetNativeControl`。

所有`ViewRenderer`派生类包含`ExportRenderer`上将相关联的程序集级别特性`HelloView`类的特定`HelloViewRenderer`类。 这是 Xamarin.Forms 如何定位单个平台项目中的呈现器：

[![Hello 视图的三个屏幕截图](images/ch27fg02-small.png "自定义呈现器")](images/ch27fg02-large.png#lightbox "自定义呈现器")

## <a name="renderers-and-properties"></a>呈现器和属性

下一组呈现器实现椭圆绘图，并位于中的各种项目[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案。

[ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs)类位于**Xamarin.FormsBook.Platform**平台。 此类是类似于`BoxView`，并定义一个属性：`Color`类型的`Color`。

呈现器可以传输上设置的属性值`View`对本机对象通过重写`OnElementPropertyChanged`呈现器中的方法。 在此方法内 （和大部分呈现器中），两个属性有：

- `Element`Xamarin.Forms 元素
- `Control`本机视图或小组件或控件的对象

这些属性的类型由泛型参数`ViewRenderer`。 在此示例中，`Element`属于类型`EllipseView`。

`OnElementPropertyChanged`重写因此可将传输`Color`的值`Element`本机`Control`可能具有某种类型的转换对象。 三个呈现器有：

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs)，使用[ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs)椭圆的类。
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs)，使用[ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs)椭圆的类。
- UWP: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs)，这可以使用本机 Windows [ `Ellipse` ](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse)类。

[ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo)类显示多个这样的`EllipseView`对象：

[![椭圆演示的三个屏幕截图](images/ch27fg03-small.png "EllipseView 自定义呈现器")](images/ch27fg03-large.png#lightbox "EllipseView 自定义呈现器")

[ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall)退回`EllipseView`关闭屏幕侧边。

## <a name="renderers-and-events"></a>呈现器和事件

还有可能对于呈现器间接生成事件。 [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs)类是类似于普通的 Xamarin.Forms`Slider`允许指定多个离散步骤之间，但`Minimum`和`Maximum`值。

三个呈现器有：

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

呈现器检测到本机控件，更改，然后调用`SetValueFromRenderer`，该引用中定义的可绑定属性`StepSlider`，这将导致更改`StepSlider`激发`ValueChanged`事件。

[ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo)示例演示了此新的滑块。



## <a name="related-links"></a>相关链接

- [第 27 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [第 27 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
