---
title: Xamarin. 窗体 Web 视图
description: 本文介绍如何使用 Xamarin web 视图类向用户显示本地或网络 web 内容和文档。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: cdb2a9f1a37dc7bca458a4291ce6fe5ac9c120aa
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696046"
---
# <a name="xamarinforms-webview"></a>Xamarin. 窗体 Web 视图

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView)是用于在应用程序中显示 WEB 和 HTML 内容的视图：

![在应用浏览器中](webview-images/in-app-browser.png)

## <a name="content"></a>内容

`WebView` 支持以下类型的内容：

- HTML & CSS 网站 &ndash; Web 视图完全支持使用 HTML & CSS （包括 JavaScript 支持）编写的网站。
- 文档 &ndash; 因为 Web 视图是使用每个平台上的本机组件实现的，所以 Web 视图能够显示可在每个平台上查看的文档。 这意味着 PDF 文件在 iOS 和 Android 上运行。
- @No__t_0 Web 视图的 HTML 字符串可以显示内存中的 HTML 字符串。
- 本地文件 &ndash; Web 视图可以提供应用中嵌入的任何内容类型。

> [!NOTE]
> Windows 上的 `WebView` 不支持 Silverlight、Flash 或任何 ActiveX 控件，即使该平台上的 Internet Explorer 支持。

### <a name="websites"></a>网站

若要从 internet 显示网站，请将 `WebView` 的[`Source`](xref:Xamarin.Forms.WebViewSource)属性设置为字符串 URL：

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必须具有指定的协议（即，必须在其前面加上 "http://" 或 "https://"）。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自第9版起，iOS 将仅允许应用程序与默认实现最佳做法安全性的服务器进行通信。 若要启用与不安全服务器的通信，必须在 `Info.plist` 中设置值。

> [!NOTE]
> 如果你的应用程序需要连接到不安全的网站，则应始终使用 `NSExceptionDomains` 将该域输入为异常，而不是完全使用 `NSAllowsArbitraryLoads` 来关闭 ATS。 `NSAllowsArbitraryLoads` 只应在极端紧急情况下使用。

下面演示了如何启用特定域（在本例中为 xamarin.com），以绕过 ATS 要求：

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

最佳做法是仅使某些域能够绕过 ATS，使你可以使用受信任的站点，同时受益不受信任的域中的其他安全性。 下面演示了禁用应用程序的 ATS 的安全方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

有关 iOS 9 中的此新功能的详细信息，请参阅[应用传输安全性](~/ios/app-fundamentals/ats.md)。

### <a name="html-strings"></a>HTML 字符串

