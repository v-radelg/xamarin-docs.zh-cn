---
title: Xamarin.Forms 材料视觉对象
description: Xamarin.Forms 材料视觉对象可以用于创建 iOS 和 Android 上 Xamarin.Forms 应用程序看起来完全相同，或大致相同。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 46ffe29f898e2f7bd8fcbe759f5a2f54e3dd60e1
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557499"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.Forms 材料视觉对象

Xamarin.Forms 材料视觉对象可以用于创建 iOS 和 Android 上 Xamarin.Forms 应用程序看起来完全相同，或大致相同。 这通过使用实现材料外观，从而选择启用通过外观的应用程序的其他呈现器`Visual`属性：

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>
```

等效的 C# 代码是：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

> [!IMPORTANT]
> `Visual`属性中定义`VisualElement`类，与视图继承`Visual`从其父级的属性值。 因此，设置`Visual`属性上的`ContentPage`可确保任何支持的视图页中将使用该可视外观。 此外，`Visual`属性可以重写上一个视图。

材料呈现器有当前包括 iOS 和 Android 上的以下视图：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)

就功能而言，材料呈现器是不不同的默认呈现器。

在 iOS 和 Android，平台项目必须有[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/ https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)安装 NuGet 包。 安装 NuGet 包后, 初始化代码需要在每个平台项目中，*后*`Xamarin.Forms.Forms.Init`方法调用。 对于 iOS，请使用以下代码：

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

必须在 Android 上，通过相同的参数传递给`Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, bundle);
FormsMaterial.Init(this, bundle);
```

> [!IMPORTANT]
> 在 Android，材料仅适用于 TargetFramework 9.0 (API 28)。 此外，平台项目必须使用 v28 支持库，并且其主题需要继承材料组件主题或继续继承 AppCompat 主题。 有关详细信息，请参阅[适用于 Android 入门材料组件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

以下屏幕截图显示的用户界面中包括哪些材料呈现器已存在，但使用的默认呈现器呈现的四个视图：

[![默认呈现器，在 iOS 和 Android 上的屏幕截图](material-visual-images/default-renderers.png "视图使用默认呈现器")](material-visual-images/default-renderers-large.png#lightbox)

以下屏幕截图显示使用材料呈现器呈现相同的用户界面：

[![在 iOS 和 Android 的材料呈现器的屏幕截图](material-visual-images/material-renderers.png "视图使用材料呈现器")](material-visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> 使用材料呈现器呈现的控件仍将本机控件，因此存在仍将用户界面区域，例如字体、 阴影、 颜色和提升的平台之间的差异。

## <a name="material-renderers"></a>材料呈现器

材料呈现器可以自定义，就像默认呈现器，通过使用以下基本类：

- `MaterialButtonRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

下面的代码演示的自定义示例`MaterialProgressBarRenderer`类：

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        ...
    }
}
```

## <a name="related-links"></a>相关链接

- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
