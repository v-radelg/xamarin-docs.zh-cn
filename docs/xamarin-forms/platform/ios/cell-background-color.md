---
title: 在 iOS 上的单元格背景色
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用平台特定于 iOS 的 ios 设备上设置单元格的默认背景色。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 6b1e2fe534c8b7d0c3346a18d1b82d797e52dba1
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926764"
---
# <a name="cell-background-color-on-ios"></a>在 iOS 上的单元格背景色

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此 iOS 平台特定设置的默认背景色[ `Cell` ](xref:Xamarin.Forms.Cell)实例。 设置使用在 XAML`Cell.DefaultBackgroundColor`可绑定属性设置为[ `Color` ](xref:Xamarin.Forms.Color):

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

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Cell.SetDefaultBackgroundColor`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中，将单元格背景色设置为指定[ `Color` ](xref:Xamarin.Forms.Color)。 此外，`Cell.DefaultBackgroundColor`方法可以用于检索当前的单元格背景色。

结果是，中的背景色[ `Cell` ](xref:Xamarin.Forms.Cell)可以设置为特定[ `Color` ](xref:Xamarin.Forms.Color):

[![青色组标头单元格，在 iOS 上的屏幕截图](cell-background-color-images/group-header-cell-color.png "青色组标头单元格与 ListView")](cell-background-color-images/group-header-cell-color-large.png#lightbox "青色组标头单元格与 ListView")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
