---
title: iOS 11 中的 Web 更改
description: 本文档讨论了易于使用的功能和 iOS 11 中的 Safari 服务框架所做的更改。 它介绍了如何使用 SFSafariViewController 中的更新和 WKWebView 中的新增功能设置样式。
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/12/2017
ms.openlocfilehash: ba691a6605dcf7e86a76ed13d4c8ef5f0984ff6e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399654"
---
# <a name="web-changes-in-ios-11"></a>iOS 11 中的 Web 更改

iOS 11 引入了新版本的 Safari web 浏览器 – Safari 11.0 – 其中包括易于使用的功能与 SafariServices 的变化。 本指南探讨了这些更改。

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` 中引入了 iOS 9 作为一个选项来显示 web 内容或从您的应用程序的用户进行身份验证。 其功能的详细信息可在[Web 视图](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller)指南。

引入了 iOS 11 样式更新到 Safari 视图控制器，为用户提供应用程序和 web 之间更加无缝体验。 例如，在地址栏现在提供 Safari 视图控制器的应用程序内浏览器中，而不是最小化浏览器的感觉的删除。 此外可以自定义配色方案以适应您的应用程序的配色方案，通过设置`preferredBarTintColor`和`PreferredControlTintColor`属性：

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

以下代码片段呈现紫色和白色中的条形图中所示：

![SFSafariViewController 条以紫色和白色呈现](web-images/image1.png)

此外可以通过设置更改显示在 Safari 视图控制器消除按钮`DismissButtonStyle`属性设置为任一`Done`， `Close`，或`Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![关闭更改按钮文本](web-images/image2.png)

可以更改此值时`SFSafariViewController`显示。


具体取决于 Safari 视图控制器内显示的内容，可能需要确保，当用户滚动不折叠的菜单栏。 启用此功能通过设置新`BarCollapsedEnabled`属性设置为`false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![栏折叠已禁用](web-images/image3.png)

Apple 还具有对 iOS 11 中的 Safari 视图控制器中的隐私的更新。 现在，浏览数据，例如 cookie 和本地存储仅存在根据每个应用，而不是跨 Safari 视图控制器的所有实例。 这会使用户浏览活动专用应用程序中。

其他功能如拖放支持 Url，支持`window.open()`还添加了`SFSafariViewController`iOS 11 中。 您可以找到有关中的新功能的详细信息[Apple 的 SFSafariViewController 文档](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor)。


## <a name="webkit"></a>WebKit

`WKWebView` 是作为 iOS 8 中作为一种方式向用户显示 web 内容的易于使用的功能的一部分引入。 它是比更可自定义`SFSafariViewController`，它允许您创建您自己的导航和用户界面。

Apple 引入了三个主要改进`WKWebView`ios 11: 

- 管理 cookie 的功能
- 内容筛选
- 自定义资源加载。 

Cookie 管理通过新实现[ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore)类，以便您可以添加和删除 cookie，若要获取存储在 WKWebView 中的所有 cookie，并要观察的更改将存储的 cookie。

内容筛选，您可以管理你的用户将看到的内容的类型允许您以确保它是安全的系列友好，并且如有必要，仅供选择的用户组。 可通过新实现，这[ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist)类，通过提供的 JSON 中触发器和操作对。 这些触发器和操作的详细信息可在 Apple[内容阻止规则](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html)指南。

iOS 11 现在允许你自定义`WKWebView`自定义资源加载为你的 web 内容。 这通过实现`IWKUrlSchemeHandler`接口，这样就可以处理不是本机 Web 工具包的 URL 方案。 此接口包含必须实现启动和停止方法：

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

一旦已实现该处理程序，使用它来设置`SetUrlSchemeHandler`属性上的`WKWebViewConfiguration`。 然后，将加载使用自定义方案的内容的 URL:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

