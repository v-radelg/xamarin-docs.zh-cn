---
title: 在 Android 上的 WebView 缩放
description: 平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。 本文介绍如何使用 Android-特定于平台，使 web 视图上的缩放比例。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 0e180ca5019bd4e5d1351cfb2f7a8c28ade2afe2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971639"
---
# <a name="webview-zoom-on-android"></a>在 Android 上的 WebView 缩放

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 Android-特定于平台上启用捏合缩放和缩放控件[ `WebView` ](xref:Xamarin.Forms.WebView)。 设置使用在 XAML`WebView.EnableZoomControls`并`WebView.DisplayZoomControls`到可绑定属性`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

`WebView.EnableZoomControls`可绑定属性控制是否启用捏合缩放[ `WebView` ](xref:Xamarin.Forms.WebView)，并`WebView.DisplayZoomControls`可绑定属性控制是否上覆盖缩放控件`WebView`。

或者，可以从使用特定于平台的C#使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法指定仅将在 Android 上运行此特定于平台的。 `WebView.EnableZoomControls`方法，请在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空间，用于控制是否在启用捏合缩放[ `WebView` ](xref:Xamarin.Forms.WebView)。 `WebView.DisplayZoomControls`方法，在相同的命名空间，用于控制是否上覆盖缩放控件`WebView`。 此外，`WebView.ZoomControlsEnabled`和`WebView.ZoomControlsDisplayed`方法可以用于返回是否启用了捏合缩放和缩放控件，分别。

结果是在上启用该捏合缩放[ `WebView` ](xref:Xamarin.Forms.WebView)，缩放控件可以是上并叠加到`WebView`:

[![在 Android 上的缩放 web 视图的屏幕截图](webview-zoom-controls-images/webview-zoom.png "放大 WebView")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "放大 WebView")

> [!IMPORTANT]
> 缩放控件必须启用并显示通过相应的可绑定属性或方法，以便上叠加[ `WebView` ](xref:Xamarin.Forms.WebView)。

## <a name="related-links"></a>相关链接

- [PlatformSpecifics （示例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [创建平台特定信息](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
