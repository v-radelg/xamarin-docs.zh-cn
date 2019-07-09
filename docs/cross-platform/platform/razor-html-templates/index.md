---
title: 使用 Razor 模板构建 HTML 视图
description: " 使用全屏网页以呈现 HTML 可以是简单而有效的方式来呈现复杂的格式以跨平台方式，尤其是如果网站项目中已有的 HTML、 JavaScript 和 CSS。"
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: 539f59b9835cab6281327bcd1a37482ef82b62cc
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650179"
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 模板构建 HTML 视图

在移动开发领域中术语"混合应用"通常是指显示部分 （或全部） 其屏幕内托管的 web 查看器控件的 HTML 页面，并且应用程序。

有某些开发环境中，可生成也限制了在平台中移动应用完全在 HTML 和 JavaScript，但这些应用程序可能会降低了性能问题时想要实现复杂的处理或用户界面效果，而是它们可以访问的功能。

Xamarin 提供的这两个优势，尤其是在利用 Razor HTML 模板化引擎。 使用 Xamarin 可以灵活地构建跨平台模板化 HTML 视图，使用 JavaScript 和 CSS，但还具有完全访问基础平台 Api 和快速处理使用C#。

本文档介绍如何使用 Razor 模板化引擎来构建可跨移动平台使用 Xamarin 的 HTML + JavaScript + CSS 视图。

## <a name="using-web-views-programmatically"></a>以编程方式使用 Web 视图

我们了解 Razor 之前本部分介绍如何使用 web 视图以显示 HTML 内容直接 – 尤其是应用内生成的 HTML 内容。

Xamarin iOS 和 Android 上提供完全访问基础平台 Api，因此很容易地创建并显示使用 C# 的 HTML。 每个平台的基本语法如下所示。

### <a name="ios"></a>iOS

在 Xamarin.iOS UIWebView 控件中显示 HTML 还需要只需几行代码：

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

请参阅[iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/)方案使用 UIWebView 控件有关的详细信息。

### <a name="android"></a>Android

使用 Xamarin.Android 的 WebView 控件中显示 HTML 只需几行代码来完成：

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

请参阅[Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/)方案使用 WebView 控件的更多详细信息。

### <a name="specifying-the-base-directory"></a>指定的基目录

在这两个平台上是一个参数，指定的 HTML 页的基目录。 这是用于解析相对引用到资源，如图像和 CSS 文件的设备的文件系统上的位置。 例如，这样的标签

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

这些文件，请参阅： **style.css**， **monkey.jpg**并**jscript.js**。 基目录设置告诉 web 视图，这些文件的位置使他们可以加载到页面。

#### <a name="ios"></a>iOS

模板输出呈现在 iOS 中使用以下 C# 代码：

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

基目录指定为`NSBundle.MainBundle.BundleUrl`这是指在安装了应用程序的目录。 中的所有文件**资源**文件夹复制到此位置，如**style.css**文件如下所示：

 ![iPhoneHybrid 解决方案](images/image1_240x163.png)

