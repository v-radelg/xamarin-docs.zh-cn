---
title: IOS 11 中的 WebKit 和 Safari 更改
description: 本文档讨论对 iOS 11 中的 WebKit 和 Safari 服务框架所做的更改。 它介绍了如何在 SFSafariViewController 中使用样式更新和 WKWebView 中的新功能。
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/12/2017
ms.openlocfilehash: c52ac3c0f06d58ab5fff8228ca3bdf722056b5b6
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663442"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>IOS 11 中的 WebKit 和 Safari 更改

iOS 11 引入了新版本的 Safari web 浏览器– Safari 11.0 –其中包括对 WebKit 和 SafariServices 的更改。 本指南将探讨这些更改。

## <a name="safariservices"></a>SafariServices

在 iOS 9 中引入 `SFSafariViewController` 作为显示 web 内容或从应用对用户进行身份验证的选项。 有关其功能的详细信息，请参阅[Web 视图](~/ios/user-interface/controls/webview.md#sfsafariviewcontroller)指南。

iOS 11 引入了对 Safari 视图控制器的样式更新，使用户能够在应用程序和 web 之间获得更流畅的体验。 例如，删除地址栏现在为 Safari 视图控制器提供应用内浏览器的外观，而不是小型浏览器。 还可以通过设置 "`preferredBarTintColor`" 和 "`PreferredControlTintColor`" 属性，自定义颜色方案，使其适合应用的配色方案：

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

下面的代码段以紫色和白色呈现条形，如下图所示：

![以紫色和白色呈现的 SFSafariViewController 栏](web-images/image1.png)

还可以通过将 "`DismissButtonStyle`" 属性设置为 "`Done`"、"`Close`" 或 "`Cancel`" 来更改 Safari 视图控制器中显示的 "解除" 按钮：

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![消除按钮文本更改](web-images/image2.png)

显示 `SFSafariViewController` 时，可以更改此值。

根据在 Safari 视图控制器内显示的内容，可能需要确保在用户滚动时菜单栏不会折叠。 为此，可将新的 `BarCollapsedEnabled` 属性设置为 `false`：

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![已禁用条折叠](web-images/image3.png)

Apple 还在 iOS 11 中更新了 Safari 视图控制器中的隐私。 现在，浏览 cookie 和本地存储等数据仅存在于每个应用程序的基础上，而不是在 Safari 视图控制器的所有实例中存在。 这会使用户浏览活动在应用中保持私有。

其他功能（如对 Url 的拖放支持以及对 `window.open()` 的支持也已添加到 iOS 11 `SFSafariViewController`。 可在[Apple SFSafariViewController 文档](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor)中找到有关这些新功能的详细信息。

## <a name="webkit"></a>WebKit

`WKWebView` 在 iOS 8 中作为 WebKit 的一部分引入，作为向用户显示 web 内容的一种方式。 与 `SFSafariViewController`相比，它的自定义性要多得多，因此您可以创建自己的导航界面和用户界面。

Apple 在 iOS 11 中引入了三项 `WKWebView` 的主要改进： 

- 管理 cookie 的功能
- 内容筛选
- 自定义资源加载

Cookie 管理通过新的[`WKHttpCookieStore`](https://developer.apple.com/documentation/webkit/wkhttpcookiestore)类完成，此类允许你添加和删除 cookie，获取存储在 WKWebView 中的所有 cookie，并观察 cookie 存储区中的更改。

内容筛选使你能够管理你的用户将看到的内容类型，使你可以确保它是安全的，并且在必要的情况下，仅供一组选择的用户使用。 这是通过新的[`WKContentRuleList`](https://developer.apple.com/documentation/webkit/wkcontentrulelist)类实现的，通过提供 JSON 中的触发器和操作对。 有关这些触发器和操作的详细信息，请参阅 Apple 的[内容阻止规则](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html)指南。

iOS 11 现在允许你自定义 `WKWebView`，并为 web 内容自定义资源加载。 这是通过 `IWKUrlSchemeHandler` 接口实现的，它使你能够处理不是本机到 Web 工具包的 URL 方案。 此接口具有必须实现的启动和停止方法：

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

实现该处理程序后，使用它来设置 `WKWebViewConfiguration`上的 `SetUrlSchemeHandler` 属性。 然后，加载使用自定义方案的某些内容的 URL：

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```
