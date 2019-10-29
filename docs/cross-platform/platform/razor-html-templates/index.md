---
title: 使用 Razor 模板构建 HTML 视图
description: " 在跨平台的方式下，使用全屏网页呈现 HTML 可能是一种简单而有效的格式，尤其是在您已经具有网站项目的 HTML、JavaScript 和 CSS 的情况下。"
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: davidortinau
ms.author: daortin
ms.date: 07/24/2018
ms.openlocfilehash: 5f1b1345f9abbf891cfbea6e45a8ed2abd7c0dac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014605"
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 模板构建 HTML 视图

在移动开发世界，术语 "混合应用" 通常指的是在托管的 web 查看器控件内将其屏幕显示为 HTML 页面的应用程序。

在某些开发环境中，你可以完全在 HTML 和 JavaScript 中构建移动应用程序，但在尝试完成复杂的处理或 UI 效果时，这些应用程序可能会遇到性能问题，并且在平台中也受到限制可访问的功能。

Xamarin 提供两种世界的优势，特别是在利用 Razor HTML 模板化引擎时。 借助 Xamarin，你可以灵活地生成使用 JavaScript 和 CSS 的跨平台模板化 HTML 视图，还可以使用C#来完全访问基础平台 api 和快速处理。

本文档介绍了如何使用 Razor 模板化引擎来构建可跨使用 Xamarin 的移动平台使用的 HTML + JavaScript + CSS 视图。

## <a name="using-web-views-programmatically"></a>以编程方式使用 Web 视图

在了解 Razor 之前，此部分介绍如何使用 web 视图直接显示 HTML 内容，具体来说就是在应用程序中生成的 HTML 内容。

Xamarin 提供对 iOS 和 Android 上底层平台 Api 的完全访问权限，因此可以使用C#轻松地创建和显示 HTML。 每个平台的基本语法如下所示。

### <a name="ios"></a>iOS

在 Xamarin 中的 UIWebView 控件中显示 HTML 也只需几行代码：

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