所有静态内容文件的生成操作应**BundleResource**:

 ![iOS 项目的生成操作：BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android 还需要时显示在 web 视图中 html 字符串作为参数传递的基目录。

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊的字符串**file:///android_asset/** 是指应用程序中，显示在此处包含 Android 资产文件夹**style.css**文件。

 ![AndroidHybrid 解决方案](images/image3_240x167.png)

所有静态内容文件的生成操作应**AndroidAsset**。

 ![Android 项目生成操作：AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>调用C#从 HTML 和 JavaScript

当 html 页加载到 web 视图时，它将处理的链接和窗体就像从服务器加载了页面。 这意味着，如果用户单击链接或提交窗体的 web 视图将尝试导航到指定的目标。

如果链接是指向外部服务器 （例如 google.com) 然后 web 视图将尝试加载外部网站 （假设没有 internet 连接）。

```html
<a href="http://google.com/">Google</a>
```

如果链接为相对然后 web 视图将尝试从基目录中加载该内容。 显然，内容存储在设备上的应用，也不不需要为实现此目的，任何网络连接。

```html
<a href="somepage.html">Local content</a>
```

窗体操作遵循相同的规则。

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

您不打算承载客户端; 上的 web 服务器但是，可以使用当今的响应式设计模式中采用相同的服务器的通信技术通过 HTTP GET 时，调用服务并以异步方式处理响应，通过发出 JavaScript （或调用 JavaScript 已托管在 web 视图中）。 这使您轻松地将数据从 HTML 传递回处理然后将结果返回 HTML 页显示的 C# 代码。

IOS 和 Android 提供用于应用程序代码以截获这些导航事件，以便应用程序代码可以响应 （如果需要） 的机制。 此功能对构建混合应用程序，因为它允许与 web 视图进行交互的本机代码至关重要。

#### <a name="ios"></a>iOS

在 iOS web 视图上的 ShouldStartLoad 事件可以重写以允许应用程序代码处理导航请求 （例如链接单击）。 方法参数提供的所有信息

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

然后将分配的事件处理程序：

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上只需子类 WebViewClient，然后实现代码来响应导航请求。

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

### <a name="calling-javascript-from-c"></a>从调用 JavaScriptC#

除了告诉 web 视图，以加载新的 HTML 页面，C#代码还可以在当前显示页内运行 JavaScript。 可以使用创建整个 JavaScript 代码块C#字符串，并执行，或您就可以完成对 JavaScript 通过本页中已提供的方法调用`script`标记。

#### <a name="android"></a>Android

创建 JavaScript 代码来执行，然后将其与前缀"javascript:"，并指示要加载该字符串的 web 视图：

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web 视图提供了专门用于 JavaScript 调用的方法：

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>总结

本部分中引入了 Android 和 iOS，让我们构建使用 Xamarin，混合应用程序上的 web 视图控件的功能包括：

-  从字符串生成在代码中，加载 HTML 的功能
-  能够引用本地文件 （CSS、 JavaScript、 图像或其他 HTML 文件）
-  拦截在 C# 代码中，导航请求
-  从 JavaScript 调用的能力C#代码。


下一部分将介绍 Razor，轻松地创建混合应用中使用的 HTML。

## <a name="what-is-razor"></a>Razor 是什么？

Razor 是一个模板化引擎，引入了 ASP.NET MVC 中，最初以在服务器上运行，并生成要提供给 web 浏览器的 HTML。

Razor 模板化引擎扩展了标准 HTML 语法与C#，以便可以表达布局并轻松地将 CSS 样式表和 JavaScript 的合并。 该模板可以引用 Model 类，可以是任何自定义类型和可以直接从该模板访问其属性。 其主要优势之一是能够轻松地混合使用 HTML 和 C# 语法。

Razor 模板不限于服务器端使用，也可以在 Xamarin 应用中包含它们。 使用 Razor 模板能够以编程方式使用 web 视图，若要使用 Xamarin 生成的复杂的跨平台混合应用程序。

### <a name="razor-template-basics"></a>Razor 模板的基础知识

Razor 模板文件具有 **.cshtml**文件扩展名。 他们可以添加到 Xamarin 项目中的文本模板化部分从**新的文件**对话框：

 ![新文件-Razor 模板](images/image5_400x201.png)

一个简单的 Razor 模板 ( **RazorView.cshtml**) 如下所示。

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

请注意，与常规的 HTML 文件的以下差异：

-  `@`符号 Razor 模板中具有特殊含义，它表示以下表达式是 C#，以进行评估。
- `@model` 指令始终显示为 Razor 模板文件的第一行。
-  `@model`指令后应执行一种类型。 在此示例中被一个简单的字符串传递给模板，但这可能是任何自定义类。
-  当`@Model`引用在整个模板，它提供对生成 （在此示例中它将是一个字符串） 时传递给模板的对象的引用。
-  IDE 将自动生成分部类模板 (文件的工具 **.cshtml**扩展)。 你可以查看此代码，但不是应进行编辑。
 ![RazorView.cshtml](images/image6_125x34.png)分部类名为 RazorView 以匹配.cshtml 模板文件的名称。 它是用于在 C# 代码中模板引用此名称。
- `@using` 语句还可以包含在使用 Razor 模板来包含其他命名空间的顶部。


然后可以使用以下 C# 代码生成最终的 HTML 输出。 请注意，我们指定为字符串"Hello World"这将合并到呈现的模板输出的模型。

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

下面是 iOS 模拟器和 Android 的仿真程序上的 web 视图中所示的输出：

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多的 Razor 语法

在本部分中我们将介绍一些基本的 Razor 语法，以帮助你开始使用它。 在本部分中的示例填充以下数据类并将其使用 Razor 显示：

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

具有属性的类模型时，它们轻松地引用 Razor 模板中，在此示例模板中所示：

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

这可以呈现为字符串使用以下代码：

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

最终输出 iOS 模拟器和 Android 的仿真程序上的 web 视图中的如下所示：

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C# 语句

更复杂的 C# 可以在模板中，如模型属性更新和年龄的计算在此示例包括：

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

可以通过括起的代码与编写复杂的单行 C# 表达式 （如格式设置年龄） `@()`。

可由具有其周围写入多个 C# 语句`@{}`。

#### <a name="if-else-statements"></a>If-else 语句

可以使用表示代码分支`@if`此模板的示例中所示。

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

循环结构类似`foreach`还可以添加。 `@`可以循环变量上使用前缀 (`@food`这种情况下) 以 html 格式呈现。

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

上述模板的输出所示在 iOS 模拟器或 Android 仿真器上运行：

 ![Rupert X Monkey](images/image9_520x277.png)

本部分已介绍了使用 Razor 模板来呈现简单的只读视图的基础知识。 下一部分将介绍如何构建使用 Razor 可以接受用户输入和 HTML 视图中的 JavaScript 之间进行互操作的更完整应用和C#。

## <a name="using-razor-templates-with-xamarin"></a>通过 Xamarin 使用 Razor 模板

本部分介绍如何使用自己使用的解决方案模板在 Visual Studio for mac 的混合应用程序的生成 三个模板都不能从**文件 > 新建 > 解决方案...** 窗口：

- **Android > 应用程序 > Android WebView 应用程序**
- **iOS > 应用程序 > WebView 应用程序**
- **ASP.NET MVC 项目**



**新的解决方案**窗口如下所示适用于 iPhone 和 Android 项目-在右侧的解决方案说明突出显示了支持 Razor 模板化引擎。

 ![创建 iPhone 和 Android 的解决方案](images/image13_1139x959.png)

请注意，您可以轻松地添加 **.cshtml** Razor 模板*任何*现有 Xamarin 项目，它不需要使用这些解决方案模板。 iOS 项目不需要情节提要以进行使用 Razor 这两个;只需以编程方式向任何视图添加 UIWebView 控件并呈现 Razor 模板整个 C# 代码中。

适用于 iPhone 和 Android 项目的默认模板解决方案内容如下所示：

 ![iPhone 和 Android 模板](images/image10_428x310.png)

模板会为你提供已准备就绪应用程序基础结构来加载具有数据模型对象的 Razor 模板，处理用户输入并追溯到通过 JavaScript 用户进行通信。

该解决方案的重要部分是：

-  静态内容**style.css**文件。
-  Razor 模板文件.cshtml 喜欢**RazorView.cshtml** 。
-  如 Razor 模板中引用的模型类**ExampleModel.cs** 。
-  特定于平台的类能够创建 web 视图和呈现模板中，如`MainActivity`在 Android 上和`iPhoneHybridViewController`在 iOS 上。


以下部分介绍项目的工作原理。

### <a name="static-content"></a>静态内容

静态内容包括 CSS 样式表、 图像、 JavaScript 文件或其他内容，可以从链接或 web 视图中显示的 HTML 文件的引用。

模板项目包括演示如何在混合应用中包含静态内容的最小的样式表。 CSS 样式表中与此类似模板引用：

```html
<link rel="stylesheet" href="style.css" />
```

您可以添加任何样式表和 JavaScript 所需的文件，包括 JQuery 等框架。

### <a name="razor-cshtml-templates"></a>Razor cshtml 模板

该模板包含 Razor **.cshtml**具有预编写的代码，可帮助 HTML/JavaScript 之间传输数据的文件和C#。 这样就可以的生成复杂的混合应用不只是显示只读数据模型，但还接受用户输入以 html 格式并将其传递回 C# 代码进行处理或存储。

#### <a name="rendering-the-template"></a>呈现模板

调用`GenerateString`在模板上呈现 HTML 准备好 web 视图中显示。 如果该模板使用一个模型，则它应在呈现之前提供。 此图描述了呈现的工作原理 – 未解析的 web 视图在运行时，使用提供的基目录来查找指定的文件、 静态资源。

 ![Razor 流程图](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>从模板中调用 C# 代码

来自呈现的 web 视图回调到 C# 的通信可通过设置 web 视图的 URL，然后截获 C# 中的请求处理本机请求，而不重新加载 web 视图。

一个示例所示 RazorView 的按钮的处理方式。 该按钮具有以下 HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

`InvokeCSharpWithFormValues` JavaScript 函数中读取的所有值的 HTML 窗体和集从`location.href`web 视图：

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

此操作只能导航到自定义方案 （例如使用 URL 的 web 视图。 `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

当本机 web 视图处理此导航请求时，我们有机会截获。 在 iOS 中，这是通过处理 UIWebView HandleShouldStartLoad 事件。 在 Android 中，我们只需子类 WebViewClient 中窗体中，使用和重写 ShouldOverrideUrlLoading。

这些两个导航侦听器的内部结构实质上是相同的。

首先，请检查 URL 的 web 视图尝试加载，并且如果它不会启动具有自定义方案 (`hybrid:`)，允许作为普通发生的导航。

对于自定义 URL 方案，方案之间的 URL 中的所有内容和"？" 是用于处理 （在此情况下，"UpdateLabel"） 的方法名称。 查询字符串中的所有内容将被视为方法调用的参数：

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` 在此示例执行最少量的字符串操作 （追加"C# 向表明:"字符串） 的文本框中参数，然后回调到 web 视图。

在处理 URL 后, 方法中止在导航窗格，以便 web 视图不会尝试完成导航到自定义 URL。

#### <a name="manipulating-the-template-from-c"></a>操作从 C# 模板

与中呈现的 HTML web 视图的通信C#通过 JavaScript 调用 web 视图中。 在 iOS 上，这是通过调用`EvaluateJavascript`UIWebView 上：

```csharp
webView.EvaluateJavascript (js);
```

在 Android 上，JavaScript 可以调用 web 视图中通过加载为 URL 使用 JavaScript `"javascript:"` URL 方案：

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>使应用真正的混合

这些模板禁止使用的每个平台上的本机控件 – 使用单个 web 视图填充整个屏幕。

HTML 可以是适合用于原型制作，显示了几种 web 最适合在多格式文本和响应式布局等。 例如，但是并非所有的任务适用于 HTML 和 JavaScript – 滚动的数据，较长的列表执行更好地在 Android 上使用本机 UI 控件，如 (UITableView 在 iOS 上的) 或 ListView。

在模板中的 web 视图可以轻松地扩充与特定于平台的控制 – 只需编辑**mainstoryboard.storyboard** iOS 设计器中或**Resources/layout/Main.axml**在 Android 上。

### <a name="razortodo-sample"></a>RazorTodo Sample

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)存储库包含两个不同的解决方案，若要显示的完全 HTML 驱动的应用并将 HTML 与本机控件相结合的应用之间的差异：

-  **RazorTodo** -使用 Razor 模板完全 HTML 驱动应用程序。
-  **RazorNativeTodo** -使用适用于 iOS 和 Android 的本机列表视图控件，但显示使用 HTML 和 Razor 的编辑屏幕。


在 iOS 和 Android，利用可移植类库 (Pcl) 以共享通用代码，如数据库和模型类上运行这些 Xamarin 应用。 Razor **.cshtml**模板也可以将包含在 pcl 中以便轻松地在跨平台共享。

Twitter 共享和演示，Xamarin 混合应用程序仍具有的访问权限的基本功能的所有 HTML Razor 模板驱动视图的文本到语音转换 Api 的本机平台中，将合并这两个示例应用。

**RazorTodo**应用使用 HTML Razor 模板列表和编辑视图。 这意味着我们可以构建的应用程序几乎完全中共享的可移植类库 (包括数据库和 **.cshtml** Razor 模板)。 下面的屏幕截图显示 iOS 和 Android 应用。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo**应用的编辑视图中，使用 HTML Razor 模板，但每个平台上实现本机滚动列表。 这提供了很多好处包括：

-  性能-本机滚动控件使用虚拟化以确保快速、 顺利地滚动甚至使用很长数据的列表。
-  本机体验-特定于平台的 UI 元素很容易被启用，如 iOS 和 Android 中的快速滚动索引支持。


构建使用 Xamarin 混合应用程序的主要优点是可以具有完全 HTML 驱动的用户界面 （如第一个示例） 开始，将特定于平台的功能 （如第二个示例所示） 需要时添加。 本机列表屏幕和 HTML Razor 编辑屏幕上这两个 iOS 和 Android 如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>总结

本文讲述了一些可用的 web 视图控件的功能在 iOS 和 Android，便于您构建混合应用程序。

然后讨论了 Razor 模板化引擎以及可用于使用 Xamarin 应用中轻松地生成 HTML 的语法。**cshtml** Razor 模板文件。 它还介绍了为 Mac 解决方案模板，您可以快速开始构建使用 Xamarin 混合应用程序的 Visual Studio。

最后，它引入了 RazorTodo 示例，演示如何将 web 视图结合使用本机用户界面和 Api。

### <a name="related-links"></a>相关链接

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor 视图引擎 (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 语法 (Microsoft) 的 ASP.NET Web 编程简介](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
