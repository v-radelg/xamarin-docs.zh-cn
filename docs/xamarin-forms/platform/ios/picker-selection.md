---
title: 在 iOS 上选取器项选择
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 特定于平台的用于控制项选择时选取器中。
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 21c4c289a3fd30db890be6811875412ce4913cf5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250462"
---
# <a name="picker-item-selection-on-ios"></a>在 iOS 上选取器项选择

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此平台特定于 iOS 的控件项选择中的发生时[ `Picker` ](xref:Xamarin.Forms.Picker)，这样就允许用户指定的项选择发生时浏览项在控件中，还是仅后**完成**按下按钮。 设置使用在 XAML`Picker.UpdateMode`附加属性的值为`UpdateMode`枚举：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `Picker.SetUpdateMode`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制项选择的发生时间、 与`UpdateMode`枚举提供两个可能值：

- `Immediately` – 项选择会出现在用户浏览中的项[ `Picker` ](xref:Xamarin.Forms.Picker)。 这是在 Xamarin.Forms 中的默认行为。
- `WhenFinished` – 项选择仅发生后用户已按**完成**按钮[ `Picker` ](xref:Xamarin.Forms.Picker)。

此外，`SetUpdateMode`方法可用于切换的枚举值通过调用`UpdateMode`方法，返回当前`UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

结果是，指定`UpdateMode`应用于[ `Picker` ](xref:Xamarin.Forms.Picker)，它可以控制项选择发生时：

[![](picker-selection-images/picker-updatemode.png "选取器 UpdateMode 平台专属")](picker-selection-images/picker-updatemode-large.png#lightbox "选取器 UpdateMode 特定于平台的")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
