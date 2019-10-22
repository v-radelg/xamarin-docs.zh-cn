---
title: Android 上的 Web 视图缩放
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，该平台可对 Web 视图进行缩放。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656010"
---
# <a name="webview-zoom-on-android"></a>Android 上的 Web 视图缩放

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定适用于[`WebView`](xref:Xamarin.Forms.WebView)上的 "缩放" 和 "缩放" 控件。 它通过将 `WebView.EnableZoomControls` 和 `WebView.DisplayZoomControls` 可绑定属性设置为 `boolean` 值，在 XAML 中使用：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

@No__t_0 的可绑定属性控制是否在[`WebView`](xref:Xamarin.Forms.WebView)上启用了 "缩小到缩放"，以及 "`WebView.DisplayZoomControls` 可绑定" 属性控制是否在 `WebView` 上覆盖缩放控件。

或者，可以C#使用 Fluent API 使用特定于平台的：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

@No__t_0 方法指定此平台特定的仅在 Android 上运行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间中的 `WebView.EnableZoomControls` 方法用于控制是否对[`WebView`](xref:Xamarin.Forms.WebView)启用 "缩小到缩放"。 同一命名空间中的 `WebView.DisplayZoomControls` 方法用于控制是否在 `WebView` 上重叠缩放控件。 此外，`WebView.ZoomControlsEnabled` 和 `WebView.ZoomControlsDisplayed` 方法可用于分别返回是否启用了 "缩小到缩放" 和 "缩放" 控件。

因此，可以在[`WebView`](xref:Xamarin.Forms.WebView)上启用 "缩小到缩放"，并在 `WebView` 上重叠缩放控件：

[![Android 上缩放的 Web 视图的屏幕截图](webview-zoom-controls-images/webview-zoom.png "缩放的 Web 视图")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "缩放的 Web 视图")

> [!IMPORTANT]
> 缩放控件必须通过各自的可绑定属性或方法启用和显示，才能在[`WebView`](xref:Xamarin.Forms.WebView)上重叠。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
