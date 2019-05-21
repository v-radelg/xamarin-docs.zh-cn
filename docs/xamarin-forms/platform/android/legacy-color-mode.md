---
title: 在 Android 上 VisualElement 旧式颜色模式
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 特定于平台的 Xamarin.Forms 旧颜色模式中禁用的。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: e22d857ab3b254d101d044bf32a9f0b6312a3ce3
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926592"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>在 Android 上 VisualElement 旧式颜色模式

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

某些 Xamarin.Forms 视图功能旧颜色模式。 在此模式下，当[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)视图的属性设置为`false`，视图将重写由具有已禁用状态的默认本机颜色用户设置的颜色。 有关向后兼容性，这种旧颜色模式保持不受支持视图的默认行为。

此 Android 特定于平台的禁用此旧颜色模式下，以便对视图由用户设置的颜色保持，即使禁用的视图。 设置使用在 XAML [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty)附加到属性`false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否将禁用旧颜色模式。 此外， [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement}))方法可以用于返回是否禁用旧颜色模式。

结果是，可以禁用旧版颜色模式，以便对视图由用户设置的颜色甚至保持禁用视图时：

![](legacy-color-mode-images/legacy-color-mode-disabled.png "旧颜色模式已禁用")

> [!NOTE]
> 设置时[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)旧颜色模式被完全忽略上一个视图。 可视状态的详细信息，请参阅[Xamarin.Forms 视觉状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
