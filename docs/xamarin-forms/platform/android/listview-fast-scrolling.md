---
title: 在 Android 上滚动 ListView 快速
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android-特定于平台，快速滚动 ListView 中的数据。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: d4b4a72d2bfe77c433c17fa9f427a95121855e01
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216420"
---
# <a name="listview-fast-scrolling-on-android"></a>在 Android 上滚动 ListView 快速

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 Android 特定于平台的用于启用通过中的数据快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)。 设置使用在 XAML`ListView.IsFastScrollEnabled`附加属性设置为`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `ListView.SetIsFastScrollEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于启用通过中的数据快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)。 此外，`SetIsFastScrollEnabled`方法可用于切换通过调用快速滚动`IsFastScrollEnabled`方法以返回指示是否启用快速滚动：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

结果是通过中的数据的快速滚动[ `ListView` ](xref:Xamarin.Forms.ListView)可以启用，这将更改滚动块的大小：

[![](listview-fast-scrolling-images/fastscroll.png "ListView FastScroll 平台专属")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll 特定于平台的")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
