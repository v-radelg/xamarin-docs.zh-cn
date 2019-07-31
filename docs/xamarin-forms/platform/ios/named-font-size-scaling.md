---
title: IOS 上命名字体大小的辅助功能缩放
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来禁用命名字体大小的辅助功能缩放。
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 678ea2210389eb33bc3a184d758f2bfa07c60bd9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656736"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>IOS 上命名字体大小的辅助功能缩放

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定用于禁用命名字体大小的辅助功能缩放。 设置使用在 XAML`Application.EnableAccessibilityScalingForNamedFontSizes`可绑定属性设置为`false`:

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

`Application.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [命名`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)空间中的`Application.SetEnableAccessibilityScalingForNamedFontSizes`方法用于禁用通过 iOS 辅助功能设置进行缩放的命名字体大小。 此外, 该`Application.GetEnableAccessibilityScalingForNamedFontSizes`方法还可用于返回命名字体大小是否由 iOS 辅助功能设置缩放。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
