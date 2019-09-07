---
title: Xamarin 中的 Web 视图
description: 本文档介绍了 Xamarin iOS 应用可显示 web 内容的各种方式。 它讨论了 UIWebView、WKWebView、SFSafariViewController、Safari 和应用传输安全性。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: bc97f14066456a07ee7ce62131985194bbe83811
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768377"
---
# <a name="web-views-in-xamarinios"></a>Xamarin 中的 Web 视图

在 iOS 的生存期内，应用开发人员可以通过多种方式在其应用中合并 web 视图功能。 大多数用户都利用其 iOS 设备上的内置 Safari web 浏览器，因此，从其他应用程序获得的 web 视图功能与此体验一致。 它们期望使用相同的笔势，性能应为 par，并具有相同的功能。

在本文中，我们将探讨 Apple 提供的三个 Web 视图中的每`UIWebView`个`WKWebview`视图： `SFSafariViewController`、、和，它们之间的相似性和差异，以及如何使用它们。 

iOS 11 引入了对`WKWebView`和`SFSafariViewController`的新更改。 有关这些内容的详细信息，请参阅[iOS 11 指南中的 Web 更改](~/ios/platform/introduction-to-ios11/web.md)。

## <a name="uiwebview"></a>UIWebView

`UIWebView`是 Apple 在您的应用程序中提供 web 内容的传统方法。 它已在 iOS 2.0 中发布，并已在8.0 后弃用。

如果计划支持早于8.0 的 iOS 版本，则必须使用`UIWebView`。 `UIWebView`由于对性能的优化比替代方法更少，因此建议你检查用户的 iOS 版本。 如果8.0 或更高版本，使用下面的其中一个选项将创建更好的用户体验。

若要将 UIWebView 添加到你的 Xamarin iOS 应用，请使用以下代码：

```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

这会生成以下 web 视图：

[![](uiwebview-images/webview.png "ScalesPagesToFit 的效果")](uiwebview-images/webview.png#lightbox)

有关使用`UIWebView`的详细信息，请参阅以下食谱：

- [加载网页](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [加载本地内容](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [加载非 Web 文档](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView`已在 iOS 8 中引入，应用开发人员可实现类似于 mobile Safari 的 web 浏览界面。 这部分原因是， `WKWebView`使用 Nitro Javascript 引擎的事实是移动 Safari 使用的同一个引擎。 `WKWebView`应始终在 UIWebView 上使用，这是因为[性能提高](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview)、内置用户友好的手势，以及网页与应用程序之间的轻松交互。
  
`WKWebView`可以通过与 UIWebView 几乎完全相同的方式添加到你的应用程序，但作为开发人员，你可以更好地控制 UI/UX 和功能。 创建和显示 web 视图对象将显示请求的页面，但你可以控制视图的显示方式、用户可以导航的方式，以及用户如何退出视图。  

下面的代码可用于`WKWebView`在 Xamarin iOS 应用程序中启动。

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

这会生成以下 web 视图：

[![](uiwebview-images/wkwebview.png "不带 ScalesPagesToFit 的示例 web 视图")](uiwebview-images/wkwebview.png#lightbox)

请注意`WKWebView` ，在 WebKit 命名空间中，必须将此 using 指令添加到类的顶部。

`WKWebView`还可在 Xamarin 应用程序中使用，因此，如果要创建跨平台 Mac/iOS 应用，则可能需要考虑使用该应用。

[处理 Javascript 警报](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)食谱还提供有关将 WKWebView 与 JavaScript 一起使用的信息

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController`是从应用提供 web 内容并在 iOS 9 及更高版本中提供的最新方式。 与`UIWebView`或`WKWebView`不同，`SFSafariViewController`是视图控制器，因此不能与其他视图一起使用。 你应以`SFSafariViewController`新的视图控制器形式显示，其方式与显示任何视图控制器的方式相同。

 `SFSafariViewController`实质上是一个可以嵌入到应用中的 "小型 safari"。 与 WKWebView 一样，它使用相同的 Nitro Javascript 引擎，还提供了一系列附加的 Safari 功能，如自动填充、读者，以及在移动 Safari 中共享 cookie 和数据的功能。 用户和`SFSafariViewController`之间的交互对你的应用程序不可访问。 您的应用程序将不能访问任何默认的 Safari 功能。

默认情况下，它还实现 "**完成**" 按钮，使用户能够轻松返回到你的应用程序、前进和后退导航按钮，使用户能够在网页堆栈中导航。 此外，它还为用户提供了一个地址栏，使他们可以放心地使用它们。 地址栏不允许用户更改 url。 

这些实现无法更改，因此`SFSafariViewController` ，如果您的应用程序想要在不进行任何自定义的情况下显示网页，则最好将其用作默认浏览器。

下面的代码可用于`SFSafariViewController`在 Xamarin iOS 应用程序中启动。

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

这会生成以下 web 视图：

[![](uiwebview-images/sfsafariviewcontroller.png "使用 SFSafariViewController 的示例 web 视图")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

还可以通过使用以下代码，从应用内打开移动 Safari 应用：

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

这会生成以下 web 视图：

[![](uiwebview-images/safari.png "Safari 中显示的网页")](uiwebview-images/safari.png#lightbox)

通常应始终避免将用户从应用导航到 Safari。 大多数用户不需要在应用程序外导航，因此，如果离开应用，用户可能永远都不会返回它，实质上是终止了订婚。

iOS 9 改进使用户可以通过 Safari 页面左上角提供的 "后退" 按钮轻松返回到应用。

## <a name="app-transport-security"></a>应用传输安全性

应用传输安全性或*ATS*是由 Apple 在 iOS 9 中引入的，以确保所有 internet 通信都符合安全连接最佳实践。

有关 ATS 的详细信息，包括如何在应用中实现它，请参阅[应用传输安全](~/ios/app-fundamentals/ats.md)指南。

## <a name="related-links"></a>相关链接

- [Webview （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
