---
title: IOS 上的 SwipeView 滑动过渡模式
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 平台特定的来控制打开 SwipeView 时所使用的转换。
ms.prod: xamarin
ms.assetid: C667F24C-BAD8-47E0-9285-D3546BEF703B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: d5ba92d008cf3431bce2c197aca45c894eb3d5c7
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490450"
---
# <a name="swipeview-swipe-transition-mode-on-ios"></a>IOS 上的 SwipeView 滑动过渡模式

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平台特定控制在打开 `SwipeView`时使用的转换。 它通过将 `SwipeView.SwipeTransitionMode` 可绑定属性设置为 `SwipeTransitionMode` 枚举的值，在 XAML 中使用：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

`SwipeView.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间中的 `SwipeView.SetSwipeTransitionMode` 方法用于控制打开 `SwipeView`时使用的转换。 `SwipeTransitionMode` 枚举提供两个可能的值：

- `Reveal` 指示 `SwipeView` 内容为 "重击" 时，将显示滑动项，这是 `SwipeView.SwipeTransitionMode` 属性的默认值。
- `Drag` 指示 `SwipeView` 内容为重击，则会将滑动项拖动到视图中。

此外，`SwipeView.GetSwipeTransitionMode` 方法可用于返回应用于 `SwipeView`的 `SwipeTransitionMode`。

结果就是将指定 `SwipeTransitionMode` 值应用于 `SwipeView`，该控制打开 `SwipeView`时使用的转换：

[![IOS 上的 SwipeView SwipeTransitionModes 的屏幕截图](swipeview-swipetransitionmode-images/swipetransitionmode.png "IOS 上的 SwipeTransitionModes")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "IOS 上的 SwipeTransitionModes")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
