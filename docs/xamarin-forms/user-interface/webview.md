---
title: Xamarin.Forms WebView
description: 本文介绍如何使用 Xamarin.Forms WebView 类以提供本地或网络的 web 内容和文档的用户。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: 65bcd9f534685a8a953d217d3573f5fe4b0b7400
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887920"
---
# <a name="xamarinforms-webview"></a>Xamarin.Forms WebView

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) 是应用程序中显示 web 和 HTML 内容的视图。 与不同`OpenUri`，使用户转到 web 浏览器在设备上，`WebView`显示您的应用程序中的 HTML 内容。

![](webview-images/in-app-browser.png "在应用程序浏览器中")

## <a name="content"></a>内容

`WebView` 支持以下类型的内容：

- HTML 和 CSS 的网站&ndash;WebView 具有完全支持使用 HTML 和 CSS，包括支持 JavaScript 编写的网站。
- 文档&ndash;WebView WebView 每个平台上使用本机组件实现的因为它能够显示每个平台上可查看的文档。 这意味着在 iOS 和 Android 上工作的 PDF 文件。
- HTML 字符串&ndash;web 视图可以显示内存中的 HTML 字符串。
- 本地文件&ndash;WebView 可以提供更高版本的内容类型的任何嵌入在应用程序。

> [!NOTE]
> `WebView` 在 Windows 上不支持 Silverlight、 Flash 或任何 ActiveX 控件，即使它们在该平台上支持的 Internet 资源管理器。

### <a name="websites"></a>网站

若要显示来自 internet 的网站，请设置`WebView`的[ `Source` ](xref:Xamarin.Forms.WebViewSource)属性设置为字符串 URL:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必须完全与指定的协议格式 （即它必须具有"http://"或"https://"开头加）。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自版本 9，iOS 将只允许你的应用程序与实现最佳安全性，默认情况下的服务器进行通信。 值必须设置`Info.plist`能够与不安全的服务器的通信。

> [!NOTE]
> 如果你的应用程序需要连接到不安全的网站，始终应为异常使用输入的域`NSExceptionDomains`而不是关闭 ATS 完全使用`NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 仅应在极端的紧急情况下使用。

下面演示了如何启用特定域 （在此事例 xamarin.com) 以绕过 ATS 要求：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
    ...
</key>
```

它是最佳做法，只允许某些域绕过 ATS，从而可以受益于对不受信任域的附加安全性时使用受信任的站点。 下面演示了如何不安全的应用程序禁用 ATS 方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

请参阅[应用程序传输安全](~/ios/app-fundamentals/ats.md)有关 iOS 9 中此新功能的详细信息。

### <a name="html-strings"></a>HTML 字符串

如果你想要显示的代码中动态定义的 HTML 字符串，你将需要创建的实例[ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "WebView 显示 HTML 字符串")

在上面的代码，`@`用于将 HTML 标记作为字符串文本，这意味着，将忽略所有常见的转义字符。

> [!NOTE]
> 可能需要设置`WidthRequest`的和`HeightRequest`属性[`WebView`](xref:Xamarin.Forms.WebView)以查看`WebView` HTML 内容, 具体取决于是的子布局。 例如, 在中[`StackLayout`](xref:Xamarin.Forms.StackLayout), 这是必需的。

### <a name="local-html-content"></a>本地 HTML 内容

Web 视图可以显示内容从 HTML、 CSS 和 Javascript 嵌入在应用中。 例如：

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

请注意，在上面的 CSS 中指定的字体将需要为每个平台自定义为不是每个平台都有相同的字体。

显示本地内容使用`WebView`，将需要打开任何其他 HTML 文件，然后将内容加载到字符串形式`Html`属性的`HtmlWebViewSource`。 打开文件的详细信息，请参阅[使用文件](~/xamarin-forms/data-cloud/data/files.md)。

以下屏幕截图显示在每个平台上显示本地内容的结果：

![](webview-images/local-content.png "WebView 显示本地内容")

已加载的第一页，尽管`WebView`一无所知的 HTML 原来所在的位置。 在处理引用本地资源的页面时，这是个问题。 时，可能会执行此操作的示例包括为每个其他页面使本地的页面链接使用的单独的 JavaScript 文件，或一个页面链接到 CSS 样式表时。  

若要解决此问题，需要告诉`WebView`查找文件系统上的文件位置。 会通过设置`BaseUrl`上的属性`HtmlWebViewSource`由`WebView`。

由于每个操作系统上的文件系统不同，您需要确定每个平台上的该 URL。 Xamarin.Forms 公开`DependencyService`用于解析在每个平台上运行时依赖项。

若要使用`DependencyService`，首先定义一个可以在每个平台实现的接口：

```csharp
public interface IBaseUrl { string Get(); }
```

请注意，每个平台上实现接口，直到应用程序将不会运行。 在常见的项目中，请确保你请别忘记设置`BaseUrl`使用`DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

然后必须提供每个平台接口的实现。

#### <a name="ios"></a>iOS

在 iOS 上，web 内容应位于项目的根目录中或**资源**生成操作目录*BundleResource*如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/ios-vs.png "在 iOS 上的本地文件")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](webview-images/ios-xs.png "在 iOS 上的本地文件")

-----

`BaseUrl`应设置为主要捆绑包的路径：

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

在 Android 上，HTML、 CSS 和图像的文件夹中放置资产与生成操作*AndroidAsset*如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/android-vs.png "在 Android 上的本地文件")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](webview-images/android-xs.png "在 Android 上的本地文件")

-----

在 Android 上，`BaseUrl`应设置为`"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

在 Android 上，文件中**资产**文件夹也可以访问通过当前的 Android 上下文，它公开由`MainActivity.Instance`属性：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 项目放在 HTML、 CSS 和图像中的项目根目录生成操作设置为*内容*。

