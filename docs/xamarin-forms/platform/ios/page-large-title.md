---
title: 在 iOS 上的大型页标题
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用平台特定于 iOS 的 NavigationPage 的导航栏上的大型标题显示的页面标题。
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ad1c7f94467c6f32b9108ab7f6abe85d31679d3a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952115"
---
# <a name="large-page-titles-on-ios"></a>在 iOS 上的大型页标题

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台特定用于显示页面标题为的导航栏上的大型标题[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，对于使用 iOS 11 或更高版本的设备。 大型标题左对齐和使用的较大的图标，并将转换为标准标题当用户开始滚动的内容，以便有效地使用屏幕空间。 但是，在横向方向，标题将返回到导航栏来优化内容布局的中心。 设置使用在 XAML`NavigationPage.PrefersLargeTitles`附加属性设置为`boolean`值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `NavigationPage.SetPrefersLargeTitle`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中，控制是否启用大标题。

前提是大标题上启用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，在导航堆栈中的所有页面将都显示大标题。 可以通过设置页上写此行为`Page.LargeTitleDisplay`附加属性的值为`LargeTitleDisplayMode`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

或者，可以从使用 fluent API 通过 C# 重写页面行为：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

`Page.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Page.SetLargeTitleDisplay`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，在控制大标题行为[ `Page` ](xref:Xamarin.Forms.Page)，与`LargeTitleDisplayMode`提供三个可能的枚举值：

- `Always` – 强制的导航栏和字体大小，以使用较大的格式。
- `Automatic` – 相同的样式 （大或小） 用作导航堆栈中的上一项。
- `Never` – 强制使用正则、 小型格式导航栏。

此外，`SetLargeTitleDisplay`方法可用于切换的枚举值通过调用`LargeTitleDisplay`方法，返回当前`LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

结果是，指定`LargeTitleDisplayMode`应用于[ `Page` ](xref:Xamarin.Forms.Page)，它可以控制大标题行为：

![](page-large-title-images/large-title.png "模糊效果特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
