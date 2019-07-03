---
title: Xamarin.Forms 材料视觉对象
description: Xamarin.Forms 材料视觉对象可以用于创建 iOS 和 Android 上 Xamarin.Forms 应用程序看起来完全相同，或大致相同。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: a626532ac507185b6c01abb5327efa7015c787f5
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512896"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.Forms 材料视觉对象

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

[材料设计](https://material.io)是由 Google，规定了大小、 颜色、 间距和如何视图和布局的外观和行为的其他方面，创建一个固执设计系统。

Xamarin.Forms 材料视觉对象可用来将材料设计规则应用于 Xamarin.Forms 应用程序，iOS 和 Android 上创建的应用程序看起来完全相同，或大致相同。 启用 Visual 材料后，支持的视图将采用相同设计跨平台，创建统一的外观和感觉。 这是与材料呈现器，应用 Material Design 规则来实现。

在应用程序中启用 Xamarin.Forms 材料 Visual 的过程是：

1. 添加[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)到 iOS 和 Android 平台项目的 NuGet 包。 此 NuGet 包提供优化在 iOS 和 Android 的 Material Design 呈现器。 在 iOS 上，提供了包，可传递依赖项[Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)，即C#绑定到 Google[适用于 iOS 的材料组件](https://material.io/develop/ios/)。 在 Android 上，包提供了生成目标，以确保你 TargetFramework 已正确设置。
1. 初始化每个平台项目中的材料呈现器。 有关详细信息，请参阅[初始化材料呈现器](#initialize-material-renderers)。
1. 通过设置使用的材料呈现器[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)属性设置为`Material`应采用的 Material Design 规则任何页上。 有关详细信息，请参阅[使用材料呈现器](#consume-material-renderers)。
1. [可选]自定义的材料呈现器。 有关详细信息，请参阅[自定义呈现器材料](#customize-material-renderers)。

> [!IMPORTANT]
> 在 Android 上，材料呈现器要求的最低版本为 5.0 (API 21) 或更高版本，以及 (API 28) 9.0 版 TargetFramework。 此外，平台项目需要 Android 支持库 28.0.0 或更高版本，并且其主题需要继承材料组件主题或继续继承 AppCompat 主题。 有关详细信息，请参阅[适用于 Android 入门材料组件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

材料呈现器目前纳入[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包的以下视图：

- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
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

## <a name="initialize-material-renderers"></a>初始化材料呈现器

安装后[Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包，必须在每个平台项目中初始化呈现器的材料。

在 iOS 上，这应发生在**AppDelegate.cs**通过调用`FormsMaterial.Init`方法*后*`Xamarin.Forms.Forms.Init`方法：

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

在 Android 上，这应发生在**MainActivity.cs**通过调用`FormsMaterial.Init`方法*后*`Xamarin.Forms.Forms.Init`方法：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>使用材料呈现器

应用程序可以选择加入使用材料呈现器通过设置[ `VisualElement.Visual` ](xref:Xamarin.Forms.VisualElement.Visual)属性页面、 布局或视图中，为`Material`:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

等效 C# 代码如下：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)属性可以设置为实现的任何类型`IVisual`，与[ `VisualMarker` ](xref:Xamarin.Forms.VisualMarker)类，用于提供以下`IVisual`属性：

- `Default` – 指示应使用默认的呈现器呈现视图。
- `MatchParent` – 表示视图应使用相同的呈现器作为其直接父级。
- `Material` – 指示该视图应使用的材料呈现器呈现。

> [!IMPORTANT]
> [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)属性中定义[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类，与视图继承`Visual`从其父级的属性值。 因此，设置`Visual`上的属性[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)可确保任何支持的视图页中将使用该视觉对象。 此外，`Visual`属性可以重写上一个视图。

以下屏幕截图显示使用的默认呈现器呈现的用户界面：

[![默认呈现器，在 iOS 和 Android 上的屏幕截图](material-visual-images/default-renderers.png "视图使用默认呈现器")](material-visual-images/default-renderers-large.png#lightbox)

以下屏幕截图显示使用材料呈现器呈现相同的用户界面：

[![在 iOS 和 Android 的材料呈现器的屏幕截图](material-visual-images/material-renderers.png "视图使用材料呈现器")](material-visual-images/material-renderers-large.png#lightbox)

之间的默认呈现器和材料呈现器，如下所示的主要可见差异有材料呈现器首字母大写[ `Button` ](xref:Xamarin.Forms.Button)文本、 和的角变圆[ `Frame` ](xref:Xamarin.Forms.Frame)边框。 但是，材料呈现器使用的本机控件，并因此可能仍有用户界面区域，例如字体、 阴影、 颜色和提升的平台之间的差异。

> [!NOTE]
> 材料设计组件紧密地符合 Google 的准则。 因此，Material Design 呈现器偏向该大小调整和行为。 当您需要更好地控制样式或行为时，您仍然可以创建您自己[效果](~/xamarin-forms/app-fundamentals/effects/index.md)，[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)，或[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)以实现所需的详细信息。

## <a name="customize-material-renderers"></a>自定义材料呈现器

就像默认呈现器，通过以下基类一样，材料呈现器可以根据需要进行自定义：

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
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

在此示例中，`ExportRendererAttribute`规定`CustomMaterialProgressBarRenderer`类将用于呈现[ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)视图中，使用`IVisual`类型注册为第三个参数。

> [!NOTE]
> 指定的呈现器`IVisual`类型，作为的一部分其`ExportRendererAttribute`，将用于呈现视图，而不是默认的呈现器中选择。 在呈现器所选内容时，`Visual`检查并呈现器选择过程中包含在视图的属性。

有关自定义呈现器的详细信息，请参阅[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="related-links"></a>相关链接

- [材料视觉对象 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [创建 Xamarin.Forms 视觉对象呈现器](create.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
