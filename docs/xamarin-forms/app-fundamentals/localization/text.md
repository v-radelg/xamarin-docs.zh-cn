---
title: 字符串和图像本地化
description: 可以使用 .NET 资源文件本地化 Xamarin.Forms 应用。
ms.prod: xamarin
ms.assetid: 852B4ED3-2D2D-48A5-A759-A6591F6A1509
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/06/2016
ms.openlocfilehash: 31992c7d9219289847ebc3e9c8af755d54dc18ab
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672711"
---
# <a name="localization"></a>本地化

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)

可以使用 .NET 资源文件本地化 Xamarin.Forms 应用。

## <a name="overview"></a>概述

内置的 .NET 应用程序本地化机制使用 [RESX 文件](https://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)以及 `System.Resources` 和 `System.Globalization` 命名空间中的类。 包含已翻译字符串的 RESX 文件嵌入在 Xamarin.Forms 程序集中，该程序集中还包括编译器生成的类（可提供对翻译的强类型访问）。 然后可在代码中检索翻译文本。

### <a name="sample-code"></a>代码示例

有两个示例与此文档关联：

* [UsingResxLocalization](https://github.com/xamarin/xamarin-forms-samples/tree/master/UsingResxLocalization) 是关于所述概念的非常简单的演示。 如下所示的代码片段均来自此示例。
* [TodoLocalized](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized) 是可使用这些本地化技的基本工作应用。

#### <a name="shared-projects-are-not-recommended"></a>不建议使用共享的项目

TodoLocalized 示例包括[共享项目演示](https://github.com/xamarin/xamarin-forms-samples/tree/master/TodoLocalized/SharedProject/)，但是，由于生成系统的限制，资源文件不会收到生成的 .designer.cs 文件，这将导致无法访问代码中强类型的已翻译字符串。

此文档的其余部分与使用 Xamarin.Forms.NET Standard 库模板的项目相关。

## <a name="globalizing-xamarinforms-code"></a>全球化 Xamarin.Forms 代码

全球化应用程序是使其“世界通用”的过程。 这意味着编写能够显示不同语言的代码。

全球化应用程序的关键部分之一是构建用户界面，以便没有硬编码文本。 相反，向用户显示的任何内容都应从一组已翻译为所选语言的字符串中进行检索。

本文档将介绍如何使用 RESX 文件来存储这些字符串并对其进行检索，然后根据用户首选项进行显示。

这些示例提供英语、法语、西班牙语、德语、中文、日语、俄语和葡萄牙语（巴西）等语言版本。 根据需要，应用程序可翻译为更少或更多种语言。

> [!NOTE]
> 在通用 Windows 平台上，应将 RESW 文件（而不是 RESX 文件）用于推送通知本地化。 有关详细信息，请参阅 [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

### <a name="adding-resources"></a>添加资源

全球化 Xamarin.Forms.NET Standard 库应用程序的第一步是添加 RESX 资源文件，用于存储应用中所用的所有文本。 我们需要添加包含默认文本的 RESX 文件，然后添加其他适用于我们想要支持的每种语言的 RESX 文件。

#### <a name="base-language-resource"></a>基础语言资源

基础资源 (RESX) 文件将包含默认语言字符串（这些示例假定默认语言是英语）。 右键单击项目并选择“添加”>“新建文件...”，将文件添加到 Xamarin.Forms 通用代码项目中。

选择有意义的名称（如 AppResources）并按“确定”。

[![添加资源文件](text-images/resx-new-file-sml.png "“新建文件”对话框")](text-images/resx-new-file.png#lightbox "New File Dialog")

两个文件即会添加到项目中：

* AppResources.resx 文件，其中以 XML 格式存储已翻译的字符串。
* AppResources.designer.cs 文件，声明分部类，以包含对 RESX XML 文件中创建的所有元素的引用。

解决方案树将显示相关的文件。 应编辑 RESX 文件，以添加新的已翻译字符串；不应编辑 .designer.cs 文件。

![](text-images/appresources-tree.png "AppResources.resx 文件")

##### <a name="string-visibility"></a>字符串可见性

默认情况下，如果生成对字符串的强类型引用，则它们对程序集将为 `internal`。 这是因为 RESX 文件的默认生成工具将生成具有 `internal` 属性的 .designer.cs 文件。

选择 AppResources.resx 文件，并显示 Properties Pad，以查看配置此生成工具的位置。 下面的截图显示“自定义工具：ResXFileCodeGenerator”。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-internal-sml.png "AppResources.Resx 的属性窗口")](text-images/vs-resx-internal.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "AppResources.Resx 的 Properties Pad")](text-images/xs-resx-internal.png#lightbox)

-----

若要使强类型字符串属性为 `public`，必须手动将配置更改为“自定义工具：PublicResXFileCodeGenerator”，如以下屏幕截图中所示：


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](text-images/vs-resx-public-sml.png "AppResources.Resx 的属性窗口")](text-images/vs-resx-public.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](text-images/xs-resx-internal-sml.png "AppResources.Resx 的 Properties Pad")](text-images/xs-resx-internal.png#lightbox)


[![](text-images/xs-resx-public-sml.png "AppResources.Resx 的 Properties Pad")](text-images/xs-resx-public.png#lightbox)

-----

此更改是可选的，并且仅在要在不同程序集中引用本地化字符串时必需（例如，如果要将不同程序集中的 RESX 文件放入你的代码）。 此主题的示例使字符串保持 `internal`，因为它们在使用它们的相同 Xamarin.Forms.NET Standard 中定义。

如上所示，只需在基础 RESX 文件上设置自定义工具即可；无需在以下各节中所述的特定语言 RESX 文件上设置任何生成工具。

##### <a name="editing-the-resx-file"></a>编辑 RESX 文件

遗憾的是，Visual Studio for Mac 中没有内置 RESX 编辑器。 添加新的可翻译字符串需要为每个字符串添加新的 XML `data` 元素。 每个 `data` 元素均可包含以下内容：

* `name` 属性（必需）是此可翻译字符串的关键值。 它必须是有效的 C# 属性名称 - 因此不允许使用任何空格或特殊字符。
* `value` 元素（必需）是应用程序中显示的实际字符串。
* `comment` 元素（可选）可以包含介绍如何使用此字符串的翻译工具的说明。
* `xml:space` 属性（可选）用于控制如何保留字符串中的间距。

部分示例 `data` 元素如下所示：

```xml
<data name="NotesLabel" xml:space="preserve">
    <value>Notes:</value>
    <comment>label for input field</comment>
</data>
<data name="NotesPlaceholder" xml:space="preserve">
    <value>eg. buy milk</value>
    <comment>example input for notes field</comment>
</data>
<data name="AddButton" xml:space="preserve">
    <value>Add new item</value>
</data>
```

编写应用程序时，向用户显示的每一段文本都应添加到新 `data` 元素中的基础 RESX 资源文件。 建议添加尽可能多的 `comment`，以确保高质量的翻译。

> [!NOTE]
> Visual Studio（包括免费的社区版本）包含基本的 RESX 编辑器。 如果你有权访问 Windows 计算机，则可以方便地在 RESX 文件中添加和编辑字符串。

#### <a name="language-specific-resources"></a>特定语言资源

通常情况下，在编写了大型应用程序块之前，不会进行默认文本字符串的实际翻译（在这种情况下，默认 RESX 文件将包含大量字符串）。 在开发周期早期添加特定语言资源仍然是个好办法，可以选择使用计算机翻译的文本来帮助测试本地化代码。

将为我们要支持的每种语言添加一个额外的 RESX 文件。
特定语言资源文件必须遵循特定的命名约定：使用相同的文件名作为基础资源文件（例如， AppResources），后面跟一个句点 (.) 和语言代码。 简单的示例包括：

* **AppResources.fr.resx** - 法语翻译。
* **AppResources.es.resx** - 西班牙语翻译。
* **AppResources.de.resx** - 德语翻译。
* **AppResources.ja.resx** - 日语翻译。
* **AppResources.zh-Hans.resx** - 中文（简体）翻译。
* **AppResources.zh-Hant.resx** - 中文（繁体）翻译。
* **AppResources.pt.resx** - 葡萄牙语翻译。
* **AppResources.pt-BR.resx** - 葡萄牙语（巴西）翻译。

常规模式是使用两个字母的语言代码，但也有使用不同格式的示例（例如中文）以及需要 4 字符区域设置标识符的其他示例（例如葡萄牙语（巴西））。

这些特定语言资源文件不需要 .designer.cs 分部类，因此如果已设置“生成操作：EmbeddedResource”，则可添加为常规的 XML 文件。 此屏幕截图显示了一个包含特定语言资源文件的解决方案：

![](text-images/appresources-langs.png "特定语言资源文件")

开发应用程序且基础 RESX 文件已添加文本后，应将其发送给可翻译每个 `data` 元素的翻译器，并返回特定语言资源文件（使用如下所示命名约定），以包括在应用中。 部分“计算机翻译”示例如下所示：

**AppResources.es.resx（西班牙语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>Escribir un artículo</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.ja.resx（日语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>新しい項目を追加</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

**AppResources.pt-BR.resx（巴西葡萄牙语）**

```xml
<data name="AddButton" xml:space="preserve">
    <value>adicionar novo item</value>
    <comment>this string appears on a button to add a new item to the list</comment>
</data>
```

翻译工具只需更新 `value` 元素 - 不打算翻译 `comment`。 请记住：编辑 XML 文件以转义保留字符（如 `<`、`>`、`&`）时，如果它们出现在 `value` 或 `comment` 中，则使用 `&lt;`、`&gt;` 和 `&amp;`。

<a name="incode" />

### <a name="using-resources-in-code"></a>在 Code 中使用资源

RESX 资源文件中的字符串可用于使用 `AppResources` 类的用户界面代码。 分配给 RESX 文件中每个字符串的 `name` 将成为该类的属性，可在 Xamarin.Forms 代码中引用，如下所示：

```csharp
var myLabel = new Label ();
var myEntry = new Entry ();
var myButton = new Button ();
// populate UI with translated text values from resources
myLabel.Text = AppResources.NotesLabel;
myEntry.Placeholder = AppResources.NotesPlaceholder;
myButton.Text = AppResources.AddButton;
```

iOS、Android 和通用 Windows 平台 (UWP) 版用户界面将按预期呈现，只不过现在可以将应用翻译为多种语言，因为要从资源中加载本部，而不是对其进行硬编码。 下面是翻译前，每个平台上的 UI 的屏幕截图：

![](text-images/simple-example-english.png "翻译前的跨平台 UI")

### <a name="troubleshooting"></a>疑难解答

#### <a name="testing-a-specific-language"></a>测试特定语言

将模拟器或设备切换为不同的语言可能很棘手，特别是开发过程中，你希望快速测试不同的区域性。

可以通过设置 `Culture`，强制加载特定语言，如此代码片段中所示：

```csharp
// force a specific culture, useful for quick testing
AppResources.Culture =  new CultureInfo("fr-FR");
```

这种直接对 `AppResources` 类设置区域性的方法，也可用于实现在应用内实现语言选择器（而不是使用设备的区域设置）。

#### <a name="loading-embedded-resources"></a>加载嵌入的资源

以下代码片段可用于尝试调试嵌入资源的问题（例如 RESX 文件）。 将此代码添加到你的应用（应用程序生命周期早期），将会列出程序集中嵌入的所有资源，并显示完整的资源标识符：

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly; // "EmbeddedImages" should be a class in your app
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

在 AppResources.Designer.cs 文件中（约第 33 行），指定完整的资源管理器名称（例如， `"UsingResxLocalization.Resx.AppResources"`），与下面的代码类似：

```csharp
System.Resources.ResourceManager temp =
        new System.Resources.ResourceManager(
                "UsingResxLocalization.Resx.AppResources",
                typeof(AppResources).GetTypeInfo().Assembly);
```

检查“应用程序输出”，获取上述调试的结果，以确认已列出正确的资源（例如 `"UsingResxLocalization.Resx.AppResources"`）。

如果没有，则 `AppResources` 类将无法加载其资源。
检查以下项目，解决找不到资源的问题：

* 项目的默认命名空间与 AppResources.Designer.cs 文件中的根命名空间匹配。
* 如果 AppResources.resx 文件位于子目录中，则子目录名称应为命名空间和资源标识符的一部分。
* AppResources.resx 文件包含“生成操作：EmbeddedResource”。
* 勾选了“项目选项”>“源代码”>“.NET 命名策略”>“使用 Visual Studio 样式的资源名称”。 如果愿意可以取消勾选，但是引用 RESX 资源时将使用命名空间，以更新整个应用。

#### <a name="doesnt-work-in-debug-mode-android-only"></a>不能在调试模式下操作（仅限 Android）

如果已翻译的字符串在发行 Android 内部版本中正常，但调试时不正常，请右键单击“Android 项目”并选择“选项”>“生成”>“Android 内部版本”，然后确保未勾选“快速程序集部署”。 此选项会导致加载资源的问题，并且在测试本地化应用时序不应使用。

### <a name="displaying-the-correct-language"></a>显示正确的语言

到目前为止，我们探讨了如何编写代码，以便可以提供翻译，而不是如何实际显示它们。 Xamarin.Forms 代码可以利用 .NET 的资源加载正确的语言翻译，但我们需要查询每个平台上的操作系统，以确定用户所选的语言。

由于获取用户的语言首选项需要某些平台特定代码，请使用[依赖项服务](~/xamarin-forms/app-fundamentals/dependency-service/index.md)来公开 Xamarin.Forms 应用中的信息，并针对每个平台实现它。

首先，定义一个界面来公开用户首选的区域性，类似于如下代码：

```csharp
public interface ILocalize
{
    CultureInfo GetCurrentCultureInfo ();
    void SetLocale (CultureInfo ci);
}
```

第二，使用 Xamarin.Forms [ 类中的 ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)DependencyService`App`，以调用界面并将 RESX 资源区域性设置为正确值。 请注意，我们无需为通用 Windows 平台设置此值，因为资源框架会自动识别这些平台上所选的语言。

```csharp
if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
{
    var ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
    Resx.AppResources.Culture = ci; // set the RESX for resource localization
    DependencyService.Get<ILocalize>().SetLocale(ci); // set the Thread for locale-aware methods
}
```

首次加载应用程序时，需要设置资源 `Culture`，以便使用正确的语言字符串。 可选择根据特定平台事件更新此值，如果用户在应用运行时更新其语言首选项，则可能在 iOS 或 Android 上引发上述事件。

`ILocalize` 界面的实现在下面的[特定平台代码](#Platform-specific_Code)部分中显示。 这些实现充分利用此 `PlatformCulture` 帮助程序类：

```csharp
public class PlatformCulture
{
    public PlatformCulture (string platformCultureString)
    {
        if (String.IsNullOrEmpty(platformCultureString))
        {
            throw new ArgumentException("Expected culture identifier", "platformCultureString"); // in C# 6 use nameof(platformCultureString)
        }
        PlatformString = platformCultureString.Replace("_", "-"); // .NET expects dash, not underscore
        var dashIndex = PlatformString.IndexOf("-", StringComparison.Ordinal);
        if (dashIndex > 0)
        {
            var parts = PlatformString.Split('-');
            LanguageCode = parts[0];
            LocaleCode = parts[1];
        }
        else
        {
            LanguageCode = PlatformString;
            LocaleCode = "";
        }
    }
    public string PlatformString { get; private set; }
    public string LanguageCode { get; private set; }
    public string LocaleCode { get; private set; }
    public override string ToString()
    {
        return PlatformString;
    }
}
```

<a name="Platform-specific_Code" />

### <a name="platform-specific-code"></a>特定于平台的代码

用于检测要显示语言的代码必须特定于平台，因为 iOS、Android 和 UWP 公开该信息的方式都有所不同。 下面提供适用于每个平台的 `ILocalize` 依赖项服务的代码，以及其他平台特定要求，以确保本地化文本呈现正确。

特定于平台的代码还必须处理操作系统允许用户配置 .NET 的 `CultureInfo` 类不支持的区域设置标识符的情况。 在这些情况下必须编写自定义代码，以检测不受支持的区域设置并替换最佳的 .NET 兼容区域设置。

#### <a name="ios-application-project"></a>iOS 应用程序项目

iOS 用户将独立于日期和时间格式区域性选择首选语言。 要加载正确的资源本地化 Xamarin.Forms 应用，只需对第一个元素查询 `NSLocale.PreferredLanguages` 数组。

以下 `ILocalize` 依赖项服务的实现应置于 iOS 应用程序项目中。 由于 iOS 使用下划线而不是短划线（这是 .NET 的标准表示方法），因此代码在实例化 `CultureInfo` 类之前将替换下划线：

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.iOS.Localize))]

