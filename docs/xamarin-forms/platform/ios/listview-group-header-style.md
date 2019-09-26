---
title: IOS 上的 ListView 组标头样式
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制在滚动过程中 ListView 标头单元格是否浮动。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "68648323"
---
# <a name="listview-group-header-style-on-ios"></a>IOS 上的 ListView 组标头样式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制在滚动[`ListView`](xref:Xamarin.Forms.ListView)过程中标题单元是否浮动。 它通过将可`ListView.GroupHeaderStyle`绑定的属性设置为`GroupHeaderStyle`枚举的值，在 XAML 中使用：

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

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 命名空间`ListView.SetGroupHeaderStyle`中的方法用于控制标题单元是否[`ListView`](xref:Xamarin.Forms.ListView)在滚动过程中浮动。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) `GroupHeaderStyle`枚举提供了两个可能的值：

- `Plain`–指示在滚动时[`ListView`](xref:Xamarin.Forms.ListView)标题单元浮动（默认值）。
- `Grouped`–指示在滚动时[`ListView`](xref:Xamarin.Forms.ListView) ，标头单元格不浮动。

此外， `ListView.GetGroupHeaderStyle`方法可用于`GroupHeaderStyle`返回应用于的[`ListView`](xref:Xamarin.Forms.ListView)。

结果是`GroupHeaderStyle` [`ListView`](xref:Xamarin.Forms.ListView)，将指定的值应用于，后者控制在滚动过程中标题单元格是否浮动：

[![IOS 上的浮动和非浮动 ListView 标题单元的屏幕截图](listview-group-header-style-images/group-header-styles.png "包含浮动和非浮动标题单元格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "包含浮动和非浮动标题单元格的 ListView")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