`BaseUrl`应设置为`"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>导航

WebView 支持通过多种方法和属性，可通过其导航：

- **GoForward()** &ndash;如果`CanGoForward`为 true，调用`GoForward`向前导航到下一步访问过的页。
- **GoBack()** &ndash;如果`CanGoBack`为 true，调用`GoBack`将导航到访问过的最后一页。
- **CanGoBack** &ndash; `true`是否存在页导航返回到`false`如果浏览器在启动的 URL。
- **CanGoForward** &ndash; `true`如果用户已向后导航，并且可以向前移动到已访问过的页。

在页中，`WebView`不支持多点触控笔势。 务必要确保该内容是移动优化，并显示而无需缩放。

通常，应用程序以显示中的链接`WebView`，而不是设备的浏览器。 在这些情况下，最好允许正常导航栏中，但当起始链接上，则后退中的用户点击率、 应用程序应返回到正常的应用视图。

使用内置的导航方法和属性来启用此方案。

首先创建在浏览器视图的页：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

在代码隐藏：

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

就这么简单！

![](webview-images/in-app-browser.png "Web 视图的导航按钮")

## <a name="events"></a>事件

Web 视图会引发以下事件以帮助您响应状态的变化：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating)–当 Web 视图开始加载新页时引发的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated)–加载页面和导航停止时引发的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested)–发出请求以重新加载当前内容时引发的事件。

事件附带的对象具有四个属性: [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) [`Navigating`](xref:Xamarin.Forms.WebView.Navigating)

- `Cancel`-指示是否取消导航。
- `NavigationEvent`–引发的导航事件。
- `Source`–执行导航的元素。
- `Url`–导航目标。

事件附带的对象具有四个属性: [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) [`Navigated`](xref:Xamarin.Forms.WebView.Navigated)

- `NavigationEvent`–引发的导航事件。
- `Result`–介绍使用[`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult)枚举成员的导航结果。 有效值为 `Cancel`、`Failure`、`Success` 和 `Timeout`。
- `Source`–执行导航的元素。
- `Url`–导航目标。

如果预计使用需要很长时间才能加载的网页, 请考虑使用[`Navigating`](xref:Xamarin.Forms.WebView.Navigating)和[`Navigated`](xref:Xamarin.Forms.WebView.Navigated)事件来实现状态指示器。 例如:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

两个事件处理程序：

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

这会导致以下输出 （加载）：

![](webview-images/loading-start.png "WebView 导航事件示例")

已完成的加载：

![](webview-images/loading-end.png "WebView 导航事件示例")

## <a name="reloading-content"></a>重新加载内容

[`WebView`](xref:Xamarin.Forms.WebView) 具有`Reload`方法，可以用来重新加载当前内容：

```csharp
var webView = new WebView();
...
webView.Reload();
```

当`Reload`方法调用`ReloadRequested`激发事件时，指示已发出的请求来重新加载当前内容。

## <a name="performance"></a>性能

常用 web 浏览器现在采用技术，如硬件加速呈现和 JavaScript 编译。 在 iOS 上，默认情况下，Xamarin.Forms`WebView`由实现`UIWebView`类，其中许多技术是在此实现中不可用。 但是，应用程序可以选择项以使用 iOS`WkWebView`类，以实现 Xamarin.Forms `WebView`，它支持更快地浏览。 这可以通过添加以下代码来实现**AssemblyInfo.cs** iOS 平台项目中的应用程序的文件：

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> 在 iOS 上, `WkWebViewRenderer`具有`WkWebViewConfiguration`接受参数的构造函数重载。 这样, 便可以在创建时配置呈现器。

`WebView` 默认情况下在 Android 上是大约与内置浏览器一样快。

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)使用 Microsoft Edge 呈现引擎。 台式机和平板电脑设备应看到与使用 Edge 浏览器本身相同的性能。

## <a name="permissions"></a>权限

为了使`WebView`工作，您必须确保为每个平台设置权限。 请注意，在某些平台上`WebView`处于调试模式，但不是在生成要发布时将工作。 这是因为 for Mac 在调试模式下时，默认情况下，Visual studio 的某些权限，如用于在 Android 上，internet 访问权限设置。

- **UWP** &ndash;显示网络内容时需要 Internet （客户端和服务器） 功能。
- **Android** &ndash;需要`INTERNET`仅显示网络中的内容时。 本地内容需要的任何特殊权限。
- **iOS** &ndash;不需任何特殊权限。

## <a name="layout"></a>布局

与大多数其他 Xamarin.Forms 视图，不同`WebView`要求`HeightRequest`和`WidthRequest`时包含在 StackLayout 或 RelativeLayout 所指定的。 如果您不能指定这些属性，`WebView`将不会呈现。

下面的示例演示工作，所导致的布局呈现`WebView`s:

与 WidthRequest HeightRequest StackLayout:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

与 WidthRequest HeightRequest RelativeLayout:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout*而无需*WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

网格*而无需*WidthRequest & HeightRequest。 网格是一种不需要指定请求的高度和宽度的几个版式。:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>调用 JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) 包括能够调用一个 JavaScript 函数中的C#，并将任何结果返回到调用C#代码。 这通过实现[ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法，在以下示例中所示[WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)示例：

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法将评估指定为参数，并返回任何结果作为 JavaScript `string`。 在此示例中，`factorial`调用 JavaScript 函数时，它返回的阶乘`number`作为结果。 此 JavaScript 函数定义中的本地 HTML 文件[ `WebView` ](xref:Xamarin.Forms.WebView)加载，并在下面的示例所示：

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="related-links"></a>相关链接

- [使用 WebView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
