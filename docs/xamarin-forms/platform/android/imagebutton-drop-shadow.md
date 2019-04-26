---
title: 在 Android 上的 ImageButton 投影
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android-特定于平台，使上 ImageButton 投影。
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 56415aff251149aee01c2e2eb7e335e157180962
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293766"
---
# <a name="imagebutton-drop-shadows-on-android"></a>在 Android 上的 ImageButton 投影

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 Android 特定于平台的用于上启用投影`ImageButton`。 设置使用在 XAML`ImageButton.IsShadowEnabled`可绑定属性设置为`true`，以及数量的其他控制投影的可选可绑定属性：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> 作为的一部分绘制投影`ImageButton`如果仅绘制背景，并在后台`BackgroundColor`属性设置。 因此，投影将不绘制如果`ImageButton.BackgroundColor`属性未设置。

`ImageButton.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `ImageButton.SetIsShadowEnabled`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否在启用投影`ImageButton`。 此外，可以调用以下方法来控制投影：

- `SetShadowColor` -设置投影的颜色。 默认颜色[ `Color.Default` ](xref:Xamarin.Forms.Color.Default*)。
- `SetShadowOffset` -设置投影的偏移量。 偏移量更改阴影被强制转换，并指定为方向[ `Size` ](xref:Xamarin.Forms.Size)值。 `Size`正在向左 （负值） 或向右 （正值） 的距离的第一个值和第二个值被更高版本的距离 （负值） 或下方 （正值） 结构的值以与设备无关单位表示. 此属性的默认值为 （0.0，0.0），这会导致卷影被强制转换涉及的每个方面`ImageButton`。
- `SetShadowRadius`– 设置用于呈现阴影的模糊半径。 默认半径值为 10.0。

> [!NOTE]
> 可以通过调用查询投影的状态`GetIsShadowEnabled`， `GetShadowColor`， `GetShadowOffset`，和`GetShadowRadius`方法。

结果是，可以在启用投影`ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "带投影 ImageButton")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
