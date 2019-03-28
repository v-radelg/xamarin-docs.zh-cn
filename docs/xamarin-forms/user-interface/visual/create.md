---
title: 创建 Xamarin.Forms 视觉对象呈现器
description: 创建 Xamarin.Forms 视觉对象将有选择地应用于 VisualElement 对象，而不必子类 Xamarin.Forms 视图。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: a11c2045fa6119d0689834c35794bc8913c80bd6
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58506962"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>创建 Xamarin.Forms 视觉对象呈现器

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

Xamarin.Forms Visual 使呈现器中创建并有选择地应用于[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)对象，而不必子类 Xamarin.Forms 视图。 指定的呈现器`IVisual`类型，作为的一部分其`ExportRendererAttribute`，将用于呈现视图，而不是默认的呈现器中选择。 在呈现器所选内容时，`Visual`检查并呈现器选择过程中包含在视图的属性。

> [!IMPORTANT]
> 目前[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)已呈现视图，但这将在未来版本中更改后，无法更改属性。

用于创建和使用 Xamarin.Forms 视觉对象呈现器的过程是：

1. 创建平台呈现器为所需的视图。 有关详细信息，请参阅[创建呈现器](#create-platform-renderers)。
1. 创建派生的类型`IVisual`。 有关详细信息，请参阅[创建 IVisual 类型](#create-an-ivisual-type)。
1. 注册`IVisual`类型作为的一部分`ExportRendererAttribute`修饰的呈现器。 有关详细信息，请参阅[注册 IVisual 类型](#register-the-ivisual-type)。
1. 通过设置使用 Visual 呈现器[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)到视图上的属性`IVisual`名称。 有关详细信息，请参阅[使用 Visual 呈现器](#consume-the-visual-renderer)。
1. [可选]为注册名称`IVisual`类型。 有关详细信息，请参阅[IVisual 类型为注册名称](#register-a-name-for-the-ivisual-type)。

## <a name="create-platform-renderers"></a>创建平台呈现器

有关创建呈现器类的信息，请参阅[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 但是，请注意，Xamarin.Forms 视觉对象呈现器应用到一个视图，且不会在视图子类。

此处所述的呈现器类实现一个自定义[ `Button` ](xref:Xamarin.Forms.Button)显示其文本具有阴影。

### <a name="ios"></a>iOS

下面的代码示例显示了适用于 iOS 的按钮呈现器：

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

下面的代码示例显示了适用于 Android 的按钮呈现器：

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>创建 IVisual 类型

在你的跨平台库，创建派生的类型`IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

`CustomVisual`类型可以然后注册对呈现器类，允许[ `Button` ](xref:Xamarin.Forms.Button)要选择加入使用呈现器的对象。

## <a name="register-the-ivisual-type"></a>注册 IVisual 类型

在平台项目中，修饰的呈现器类与`ExportRendererAttribute`:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        ...
    }
}
```

在此示例中，`ExportRendererAttribute`规定`CustomButtonRenderer`类将用于呈现使用[ `Button` ](xref:Xamarin.Forms.Button)对象，与`IVisual`类型注册为第三个参数。 指定的呈现器`IVisual`类型，作为的一部分其`ExportRendererAttribute`，将用于呈现视图，而不是默认的呈现器中选择。

## <a name="consume-the-visual-renderer"></a>使用 Visual 呈现器

一个[ `Button` ](xref:Xamarin.Forms.Button)对象可以选择加入使用呈现器类通过设置其[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)属性设置为`Custom`:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> 在 XAML 中，类型转换器不需要包括中的"可视"后缀[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)属性值。 但是，还可以指定完整类型名称。

等效的 C# 代码是：

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

在呈现器所选内容时， [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)的属性[ `Button` ](xref:Xamarin.Forms.Button)检查并包含在呈现器选择过程。 如果将呈现器不位于，将使用 Xamarin.Forms 默认的呈现器。

以下屏幕截图显示了呈现[ `Button` ](xref:Xamarin.Forms.Button)，这将显示其文本具有阴影：

[![用阴影的文本、 iOS 和 Android 上的自定义按钮的屏幕截图](material-visual-images/custom-button.png "具有阴影的文本按钮")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>注册 IVisual 类型的名称

[ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute)可用于选择注册的不同名称`IVisual`类型。 这种方法可用来解决命名冲突之间不同的可视库，或在其中你只是想要向视觉对象，请参阅的情况下通过不同于其类型名称的名称。

[ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute)应在程序集级别的跨平台库或平台项目中定义：

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

`IVisual`通过其注册的名称也可以使用类型：

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> 当使用视觉对象通过其注册的名称，必须包含任何"视觉对象"后缀。

## <a name="related-links"></a>相关链接

- [材料视觉对象 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Xamarin.Forms 材料视觉对象](material-visual.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
