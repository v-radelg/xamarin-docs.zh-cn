---
title: 在 Xamarin.Forms 中的自定义动画
description: 本文演示如何使用 Xamarin.FOrms 动画类来创建和取消动画，同步多个动画，并创建不通过现有的动画方法进行动画处理的属性进行动画处理的自定义动画。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 157e044fd96cdeff87d8fb56029fe625b7312bf4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345735"
---
# <a name="custom-animations-in-xamarinforms"></a>在 Xamarin.Forms 中的自定义动画

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)

_动画类是所有 Xamarin.Forms 动画，创建一个或多个动画对象 ViewExtensions 类中的扩展方法使用构建基块。本文演示如何使用动画类来创建和取消动画，同步多个动画，并创建不通过现有的动画方法进行动画处理的属性进行动画处理的自定义动画。_


在创建时，必须指定多个参数`Animation`对象，包括开始和结束值的属性进行动画处理，并更改属性的值的回调。 `Animation`对象还可以维护一系列子动画，可以运行并同步。 有关详细信息，请参阅[子动画](#child)。

运行与创建的动画[ `Animation` ](xref:Xamarin.Forms.Animation)类，它可能包含或不包含子动画，实现通过调用[ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法。 此方法指定的持续时间的动画，并在其他项目之间的回调，用于控制是否重复动画。

## <a name="creating-an-animation"></a>创建动画

创建时[ `Animation` ](xref:Xamarin.Forms.Animation)对象，通常情况下，最少三个参数是必需的如下面的代码示例中所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此代码定义的动画[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)的属性[ `Image` ](xref:Xamarin.Forms.Image)实例从一个值为 1 到 2 的值。 通过 Xamarin.Forms 派生的动画的值传递给回调指定为第一个参数，它用于更改的值`Scale`属性。

通过调用启动动画[ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法，如下面的代码示例中所示：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

请注意， [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法不返回`Task`对象。 相反，它们通过回调方法提供通知。

中指定以下参数`Commit`方法：

- 第一个参数 (*所有者*) 标识的所有者动画。 这可以是应用动画的可视元素或另一个可视元素，如页。
- 第二个参数 (*名称*) 标识的名称的动画。 名称结合了多个要唯一标识该动画的所有者。 然后可以使用此唯一标识来确定是否正在运行的动画 ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String)))，或取消它 ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)))。
- 第三个参数 (*速率*) 指示每次调用中定义的回调方法之间的毫秒数[ `Animation` ](xref:Xamarin.Forms.Animation)构造函数
- 第四个参数 (*长度*) 指示动画，以毫秒为单位的持续时间。
- 第五个参数 (*缓动*) 定义了用于动画的缓动函数。 或者，可以将缓动函数指定的参数为[ `Animation` ](xref:Xamarin.Forms.Animation)构造函数。 缓动函数的详细信息，请参阅[缓动函数](~/xamarin-forms/user-interface/animation/easing.md)。
- 第六个参数 (*完成*) 将在动画完成时执行的回调。 此回调采用两个参数，与第一个参数，该值指示最终值和第二个参数所`bool`设置为`true`已取消的动画。 或者，*完成*回调可以指定为参数[ `Animation` ](xref:Xamarin.Forms.Animation)构造函数。 具有单个动画，但是，如果*完成*中均指定了回调`Animation`构造函数和`Commit`方法，仅在指定的回调`Commit`将执行方法。
- 第七个参数 (*重复*) 允许重复的动画的回调。 动画结束时调用并返回`true`指示应该重复动画。

总体效果是要创建的动画，可提高[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)的属性[ `Image` ](xref:Xamarin.Forms.Image)从 1 到 2，超过 2 秒 （2000年毫秒），使用[ `Linear`](xref:Xamarin.Forms.Easing.Linear)缓动函数。 每次在动画完成后，其`Scale`属性重置为 1，并在动画重复。

> [!NOTE]
> 可以通过创建构造均可独立运行的并发的动画`Animation`为每个动画对象，然后再调用`Commit`上每个动画的方法。

<a name="child" />

### <a name="child-animations"></a>子动画

[ `Animation` ](xref:Xamarin.Forms.Animation)类还支持子动画，其中涉及到创建`Animation`对象的其他`Animation`对象将被添加。 这样，动画将运行和同步的一系列。 下面的代码示例演示如何创建和运行子动画：

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

或者，可以更加简洁，如下面的代码示例所示编写代码示例：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在这两个代码示例中，父级[ `Animation` ](xref:Xamarin.Forms.Animation)创建对象后，向其附加`Animation`然后添加对象。 前两个参数[ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))方法指定何时开始和完成子动画。 参数值必须是 0 和 1 之间，并且表示在父动画指定的子动画将处于活动状态的相对期限。 因此，在此示例`scaleUpAnimation`将处于活动状态的第一个动画，另一半`scaleDownAnimation`将处于活动状态的动画，虚拟机和`rotateAnimation`的整个持续时间内将处于活动状态。

总体效果是动画发生超过 4 秒 （4000 毫秒为单位）。 `scaleUpAnimation`之间进行动画处理[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)属性从 1 到 2，超过 2 秒。 `scaleDownAnimation`然后进行动画处理`Scale`属性从 2 为 1，超过 2 秒。 出现这两个缩放动画，而`rotateAnimation`之间进行动画处理[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)属性从 0 到 360 之间，4 秒。 请注意，缩放动画还使用缓动函数。 [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn)缓动函数会导致[ `Image` ](xref:Xamarin.Forms.Image)获取较大前, 最初收缩和[ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut)缓动函数导致`Image`以变得比其末尾的完整的动画的实际大小更小。

