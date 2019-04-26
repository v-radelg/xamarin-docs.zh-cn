---
title: 在 Android 上 TabbedPage 页面过渡动画
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 特定于平台的以在您不要将 TabbedPage 页面中导航时禁用过渡动画。
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 1e9c46fe9535c313581d6d0053559a28aa327887
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359890"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>在 Android 上 TabbedPage 页面过渡动画

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 Android 平台特定于用于时的页面之间导航，或者以编程方式或在使用选项卡栏中，禁用过渡动画[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 设置使用在 XAML`TabbedPage.IsSmoothScrollEnabled`可绑定属性设置为`false`:

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

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