如果要显示在代码中动态定义的 HTML 字符串，则需要创建[`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource)的实例：

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![显示 HTML 字符串的 Web 视图](webview-images/html-string.png)

在上面的代码中，`@` 用于将 HTML 标记为字符串文字，这意味着所有常用转义字符都将被忽略。

> [!NOTE]
> 可能需要将[`WebView`](xref:Xamarin.Forms.WebView)的 `WidthRequest` 和 `HeightRequest` 属性设置为查看 HTML 内容，具体取决于 `WebView` 是其子元素的布局。 例如，在[`StackLayout`](xref:Xamarin.Forms.StackLayout)中，这是必需的。

### <a name="local-html-content"></a>本地 HTML 内容

Web 视图可以显示嵌入在应用中的 HTML、CSS 和 Javascript 的内容。 例如:

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

CSS

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

请注意，需要为每个平台自定义上述 CSS 中指定的字体，因为并非每个平台都具有相同的字体。

若要使用 `WebView` 显示本地内容，需要以任何其他方式打开 HTML 文件，然后将内容作为字符串加载到 `HtmlWebViewSource` 的 `Html` 属性中。 有关打开文件的详细信息，请参阅使用[文件](~/xamarin-forms/data-cloud/data/files.md)。

以下屏幕截图显示了在每个平台上显示本地内容的结果：

![显示本地内容的 Web 视图](webview-images/local-content.png)

尽管第一页已加载，但 `WebView` 不知道 HTML 来自何处。 当处理引用本地资源的页面时，这是一个问题。 可能出现这种情况的示例包括：本地页面相互链接，页使用单独的 JavaScript 文件或页面链接到 CSS 样式表。  

若要解决此情况，需告诉 `WebView` 在何处查找文件系统上的文件。 通过设置 `WebView` 所使用的 `HtmlWebViewSource` 上的 `BaseUrl` 属性来执行此操作。

由于每个操作系统上的 filesystem 都不同，因此需要在每个平台上确定 URL。 Xamarin。 Forms 公开了用于在每个平台上的运行时解析依赖项的 `DependencyService`。

若要使用 `DependencyService`，请先定义一个可在每个平台上实现的接口：

```csharp
public interface IBaseUrl { string Get(); }
```

请注意，在每个平台上实现接口之前，应用程序将不会运行。 在通用项目中，请确保记得使用 `DependencyService` 设置 `BaseUrl`：

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

然后必须提供每个平台的接口实现。

#### <a name="ios"></a>iOS

在 iOS 上，web 内容应位于项目的根目录或包含生成操作*BundleResource*的**Resources**目录中，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![IOS 上的本地文件](webview-images/ios-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![IOS 上的本地文件](webview-images/ios-xs.png)

-----

应将 `BaseUrl` 设置为主绑定的路径：

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

在 Android 上，在 "资产" 文件夹中放置 HTML、CSS 和图像，其中包含生成操作*AndroidAsset* ，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Android 上的本地文件](webview-images/android-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Android 上的本地文件](webview-images/android-xs.png)

-----

在 Android 上，`BaseUrl` 应设置为 `"file:///android_asset/"`：

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

在 Android 上，还可以通过当前 Android 上下文访问 "**资产**" 文件夹中的文件，该上下文由 `MainActivity.Instance` 属性公开：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台（UWP）项目上，将 HTML、CSS 和图像放在项目根中，并将 "生成" 操作设置为 "*内容*"。

应将 `BaseUrl` 设置为 `"ms-appx-web:///"`：

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

Web 视图支持通过其提供的多个方法和属性进行导航：

- **GoForward （）** &ndash; 如果 `CanGoForward` 为 true，则调用 `GoForward` 向下定位到下一个访问的页面。
- **GoBack （）** &ndash; 如果 `CanGoBack` 为 true，则调用 `GoBack` 将导航到上次访问的页面。
- **CanGoBack** &ndash; `true` 如果有要导航到的页，则 `false` 浏览器是否位于开始 URL。
- **CanGoForward** &ndash; `true` 用户是否已向后导航并可以向前移动到已访问的页面。

在页面中，`WebView` 不支持多点触控手势。 务必确保内容经过移动优化，并显示而无需缩放。

应用程序通常会在 `WebView` 而不是设备的浏览器中显示链接。 在这些情况下，允许正常导航是非常有用的，但当用户在开始链接上点击时，应用程序应返回到普通的应用程序视图。

使用内置的导航方法和属性来启用此方案。

首先创建浏览器视图的页面：

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

在代码隐藏中：

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

![Web 视图导航按钮](webview-images/in-app-browser.png)

## <a name="events"></a>事件

Web 视图将引发以下事件，以帮助你响应状态更改：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) –当 web 视图开始加载新页时引发的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) –加载页面和导航停止后引发的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) –发出请求以重新加载当前内容时引发的事件。

[@No__t_3](xref:Xamarin.Forms.WebView.Navigating)事件附带的[`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs)对象具有四个属性：

- `Cancel` –指示是否取消导航。
- `NavigationEvent` –引发的导航事件。
- `Source` –执行导航的元素。
- `Url` –导航目标。

