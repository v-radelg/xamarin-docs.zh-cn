---
title: 在 iOS 上名为字体大小的辅助功能缩放
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用平台特定于 iOS 的以禁用针对命名的字体大小进行扩展的可访问性。
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 1fc6fefe1f9fe48fe2abb367b376a5a6f3484462
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517946"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>在 iOS 上名为字体大小的辅助功能缩放

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此平台特定于 iOS 的禁用针对命名的字体大小进行扩展的可访问性。 设置使用在 XAML`Application.EnableAccessibilityScalingForNamedFontSizes`可绑定属性设置为`false`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

`Application.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Application.SetEnableAccessibilityScalingForNamedFontSizes`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于禁用 iOS 辅助功能设置所缩放的已命名的字体大小。 此外，`Application.GetEnableAccessibilityScalingForNamedFontSizes`方法可以用于返回命名的字体大小进行缩放的 iOS 辅助功能设置。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