namespace UsingResxLocalization.iOS
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale (CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }

        public CultureInfo GetCurrentCultureInfo ()
        {
            var netLanguage = "en";
            if (NSLocale.PreferredLanguages.Length > 0)
            {
                var pref = NSLocale.PreferredLanguages [0];
                netLanguage = iOSToDotnetLanguage(pref);
            }
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }

        string iOSToDotnetLanguage(string iOSLanguage)
        {
            // .NET cultures don't support underscores
            string netLanguage = iOSLanguage.Replace("_", "-");

            //certain languages need to be converted to CultureInfo equivalent
            switch (iOSLanguage)
            {
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":    // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }

        string ToDotnetFallbackLanguage (PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "pt":
                    netLanguage = "pt-PT"; // fallback to Portuguese (Portugal)
                    break;
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                // add more application-specific cases here (if required)
                // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `GetCurrentCultureInfo` 方法中的 `try/catch` 块可模拟经常与区域设置说明符一起使用的回退行为 - 如果找不到确切的匹配，则只需根据该语言寻找接近的匹配即可（区域设置中的第一个字符块）。
>
> 在 Xamarin.Forms 中，部分区域设置在 iOS 中有效，但不对应 .NET 中的有效 `CultureInfo`，且上述代码会尝试处理此情况。
>
> 例如，iOS 的“设置”>“通用语言”&amp;“区域”屏幕允许你将手机的“语言”设置为“英语”，但“地区”设置为“西班牙”，这将导致 `"en-ES"` 的区域设置字符串。 如果 `CultureInfo` 创建失败，代码将回退到仅使用前两个字母，以选择显示语言。
>
> 开发人员应修改 `iOSToDotnetLanguage` 和 `ToDotnetFallbackLanguage` 方法，以处理其所支持语言需要的特定用例。

存在 iOS 自动翻译的系统定义用户界面元素，例如 `Picker` 控件上的“完成”按钮。 若要强制 iOS 翻译这些元素，需要在 Info.plist 文件中指示支持的语言。 可以通过 Info.plist >“源”添加这些值，如下所示：

![Info.plist 中的本地化关键值](text-images/info-plist.png "Localization Keys in Info.plist")

或者，在 XML 编辑器中打开 Info.plist 文件并直接编辑值：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>de</string>
    <string>es</string>
    <string>fr</string>
    <string>ja</string>
    <string>pt</string> <!-- Brazil -->
    <string>pt-PT</string> <!-- Portugal -->
    <string>ru</string>
    <string>zh-Hans</string>
    <string>zh-Hant</string>
</array>
<key>CFBundleDevelopmentRegion</key>
<string>en</string>
```

实现依赖项服务并更新 Info.plist 后，iOS 应用将能够显示本地化文本。

> [!NOTE]
> 请注意，Apple 处理葡萄牙语的方式略有不同。
> 来源于[其文档](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW2)：“使用 pt 作为葡萄牙语的语言 ID（因为在巴西使用），pt-PT 用作葡萄牙语的语言 ID（因为在葡萄牙使用）”。
> 这意味着，如果在非标准区域设置中选择葡萄牙语，除非编写代码（如上述 `ToDotnetFallbackLanguage`）更改此行为，否则 iOS 上的回退语言为巴西葡萄牙语。

有关 iOS 本地化的详细信息，请参阅 [iOS 本地化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="android-application-project"></a>Android 应用程序项目

Android 通过 `Java.Util.Locale.Default` 公开当前选定的区域设置，并且还使用下划分隔符而不是短横线（由以下代码替换）。 将此依赖项服务实现添加到 Android 应用程序项目：

```csharp
[assembly:Dependency(typeof(UsingResxLocalization.Android.Localize))]

namespace UsingResxLocalization.Android
{
    public class Localize : UsingResxLocalization.ILocalize
    {
        public void SetLocale(CultureInfo ci)
        {
            Thread.CurrentThread.CurrentCulture = ci;
            Thread.CurrentThread.CurrentUICulture = ci;
        }
        public CultureInfo GetCurrentCultureInfo()
        {
            var netLanguage = "en";
            var androidLocale = Java.Util.Locale.Default;
            netLanguage = AndroidToDotnetLanguage(androidLocale.ToString().Replace("_", "-"));
            // this gets called a lot - try/catch can be expensive so consider caching or something
            System.Globalization.CultureInfo ci = null;
            try
            {
                ci = new System.Globalization.CultureInfo(netLanguage);
            }
            catch (CultureNotFoundException e1)
            {
                // iOS locale not valid .NET culture (eg. "en-ES" : English in Spain)
                // fallback to first characters, in this case "en"
                try
                {
                    var fallback = ToDotnetFallbackLanguage(new PlatformCulture(netLanguage));
                    ci = new System.Globalization.CultureInfo(fallback);
                }
                catch (CultureNotFoundException e2)
                {
                    // iOS language not valid .NET culture, falling back to English
                    ci = new System.Globalization.CultureInfo("en");
                }
            }
            return ci;
        }
        string AndroidToDotnetLanguage(string androidLanguage)
        {
            var netLanguage = androidLanguage;
            //certain languages need to be converted to CultureInfo equivalent
            switch (androidLanguage)
            {
                case "ms-BN":   // "Malaysian (Brunei)" not supported .NET culture
                case "ms-MY":   // "Malaysian (Malaysia)" not supported .NET culture
                case "ms-SG":   // "Malaysian (Singapore)" not supported .NET culture
                    netLanguage = "ms"; // closest supported
                    break;
                case "in-ID":  // "Indonesian (Indonesia)" has different code in  .NET
                    netLanguage = "id-ID"; // correct code for .NET
                    break;
                case "gsw-CH":  // "Schwiizertüütsch (Swiss German)" not supported .NET culture
                    netLanguage = "de-CH"; // closest supported
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
        string ToDotnetFallbackLanguage(PlatformCulture platCulture)
        {
            var netLanguage = platCulture.LanguageCode; // use the first part of the identifier (two chars, usually);
            switch (platCulture.LanguageCode)
            {
                case "gsw":
                    netLanguage = "de-CH"; // equivalent to German (Switzerland) for this app
                    break;
                    // add more application-specific cases here (if required)
                    // ONLY use cultures that have been tested and known to work
            }
            return netLanguage;
        }
    }
}
```

> [!NOTE]
> `GetCurrentCultureInfo` 方法中的 `try/catch` 块可模拟经常与区域设置说明符一起使用的回退行为 - 如果找不到确切的匹配，则只需根据该语言寻找接近的匹配即可（区域设置中的第一个字符块）。
>
> 在 Xamarin.Forms 中，部分区域设置在 Android 中有效，但不对应 .NET 中的有效 `CultureInfo`，且上述代码会尝试处理此情况。
>
> 开发人员应修改 `iOSToDotnetLanguage` 和 `ToDotnetFallbackLanguage` 方法，以处理其所支持语言需要的特定用例。

此代码添加到 Android 应用程序项目后，它将能够自动显示已翻译的字符串。

> [!NOTE]
>**警告：** 如果已翻译的字符串在发行 Android 内部版本中正常，但调试时不正常，请右键单击“Android 项目”并选择“选项”>“生成”>“Android 内部版本”，然后确保未勾选“快速程序集部署”。 此选项会导致加载资源的问题，并且在测试本地化应用时序不应使用。

有关 Android 本地化的详细信息，请参阅 [Android 本地化](~/android/app-fundamentals/localization.md)。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 项目不需要依赖项服务。 相反，此平台可自动将资源的区域性设置正确。

##### <a name="assemblyinfocs"></a>AssemblyInfo.cs

在 .NET Standard 库项目中展开“属性”节点，并双击“AssemblyInfo.cs”文件。 将以下行添加到文件，将非特定资源程序集语言设为英语：

```csharp
[assembly: NeutralResourcesLanguage("en")]
```

这会通知资源管理器应用的默认区域性，因此，请确保语言非特定 RESX 文件中定义的字符串 (AppResources.resx) 将在应用在其中一个英语区域设置中运行时显示。

### <a name="example"></a>示例

如上所述，更新平台特定项目并使用已翻译的 RESX 文件重新编译应用后，将在每个应用中提供更新后的翻译。 下面是翻译为简体中文的代码示例的屏幕截图：

![](text-images/simple-example-hans.png " UI")

有关 UWP 本地化的详细信息，请参阅 [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

## <a name="localizing-xaml"></a>本地化 XAML

采用 XAML 生成 Xamarin.Forms 用户界面时，标记将与此类似，并且字符串直接嵌入 XML：

```xaml
<Label Text="Notes:" />
<Entry Placeholder="eg. buy milk" />
<Button Text="Add to list" />
```

理想情况下，我们可以直接在 XAML 中翻译用户界面控件，可通过创建标记扩展实现。 向 XAML 公开 RESX 资源的标记扩展代码如下所示。 此类应添加到 Xamarin.Forms 通用代码（以及 XAML 页）：

```csharp
using System;
using System.Globalization;
using System.Reflection;
using System.Resources;
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

namespace UsingResxLocalization
{
    // You exclude the 'Extension' suffix when using in XAML
    [ContentProperty("Text")]
    public class TranslateExtension : IMarkupExtension
    {
        readonly CultureInfo ci = null;
        const string ResourceId = "UsingResxLocalization.Resx.AppResources";

        static readonly Lazy<ResourceManager> ResMgr = new Lazy<ResourceManager>(
            () => new ResourceManager(ResourceId, IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly));

        public string Text { get; set; }

        public TranslateExtension()
        {
            if (Device.RuntimePlatform == Device.iOS || Device.RuntimePlatform == Device.Android)
            {
                ci = DependencyService.Get<ILocalize>().GetCurrentCultureInfo();
            }
        }

        public object ProvideValue(IServiceProvider serviceProvider)
        {
            if (Text == null)
                return string.Empty;

            var translation = ResMgr.Value.GetString(Text, ci);
            if (translation == null)
            {
#if DEBUG
                throw new ArgumentException(
                    string.Format("Key '{0}' was not found in resources '{1}' for culture '{2}'.", Text, ResourceId, ci.Name),
                    "Text");
#else
                translation = Text; // HACK: returns the key, which GETS DISPLAYED TO THE USER
#endif
            }
            return translation;
        }
    }
}
```

以下项目符号说明了上述代码中的重要元素：

* 该类名为 `TranslateExtension`，但根据约定，我们可在标记中翻译时引用。
* 该类可实现 `IMarkupExtension`，这是 Xamarin.Forms 正常工作所必需。
* `"UsingResxLocalization.Resx.AppResources"` 是 RESX 资源的资源标识符。 它由默认命名空间、资源文件所处文件夹和默认 RESX 文件名组成。
* 使用 `IntrospectionExtensions.GetTypeInfo(typeof(TranslateExtension)).Assembly)` 创建 `ResourceManager` 类，以确定要从其加载资源的当前程序集，并缓存在静态 `ResMgr` 字段中。 它将作为 `Lazy` 类型创建，因此创建操作将推迟到在 `ProvideValue` 方法中第一次使用它时发生。
* `ci` 使用依赖项服务获取本机操作系统中的用户所选语言。
* `GetString` 是从资源文件中检索实际的已翻译字符串的方法。 在通用 Windows 平台上，`ci` 将为 null，因为在这些平台上无法实现 `ILocalize` 界面。 这相当于仅使用第一个参数调用 `GetString` 方法。 相反，资源框架会自动识别区域设置，并从相应的 RESX 文件中检索已翻译的字符串。
* 错误处理已包括在内，以通过引发异常帮助调试缺少的资源（仅限 `DEBUG` 模式下）。

以下 XAML 代码片段演示如何使用标记扩展。 可通过两个步骤实现：

1. 在根节点中声明自定义 `xmlns:i18n` 命名空间。 `namespace` 和 `assembly` 必须完全匹配项目设置 - 在此示例中，它们完全一样，但在你的项目中可能不同。
2. 对属性使用 `{Binding}` 通常包含调用 `Translate` 标记扩展的文本。 资源键是唯一的必需参数。

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        x:Class="UsingResxLocalization.FirstPageXaml"
        xmlns:i18n="clr-namespace:UsingResxLocalization;assembly=UsingResxLocalization">
    <StackLayout Padding="0, 20, 0, 0">
        <Label Text="{i18n:Translate NotesLabel}" />
        <Entry Placeholder="{i18n:Translate NotesPlaceholder}" />
        <Button Text="{i18n:Translate AddButton}" />
    </StackLayout>
</ContentPage>
```

以下更详细的语法也适用于标记扩展：

```xaml
<Button Text="{i18n:TranslateExtension Text=AddButton}" />
```

## <a name="localizing-platform-specific-elements"></a>本地化特定平台的元素

尽管我们可以在 Xamarin.Forms 代码中处理用户界面翻译，但在每个平台特定项目中，均存在必须完成的某些元素。 本部分将介绍如何本地化：

* Application Name
* 图像

示例项目包括名为 flag.png 的示例项目，在 C# 中引用，如下所示：

```csharp
var flag = new Image();
switch (Device.RuntimePlatform)
{
    case Device.iOS:
    case Device.Android:
        flag.Source = ImageSource.FromFile("flag.png");
        break;
    case Device.UWP:
        flag.Source = ImageSource.FromFile("Assets/Images/flag.png");
        break;
}
```

标记图像也在 XAML 中引用，如下所示：

```xaml
<Image>
  <Image.Source>
    <OnPlatform x:TypeArguments="ImageSource">
        <On Platform="iOS, Android" Value="flag.png" />
        <On Platform="UWP" Value="Assets/Images/flag.png" />
    </OnPlatform>
  </Image.Source>
</Image>
```

只要实现如下所述的项目结构，所有平台就会自动将此类图像引用解析为图像的本地化版本。

### <a name="ios-application-project"></a>iOS 应用程序项目

iOS 使用名为本地化项目或 .lproj 目录的命名标准，以包含图像和字符串资源。 这些目录可以包含应用中使用的图像的本地化版本以及可用于本地化应用名称的 InfoPlist.strings 文件。 有关 iOS 本地化的详细信息，请参阅 [iOS 本地化](~/ios/app-fundamentals/localization/index.md)。

#### <a name="images"></a>图像

此屏幕截图显示了具有特定语言 .lproj 目录的 iOS 示例应用。 称为 es.lproj 的西班牙语目录包含默认图像的本地化版本，以及 flag.png：

![](text-images/ios-resources.png "iOS 本地化项目目录")

每个语言目录都包含针对该语言本地化的 flag.png 副本。 如果没有提供图像，则操作系统将默认使用默认语言目录中的图像。 有关完整的 Retina 支持，应提供每个图像的 @2x 和 @3x 副本。

#### <a name="app-name"></a>应用程序名称

InfoPlist.strings 的内容只是一个键值，用于配置应用名称：

```csharp
"CFBundleDisplayName" = "ResxEspañol";
```

运行应用程序时，将同时本地化应用名称和图像：

![](text-images/ios-imageicon.png "iOS 示例应用文本和图像本地化")

### <a name="android-application-project"></a>Android 应用程序项目

Android 遵循不同的方案用于存储本地化图像，使用具有语言代码后缀的可绘制资源和字符串目录。 如果需要四个字母的区域设置代码（例如，zh-TW 或 pt-BR），请注意，Android 在短划线/区域设置代码后需要一个额外的 r（例如， zh-rTW 或 pt-rBR）。 有关 Android 本地化的详细信息，请参阅 [Android 本地化](~/android/app-fundamentals/localization.md)。

#### <a name="images"></a>图像

此屏幕截图显示具有部分本地化可绘制资源和字符串的 Android 示例：

![](text-images/android-resources.png "Android 本地化可绘制资源和字符串目录")

请注意，Android 不使用 Zh-Hans 和 Zh-Hant 代码表示简体中文和繁体中文；相反，它仅支持特定于国家/地区的代码 zh-CN 和 zh-TW。

若要支持适用于高密度屏幕的不同分辨率图像，请创建具有 `-*dpi` 后缀的其他语言文件夹，例如，drawables-es-mdpi、drawables-es-xdpi、drawables-es-xxdpi 等。有关详细信息，请参阅[提供替代的 Android 资源](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。

#### <a name="app-name"></a>应用程序名称

strings.xml 的内容只是一个键值，用于配置应用名称：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">ResxEspañol</string>
</resources>
```

更新 Android 应用中的 MainActivity.cs，使 `Label` 引用字符串 XML。

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

现在，应用将本地化应用名称和图像。 下面是结果的屏幕截图（西班牙语）：

![](text-images/android-imageicon.png "Android 示例应用文本和图像本地化")

### <a name="universal-windows-platform-application-projects"></a>通用 Windows 平台应用程序项目

通用 Windows 平台具有一个资源基础结构，可简化图像和应用程序名称的本地化。 有关 UWP 本地化的详细信息，请参阅 [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)。

#### <a name="images"></a>图像

可以通过将图像放在特定资源的文件夹中对其本地化，如以下屏幕截图中所示：

![](text-images/uwp-image-folder-structure.png "UWP 图像本地化文件夹结构")

在运行时，Windows 资源基础结构将根据用户的区域设置选择相应的图像。

## <a name="summary"></a>总结

可以使用 RESX 文件和 .NET 全球化类本地化 Xamarin.Forms 应用程序。 除少量平台特定代码用于检测用户首选语言外，大部分本地化工作均围绕通用代码进行。

通常以特定平台的方式处理图像，以充分利用 iOS 和 Android 中提供的多分辨率支持。

## <a name="related-links"></a>相关链接

- [RESX 本地化示例](https://developer.xamarin.com/samples/xamarin-forms/UsingResxLocalization/)
- [TodoLocalized 示例应用](https://developer.xamarin.com/samples/xamarin-forms/TodoLocalized/)
- [跨平台本地化](~/cross-platform/app-fundamentals/localization.md)
- [iOS 本地化](~/ios/app-fundamentals/localization/index.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
- [UWP 本地化](/windows/uwp/design/globalizing/globalizing-portal/)
- [使用 CultureInfo 类 (MSDN)](https://msdn.microsoft.com/library/87k6sx8t%28v=vs.90%29.aspx)
- [使用特定区域性的资源 (MSDN)](https://msdn.microsoft.com/library/s9ckwb4b%28v=vs.90%29.aspx)