[@No__t_3](xref:Xamarin.Forms.WebView.Navigated)事件附带的[`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs)对象具有四个属性：

- `NavigationEvent` –引发的导航事件。
- `Result` –使用[`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult)枚举成员描述导航的结果。 有效值为 `Cancel`、`Failure`、`Success` 和 `Timeout`。
- `Source` –执行导航的元素。
- `Url` –导航目标。

如果预计使用需要很长时间才能加载的网页，请考虑使用[`Navigating`](xref:Xamarin.Forms.WebView.Navigating)和[`Navigated`](xref:Xamarin.Forms.WebView.Navigated)事件来实现状态指示器。 例如:

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

这会生成以下输出（加载）：

![Web 视图导航事件示例](webview-images/loading-start.png)

已完成加载：

![Web 视图导航事件示例](webview-images/loading-end.png)

## <a name="reloading-content"></a>重新加载内容

[`WebView`](xref:Xamarin.Forms.WebView)具有可用于重载当前内容的 `Reload` 方法：

```csharp
var webView = new WebView();
...
webView.Reload();
```

调用 `Reload` 方法时，将激发 `ReloadRequested` 事件，指示已发出请求以重新加载当前内容。

## <a name="performance"></a>性能

常用的 web 浏览器现在采用硬件加速呈现和 JavaScript 编译等技术。 默认情况下，在 iOS 上，Xamarin `WebView` 由 `UIWebView` 类实现，其中许多技术在此实现中不可用。 但是，应用程序可以选择使用 iOS `WkWebView` 类来实现 Xamarin. Forms `WebView`，它支持更快的浏览。 可以通过将以下代码添加到应用程序的 iOS 平台项目中的**AssemblyInfo.cs**文件来实现此目的：

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> 在 iOS 上，`WkWebViewRenderer` 具有接受 `WkWebViewConfiguration` 参数的构造函数重载。 这样，便可以在创建时配置呈现器。

默认情况下，Android 上的 `WebView` 与内置浏览器一样快。

[UWP Web 视图](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)使用 Microsoft Edge 呈现引擎。 台式机和平板电脑的性能应与使用 Edge 浏览器本身的性能相同。

## <a name="permissions"></a>权限

若要运行 `WebView`，必须确保为每个平台设置权限。 请注意，在某些平台上，`WebView` 将在调试模式下运行，但不能在生成发布时使用。 这是因为，默认情况下，在调试模式下 Visual Studio for Mac 设置某些权限，如 Android 上的 internet 访问权限。

- **UWP** &ndash; 在显示网络内容时需要 Internet （客户端 & 服务器）功能。
- 仅当显示网络中的内容时， **Android** &ndash; 才需要 `INTERNET`。 本地内容不需要特殊权限。
- **iOS** &ndash; 不需要特殊权限。

## <a name="layout"></a>布局

与大多数其他 Xamarin. Forms 视图不同，`WebView` 要求 `HeightRequest` 和 `WidthRequest` 在 StackLayout 或 RelativeLayout 中包含时指定。 如果无法指定这些属性，则不会呈现 `WebView`。

下面的示例演示了导致工作 `WebView`s 的布局：

StackLayout with WidthRequest & HeightRequest：

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout with WidthRequest & HeightRequest：

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

*不带*WidthRequest & HeightRequest 的 AbsoluteLayout：

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

*不带*WidthRequest & HeightRequest 的网格。 网格是不需要指定请求的高度和宽度的几个布局之一：

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

[`WebView`](xref:Xamarin.Forms.WebView)包括从C#调用 JavaScript 函数的功能，并将任何结果返回给调用C#代码。 这是通过[`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法实现的，该方法在以下来自[web 视图](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)示例的示例中所示：

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[@No__t_1](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法计算指定为参数的 JavaScript，并以 `string` 的形式返回任何结果。 在此示例中，将调用 `factorial` JavaScript 函数，该函数将返回 `number` 的阶乘。 此 JavaScript 函数在[`WebView`](xref:Xamarin.Forms.WebView)加载的本地 HTML 文件中定义，如以下示例中所示：

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

- [使用 Web 视图（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Web 视图（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
