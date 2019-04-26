---
title: 在 iOS 上的页状态条可见性
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的设置页面上的状态栏的可见性。
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 98eba6dea1fb528aa15a1fb242b0fb0eb7dada56
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258577"
---
# <a name="page-status-bar-visibility-on-ios"></a>在 iOS 上的页状态条可见性

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此平台特定于 iOS 的用于上设置的可见性状态栏[ `Page` ](xref:Xamarin.Forms.Page)，并包括能够控制状态栏如何进入或离开`Page`。 设置使用在 XAML 中`Page.PrefersStatusBarHidden`附加属性设置为值`StatusBarHiddenMode`枚举，并选择性地`Page.PreferredStatusBarUpdateAnimation`附加属性的值为`UIStatusBarAnimation`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetPrefersStatusBarHidden`方法，请在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空间，用于上设置的可见性状态栏[ `Page` ](xref:Xamarin.Forms.Page)通过指定之一`StatusBarHiddenMode`枚举值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`并`StatusBarHiddenMode.False`值设置而不考虑设备方向状态栏可见性和`StatusBarHiddenMode.Default`值将隐藏垂直 compact 环境中的状态栏。

结果是，在状态栏的可见性[ `Page` ](xref:Xamarin.Forms.Page)可以设置：

![](page-status-bar-visibility-images/hide-status-bar.png "平台特有的状态栏的可见性")

> [!NOTE]
> 上[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，指定`StatusBarHiddenMode`枚举值还将更新所有的子页面上的状态栏。 所有其他[ `Page`](xref:Xamarin.Forms.Page)的派生类型，指定的`StatusBarHiddenMode`枚举值只会更新当前页面上的状态栏。

`Page.SetPreferredStatusBarUpdateAnimation`方法用于设置状态栏如何进入或离开[ `Page` ](xref:Xamarin.Forms.Page)通过指定之一`UIStatusBarAnimation`枚举值： `None`， `Fade`，或`Slide`。 如果`Fade`或`Slide`指定枚举值，0.25 第二个动画执行如状态栏进入或离开`Page`。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
