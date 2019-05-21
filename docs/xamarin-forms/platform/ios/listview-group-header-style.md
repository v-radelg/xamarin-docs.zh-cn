---
title: 在 iOS 上的 ListView 组标头样式
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 特定于平台的用于控制是否在滚动过程 float ListView 标头单元格。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: e2f6a402d1df8b842e147ab145474688c203aaa6
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925865"
---
# <a name="listview-group-header-style-on-ios"></a>在 iOS 上的 ListView 组标头样式

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此 iOS 特定于平台的控件是否[ `ListView` ](xref:Xamarin.Forms.ListView)标头单元格浮动在滚动过程。 设置使用在 XAML`ListView.GroupHeaderStyle`可绑定属性的值的`GroupHeaderStyle`枚举：

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

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `ListView.SetGroupHeaderStyle`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，是否用于控制[ `ListView` ](xref:Xamarin.Forms.ListView)标头单元格浮动在滚动过程。 `GroupHeaderStyle`枚举提供两个可能值：

- `Plain` – 指示标头单元格 float 何时[ `ListView` ](xref:Xamarin.Forms.ListView)是滚动 （默认值）。
- `Grouped` – 指示标头单元格不浮动何时[ `ListView` ](xref:Xamarin.Forms.ListView)滚动。

此外，`ListView.GetGroupHeaderStyle`方法可用于返回`GroupHeaderStyle`应用于[ `ListView` ](xref:Xamarin.Forms.ListView)。

结果是，指定`GroupHeaderStyle`值应用于[ `ListView` ](xref:Xamarin.Forms.ListView)，它可以控制标头单元格是否浮动在滚动过程：

[![浮点型和非浮动 ListView 标头单元格，在 iOS 上的屏幕截图](listview-group-header-style-images/group-header-styles.png "浮点型和非浮点的标头单元格与 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "使用浮点型和非浮点的标头单元格的 ListView")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
