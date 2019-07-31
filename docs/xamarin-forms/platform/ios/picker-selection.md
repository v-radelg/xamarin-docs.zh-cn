---
title: IOS 上的选取器项选择
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制选取器中发生项目选择的时间。
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 57420921100c99db1e2c3a5259ece30cfda719f2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651843"
---
# <a name="picker-item-selection-on-ios"></a>IOS 上的选取器项选择

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定的控件[`Picker`](xref:Xamarin.Forms.Picker): 当项在中发生选择时, 允许用户指定在浏览控件中的项时或在按 "**完成**" 按钮后所做的选择。 设置使用在 XAML`Picker.UpdateMode`附加属性的值为`UpdateMode`枚举：

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

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
