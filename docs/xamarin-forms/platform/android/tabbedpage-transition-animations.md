---
title: Android 上的 TabbedPage 页面过渡动画
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的, 在 TabbedPage 中导航页面时禁用过渡动画。
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4f8d6ec2b06855364970bc9b672c3d3f7b9bfdfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649843"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Android 上的 TabbedPage 页面过渡动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定用于在中通过编程方式或使用选项卡栏在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)页面间导航时禁用转换动画。 设置使用在 XAML`TabbedPage.IsSmoothScrollEnabled`可绑定属性设置为`false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

`TabbedPage.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `TabbedPage.SetIsSmoothScrollEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否在页之间导航时，将显示过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 此外，`TabbedPage`类中`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空间还具有以下方法：

- `IsSmoothScrollEnabled`它用于检索是否之间导航页中，将显示过渡动画`TabbedPage`。
- `EnableSmoothScroll`用于启用过渡动画中的页面之间导航时`TabbedPage`。
- `DisableSmoothScroll`用于在页面之间导航时禁用过渡动画`TabbedPage`。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
