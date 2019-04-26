---
title: 在 Xamarin.iOS 中的 web 视图
description: 本文档介绍 Xamarin.iOS 应用程序可以在显示 web 内容的各种方法。 它讨论 UIWebView、 WKWebView、 SFSafariViewController、 Safari 和应用程序传输安全。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9adf514e4fc510617e3f4d801569935ee4a03f25
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093750"
---
# <a name="web-views-in-xamarinios"></a>在 Xamarin.iOS 中的 web 视图

IOS 的生存期内 Apple 已发布应用程序开发人员将在其应用中的 web 视图功能的方法的数。 大多数用户使用其 iOS 设备上的内置 Safari web 浏览器，并因此期望从其他应用的 web 视图功能是与此体验相一致。 他们希望相同的手势，若要运行 par 和功能上是相同的性能。

在本文中，我们将探讨每个由 Apple 提供的三个 Web 视图： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似之处和差异，以及如何使用它们。 

iOS 11 引入了新的更改，同时`WKWebView`和`SFSafariViewController`。 有关详细信息，请参阅[Web iOS 11 指南中的更改](~/ios/platform/introduction-to-ios11/web.md)指南。

## <a name="uiwebview"></a>UIWebView

`UIWebView` 是提供应用程序中的 web 内容的 Apple 的旧方法。 它在 2.0 中，iOS 中发布和 8.0 开始已弃用。

如果您计划支持 iOS 版本早于 8.0，您将必须使用`UIWebView`。 原因在于`UIWebView`更少的性能最佳，比起其他方案，建议应检查用户的 iOS 版本。 如果它 8.0 或更高版本，使用任一选项的说明如下将创建更好的用户体验。
 
若要向 Xamarin.iOS 应用添加 UIWebView，请使用以下代码：
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

这将产生以下 web 视图：

[![](uiwebview-images/webview.png "ScalesPagesToFit 的效果")](uiwebview-images/webview.png#lightbox)

有关详细信息使用`UIWebView`，请参阅以下方案：


- [加载 Web 页](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [加载本地内容](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [加载非 Web 文档](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView` 在 iOS 8 让应用程序开发人员实现的 web 浏览接口类似于移动 Safari 中引入。 这是因为，在情况下，这一事实，`WKWebView`使用 Nitro Javascript 引擎，使用移动 Safari 的相同引擎。 `WKWebView` 应始终使用的 over UIWebView 已可能因[更高的性能](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview)，内置的用户友好的手势，并且简化了 web 页面和您的应用程序之间的交互。
  
`WKWebView` 可以将添加到您的应用程序几乎完全相同的方式与 UIWebView，但是作为开发人员具有更多控制的 UI/用户体验和功能。 创建并显示 web 视图对象将显示请求的页面，但您可以控制如何显示视图、 如何用户可以导航，以及如何在用户退出该视图。  

下面的代码可用于启动`WKWebView`Xamarin.iOS 应用程序中：

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

这将产生以下 web 视图：

[![](uiwebview-images/wkwebview.png "而无需 ScalesPagesToFit 示例 web 视图")](uiwebview-images/wkwebview.png#lightbox)

务必要注意`WKWebView`为 WebKit 命名空间，因此必须添加以下 using 指令到您的类的顶部。

`WKWebView` 此外可以使用 Xamarin.Mac 应用中，并因此可能需要考虑使用它，如果要创建跨平台 Mac/iOS 应用。

[处理 JavaScript 警报](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)方案还介绍了在 Javascript 中使用 WKWebView

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` 提供从您的应用程序的 web 内容的最新方法，可在 iOS 9 及更高版本。 与不同`UIWebView`或`WKWebView`，`SFSafariViewController`是视图控制器，因此不能使用与其他视图。 应存在`SFSafariViewController`为新视图控制器，相同的方式将提供的任何视图控制器。
 
 `SFSafariViewController` 是实质上是最小 safari，可以嵌入到您的应用程序。 如 WKWebView 它使用相同的 Nitro Javascript 引擎，但还提供了一系列其他 Safari 功能，例如自动填充、 读取器，并能够与移动 Safari 共享 cookie 和数据。 用户之间的交互和`SFSafariViewController`不能访问您的应用程序。 您的应用程序将没有任何默认 Safari 功能的访问权限。
 
它还，默认情况下，实现**完成**按钮，允许用户轻松地返回到你的应用，并前进和后退导航按钮，允许用户通过 web 页面堆栈的导航。 此外，它还提供用户的地址栏为他们提供预期的 web 页面上的高枕无忧。 在地址栏不允许用户更改 url。 

这些实现不能更改，因此`SFSafariViewController`是用作默认浏览器，如果希望显示网页而无需进行任何自定义您的应用程序的理想之选。

下面的代码可用于启动`SFSafariViewController`Xamarin.iOS 应用程序中：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

这将产生以下 web 视图：

[![](uiwebview-images/sfsafariviewcontroller.png "使用 SFSafariViewController 示例 web 视图")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

还有可能打开移动 Safari 应用从在应用中，通过使用下面的代码：

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

这将产生以下 web 视图：

[![](uiwebview-images/safari.png "在 Safari 中显示的网页")](uiwebview-images/safari.png#lightbox)

导航用户离开您的应用程序到 Safari 通常应当始终避免。 大多数用户不希望导航外部应用程序中，因此，如果导航离开您的应用程序时，用户可能永远不会返回它，实质上终止 engagement。

iOS 9 改进允许用户轻松地返回到 Safari 页左上角中提供了一个后退按钮通过对应用程序。

## <a name="app-transport-security"></a>应用传输安全性

应用程序传输安全模式，或*ATS*引入 apple 在 iOS 9 以确保所有 internet 通信都符合以确保连接安全最佳实践。

有关 ATS 的详细信息，包括如何实现在应用中，请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)指南。

## <a name="related-links"></a>相关链接

- [WebViews （示例）](https://developer.xamarin.com/samples/monotouch/WebView/)
