---
title: 在 iOS 上的 ListView 行动画
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 特定于平台的用于控制更新 ListView 项集合时是否禁用行动画。
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 50480f5b21c6f0c855ff6f9aa22b6126c6a6787c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945681"
---
# <a name="listview-row-animations-on-ios"></a>在 iOS 上的 ListView 行动画

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 特定于平台的控件是否行动画时禁用[ `ListView` ](xref:Xamarin.Forms.ListView)正在更新项集合。 设置使用在 XAML`ListView.RowAnimationsEnabled`可绑定属性设置为`false`:

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

`ListView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `ListView.SetRowAnimationsEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制行动画是否后将被禁用[ `ListView` ](xref:Xamarin.Forms.ListView)正在更新项集合。 此外，`ListView.GetRowAnimationsEnabled`方法可用于返回是否被禁用，行动画`ListView`。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) 默认情况下启用行动画。 因此，动画会出现一个新行插入到中时`ListView`。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
