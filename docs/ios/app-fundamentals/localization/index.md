---
title: 在 Xamarin.iOS 的本地化
description: 本文档介绍 iOS 本地化功能以及如何在 Xamarin.iOS 应用中使用这些功能。 它讨论了语言、 区域设置、 字符串文件、 启动图像和的详细信息。
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
ms.openlocfilehash: 11926d3cf0c3954c97cdd1e92283626067c9623f
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58678010"
---
# <a name="localization-in-xamarinios"></a>在 Xamarin.iOS 的本地化

_本文档介绍 iOS SDK 的本地化功能以及如何使用 Xamarin 访问它们。_

请参阅[国际化编码](encodings.md)有关包括字符集/代码页必须处理非 Unicode 数据的应用程序中的说明。

## <a name="ios-platform-features"></a>iOS 平台功能

本部分介绍了一些在 iOS 中的本地化功能。 请跳到[下一节](#localization-basics-in-ios)若要查看特定的代码和示例。

### <a name="language"></a>语言

用户选择在其语言**设置**应用。 此设置会影响语言字符串和由操作系统和应用中显示的图像。

若要确定在应用中所用的语言，请获取的第一个元素`NSBundle.MainBundle.PreferredLocalizations`:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

此值将为语言代码等`en`英文版`es`西班牙语，`ja`对于日语，等等。返回的值仅限于一个 （使用回退规则来确定最佳匹配项） 的应用程序支持本地化信息。

应用程序代码不总是需要检查此值 – Xamarin 和 iOS 这两个提供功能，可帮助自动为用户的语言中提供正确的字符串或资源。 本文档的其余部分中介绍了这些功能。

> [!NOTE]
> 使用`NSLocale.PreferredLanguages`来确定用户的语言首选项，而不考虑应用支持的本地化信息。 在 iOS 9; 中更改此方法返回的值请参阅[技术注意 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html)有关详细信息。

### <a name="locale"></a>区域设置

用户选择其区域设置**设置**应用。 此设置会影响日期、 时间、 数字和货币的格式的方式。

这允许用户选择他们是否看到 12 小时或 24 小时时间格式，无论其小数分隔符是逗号，或一个点，并且的日、 月和年中日期的显示顺序。

可以使用 Xamarin 访问这两个 Apple 的 iOS 类 (`NSNumberFormatter`) 以及 System.Globalization 中的.NET 类。 由于有不同的功能可在每个开发人员应评估这是更好地适合其需求。 具体而言，如果您是检索并显示应用内购买价格使用 StoreKit 应返回的价格信息的使用 Apple 的格式设置类。

可以通过两种方式查询当前区域设置：

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一个值可以由操作系统缓存，因此可能不总能反映用户的当前所选区域设置。 第二个值用于获取当前所选的区域设置。

> [!NOTE]
> Mono （.NET 运行时基于 Xamarin.iOS） 和 Apple 的 iOS Api 不支持完全相同的语言/区域组合集。
> 正因为如此，则可以在 iOS 中选择语言/区域组合**设置**不映射到有效的值在 Mono 中的应用。 例如，iPhone 的语言为英语和其区域设置为西班牙将导致以下 Api 来产生不同的值：
>
> - `CurrentThead.CurrentCulture`: EN-US (Mono API)
> - `CurrentThread.CurrentUICulture`: EN-US (Mono API)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (Apple API)
>
> 由于使用 Mono`CurrentThread.CurrentUICulture`若要选择的资源和`CurrentThread.CurrentCulture`设置日期和货币的格式，基于 Mono 的本地化 （例如，使用.resx 文件） 可能不产生预期的结果为这些语言/区域组合。 在这些情况下，依赖于 Apple 的 Api，以根据需要本地化。

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS 生成`NSCurrentLocaleDidChangeNotification`用户更新其区域设置时。 应用程序可以侦听此通知，虽然它们正在运行，并可以对 UI 进行适当更改。

## <a name="localization-basics-in-ios"></a>在 iOS 中的本地化基础知识

若要显示给用户提供本地化的资源的 Xamarin 中可轻松利用 iOS 的以下功能。 请参阅[TaskyL10n 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)若要了解如何实现这些概念。

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>在 info.plist 文件中指定默认值和支持的语言

在[技术问题与解答 QA1828:IOS 如何确定语言为您的应用程序](https://developer.apple.com/library/content/qa/qa1828/_index.html)，Apple 介绍 iOS 如何选择要在应用中使用的语言。 以下因素会影响显示的语言：

- 用户的首选语言 (在中找到**设置**应用)
- 与应用程序 （.lproj 文件夹） 捆绑在一起的本地化信息
- `CFBundleDevelopmentRegion` (**Info.plist**值，该值指定该应用程序的默认语言)
- `CFBundleLocalizations` (**Info.plist**数组中指定所有受支持的本地化信息)

如下所示技术问答，`CFBundleDevelopmentRegion`表示应用程序的默认区域和语言。 如果应用程序并不显式支持的任何用户的首选语言，它将使用此字段指定的语言。

> [!IMPORTANT]
> iOS 11 比以前版本的操作系统更严格地应用此语言选择机制。 因此，未显式声明支持的本地化 – 通过包括.lproj 文件夹或设置一个值的任何 iOS 11 应用`CFBundleLocalizations`– 不是像在 iOS 10 中可能会显示 iOS 11 中的另一种语言。

如果`CFBundleDevelopmentRegion`不中指定**Info.plist**文件中，Xamarin.iOS 生成工具当前使用的默认值为`en_US`。 虽然这可能会更改在将来的版本中，这意味着默认语言为英语。

若要确保您的应用程序选择所需的语言，请执行以下步骤：

- 指定默认语言。 打开**Info.plist**并用**源**视图，以设置的值`CFBundleDevelopmentRegion`键; 在 XML 中，它应该看起来如下所示：

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

此示例使用"es"若要指定，当无用户的首选语言支持，默认为西班牙语。

- 声明所有支持的本地化信息。 在**Info.plist**，使用**源**视图，以设置一个数组以便`CFBundleLocalizations`键; 在 XML 中，它应该看起来如下所示：

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

使用.NET 的机制，如.resx 文件必须提供这些已本地化的 Xamarin.iOS 应用**Info.plist**的值。

有关这些详细信息**Info.plist**密钥，请参阅 Apple[信息属性列表键引用](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)。

### <a name="getlocalizedstring-method"></a>GetLocalizedString 方法

`NSBundle.MainBundle.GetLocalizedString`方法查找已存储在中的本地化文本 **.strings**项目中的文件。 这些文件按语言版本，在包含的特别命名目录 **.lproj**后缀 （请注意该扩展的第一个字母为小写"L"）。

#### <a name="strings-file-locations"></a>.strings 文件位置

- **Base.lproj**是包含默认语言资源的目录。
  它通常位于项目根目录中 (但也可以置于**资源**文件夹)。
- **&lt;语言&gt;.lproj**目录创建时为每个支持的语言，通常**资源**文件夹。

可以有多个不同 **.strings**每个语言目录中的文件：

- **Localizable.strings** – 本地化文本的主列表。
- **InfoPlist.strings** – 某些特定的密钥在此文件中允许转换等应用程序名称。
- **< 情节提要的名称 >.strings** – 可选文件，其中包含翻译的情节提要中的用户界面元素。

**生成操作**这些文件应**捆绑资源**。

#### <a name="strings-file-format"></a>.strings 文件格式

本地化的字符串值的语法是：

```console
/* comment */
"key"="localized-value";
```

应转义字符串中的以下字符：

* `\"` 引号
* `\\` 反斜杠
* `\n` 换行符

这是一个示例**es/Localizable.strings** (ie。西班牙语） 文件中的示例：

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>图像

若要本地化 iOS 中的映像：

1. 请参阅图在代码中，例如：

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. 将默认图像文件放**flag.png**中**Base.lproj** （本机开发语言目录）。

3. 根据需要将中的图像的本地化的版本放 **.lproj**文件夹 （例如每种语言。 **es.lproj**， **ja.lproj**)。 使用相同的文件名**flag.png**中每个语言的目录。

如果映像不存在特定的语言，iOS 将回退到默认的本机语言文件夹并从那里加载图像。

#### <a name="launch-images"></a>启动图像

用于启动映像 （和 XIB 或情节提要的 iPhone 6 模型） 的标准命名约定时将它们中放置 **.lproj**每种语言的目录。

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>应用名称

放置**InfoPlist.strings**中的文件 **.lproj**目录可让您覆盖从应用程序的某些值**Info.plist**，包括应用程序名称：

```console
"CFBundleDisplayName" = "LeónTodo";
```

您可以使用其他密钥[本地化应用程序特有字符串](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)是：

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>日期和时间

尽管可以使用内置的.NET 日期和时间函数 (以及当前`CultureInfo`) 若要格式化日期和时间为区域设置，这会忽略特定于区域设置的用户设置 （这可以为单独设置从语言）。

使用 iOS`NSDateFormatter`以生成与用户的区域设置首选项匹配的输出。 下面的示例代码演示了基本的日期和时间格式设置选项：

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

在美国英语的结果：

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

在西班牙的西班牙语的结果：

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

请参阅 Apple[日期格式化程序](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)文档了解详细信息。 当测试区分区域设置的日期和时间格式设置，则检查这两**iPhone 语言**并**区域**设置。

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>从右到左 (RTL) 布局

iOS 提供了许多功能可帮助构建 RTL 识别应用程序：

- 使用自动布局`leading`和`trailing`控件的对齐方式 （这对应于左侧和右侧表示英语，但是对于 RTL 语言相反） 的属性。
  [ `UIStackView` ](~/ios/user-interface/controls/uistackview.md)控件是对布局控件是 RTL 感知特别有用。
- 使用`TextAlignment = UITextAlignment.Natural`（这将会保留对于大多数语言，但适合 RTL） 的文本对齐方式。
- `UINavigationController` 自动翻转后退按钮，并反转轻扫方向。

下面的屏幕截图演示[本地化 Tasky 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)阿拉伯语和希伯来语中 （尽管已在字段中输入英语）：

[![](images/rtl-ar-sml.png "在阿拉伯语中的本地化")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "希伯来语版本的本地化")](images/rtl-he.png#lightbox "Hebrew")

iOS 自动反转`UINavigationController`，和其他控件放置到`UIStackView`或使用自动布局对齐。
使用本地化的文本按 RTL **.strings** LTR 文本的方式相同的文件。

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>本地化代码中的用户界面

[Tasky （在代码中已本地化）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)示例演示如何本地化应用程序用户界面中的代码 （而不是 Xib 或情节提要） 的生成位置。

### <a name="project-structure"></a>项目结构

![](images/solution-code.png "资源树")

### <a name="localizablestrings-file"></a>Localizable.strings 文件

上文所述**Localizable.strings**文件格式包含键 / 值对。 密钥描述字符串的意图，值为要在应用中使用的已翻译的文本。

西班牙语 (**es**) 本地化信息的示例如下所示：

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>执行本地化

在应用程序代码中，无论在何处 （无论它是标签的文本，或输入的占位符，等等） 设置用户界面的显示文本的代码使用 iOS`GetLocalizedString`函数以检索正确的转换，以显示：

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>本地化情节提要 Ui

该示例[Tasky （本地化的情节提要）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)演示如何以本地化情节提要中的控件上的文本。

### <a name="project-structure"></a>项目结构

**Base.lproj**目录包含情节提要，并且还应包含应用程序中使用的任何映像。

其他语言目录包含**Localizable.strings**引用在代码中，任何字符串资源文件以及一个**MainStoryboard.strings**文件，其中包含中的文本翻译情节提要。

![](images/solution-storyboard.png "资源树")

语言目录应包含已本地化，若要重写中的任何映像的副本**Base.lproj**。

### <a name="object-id--localization-id"></a>对象 ID / 本地化 ID

创建和编辑情节提要中的控件，请选择每个控件，并选中要用于本地化的 ID:

- 在 Visual Studio for Mac 中，该文件位于**Properties Pad**称为**本地化 ID**。
- 在 Xcode 中，名为**对象 ID**。

此字符串值通常具有"NF3-h8-xmR"，如一个窗体，如以下屏幕截图中所示：

![](images/xs-designer-localization-id.png "情节提要本地化的 Xcode 视图")

在使用此值 **.strings**文件以自动将已翻译的文本分配给每个控件。

### <a name="mainstoryboardstrings"></a>MainStoryboard.strings

情节提要转换文件的格式是类似于**Localizable.strings**文件，只不过密钥 （在左侧的值） 不能是用户定义，但改为必须具有非常特定的格式： `ObjectID.property`。

在示例**Mainstoryboard.strings**下面介绍`UITextField`具有`placeholder`可本地化; 的 text 属性`UILabel`具有`text`属性; 并且`UIButton`s 默认文本使用设置`normalTitle`:

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> 使用情节提要的大小类使用可能会导致不会显示在应用程序中的翻译。 [Apple 的 Xcode 发行说明](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html)指示，情节提要或 XIB 将本地化正确是否三件事是，则返回 true： 它使用大小类、 基本地化和 build 目标设置为世界，并生成面向 iOS 7.0。 解决方法是在情节提要字符串文件复制到两个完全相同的文件：**MainStoryboard~iphone.strings**并**MainStoryboard~ipad.strings**，如以下屏幕截图中所示：
>
> ![](images/xs-dup-strings.png "字符串的文件")

<a name="appstore" />

## <a name="app-store-listing"></a>应用商店列表

遵守 Apple 的常见问题解答[应用程序存储区本地化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)输入您的应用程序是在销售的每个国家/地区的翻译。 请注意如果您的应用程序还包含本地化翻译将仅显示其警告 **.lproj**目录的语言。

## <a name="summary"></a>总结

本文介绍如何本地化 iOS 应用程序使用内置的资源处理和情节提要功能的基础知识。

您可以了解更多有关 i18n 和 L10n 的 iOS、 Android 和跨平台应用 （包括 Xamarin.Forms） 中[此跨平台指南](~/cross-platform/app-fundamentals/localization.md)。

## <a name="related-links"></a>相关链接

- [Tasky （在代码中已本地化） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky （本地化的情节提要） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 本地化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 本地化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
