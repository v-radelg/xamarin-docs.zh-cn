---
title: 第 21 章的摘要。 转换
description: 用 Xamarin 创建移动应用程序：第 21 章的摘要。 转换
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760583"
---
# <a name="summary-of-chapter-21-transforms"></a>第 21 章的摘要。 转换

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

在的位置和大小由其父级，通常是在屏幕上会显示一个 Xamarin.Forms 视图`Layout`或`Layout<View>`派生类。 *转换*是 Xamarin.Forms 功能，可以修改该位置、 大小或甚至方向。

Xamarin.Forms 支持三种基本类型的转换：

- *翻译*&mdash;水平或垂直移动元素
- *规模*&mdash;更改元素的大小
- *旋转*&mdash;打开的元素周围的点或轴

在 Xamarin.Forms 中，缩放是增益;它会影响的宽度和高度统一。 同时在二维表面的屏幕和在 3D 空间中支持显示旋转。 没有任何倾斜 （或数量较） 转换，并且没有通用的矩阵转换。

支持使用八个属性的类型转换`double`定义的`VisualElement`类：

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

所有这些属性可绑定属性受支持。 它们可以是数据绑定的目标，并且设置样式。 [**第 22 章。动画**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md)演示了如何这些属性可进行动画处理，但这一章中的某些示例演示如何动态显示它们使用 Xamarin.Forms[计时器](~/xamarin-forms/platform/device.md#devicestarttimer)。

转换的元素，仅如何呈现时，并执行操作的属性影响*不*影响如何布局中识别该元素。

## <a name="the-translation-transform"></a>平移转换

非零值的[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)并[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)属性水平或垂直移动元素。

[ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo)程序，您可以使用这些属性具有两个试验`Slider`控制元素`TranslationX`和`TranslationY`属性`Frame`. 转换还会影响所有子级的`Frame`。

### <a name="text-effects"></a>文本效果

转换属性的一个常见用途是文本的略有偏移呈现。 了这一点[ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)示例：

[![文本的偏移量的三个屏幕截图](images/ch21fg03-small.png "文本偏移量")](images/ch21fg03-large.png#lightbox "文本偏移量")

另一个问题是要呈现的多个副本`Label`类似于三维块，如中所示[ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)示例。

### <a name="jumps-and-animations"></a>跳转和动画

[ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump)示例使用转换来移动`Button`只要点击，但主要目的是为了演示，`Button`接收用户输入的位置，呈现的按钮。

[ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide)示例类似，但使用计时器进行动画处理`Button`从一个点到另一个。

## <a name="the-scale-transform"></a>缩放转换

[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)转换可以增加或减少的元素的呈现的大小。 默认值为 1。 值为 0 会导致元素不可见。 负值会导致出现旋转 180 度的元素。 `Scale`属性不会影响`Width`或`Height`元素的属性。 这些值保持不变。

您可以体验`Scale`属性使用[ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)示例。

[ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler)示例演示之间进行动画处理的差异`Scale`属性的`Button`进行动画处理和`FontSize`属性。 `FontSize`属性影响如何`Button`，所以我们认为在布局中;`Scale`属性却没有。

[ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize)示例计算`Scale`属性，可应用于`Label`元素以使其仍在页面内调整时一样大。

### <a name="anchoring-the-scale"></a>锚定规模

在以前的三个示例进行缩放的元素具有所有增加或减少大小相对于元素的中心。 换而言之，该元素增大或减小大小在所有方向上相同。 仅在中心元素点仍保留在相同位置在缩放过程。

你可以通过设置缩放的中心[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)并[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)属性。 这些属性是相对于元素本身。 有关`AnchorX`、 左侧和右侧的元素引用的值为 0 和 1 是指在右侧。 同样对于`AnchorY`，0 是顶部 1 是底部。 这两个属性具有默认值为 0.5，它是中心。

[ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo)示例允许用户使用体验`AnchorX`并`AnchorY`属性以及`Scale`属性。

在 iOS 上，使用非默认值`AnchorX`和`AnchorY`属性是与手机方向更改通常不兼容。

## <a name="the-rotation-transform"></a>旋转转换

[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性指定以度为单位，并指示绕的要素定义的一个点沿顺时针方向旋转`AnchorX`和`AnchorY`。 [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo)允许用户体验与这三个属性。

### <a name="rotated-text-effects"></a>旋转的文本效果

[ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle)示例演示如何绘制一个圆圈使用 64 小旋转所需的数学`BoxView`元素。

[ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText)示例显示多个`Label`具有相同的文本字符串的元素旋转以看起来像是辐射。

[ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText)示例显示将显示在一个圆周中换行的文本字符串。

### <a name="an-analog-clock"></a>模拟时钟

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库包含[ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs)计算的一个时钟的手中的角度的类。 若要避免在 ViewModel 中，此类使用的平台依赖项`Task.Delay`而不是用于查找一个新计时器`DateTime`值。

也包含在**Xamarin.FormsBook.Toolkit**是[ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs)类，该类实现`IValueConverter`并提供要舍入到最接近的第二个第二个角度。

[ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock)使用三个旋转`BoxView`元素来绘制了模拟时钟。

[ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock)使用`BoxView`的更广泛的图形，包括刻度线会标记周围的时钟，人脸，并将从其结束的旋转的小距离：

[![字数时钟的三个屏幕截图](images/ch21fg17-small.png "模拟时钟表面")](images/ch21fg17-large.png#lightbox "模拟时钟人脸")

此外[ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs)类**Xamarin.FormsBook.Toolkit**导致第二个指针看上去有点取回之前，向前跳转，然后将移回其正确的位置。

### <a name="vertical-sliders"></a>垂直滑块？

[ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders)示例演示`Slider`元素可以旋转 90 度和仍然有效。 但是，很难定位这些旋转`Slider`元素因为它们仍在布局中显示为水平。

## <a name="3d-ish-rotations"></a>3D 风格旋转

[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX)属性似乎旋转 3D x 轴周围的元素，以使顶部和底部元素似乎移入或向后翻转。 同样， [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY)似乎旋转绕 y 轴以便似乎移入或向后翻转的左侧和右侧的元素的元素。

`AnchorX`属性会影响`RotationY`但不是`RotationX`。 `AnchorY`属性会影响`RotationX`但不是`RotationY`。 您可以体验[ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo)示例探索这些属性的交互。

3D 坐标系统权限隐含的 Xamarin.Forms 是惯用左手。 如果您指向左手递增 X 方向的食指协调 （右侧） 和右手食指的不断增加 Y 方向 （向下），然后您增加 Z 坐标 （带屏幕） 的方向中的 thumb 点坐标。

此外，对于任何三个轴，如果增加值的方向中点左侧 thumb 然后手指的曲线指示正旋转角度的旋转的方向。

## <a name="related-links"></a>相关链接

- [第 21 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [第 21 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
