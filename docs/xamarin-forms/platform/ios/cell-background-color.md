---
title: IOS 上的单元格背景色
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来设置 iOS 上单元格的默认背景色。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651379"
---
# <a name="cell-background-color-on-ios"></a>IOS 上的单元格背景色

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定的设置[`Cell`](xref:Xamarin.Forms.Cell)实例的默认背景色。 它通过将`Cell.DefaultBackgroundColor` [`Color`](xref:Xamarin.Forms.Color)可绑定的属性设置为来在 XAML 中使用:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
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

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 命名空间`Cell.SetDefaultBackgroundColor`中的方法将单元格背景色设置为指定[`Color`](xref:Xamarin.Forms.Color)的。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 此外, 该`Cell.DefaultBackgroundColor`方法可用于检索当前单元格背景色。

结果就是, [`Cell`](xref:Xamarin.Forms.Cell)可以将中的背景色设置为特定: [`Color`](xref:Xamarin.Forms.Color)

[IOS(cell-background-color-images/group-header-cell-color.png "ListView 上带有青色组标题单元格")的![青色组头单元的屏幕截图]](cell-background-color-images/group-header-cell-color-large.png#lightbox "带有蓝绿色组头单元格的 ListView")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
