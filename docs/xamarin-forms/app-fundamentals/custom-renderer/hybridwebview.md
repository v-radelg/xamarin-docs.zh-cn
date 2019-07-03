---
title: 实现 HybridWebView
description: 本文演示如何为 HybridWebView 自定义控件创建自定义呈现器，演示如何增强特定于平台的 Web 控件以允许从 JavaScript 调用 C# 代码。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: d09188373d11b33f3b3d78b92faa46bf754797f6
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268988"
---
# <a name="implementing-a-hybridwebview"></a>实现 HybridWebView

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/HybridWebView/)

_Xamarin.Forms 自定义用户界面控件应派生自视图类，该类用于在屏幕上放置布局和控件。本文演示如何为 HybridWebView 自定义控件创建自定义呈现器，演示如何增强特定于平台的 Web 控件以允许从 JavaScript 调用 C# 代码。_

每个 Xamarin.Forms 视图都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 当 Xamarin.Forms 应用程序在 iOS 中呈现 [`View`](xref:Xamarin.Forms.View) 时，会实例化 `ViewRenderer` 类，该类又会实例化本机 `UIView` 控件。 在 Android 平台上，`ViewRenderer` 类实例化 `View` 控件。 在通用 Windows 平台 (UWP) 上，`ViewRenderer` 类实例化本机 `FrameworkElement` 控件。 有关 Xamarin.Forms 控件映射到的呈现器和本机控件类的详细信息，请参阅[呈现器基类和本机控件](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下图说明了 [`View`](xref:Xamarin.Forms.View) 和实现它的相应本机控件之间的关系：

![](hybridwebview-images/view-classes.png "视图类及其实现本机类之间的关系")

通过在每个平台上为 [`View`](xref:Xamarin.Forms.View) 创建自定义呈现器，可以使用呈现过程来实现特定于平台的自定义。 执行此操作的过程如下：

1. [创建](#Creating_the_HybridWebView) `HybridWebView`自定义控件。
1. [使用](#Consuming_the_HybridWebView) Xamarin.Forms 中的 `HybridWebView`。
1. 在每个平台上为 `HybridWebView` [创建](#creating-the-custom-renderer-on-each-platform)自定义呈现器。

现在将依次讨论每个项目以实现 `HybridWebView` 呈现器，该呈现器增强特定于平台的 Web 控件以允许从 JavaScript 调用 C# 代码。 `HybridWebView` 实例将用于显示要求用户输入其名称的 HTML 页。 然后，当用户单击 HTML 按钮，JavaScript 函数将调用 C# `Action` 显示一个包含用户名称的弹出项。

若要详细了解从 JavaScript 调用 C# 的过程，请参阅[从 JavaScript 调用 C#](#Invoking_C_from_JavaScript)。 有关 HTML 页的详细信息，请参阅[创建网页](#Creating_the_Web_Page)。

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>创建 HybridWebView

通过子类化 [`View`](xref:Xamarin.Forms.View) 类，可以创建 `HybridWebView` 自定义控件，如以下代码示例所示：

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

`HybridWebView` 自定义控件创建于 .NET Standard 库项目中，它为控件定义以下 API：

- 指定要加载的网页的地址的 `Uri` 属性。
- 用于向控件注册 `Action` 的 `RegisterAction` 方法。 已注册的操作将从通过 `Uri` 属性引用的 HTML 文件中包含的 JavaScript 调用。
- 移除对已注册的 `Action` 的引用的 `CleanUp` 方法。
- 调用已注册的 `Action` 的 `InvokeAction` 方法。 将从每个特定于平台的项目中的自定义呈现器中调用此方法。

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>使用 HybridWebView

通过在自定义控件上声明 `HybridWebView` 自定义控件位置的命名空间并使用命名空间前缀，可以在 .NET Standard 库项目的 XAML 中引用该自定义控件。 以下代码示例展示了 XAML 页可以如何使用 `HybridWebView` 自定义控件：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

`local` 命名空间前缀可以命名为任何内容。 但是，`clr-namespace` 和 `assembly` 值必须与自定义控件的详细信息相匹配。 声明命名空间后，前缀用于引用自定义控件。

以下代码示例展示了 C# 页可以如何使用 `HybridWebView` 自定义控件：

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

`HybridWebView` 实例将用于显示每个平台上的本地 Web 控件。 它的 `Uri` 属性设置为一个存储在每个特定于平台的项目中的 HTML 文件，并由本机 Web 控件显示。 呈现的 HTML 要求用户输入其名称，并使用 JavaScript 函数调用 C# `Action` 以对 HTML 按钮单击进行响应。

`HybridWebViewPage` 注册要从 JavaScript 调用的操作，如以下代码示例所示：

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

此操作调用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 方法以显示模式弹出项，该弹出项显示在 `HybridWebView` 实例显示的 HTML 页面中输入的名称。

现在可以将自定义呈现器添加到每个应用程序项目，以通过允许从 JavaScript 调用 C# 代码来增强特定于平台的 Web 控件。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每个平台上创建自定义呈现器

创建自定义呈现器类的过程如下所示：

1. 创建呈现自定义控件的 `ViewRenderer<T1,T2>` 类的子类。 第一个类型参数应该是使用呈现器的自定义控件，在本例中为 `HybridWebView`。 第二个类型参数应该是实现自定义视图的本机控件。
1. 替代呈现自定义控件的 `OnElementChanged` 方法，并编写逻辑以自定义控件。 创建相应的 Xamarin.Forms 自定义控件时将调用此方法。
1. 向自定义呈现器类添加 `ExportRenderer` 属性，以指定其将用于呈现 Xamarin.Forms 自定义控件。 此属性用于向 Xamarin.Forms 注册自定义呈现器。

> [!NOTE]
> 对大多数 Xamarin.Forms 元素来说，可以选择在每个平台项目中提供自定义呈现器。 如果未注册自定义呈现器，将使用控件基类的默认呈现器。 但是，呈现[视图](xref:Xamarin.Forms.View)元素时，每个平台项目中都需要自定义呈现器。

下图说明了示例应用程序中每个项目的职责，以及它们之间的关系：

![](hybridwebview-images/solution-structure.png "HybridWebView 自定义呈现器项目的职责")

`HybridWebView` 自定义控件由特定于平台的呈现器类呈现，这些类全都派生自各平台的 `ViewRenderer` 类。 这导致每个 `HybridWebView` 自定义控件都使用特定于平台的 Web 控件呈现，如以下屏幕截图所示：

![](hybridwebview-images/screenshots.png "每个平台上的 HybridWebView")

`ViewRenderer` 类公开 `OnElementChanged` 方法，创建 Xamarin.Forms 自定义控件时调用此方法以呈现对应的本机 Web 控件。 此方法采用 `ElementChangedEventArgs` 参数，其中包含 `OldElement` 和 `NewElement` 属性。 这两个属性分别表示呈现器“曾经”附加到的 Xamarin.Forms 元素和呈现器“现在”附加到的 Xamarin.Forms 元素   。 在示例应用程序中，`OldElement` 属性将为 `null`，且 `NewElement` 属性将包含对 `HybridWebView` 实例的引用。

在每个特定于平台的呈现器类中，`OnElementChanged` 方法的替代版本是执行本机 Web 控件实例化和自定义的地方。 `SetNativeControl` 方法应该用于实例化本机 Web 控件，此方法还会将控件引用分配给 `Control` 属性。 此外，可以通过 `Element` 属性获取正在呈现的 Xamarin.Forms 控件的引用。

在某些情况下，可以多次调用 `OnElementChanged` 方法。 因此，为了防止内存泄漏，在实例化新的本机控件时务必要格外小心。 下面的代码示例中演示了在自定义呈现器中实例化新的本机控件时要使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

当 `Control` 属性是 `null` 时，新的本机控件只应实例化一次。  此外，仅当自定义呈现器附加到新 Xamarin.Forms 元素时，才应创建、配置该控件并订阅事件处理程序。 同样，仅当呈现器所附加到的元素更改时，才应取消订阅任何订阅的事件处理程序。 采用此方法将有助于创建不会遭受内存泄漏的高性能自定义呈现器。

> [!IMPORTANT]
> 仅当 `e.NewElement` 不是 `null` 时，才应调用 `SetNativeControl` 方法。

每个自定义呈现器类均用 `ExportRenderer` 属性修饰，该属性向 Xamarin.Forms 注册呈现器。 该属性采用两个参数：要呈现的 Xamarin.Forms 自定义控件的类型名称和自定义呈现器的类型名称。 属性的 `assembly` 前缀指示属性适用于整个程序集。

以下各节讨论每个本机 Web 控件加载的网页的结构、从 JavaScript 调用 C# 的过程以及在每个特定于平台的自定义呈现器类中的实现。

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>创建网页

下面的代码示例显示了将由 `HybridWebView` 自定义控件显示的网页：

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

网页允许用户在 `input` 元素中输入他们的名称，并在单击时提供引用 C# 代码的 `button` 元素。 实现此操作的过程如下：

- 当用户单击 `button` 元素时，调用 `invokeCSCode` JavaScript 函数，并将 `input` 元素的值传递到函数。
- `invokeCSCode` 函数调用 `log` 函数以显示发送给 C# `Action` 的数据。 然后它调用 `invokeCSharpAction` 方法以调用 C# `Action`，传递来自 `input` 元素的参数。

`invokeCSharpAction` JavaScript 函数未在网页中定义，并将由每个自定义渲染器注入其中。

在 iOS 上，此 HTML 文件位于平台项目的“内容”文件夹中，包含 BundleResource 的生成操作  。 在 Android 上，此 HTML 文件位于平台项目的“资产/内容”文件夹中，包含 AndroidAsset 的生成操作  。

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>从 JavaScript 调用 C#

从 JavaScript 调用 C# 的过程在每个平台上完全相同：

- 自定义呈现器创建本机 Web 控件并加载 `HybridWebView.Uri` 属性指定的 HTML 文件。
- 加载网页后，自定义呈现器将 `invokeCSharpAction` JavaScript 函数注入到网页中。
- 当用户输入其名称并单击 HTML `button` 元素时，会调用 `invokeCSCode` 函数，并随之调用 `invokeCSharpAction` 函数。
- `invokeCSharpAction` 函数调用自定义呈现器中的方法，该方法随之调用 `HybridWebView.InvokeAction` 方法。
- `HybridWebView.InvokeAction` 方法调用已注册的 `Action`。

以下各节将讨论此过程在每个平台上的实现方式。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上创建自定义呈现器

以下代码示例展示了适用于 iOS 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                if (Control == null) {
                    userController = new WKUserContentController ();
                    var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                    userController.AddUserScript (script);
                    userController.AddScriptMessageHandler (this, "invokeAction");

                    var config = new WKWebViewConfiguration { UserContentController = userController };
                    var webView = new WKWebView (Frame, config);
                    SetNativeControl (webView);
                }
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

`HybridWebViewRenderer` 类将 `HybridWebView.Uri` 属性中指定的网页加载到本机 [`WKWebView`](xref:WebKit.WKWebView) 控件中，并将 `invokeCSharpAction` JavaScript 函数注入到网页中。 用户输入其名称并单击 HTML `button` 元素后，执行 `invokeCSharpAction` JavaScript 函数，并在收到来自网页的消息后调用 `DidReceiveScriptMessage` 方法。 然后此方法将调用 `HybridWebView.InvokeAction` 方法，该方法调用已注册的操作以显示弹出项。

此功能以下述方式实现：

- 如果已将自定义呈现器附加到新的 Xamarin.Forms 元素：
  - 如果 `Control` 属性为 `null`，则执行以下操作：
    - 创建了 [`WKUserContentController`](xref:WebKit.WKUserContentController) 实例，该实例允许发布消息并将用户脚本注入网页。
    - 创建 [`WKUserScript`](xref:WebKit.WKUserScript) 实例以在加载网页后将 `invokeCSharpAction` JavaScript 函数注入到网页中。
    - [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) 方法将 [`WKUserScript`](xref:WebKit.WKUserScript) 实例添加到内容控件。
    - [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) 方法将名为 `invokeAction` 的脚本消息处理程序添加到 [`WKUserContentController`](xref:WebKit.WKUserContentController) 实例，这将导致 JavaScript 函数 `window.webkit.messageHandlers.invokeAction.postMessage(data)` 在会使用 `WKUserContentController` 实例的所有 Web 视图中的所有框架中定义。
    - 创建 [`WKWebViewConfiguration`](xref:WebKit.WKWebViewConfiguration) 实例，并将 [`WKUserContentController`](xref:WebKit.WKUserContentController) 实例设置为内容控件。
    - 实例化 [`WKWebView`](xref:WebKit.WKWebView) 控件，并调用 `SetNativeControl` 方法将 `WKWebView` 控件的引用分配给 `Control` 属性。
  - [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) 方法加载 `HybridWebView.Uri` 属性指定的 HTML 文件。 该代码指定该文件存储在 `Content` 项目的文件夹中。 显示网页后，`invokeCSharpAction` JavaScript 函数将注入到网页中。
- 当呈现器附加到的元素产生更改时：
  - 资源被释放。

> [!NOTE]
> `WKWebView` 类仅支持 iOS 8 及更高版本。

此外，Info.plist 必须更新，以包含以下值  ：

```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上创建自定义呈现器

以下代码示例展示了适用于 Android 平台的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    var webView = new Android.Webkit.WebView(_context);
                    webView.Settings.JavaScriptEnabled = true;
                    webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                    SetNativeControl(webView);
                }
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

`HybridWebViewRenderer` 类将 `HybridWebView.Uri` 属性中指定的网页加载到本机 [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 控件中，并在网页加载完成后使用 `JavascriptWebViewClient` 类中 `OnPageFinished` 的替代方法将 `invokeCSharpAction` JavaScript函数注入到网页中：

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

用户输入其名称并单击 HTML `button` 元素后，执行 `invokeCSharpAction` JavaScript 函数。 此功能以下述方式实现：

- 如果已将自定义呈现器附加到新的 Xamarin.Forms 元素：
  - 如果 `Control` 属性为 `null`，则执行以下操作：
    - 创建本机 [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 实例，启用控件中的 JavaScript，并将 `JavascriptWebViewClient` 实例设置为 `WebViewClient` 实现。
    - 调用 `SetNativeControl` 方法以将对本机 [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 控件的引用分配给 `Control` 属性。
  - [`WebView.AddJavascriptInterface`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) 方法将新的 `JSBridge` 实例注入到 WebView 的 JavaScript 上下文的主框架中，并将其命名为 `jsBridge`。 这允许从 JavaScript 访问 `JSBridge` 类中的方法。
  - [`WebView.LoadUrl`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) 方法加载 `HybridWebView.Uri` 属性指定的 HTML 文件。 该代码指定该文件存储在 `Content` 项目的文件夹中。
  - 在 `JavascriptWebViewClient` 类中，页面加载完成后会将 `invokeCSharpAction` JavaScript 函数注入到网页中。
- 当呈现器附加到的元素产生更改时：
  - 资源被释放。

当执行 `invokeCSharpAction` JavaScript 函数时，它会调用 `JSBridge.InvokeAction` 方法，该方法如下面的代码示例所示：

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

该类必须派生自 `Java.Lang.Object` 且必须使用 `[JavascriptInterface]` 和 `[Export]` 属性修饰公开给 JavaScript 的方法。 因此，当 `invokeCSharpAction` JavaScript 函数注入到网页中并执行时，由于使用 `[JavascriptInterface]` 和 `[Export("invokeAction")]` 属性进行了修饰，它将调用 `JSBridge.InvokeAction` 方法。 然后 `InvokeAction` 方法将调用 `HybridWebView.InvokeAction` 方法，该方法调用已注册的操作以显示弹出项。

> [!NOTE]
> 使用 `[Export]` 属性的项目必须包含对 `Mono.Android.Export` 的引用，否则将导致编译器错误。

请注意，`JSBridge` 类保持对 `HybridWebViewRenderer` 类的 `WeakReference`。 这是为了避免在两个类之间创建循环引用。 更多详细信息请在 MSDN 上参阅[弱引用](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx)。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上创建自定义呈现器

以下代码示例展示了适用于 UWP 的自定义呈现器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
                }
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

`HybridWebViewRenderer` 类将 `HybridWebView.Uri` 属性中指定的网页加载到本机 `WebView` 控件中，并在加载网页后使用 `WebView.InvokeScriptAsync` 方法将 `invokeCSharpAction` JavaScript函数注入到网页中。 用户输入其名称并单击 HTML `button` 元素后，执行 `invokeCSharpAction` JavaScript 函数，并在收到来自网页的通知后调用 `OnWebViewScriptNotify` 方法。 然后此方法将调用 `HybridWebView.InvokeAction` 方法，该方法调用已注册的操作以显示弹出项。

此功能以下述方式实现：

- 如果已将自定义呈现器附加到新的 Xamarin.Forms 元素：
  - 如果 `Control` 属性为 `null`，则执行以下操作：
    - 调用 `SetNativeControl` 方法实例化新的本机 `WebView` 控件，并将其引用给 `Control` 属性。
  - 已注册用于 `NavigationCompleted` 和 `ScriptNotify` 事件的事件处理程序。 当本机 `WebView` 控件已完成加载当前内容或导航失败时，将触发 `NavigationCompleted` 事件。 当本机 `WebView` 控件中的内容使用 JavaScript 传递字符串到应用程序时，会激发 `ScriptNotify` 事件。 网页在传递 `string` 参数时通过调用 `window.external.notify` 激发 `ScriptNotify` 事件。
  - `WebView.Source` 属性设置为 `HybridWebView.Uri` 属性指定的 HTML 文件的 URI。 该代码假定该文件存储在项目 `Content` 文件夹中。 显示网页后，会激发 `NavigationCompleted` 事件，并调用 `OnWebViewNavigationCompleted` 方法。 导航成功完成后，会使用 `WebView.InvokeScriptAsync` 方法将 `invokeCSharpAction` JavaScript 函数注入到网页。
- 当呈现器附加到的元素产生更改时：
  - 取消订阅事件。

## <a name="summary"></a>总结

本文已演示了如何为 `HybridWebView` 自定义控件创建自定义呈现器，演示如何增强特定于平台的 Web 控件以允许从 JavaScript 调用 C# 代码。


## <a name="related-links"></a>相关链接

- [CustomRendererHybridWebView（示例）](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/HybridWebView/)
- [从 JavaScript 调用 C#](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
