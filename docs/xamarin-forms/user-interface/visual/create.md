---
title: 创建 Xamarin Forms 视觉对象呈现器
description: 创建可有选择地应用于 VisualElement 对象的 Xamarin 视觉对象, 而无需为 Xamarin. Forms 视图创建子类。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 8173ebcc174df6e34a53f226a43083bd28941031
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869377"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>创建 Xamarin Forms 视觉对象呈现器

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Xamarin。窗体视觉[`VisualElement`](xref:Xamarin.Forms.VisualElement)对象允许创建呈现器并有选择地应用于对象, 而无需对 Xamarin. Forms 视图创建子类。 指定一个`IVisual`类型 (作为其`ExportRendererAttribute`一部分) 的呈现器将用于呈现所选择的视图, 而不是默认呈现器。 在呈现器所选内容时，`Visual`检查并呈现器选择过程中包含在视图的属性。

> [!IMPORTANT]
> 当前, 在视图呈现后无法更改[属性,但在将来的版本中将发生更改。`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

创建和使用 Xamarin 的过程直观呈现器如下:

1. 为所需视图创建平台呈现器。 有关详细信息, 请参阅[创建呈现](#create-platform-renderers)器。
1. 创建一个派生自`IVisual`的类型。 有关详细信息, 请参阅[Create a IVisual type](#create-an-ivisual-type)。
1. 将类型注册为修饰呈现器`ExportRendererAttribute`的的一部分。 `IVisual` 有关详细信息, 请参阅[注册 IVisual 类型](#register-the-ivisual-type)。
1. 通过将视图的[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性设置`IVisual`为名称来使用可视化呈现器。 有关详细信息, 请参阅[使用视觉呈现](#consume-the-visual-renderer)器。
1. 可有可无注册`IVisual`类型的名称。 有关详细信息, 请参阅[注册 IVisual 类型的名称](#register-a-name-for-the-ivisual-type)。

## <a name="create-platform-renderers"></a>创建平台呈现器

有关创建呈现器类的信息, 请参阅[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。 但请注意, 在不需要为视图划分子类的情况下, 将对视图应用一个 Xamarin。

此处所述的呈现器类实现[`Button`](xref:Xamarin.Forms.Button)了一个使用阴影显示其文本的自定义。

### <a name="ios"></a>iOS

下面的代码示例演示了 iOS 的按钮呈现器:

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

下面的代码示例演示适用于 Android 的按钮呈现器:

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

在跨平台库中, 创建一个派生自`IVisual`的类型:

```csharp
public class CustomVisual : IVisual
{
}
```

然后`CustomVisual` , 可以针对呈现器类注册该类型, 允许[`Button`](xref:Xamarin.Forms.Button)对象选择使用呈现器。

## <a name="register-the-ivisual-type"></a>注册 IVisual 类型

在平台项目中, 用`ExportRendererAttribute`以下内容修饰呈现器命名空间:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

在 iOS 平台项目的此示例中`ExportRendererAttribute` , 指定`CustomButtonRenderer`类将用于呈现[`Button`](xref:Xamarin.Forms.Button)使用对象, `IVisual`其类型注册为第三个参数。 指定一个`IVisual`类型 (作为其`ExportRendererAttribute`一部分) 的呈现器将用于呈现所选择的视图, 而不是默认呈现器。

## <a name="consume-the-visual-renderer"></a>使用可视化呈现器

对象可以通过[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)将呈现器类的属性设置为来`Custom`选择使用呈现器类: [`Button`](xref:Xamarin.Forms.Button)

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> 在 XAML 中, 类型转换器无需在[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性值中包含 "视觉对象后缀"。 但是, 也可以指定完整的类型名称。

等效 C# 代码如下：

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

在呈现器选择时, [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)的[`Button`](xref:Xamarin.Forms.Button)属性被检查并包含在呈现器选择过程中。 如果找不到, 则将使用 Xamarin. Forms 默认呈现器。

下面的屏幕截图显示了[`Button`](xref:Xamarin.Forms.Button)呈现的, 其中显示了具有阴影的文本:

[![在 iOS 和 Android 上带有阴影文本的自定义按钮的屏幕截图](material-visual-images/custom-button.png "带有阴影文本的按钮")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>注册 IVisual 类型的名称

可用于为`IVisual`类型注册其他名称 (可选)。 [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) 此方法可用于解决不同可视化库之间的命名冲突, 或者只需用不同于其类型名称的名称来引用视觉对象。

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)应在跨平台库或平台项目中的程序集级别定义:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

然后`IVisual` , 可以通过其注册的名称使用该类型:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> 使用视觉对象的注册名称时, 必须包含任何 "视觉" 后缀。

## <a name="related-links"></a>相关链接

- [材料视觉对象 (示例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Xamarin 格式视觉对象](material-visual.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
