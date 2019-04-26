---
title: 在 iOS 上的滑块 Thumb 点击
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 特定于平台的 Slider.Value 属性将能够通过点击滑动条设置。
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 43cc87f9d319295ce65d55488e1be032ae00a697
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082166"
---
# <a name="slider-thumb-tap-on-ios"></a>在 iOS 上的滑块 Thumb 点击

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此平台特定于 iOS 的使[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性可以通过点击对一个位置上设置[ `Slider` ](xref:Xamarin.Forms.Slider)栏中，而不是按无需将`Slider`thumb。 设置使用在 XAML [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty)可绑定属性设置为`true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

`Slider.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否在点击`Slider`条将设置[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性。 此外， [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider}))方法可用于返回是否上点击`Slider`条将设置`Slider.Value`属性。

结果是，在点击[ `Slider` ](xref:Xamarin.Forms.Slider)栏可以移动`Slider`thumb 和设置[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)属性：

![](slider-thumb-images/slider-updateontap.png "在启用了点击滑块更新")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
