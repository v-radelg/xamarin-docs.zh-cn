---
title: IOS 上的 ListView 组标头样式
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制在滚动过程中 ListView 标头单元格是否浮动。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648323"
---
# <a name="listview-group-header-style-on-ios"></a>IOS 上的 ListView 组标头样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制在滚动期间[`ListView`](xref:Xamarin.Forms.ListView)标题单元是否浮动。 它通过将 `ListView.GroupHeaderStyle` 可绑定属性设置为 `GroupHeaderStyle` 枚举的值，在 XAML 中使用：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，可以C#使用 Fluent API：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

`ListView.On<iOS>` 方法指定此平台特定的仅在 iOS 上运行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中的 `ListView.SetGroupHeaderStyle` 方法用于控制在滚动过程中[`ListView`](xref:Xamarin.Forms.ListView)标头单元格是否浮动。 `GroupHeaderStyle` 枚举提供两个可能的值：

- `Plain` –指示在滚动[`ListView`](xref:Xamarin.Forms.ListView)时标题单元浮动（默认值）。
- `Grouped` –指示在滚动[`ListView`](xref:Xamarin.Forms.ListView)时标题单元不浮动。

此外，`ListView.GetGroupHeaderStyle` 方法可用于返回应用于[`ListView`](xref:Xamarin.Forms.ListView)的 `GroupHeaderStyle`。

结果是将指定 `GroupHeaderStyle` 值应用于 [`ListView`](xref:Xamarin.Forms.ListView)，该控制在滚动过程中是否将标题单元浮动：

[![IOS 上的浮动和非浮动 ListView 标题单元的屏幕截图](listview-group-header-style-images/group-header-styles.png "包含浮动和非浮动标题单元格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "包含浮动和非浮动标题单元格的 ListView")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
