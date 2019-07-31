---
title: IOS 上的 ListView 行动画
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制在更新 ListView 项集合时是否禁用行动画。
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 9e44c22e670847102cf0bc0f79a860abcac30760
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652837"
---
# <a name="listview-row-animations-on-ios"></a>IOS 上的 ListView 行动画

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制在更新[`ListView`](xref:Xamarin.Forms.ListView)项集合时是否禁用行动画。 设置使用在 XAML`ListView.RowAnimationsEnabled`可绑定属性设置为`false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
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

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 命名空间`ListView.SetRowAnimationsEnabled`中的方法用于控制在更新[`ListView`](xref:Xamarin.Forms.ListView)项集合时是否禁用行动画。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 此外, `ListView.GetRowAnimationsEnabled`方法可用于返回是否`ListView`在上禁用了行动画。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)默认情况下启用行动画。 因此, 当将新行插入到`ListView`时, 会发生动画。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
