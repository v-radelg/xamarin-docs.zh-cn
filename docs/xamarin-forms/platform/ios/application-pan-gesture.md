---
title: 在 iOS 上的同时进行平移手势识别
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 iOS 特定于平台的同时进行的平移手势识别要使用的应用程序中。
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 4587bb89ddfe43873e666a07514075f1a952e985
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926778"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>在 iOS 上的同时进行平移手势识别

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

当[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)附加到内部滚动视图中，所有平移手势捕获的一个视图，`PanGestureRecognizer`并不会传递给滚动视图。 因此，将无法再滚动滚动视图。

此平台特定于 iOS 的使`PanGestureRecognizer`中滚动视图来捕获和共享平移手势与滚动视图。 设置使用在 XAML [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty)附加到属性`true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>`方法指定仅将在 iOS 上运行此特定于平台的。 [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean))方法，在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空间，用于控制是否在滚动视图中的平移手势识别程序将捕获平移手势，或捕获并共享平移手势与滚动视图。 此外， [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application}))方法可用于返回是否与包含的滚动视图共享平移手势[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)。

因此，对于此特定于平台的启用，当[ `ListView` ](xref:Xamarin.Forms.ListView)包含[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)，这两个`ListView`和`PanGestureRecognizer`将收到平移手势和对其进行处理。 但是，对于此特定于平台的禁用状态，当`ListView`包含`PanGestureRecognizer`，则`PanGestureRecognizer`将捕获平移手势并处理它，并`ListView`不会收到平移手势。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
