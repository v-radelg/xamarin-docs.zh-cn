---
title: 在 Windows 上的 ListView SelectionMode
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Windows 平台-特定控制 ListView 中的项是否可响应点击手势。
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: d2a94347448af031f50341729d77c7385225d107
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60855046"
---
# <a name="listview-selectionmode-on-windows"></a>在 Windows 上的 ListView SelectionMode

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

在通用 Windows 平台上，默认情况下，Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)使用本机`ItemClick`事件响应交互，而不是本机`Tapped`事件。 这提供了可访问性功能，以便 Windows 讲述人和键盘可以与交互`ListView`。 但是，它还会呈现在任何点击手势`ListView`不可操作。

此通用 Windows 平台特定于平台的控件是否中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可响应点击手势，并因此是否本机`ListView`激发`ItemClick`或`Tapped`事件。 设置使用在 XAML [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty)附加属性的值为[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)枚举：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法指定仅将在通用 Windows 平台上运行此特定于平台的。 [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode))方法，在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空间，是否用于控制中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可响应的点击手势，[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)枚举提供两个可能值：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – 指示`ListView`将触发本机`ItemClick`事件处理的交互，并因此提供可访问性功能。 因此，Windows 讲述人和键盘可以与交互`ListView`。 但是中的项`ListView`无法响应点击手势。 这是默认行为`ListView`通用 Windows 平台上的实例。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – 指示`ListView`将触发本机`Tapped`事件用于处理的交互。 因此中的项`ListView`可响应点击手势。 但是，没有可访问性功能，因此 Windows 讲述人和键盘不能与交互`ListView`。

> [!NOTE]
> `Accessible`并`Inaccessible`选择模式是互斥的并且将需要选择可访问[ `ListView` ](xref:Xamarin.Forms.ListView)或`ListView`可响应的点击手势。

此外， [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView}))方法可用于返回当前[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)。

结果是，指定[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)应用于[ `ListView` ](xref:Xamarin.Forms.ListView)，哪些控件是否中的项`ListView`可响应点击手势，并因此是否本机`ListView`激发`ItemClick`或`Tapped`事件。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
