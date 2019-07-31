---
title: Android 上的 Web 视图混合内容
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何在面向 API 21 或更高版本的应用程序中使用在 Web 视图中显示混合内容的 Android 平台特定的。
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 286a7dceead327d727110d4ebbcecbc2341345b3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656073"
---
# <a name="webview-mixed-content-on-android"></a>Android 上的 Web 视图混合内容

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定的控制是否[`WebView`](xref:Xamarin.Forms.WebView)可以在面向 API 21 或更高版本的应用程序中显示混合内容。 混合的内容是的内容的最初已加载，通过 HTTPS 连接，但这会通过 HTTP 连接加载资源 （如图像、 音频、 视频、 样式表、 脚本）。 设置使用在 XAML [ `WebView.MixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加属性的值为[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)枚举：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，可以使用它从 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法，在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否可以显示混合的内容，与[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)提供三个可能值的枚举：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将允许从 HTTP 源加载内容的 HTTPS 原点。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将不允许 HTTPS origin 从 HTTP 源加载内容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – 指示[ `WebView` ](xref:Xamarin.Forms.WebView)将尝试与最新的设备 web 浏览器的方法兼容。 可能允许某些 HTTP 内容加载的 HTTPS origin 和其他类型的内容将被阻止。 阻止或允许的内容的类型可能会随每个操作系统版本。

结果是，指定[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值应用于[ `WebView` ](xref:Xamarin.Forms.WebView)，它可以控制是否可以显示混合的内容：

[![WebView 混合内容处理特定于平台](webview-mixed-content-images/webview-mixedcontent.png "WebView 混合内容处理特定于平台")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView 混合内容处理特定于平台")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
