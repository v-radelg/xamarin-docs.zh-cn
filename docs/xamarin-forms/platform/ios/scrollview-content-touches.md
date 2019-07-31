---
title: ScrollView 内容涉及 iOS
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用特定于 iOS 平台的来控制 ScrollView 是否处理触摸手势或将其传递给其内容。
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 154666cce4ad6c53949952fa93f5ad7dc89824ab
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651764"
---
# <a name="scrollview-content-touches-on-ios"></a>ScrollView 内容涉及 iOS

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

触摸手势中开始时触发的隐式计时器[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)在 iOS 上和`ScrollView`决定是否应处理手势，也可以将其传递给其内容基于计时器的范围内中的用户操作。 默认情况下，iOS`ScrollView`延迟内容的收尾工作了，但这可能会问题导致在某些情况下使用`ScrollView`不应获胜手势的内容。 因此，此特定于平台的控件是否`ScrollView`处理触摸手势或将其传递给其内容。 设置使用在 XAML`ScrollView.ShouldDelayContentTouches`附加属性设置为`boolean`值：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 `ScrollView.SetShouldDelayContentTouches`方法，请在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，是否用于控制[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)处理触摸手势或将其传递给其内容。 此外，`SetShouldDelayContentTouches`方法可用于切换通过调用延迟内容收尾工作了`ShouldDelayContentTouches`方法以返回是否延迟内容收尾工作了：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

结果是， [ `ScrollView` ](xref:Xamarin.Forms.ScrollView)可以禁用延迟接收内容收尾工作了，因此，在这种情况下[ `Slider` ](xref:Xamarin.Forms.Slider)接收手势而不是[ `Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)页的[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "ScrollView 延迟内容涉及特定于平台的")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView 延迟内容涉及特定于平台的")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
