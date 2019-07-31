---
title: IOS 上的页面状态栏可见性
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来设置页面上状态栏的可见性。
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a187efa9310fa150ddc884d8b42da5ccb9ecee11
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655848"
---
# <a name="page-status-bar-visibility-on-ios"></a>IOS 上的页面状态栏可见性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于设置上[`Page`](xref:Xamarin.Forms.Page)状态栏的可见性, 并且它包括控制状态栏进入或离开的`Page`方式。 设置使用在 XAML 中`Page.PrefersStatusBarHidden`附加属性设置为值`StatusBarHiddenMode`枚举，并选择性地`Page.PreferredStatusBarUpdateAnimation`附加属性的值为`UIStatusBarAnimation`枚举：

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

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
