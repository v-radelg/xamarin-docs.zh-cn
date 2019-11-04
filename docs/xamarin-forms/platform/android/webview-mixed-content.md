---
title: Android 上的 Web 视图混合内容
description: 平台说明允许使用仅在特定平台上可用的功能，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android 平台特定的，在面向 API 21 或更高版本的应用程序中显示 Web 视图中的混合内容。
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: f9b4a12d3049cea37235cc04805a7411a9bdb236
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696493"
---
# <a name="webview-mixed-content-on-android"></a>Android 上的 Web 视图混合内容

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平台特定控制[`WebView`](xref:Xamarin.Forms.WebView)是否可以在面向 API 21 或更高版本的应用程序中显示混合内容。 混合内容是最初通过 HTTPS 连接加载的内容，但它通过 HTTP 连接加载资源（例如图像、音频、视频、样式表和脚本）。 它通过将[`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加属性设置为[`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)枚举的值，在 XAML 中使用：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，可以C#使用 Fluent API：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>` 方法指定此平台特定的仅在 Android 上运行。 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间中的[`WebView.SetMixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法用于控制是否可以显示混合内容， [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)枚举提供三个可能的值：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) –表示[`WebView`](xref:Xamarin.Forms.WebView)允许 HTTPS 源从 HTTP 源加载内容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) –指示[`WebView`](xref:Xamarin.Forms.WebView)不允许 HTTPS 源从 HTTP 源加载内容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) –表示[`WebView`](xref:Xamarin.Forms.WebView)将尝试与最新设备 web 浏览器的方法兼容。 某些 HTTP 内容可能允许通过 HTTPS 源加载，其他类型的内容将被阻止。 每个操作系统版本可能会更改被阻止或允许的内容类型。

结果是将指定[`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值应用于[`WebView`](xref:Xamarin.Forms.WebView)，该控制是否可以显示混合内容：

[![Web 视图混合内容处理平台特定的](webview-mixed-content-images/webview-mixedcontent.png "Web 视图混合内容处理平台特定的")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Web 视图混合内容处理平台特定的")

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