有多种之间的差异[ `Animation` ](xref:Xamarin.Forms.Animation)用子动画对象，不会的一个：

- 使用子动画时*完成*子动画回调指示当子已完成，并且*完成*回调传递给`Commit`方法指示何时已完成整个动画。
- 当使用子动画，则返回`true`从*重复*上的回拨`Commit`方法不会导致动画重复，但该动画将继续运行而无需新值。
- 当包括中的缓动函数`Commit`方法，并且缓动函数则返回一个值大于 1，则动画将被终止。 如果缓动函数返回的值小于 0，其值限制为 0。 若要使用缓动函数，返回一个值小于 0 或大于 1，则必须指定一个子动画，而不是在中`Commit`方法。

[ `Animation` ](xref:Xamarin.Forms.Animation)类还包括[ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))方法可用于将子动画添加到父`Animation`对象。 但是，其*开始*并*完成*参数值都不限制为 0 到 1 之间，但只有这一部分对应于 0 到 1 范围子动画将处于活动状态。 例如，如果`WithConcurrent`方法调用定义面向子动画[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)属性从 1 到 6，但*开始*并*完成*的值-2 和 3，*开始*-2 的值对应于`Scale`值为 1，并且*完成*3 的值对应于`Scale`值为 6。 因为 0 和 1 的范围之外的值中不起作用动画，`Scale`属性仅进行动画处理 3 到 6。

## <a name="canceling-an-animation"></a>正在取消动画

应用程序可以取消通过调用动画[ `AbortAnimation` ](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))扩展方法，如下面的代码示例中所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

请注意动画进行动画所有者和动画名称的组合唯一标识。 因此，所有者和名称指定何时运行动画必须指定要取消动画。 因此，代码示例将立即取消名为动画`SimpleAnimation`归页。

## <a name="creating-a-custom-animation"></a>创建自定义动画

到目前为止如下所示的示例已演示了同样的方法与可达到的动画[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类。 但是，利用[ `Animation` ](xref:Xamarin.Forms.Animation)类是它有权访问经过动画处理的值更改时执行的回调方法。 这样，要实现所需的任何动画的回调。 例如，下面的代码示例进行动画处理[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)属性设置为页面[ `Color` ](xref:Xamarin.Forms.Color)创建的值[ `Color.FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))方法中，范围从 0 到 1 的色调值：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

生成的动画提供了提前出喷薄彩虹的颜色通过页面背景的外观。

创建复杂动画，其中包括贝塞尔曲线动画的更多示例请参阅[第 22 章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)的[使用 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

## <a name="creating-a-custom-animation-extension-method"></a>创建一个自定义动画的扩展方法

中的扩展方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)类对从其当前值为指定的值的属性进行动画处理。 这使得难以创建，例如，`ColorTo`可用于进行动画处理的一种颜色从一个值，因为动画方法：

- 唯一[ `Color` ](xref:Xamarin.Forms.Color)定义的属性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类是[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)，但并非总是所需`Color`属性若要进行动画处理。
- 当前值通常[ `Color` ](xref:Xamarin.Forms.Color)属性是[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)，这并不真正的颜色，并无法在计算内插中使用它。

此问题的解决方案是不让`ColorTo`方法针对特定[ `Color` ](xref:Xamarin.Forms.Color)属性。 相反，它可以编写通过内插的回调方法`Color`回调用方的值。 此外，该方法将执行开始和结束`Color`参数。

`ColorTo`方法可作为使用的扩展方法[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)中的方法[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions)类以提供其功能。 这是因为`Animate`方法可用于目标属性的类型不是`double`，如以下代码示例所示：

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法需要*转换*参数，这是一个回调方法。 此回调的输入始终是`double`范围从 0 到 1。 因此，`ColorTo`方法定义自己转换`Func`接受`double`范围从 0 到 1，且该返回[ `Color` ](xref:Xamarin.Forms.Color)对应的值相对应的值。 `Color`值计算在插值[ `R` ](xref:Xamarin.Forms.Color.R)， [ `G` ](xref:Xamarin.Forms.Color.G)， [ `B` ](xref:Xamarin.Forms.Color.B)，和[ `A`](xref:Xamarin.Forms.Color.A)提供两个值`Color`参数。 `Color`值然后传递给应用程序特定属性的回调方法。

此方法允许`ColorTo`方法进行动画处理任何[ `Color` ](xref:Xamarin.Forms.Color)属性，如下面的代码示例中所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此代码示例中，`ColorTo`方法进行动画处理[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)并[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)的属性[ `Label` ](xref:Xamarin.Forms.Label)， `BackgroundColor`属性页上，并[ `Color` ](xref:Xamarin.Forms.BoxView.Color)属性[ `BoxView` ](xref:Xamarin.Forms.BoxView)。

## <a name="summary"></a>总结

本文演示了如何使用[ `Animation` ](xref:Xamarin.Forms.Animation)类来创建和取消动画、 同步多个动画，并创建不由现有动画进行动画处理的属性进行动画处理的自定义动画方法。 `Animation`类是所有 Xamarin.Forms 动画构建基块。


## <a name="related-links"></a>相关链接

- [自定义动画 （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [动画](xref:Xamarin.Forms.Animation)
- [AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
