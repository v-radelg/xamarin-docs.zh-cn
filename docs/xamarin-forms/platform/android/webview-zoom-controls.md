---
title: Android 上的 Web 视图缩放
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的, 该平台可对 Web 视图进行缩放。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656010"
---
# <a name="webview-zoom-on-android"></a>Android 上的 Web 视图缩放

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定平台支持 "缩小到缩放" 和 "缩放" [`WebView`](xref:Xamarin.Forms.WebView)控件。 它通过将`WebView.EnableZoomControls`和可绑定属性设置为`WebView.DisplayZoomControls` `boolean`值, 在 XAML 中使用:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

可绑定属性控制是否[`WebView`](xref:Xamarin.Forms.WebView)在上启用了 "缩小到缩放", 并`WebView.DisplayZoomControls` `WebView`控制是否在上覆盖缩放控件。 `WebView.EnableZoomControls`

或者, 可以C#使用 Fluent API 使用特定于平台的:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 命名空间`WebView.EnableZoomControls`中的方法用于控制是否在上启用了 "缩小到缩放" [`WebView`。](xref:Xamarin.Forms.WebView) [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 同一`WebView.DisplayZoomControls`命名空间中的方法用于控制缩放控件是否`WebView`在上重叠。 此外, `WebView.ZoomControlsEnabled`和`WebView.ZoomControlsDisplayed`方法可用于分别返回是否启用了 "缩小到缩放" 和 "缩放" 控件。

因此[`WebView`](xref:Xamarin.Forms.WebView), 可以在上启用 "缩小到缩放" `WebView`, 并在上重叠缩放控件:

[Android(webview-zoom-controls-images/webview-zoom.png "放大的 web 视图")![中放大的 Web 视图的屏幕截图]](webview-zoom-controls-images/webview-zoom-large.png#lightbox "缩放的 Web 视图")

> [!IMPORTANT]
> 缩放控件必须通过各自的可绑定属性或方法启用和显示, 才能在上[`WebView`](xref:Xamarin.Forms.WebView)重叠。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
