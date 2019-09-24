---
title: Xamarin 格式视觉对象
description: Xamarin。窗体材料视觉对象可用于创建在 iOS 和 Android 上看起来完全相同或大致相同的 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: b735541d51321231775b025745e68c54552697d3
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198498"
---
# <a name="xamarinforms-material-visual"></a>Xamarin 格式视觉对象

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[材料设计](https://material.io)是 Google 创建的固执设计系统，它规定了视图和布局的外观和行为方式的大小、颜色、间距和其他方面。

Xamarin。窗体材料视觉对象可用于将材料设计规则应用于 Xamarin、Forms 应用程序、在 iOS 和 Android 上创建外观完全相同或大致相同的应用程序。 启用材料视觉对象后，支持的视图将采用相同的设计跨平台，从而形成统一的外观。 这是通过材料呈现器实现的，这些呈现器适用于应用材料设计规则。

在应用程序中启用 Xamarin 格式视觉对象的过程如下：

1. 将[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包添加到 IOS 和 Android 平台项目。 此 NuGet 包提供 iOS 和 Android 上经过优化的材料设计呈现器。 在 iOS 上，包提供与[MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可传递依赖项，这是与 Google 的C# [ios 材料组件](https://material.io/develop/ios/)的绑定。 在 Android 上，包提供了生成目标，以确保正确设置 TargetFramework。
1. 初始化每个平台项目中的材料呈现器。 有关详细信息，请参阅[初始化材料呈现](#initialize-material-renderers)器。
1. 通过将[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)属性设置为`Material` ，在应采用材料设计规则的任何页上，使用材料呈现器。 有关详细信息，请参阅[使用材料呈现](#consume-material-renderers)器。
1. 可有可无自定义材料呈现器。 有关详细信息，请参阅[自定义材料呈现](#customize-material-renderers)器。

> [!IMPORTANT]
> 在 Android 上，材料呈现器要求最低版本5.0 （API 21）或更高版本，以及版本9.0 （API 28）的 TargetFramework。 此外，你的平台项目需要28.0.0 或更高版本的 Android 支持库，其主题需要从材料组件主题继承或继续从 AppCompat 主题继承。 有关详细信息，请参阅[适用于 Android 入门材料组件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

材料呈现器当前包含在以下视图中的[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包中：

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

安装[Xamarin](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) NuGet 包后，必须在每个平台项目中初始化材料呈现器。

在 iOS 上，此方法应**在 AppDelegate.cs**中出现`Xamarin.Forms.FormsMaterial.Init` ，方法是`Xamarin.Forms.Forms.Init`在方法*之后*调用方法：

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

在 Android 上，此`Xamarin.Forms.Forms.Init`方法应在**MainActivity.cs**中出现`Xamarin.Forms.FormsMaterial.Init` ，方法是在方法*之后*调用方法：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>使用材料呈现器

应用程序可以通过将页面、布局或视图上[`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual)的属性设置为来选择使用材料呈现器来`Material`执行以下操作：

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

属性可以设置为任何实现`IVisual`的类型， [`VisualMarker`](xref:Xamarin.Forms.VisualMarker)该类提供以下`IVisual`属性： [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

- `Default`–指示应使用默认呈现器呈现视图。
- `MatchParent`–指示视图应使用与它的直接父级相同的呈现器。
- `Material`–指示视图应使用材料呈现器呈现。

> [!IMPORTANT]
> 属性是在类中定义的`Visual` ，视图从其父项继承属性值。 [`VisualElement`](xref:Xamarin.Forms.VisualElement) [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 因此，设置[`ContentPage`](xref:Xamarin.Forms.ContentPage)上`Visual`的属性可以确保页面中任何受支持的视图都将使用该视觉对象。 此外，`Visual`属性可以重写上一个视图。

以下屏幕截图显示了使用默认呈现器呈现的用户界面：

[![IOS 和 Android 上默认呈现器的屏幕截图](material-visual-images/default-renderers.png "使用默认呈现")器的视图](material-visual-images/default-renderers-large.png#lightbox)

以下屏幕截图显示使用材料呈现器呈现相同的用户界面：

[![IOS 和 Android 上的材料呈现器的屏幕截图](material-visual-images/material-renderers.png "使用材料呈现")器的视图](material-visual-images/material-renderers-large.png#lightbox)

默认呈现器和材料呈现器之间的主要可见差异（如下所示）是材料呈现器[`Button`](xref:Xamarin.Forms.Button)将文本呈现为大写，并[`Frame`](xref:Xamarin.Forms.Frame)将边框的角变大。 但是，材料呈现器使用本机控件，因此，字体、阴影、颜色和提升等区域的平台之间可能仍然存在用户界面差异。

> [!NOTE]
> 材料设计组件严格遵守 Google 的准则。 因此，材料设计呈现器倾向于调整大小和行为。 如果需要更好地控制样式或行为，则仍可创建自己的[效果](~/xamarin-forms/app-fundamentals/effects/index.md)、[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)或[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器来实现所需的详细信息。

## <a name="customize-material-renderers"></a>自定义材料呈现器

可以选择通过以下基类自定义材料呈现器（如默认呈现器）：

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

下面的代码演示了自定义`MaterialProgressBarRenderer`类的示例：

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

在此示例中， `ExportRendererAttribute`指定`CustomMaterialProgressBarRenderer`类将用于呈现[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)视图，并`IVisual`将类型注册为第三个参数。

> [!NOTE]
> 指定一个`IVisual`类型（作为其`ExportRendererAttribute`一部分）的呈现器将用于呈现所选择的视图，而不是默认呈现器。 在呈现器所选内容时，`Visual`检查并呈现器选择过程中包含在视图的属性。

有关自定义呈现器的详细信息，请参阅[自定义呈现](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)器。

## <a name="related-links"></a>相关链接

- [材料视觉对象（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [创建 Xamarin Forms 视觉对象呈现器](create.md)
- [自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