有关使用 UIWebView 控件的详细信息，请参阅[IOS UIWebView](https://github.com/xamarin/docs-archive/tree/master/Recipes/ios/content_controls/web_view)食谱。

### <a name="android"></a>Android

使用 Xamarin 在 Web 视图控件中显示 HTML 只需几行代码即可完成：

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

有关使用 "Web 视图" 控件的详细信息，请参阅[Android Web 视图](https://github.com/xamarin/docs-archive/tree/master/Recipes/android/controls/webview)食谱。

### <a name="specifying-the-base-directory"></a>指定基目录

在这两个平台上，都有一个参数，该参数指定 HTML 页面的基目录。 这是设备文件系统上的位置，用于解析对资源（如图像和 CSS 文件）的相对引用。 例如，像这样的标记

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

请参阅以下文件： **style .css**、**猴子**和**jscript**。 "基目录" 设置告知 web 视图这些文件所在的位置，以便可以将这些文件加载到页面中。

#### <a name="ios"></a>iOS

模板输出呈现在 iOS 中，并具有以下C#代码：

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

基本目录被指定为 `NSBundle.MainBundle.BundleUrl`，这是指在其中安装应用程序的目录。 **资源**文件夹中的所有文件都复制到此位置，如此处所示的**样式 .css**文件：

 ![iPhoneHybrid 解决方案](images/image1_240x163.png)

所有静态内容文件的生成操作应为**BundleResource**：

 ![iOS 项目生成操作： BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

当 html 字符串显示在 web 视图中时，Android 还需要一个基目录作为参数传递。

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊字符串**file:///android_asset/** 指的是应用程序中的 "android 资产" 文件夹，此处显示的是包含**样式 .css**文件。

 ![AndroidHybrid 解决方案](images/image3_240x167.png)

所有静态内容文件的生成操作都应为**AndroidAsset**。

 ![Android 项目生成操作： AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>从C# HTML 和 JavaScript 调用

当 html 页面加载到 web 视图时，它会像从服务器加载页面一样处理链接和表单。 这意味着，如果用户单击链接或提交窗体，web 视图将尝试导航到指定的目标。

如果链接指向外部服务器（如 google.com），则 web 视图将尝试加载外部网站（假定存在 internet 连接）。

```html
<a href="http://google.com/">Google</a>
```

如果链接是相对的，则 web 视图将尝试从基本目录加载该内容。 显然，此操作不需要网络连接，因为内容存储在设备上的应用程序中。

```html
<a href="somepage.html">Local content</a>
```

窗体操作遵循相同的规则。

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

不会在客户端上托管 web 服务器;但是，你可以使用目前的响应式设计模式中使用的相同服务器通信技术通过 HTTP GET 来调用服务，并通过发出 JavaScript （或调用已在 web 视图中托管的 JavaScript）来异步处理响应。 这使您可以轻松地将数据从 HTML 传递回C#代码进行处理，然后在 HTML 页上显示结果。

IOS 和 Android 均提供一种机制，使应用程序代码能够截获这些导航事件，使应用程序代码能够响应（如果需要）。 此功能对于生成混合应用至关重要，因为它允许本机代码与 web 视图交互。

#### <a name="ios"></a>iOS

可以重写 iOS 中 web 视图上的 ShouldStartLoad 事件，以允许应用程序代码处理导航请求（例如，单击链接）。 方法参数提供所有信息

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

然后分配事件处理程序：

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上，只需将 WebViewClient 为子类，然后实现代码来响应导航请求。

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

然后在 web 视图上设置客户端：

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>从 C\# 调用 JavaScript

除了告诉 web 视图加载新的 HTML 页面外， C#代码还可以在当前显示的页面中运行 JavaScript。 可以使用C#字符串并执行整个 javascript 代码块，也可以通过`script`标记将方法调用创建到页面上已提供的 JavaScript。

#### <a name="android"></a>Android

创建要执行的 JavaScript 代码，然后将其作为 "javascript：" 的前缀，并指示 web 视图加载该字符串：

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web 视图提供专用于调用 JavaScript 的方法：

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>总结

本部分介绍了 Android 和 iOS 上的 web 视图控件功能，使我们能够通过 Xamarin 构建混合应用程序，包括：

- 能够从代码中生成的字符串加载 HTML，
- 引用本地文件（CSS、JavaScript、图像或其他 HTML 文件）的功能，
- 能够在代码中C#截获导航请求，
- 从C#代码调用 JavaScript 的能力。

下一节介绍 Razor，这使得创建在混合应用程序中使用的 HTML 变得更加容易。

## <a name="what-is-razor"></a>什么是 Razor？

Razor 是一种模板化引擎，它是使用 ASP.NET MVC 引入的，最初在服务器上运行并生成用于 web 浏览器的 HTML。

Razor 模板化引擎将标准 HTML 语法扩展C#为，这样您就可以轻松表达布局，并可以轻松地合并 CSS 样式表和 JavaScript。 模板可以引用模型类，该类可以是任何自定义类型，可以直接从模板访问其属性。 它的主要优点之一是能够轻松地混合 HTML 和C#语法。

Razor 模板不仅限于服务器端使用，还可以包含在 Xamarin apps 中。 使用 Razor 模板以及通过编程方式使用 web 视图，可以通过 Xamarin 构建复杂的跨平台混合应用程序。

### <a name="razor-template-basics"></a>Razor 模板基础知识

Razor 模板文件的文件扩展名为**cshtml** 。 可以通过 "**新建文件**" 对话框中的 "文本模板化" 部分将其添加到 Xamarin 项目：

 ![新文件-Razor 模板](images/image5_400x201.png)

下面显示了一个简单的 Razor 模板（ **RazorView**）。

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

请注意与常规 HTML 文件的以下差异：

- `@` 符号在 Razor 模板中具有特殊含义–它表示C#要计算以下表达式。
- `@model` 指令始终显示为 Razor 模板文件的第一行。
- `@model` 指令后面应跟有一个类型。 在此示例中，一个简单的字符串将传递给模板，但这可以是任何自定义类。
- 在整个模板中引用 `@Model` 时，它会在生成时提供对传递给模板的对象的引用（在本示例中，它是一个字符串）。
- IDE 将自动生成模板的分部类（扩展名为 **... 扩展名为...** ）。 您可以查看此代码，但不能对其进行编辑。
 ![RazorView](images/image6_125x34.png) 分部类名为 RazorView，以匹配. # 模板文件名。 此名称用于在代码中C#引用模板。
- 还可以将 `@using` 语句包含在 Razor 模板的顶部，以包括其他命名空间。

然后，可以通过以下C#代码生成最终的 HTML 输出。 请注意，我们将模型指定为字符串 "Hello World"，该字符串将合并到呈现的模板输出中。

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

下面是 iOS 模拟器上的 web 视图中显示的输出，并 Android Emulator：

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多 Razor 语法

在本部分中，我们将介绍一些基本 Razor 语法，以帮助你开始使用它。 本节中的示例使用数据填充以下类，并使用 Razor 显示它：

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

所有示例都使用以下数据初始化代码

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>显示模型属性

如果该模型是具有属性的类，则可在 Razor 模板中轻松引用这些属性，如以下示例模板所示：

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

可以使用以下代码将其呈现给字符串：

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

最终输出显示在 iOS 模拟器上的 web 视图中，并 Android Emulator：

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C#前瞻性

可在C#模板中包含更复杂的内容，例如模型属性更新和在此示例中的期限计算：

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

您可以通过将代码与 `@()`C#包围在一起来编写复杂的单行表达式（例如设置年龄格式）。

可以C#通过将多个语句与 `@{}`包围来编写。

#### <a name="if-else-statements"></a>Else 语句

代码分支可以用 `@if` 表示，如此模板示例中所示。

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>循环

还可以添加循环构造，如 `foreach`。 `@` 前缀可用于循环变量（在本例中为 `@food`）以 HTML 格式呈现。

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

上述模板的输出显示在 iOS 模拟器上运行，并 Android Emulator：

 ![Rupert X 猴子](images/image9_520x277.png)

本部分介绍了使用 Razor 模板呈现简单的只读视图的基础知识。 下一节将介绍如何使用 Razor 生成更完整的应用程序，这些应用可以接受用户输入，并在 HTML 视图和C#中的 JavaScript 之间进行互操作。

## <a name="using-razor-templates-with-xamarin"></a>将 Razor 模板用于 Xamarin

本部分说明如何在 Visual Studio for Mac 中使用解决方案模板来构建自己的混合应用程序。 **> 新建 > 解决方案**"窗口中提供了三个模板：

- **Android > 应用 > Android Web 视图应用程序**
- **iOS > 应用 > Web 视图应用程序**
- **ASP.NET MVC 项目**

对于 iPhone 和 Android 项目，新的 "**解决方案**" 窗口将如下所示-适用于 Razor 模板化引擎的突出显示的解决方案说明。

 ![创建 iPhone 和 Android 解决方案](images/image13_1139x959.png)

请注意，你可以轻松地将**cshtml** Razor 模板添加到*任何*现有 Xamarin 项目中，无需使用这些解决方案模板。 iOS 项目不需要演示图板即可使用 Razor;只需以编程方式将 UIWebView 控件添加到任何视图，即可在代码中C#整体呈现 Razor 模板。

IPhone 和 Android 项目的默认模板解决方案内容如下所示：

 ![iPhone 和 Android 模板](images/image10_428x310.png)

模板为你提供了现成的应用程序基础结构，以便使用数据模型对象加载 Razor 模板，处理用户输入并通过 JavaScript 向用户进行通信。

解决方案的重要组成部分如下：

- 静态内容，如**样式 .css**文件。
- Razor 模板文件（如**RazorView** ）。
- Razor 模板中引用的模型类，如**ExampleModel.cs** 。
- 平台特定的类，该类创建 web 视图并呈现模板，例如 Android 上的 `MainActivity` 和 iOS 上的 `iPhoneHybridViewController`。

以下部分说明了项目的工作方式。

### <a name="static-content"></a>静态内容

静态内容包括 CSS 样式表、图像、JavaScript 文件或其他内容，可从 web 视图中显示的 HTML 文件链接或引用这些内容。

模板项目包含最小样式表，用于演示如何在混合应用中包含静态内容。 模板中引用了 CSS 样式表，如下所示：

```html
<link rel="stylesheet" href="style.css" />
```

可以添加所需的任何样式表和 JavaScript 文件，包括 JQuery 等框架。

### <a name="razor-cshtml-templates"></a>Razor cshtml 模板

该模板包含一个 Razor **. cshtml**文件，该文件具有预编写的代码，可帮助在 HTML/JavaScript 和C#之间传递数据。 这样，你便可以生成复杂的混合应用，这些应用不只是显示模型中的只读数据，而且还接受 HTML 中的用户输入，并将C#其传递回代码以进行处理或存储。

#### <a name="rendering-the-template"></a>呈现模板

对模板调用 `GenerateString` 可在 web 视图中呈现 HTML 以便显示。 如果模板使用模型，则应在呈现之前提供。 此图说明了呈现的工作方式，而不是在运行时由 web 视图解析静态资源，使用提供的基目录查找指定的文件。

 ![Razor 流程图](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>从C#模板调用代码

从呈现的 web 视图进行的调用C#是通过设置 web 视图的 URL，然后截获中C#的请求来处理本机请求，而不会重新加载 web 视图。

可在如何处理 RazorView 的按钮中查看示例。 此按钮具有以下 HTML：

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

`InvokeCSharpWithFormValues` JavaScript 函数读取 HTML 窗体中的所有值，并设置 web 视图的 `location.href`：

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

这会尝试使用自定义方案将 web 视图导航到 URL （例如 `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

当本机 web 视图处理此导航请求时，我们有机会截获该请求。 在 iOS 中，这是通过处理 UIWebView 的 HandleShouldStartLoad 事件来完成的。 在 Android 中，只需为窗体中使用的 WebViewClient 划分子类，并重写 ShouldOverrideUrlLoading。

这两个导航侦听器的内部机制实质上是相同的。

首先，检查 web 视图尝试加载的 URL，如果不是以自定义方案（`hybrid:`）开头，则允许导航正常进行。

对于自定义 URL 方案，方案和 "？" 之间 URL 中的所有内容 要处理的方法名称（在本例中为 "UpdateLabel"）。 查询字符串中的所有内容都将被视为方法调用的参数：

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

在此示例中 `UpdateLabel` 对 textbox 参数执行最少量的字符串操作（在字符串前面C#加上 "说"），然后回调到 web 视图。

处理 URL 后，方法将中止导航，以便 web 视图不会尝试完成导航到自定义 URL。

#### <a name="manipulating-the-template-from-c"></a>从 C\# 操作模板

通过在 web 视图中调用 JavaScript， C#可以从与呈现的 HTML web 视图进行通信。 在 iOS 上，这是通过对 UIWebView 调用 `EvaluateJavascript` 来完成的：

```csharp
webView.EvaluateJavascript (js);
```

在 Android 上，可以通过使用 `"javascript:"` URL 方案将 JavaScript 作为 URL 加载来在 web 视图中调用 JavaScript：

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>使应用真正混合

这些模板不会在每个平台上使用本机控件–使用单个 web 视图填充整个屏幕。

HTML 可以很好地用于原型制作，并显示 web 最适合的内容种类，例如富文本和响应式布局。 不过，并非所有任务都适用于 HTML 和 JavaScript –滚动查看长数据列表，例如，使用本机 UI 控件（如 iOS 上的 UITableView 或 Android 上的 ListView）更好地执行。

可以轻松地将模板中的 web 视图与特定于平台的控件结合，只需在 iOS 设计器中编辑**mainstoryboard.storyboard** ，或在 Android 上编辑**Resources/layout/main.axml** 。

### <a name="razortodo-sample"></a>RazorTodo 示例

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)存储库包含两个单独的解决方案，用于显示完全 html 驱动应用和将 HTML 与本机控件结合使用的应用之间的差异：

- **RazorTodo** -使用 Razor 模板的完全 HTML 驱动的应用。
- **RazorNativeTodo** -使用适用于 IOS 和 Android 的本机列表视图控件，但使用 HTML 和 Razor 显示编辑屏幕。

这些 Xamarin 应用在 iOS 和 Android 上运行，利用可移植类库（Pcl）来共享公共代码，如数据库和模型类。 Razor**模板**还可以包括在 PCL 中，以便可以轻松地在平台之间共享这些模板。

这两个示例应用都结合了本地平台中的 Twitter 共享和文本到语音 Api，演示了使用 Xamarin 的混合应用程序仍可以访问 HTML Razor 模板驱动视图中的所有基础功能。

**RazorTodo**应用使用 HTML Razor 模板作为列表和编辑视图。 这意味着我们几乎可以完全在共享的可移植类库（包括数据库和 **. cshtml**模板）中构建应用。 下面的屏幕截图显示 iOS 和 Android 应用。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo**应用使用 "编辑" 视图的 HTML Razor 模板，但在每个平台上实现本机滚动列表。 这带来了许多好处，包括：

- 性能-本机滚动控件使用虚拟化来确保快速平稳滚动，甚至包含非常长的数据列表。
- 本机体验-可轻松启用特定于平台的 UI 元素，如 iOS 和 Android 中的快速滚动索引支持。

使用 Xamarin 构建混合应用程序的一个主要优点是，您可以从完全 HTML 驱动的用户界面（如第一个示例）开始，然后在需要时添加特定于平台的功能（如第二个示例所示）。 IOS 和 Android 上的本机列表屏幕和 HTML Razor 编辑屏幕如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>总结

本文介绍了在 iOS 和 Android 上提供的用于简化混合应用程序的 web 视图控件的功能。

然后，它讨论了 Razor 模板化引擎，以及可用于在 Xamarin 应用程序中轻松生成 HTML 的语法。**cshtml**Razor 模板文件。 它还介绍了 Visual Studio for Mac 解决方案模板，可让你快速开始用 Xamarin 构建混合应用程序。

最后，它引入了 RazorTodo 示例，该示例演示如何将 web 视图与本机用户界面和 Api 组合在一起。

### <a name="related-links"></a>相关链接

- [RazorTodo 示例](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor 视图引擎（Microsoft）](https://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 语法的 ASP.NET Web 编程简介（Microsoft）](https://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
