---
title: Xamarin 中的本地化
description: 本文档介绍 iOS 本地化功能以及如何在 Xamarin iOS 应用中使用这些功能。 它讨论语言、区域设置、字符串文件、启动图像等。
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/28/2017
ms.openlocfilehash: 604313009129ad3c7133098d8e7880b0e07eef6e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527105"
---
# <a name="localization-in-xamarinios"></a>Xamarin 中的本地化

_本文档介绍 iOS SDK 的本地化功能, 以及如何使用 Xamarin 访问这些功能。_

请参阅[国际化编码](encodings.md), 了解在必须处理非 Unicode 数据的应用程序中包含字符集/代码页的说明。

## <a name="ios-platform-features"></a>iOS 平台功能

本部分介绍 iOS 中的一些本地化功能。 跳到[下一节](#localization-basics-in-ios), 查看具体的代码和示例。

### <a name="language"></a>语言

用户在 "**设置**" 应用程序中选择其语言。 此设置会影响操作系统和应用程序中显示的语言字符串和图像。

若要确定应用程序中使用的语言, 请获取的第一个`NSBundle.MainBundle.PreferredLocalizations`元素:

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

此值将是语言代码`en` , 例如英语、 `es`西班牙语`ja` 、日语等。返回的值仅限于应用程序支持的本地化之一 (使用回退规则确定最佳匹配)。

应用程序代码不会始终需要检查此值– Xamarin 和 iOS 均提供有助于自动为用户语言提供正确字符串或资源的功能。 本文档的其余部分介绍了这些功能。

> [!NOTE]
> 使用`NSLocale.PreferredLanguages`确定用户的语言首选项, 而不考虑应用程序支持的本地化。 此方法返回的值在 iOS 9 中发生了更改;有关详细信息, 请参阅[技术说明 TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) 。

### <a name="locale"></a>区域设置

用户在 "**设置**" 应用程序中选择其区域设置。 此设置会影响日期、时间、数字和货币的格式设置方式。

这样, 用户就可以选择他们是否看到12小时或24小时时间格式, 它们的小数分隔符是逗号还是一个点, 以及日期、月份和年份在日期显示中的顺序。

借助 Xamarin, 你可以访问 Apple 的 iOS 类 (`NSNumberFormatter`) 以及 system.exception 中的 .net 类。 开发人员应评估哪一项更适合于其需求, 因为每种功能都有不同的功能可用。 特别是, 如果您正在使用 StoreKit 检索和显示应用内购买价格, 则应将 Apple 的格式设置类用于返回的价格信息。

可以通过以下两种方式之一来查询当前区域设置:

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

第一个值可以由操作系统进行缓存, 因此可能不会始终反映用户当前所选的区域设置。 使用第二个值获取当前选择的区域设置。

> [!NOTE]
> Mono (Xamarin 基于的 .NET 运行时), Apple 的 iOS Api 不支持相同的语言/区域组合集。
> 因此, 可以在 iOS**设置**应用中选择不会映射到 Mono 中有效值的语言/区域组合。 例如, 将 iPhone 的语言设置为英语, 并将其区域设置为西班牙将导致以下 Api 产生不同的值:
>
> - `CurrentThead.CurrentCulture`: en-us (Mono API)
> - `CurrentThread.CurrentUICulture`: en-us (Mono API)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (Apple API)
>
> 由于 mono 使用`CurrentThread.CurrentUICulture`来选择资源并`CurrentThread.CurrentCulture`设置日期和货币的格式, 因此基于 Mono 的本地化 (例如, 对于 .resx 文件) 可能不会为这些语言/区域组合产生预期结果。 在这些情况下, 根据需要依赖于 Apple 的 Api 进行本地化。

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

`NSCurrentLocaleDidChangeNotification`当用户更新其区域设置时, iOS 会生成。 应用程序可以在运行时侦听此通知, 并且可以对 UI 做出适当的更改。

## <a name="localization-basics-in-ios"></a>IOS 中的本地化基础知识

Xamarin 中的以下功能可以轻松地在 Xamarin 中利用, 以提供用于向用户显示的本地化资源。 请参阅[TaskyL10n 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n), 了解如何实现这些想法。

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>指定 info.plist 中的默认语言和支持的语言

在[技术问答中 & QA1828:Ios 如何确定应用](https://developer.apple.com/library/content/qa/qa1828/_index.html)的语言, Apple 介绍了 ios 如何选择要在应用中使用的语言。 以下因素会影响显示的语言:

- 用户的首选语言 (在 "**设置**" 应用中找到)
- 与应用捆绑的本地化 (. lproj 文件夹)
- `CFBundleDevelopmentRegion`(**Info.plist**值指定应用的默认语言)
- `CFBundleLocalizations`(**Info.plist 数组,** 指定支持的所有本地化)

如技术 Q & 中所示, `CFBundleDevelopmentRegion`表示应用的默认区域和语言。 如果应用程序未显式支持用户的任何首选语言, 则它将使用此字段指定的语言。

> [!IMPORTANT]
> iOS 11 比以前版本的操作系统更严格地应用此语言选择机制。 因此, 任何不显式声明其受支持的本地化的 iOS 11 应用程序 (通过包括 lproj 文件夹或设置的`CFBundleLocalizations`值) 可能会在 ios 11 中显示不同于 ios 10 的语言。

如果`CFBundleDevelopmentRegion`未在**info.plist**文件中指定, 则 Xamarin build 工具当前使用默认值`en_US`。 尽管这可能会在将来的版本中发生更改, 但这意味着默认语言是英语。

若要确保你的应用选择所需的语言, 请执行以下步骤:

- 指定默认语言。 打开**info.plist**并使用**源**视图为`CFBundleDevelopmentRegion`该项设置值; 在 XML 中, 它应类似于以下内容:

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

此示例使用 "es" 指定在不支持任何用户的首选语言时, 默认为西班牙语。

- 声明所有支持的本地化。 在**info.plist**中, 使用**源**视图设置`CFBundleLocalizations`键的数组; 在 XML 中, 它应类似于以下内容:

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

使用 .NET 机制 (如 .resx 文件) 进行本地化的 Xamarin iOS 应用程序也必须提供这些**info.plist**值。

有关这些**info.plist**密钥的详细信息, 请参阅 Apple 的[信息属性列表关键参考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)。

### <a name="getlocalizedstring-method"></a>GetLocalizedString 方法

方法查找已存储在项目中的字符串文件中的本地化文本。 `NSBundle.MainBundle.GetLocalizedString` 这些文件按语言组织, 在特殊命名目录中使用 **. lproj**后缀 (请注意, 扩展的第一个字母为小写的 "L")。

#### <a name="strings-file-locations"></a>字符串文件位置

- **Lproj**是包含默认语言资源的目录。
  它通常位于项目根目录中 (但也可以放在**资源**文件夹中)。
- **&lt;将&gt;** 为每种支持的语言创建 lproj 目录, 通常在**Resources**文件夹中。

每个语言目录中可以有多个不同的**字符串**文件:

- 可**本地化的字符串**–本地化文本的主要列表。
- **InfoPlist** -此文件中允许使用某些特定的键来转换应用程序名称等项。
- 情节提要-名称 > 包含情节提要中的用户界面元素翻译的可选文件。  **\<**

应将这些文件的**生成操作** **绑定到资源**。

#### <a name="strings-file-format"></a>字符串文件格式

本地化字符串值的语法为:

```console
/* comment */
"key"="localized-value";
```

应转义字符串中的以下字符:

* `\"`引言
* `\\`符号
* `\n`换

这是**es/可本地化的示例。字符串**(即西班牙语) 文件:

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

### <a name="images"></a>映像

若要在 iOS 中本地化映像:

1. 在代码中引用图像, 例如:

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. 将默认图像文件**标记** **lproj** (本机开发语言目录)。

3. (可选) 在每种语言的**lproj**文件夹中放置图像的本地化版本 (例如 **lproj**, **lproj**)。 在每个语言目录中使用相同的文件名**标志。**

如果某一特定语言没有图像, 则 iOS 将回退到默认的本地语言文件夹, 并从该文件夹中加载图像。

#### <a name="launch-images"></a>启动映像

将启动映像的标准命名约定 (以及 iPhone 6 型号的 XIB 或情节提要) 用于每种语言。

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>应用名称

如果将**InfoPlist**文件放在**lproj**目录中, 则可以重写应用的**info.plist**中的某些值, 包括应用程序名称:

```console
"CFBundleDisplayName" = "LeónTodo";
```

可用于对[应用程序特定的字符串进行本地化](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21)的其他键包括:

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>日期和时间

尽管可以使用内置的 .net 日期和时间函数 (以及当前`CultureInfo`的) 来设置区域设置的日期和时间格式, 但这会忽略特定于区域设置的用户设置 (可与语言分开设置)。

使用 iOS `NSDateFormatter`生成与用户的区域设置首选项匹配的输出。 下面的示例代码演示了基本日期和时间格式设置选项:

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

美国中的英语结果:

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

西班牙西班牙语的结果:

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

有关详细信息, 请参阅 Apple[日期格式化](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html)程序文档。 在测试与区域设置相关的日期和时间格式时, 请检查**IPhone 语言**和**区域**设置。

<a name="rtl" />

### <a name="right-to-left-rtl-layout"></a>从右到左 (RTL) 布局

iOS 提供了许多功能, 可帮助构建 RTL 感知的应用:

- 为控件对齐使用`leading`自动`trailing`布局的和属性 (对应于中文的左侧和右侧), 但对于 RTL 语言为反转。
  [`UIStackView`](~/ios/user-interface/controls/uistackview.md)控件对于布局要为 RTL 感知的控件特别有用。
- `TextAlignment = UITextAlignment.Natural`用于文本对齐 (适用于大多数语言, 但对于 RTL 为右)。
- `UINavigationController`自动翻转后退按钮并反转滑动方向。

下面的屏幕截图显示了阿拉伯语和希伯来语 (尽管已在字段中输入英语) 的[本地化 Tasky 示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n):

[![](images/rtl-ar-sml.png "阿拉伯语本地化")](images/rtl-ar.png#lightbox "Arabic")

[![](images/rtl-he-sml.png "希伯来语本地化")](images/rtl-he.png#lightbox "Hebrew")

iOS 自动反转`UINavigationController`, 其他控件置于自动布局内部`UIStackView`或与之对齐。
使用**字符串**文件对 RTL 文本进行本地化, 其方式与 LTR 文本相同。

<a name="code"/>

## <a name="localizing-the-ui-in-code"></a>在代码中本地化 UI

[Tasky (在代码中本地化)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)示例演示如何本地化在代码 (而不是 xib 或情节提要) 中生成用户界面的应用程序。

### <a name="project-structure"></a>项目结构

![](images/solution-code.png "资源树")

### <a name="localizablestrings-file"></a>可本地化的字符串文件

如上所述, 可本地化的**字符串**文件格式由键值对组成。 键描述字符串的目的, 值是要在应用中使用的已翻译文本。

示例的西班牙语 (**es**) 本地化如下所示:

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

在应用程序代码中, 无论用户界面的显示文本是在什么位置设置的 (无论是标签文本还是输入占位符等), 代码都使用 iOS `GetLocalizedString`函数来检索要显示的正确转换:

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"/>

## <a name="localizing-storyboard-uis"></a>本地化情节提要 Ui

示例[Tasky (本地化情节提要)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)演示如何本地化情节提要中控件上的文本。

### <a name="project-structure"></a>项目结构

**Lproj**目录包含情节提要, 还应包含应用程序中使用的所有图像。

其他语言目录包含代码中引用的任何字符串资源的可**本地化的字符串**文件以及包含情节提要中的文本翻译的**mainstoryboard.storyboard**文件。

![](images/solution-storyboard.png "资源树")

语言目录应包含已本地化的任何映像的副本, 以替代**lproj**中存在的映像。

### <a name="object-id--localization-id"></a>对象 ID/本地化 ID

在情节提要中创建和编辑控件时, 请选择每个控件, 并检查要用于本地化的 ID:

- 在 Visual Studio for Mac 中, 它位于**Properties Pad**中, 称为**本地化 ID**。
- 在 Xcode 中, 它被称为**对象 ID**。

此字符串值通常具有如下所示的窗体: "NF3-h8-xmR", 如以下屏幕截图所示:

![](images/xs-designer-localization-id.png "情节提要本地化的 Xcode 视图")

此值用于**字符串**文件中, 用于将已翻译的文本自动分配给每个控件。

### <a name="mainstoryboardstrings"></a>Mainstoryboard.storyboard

情节提要翻译文件的格式类似于可本地化的**字符串**, 只不过键 (左侧的值) 不能是用户定义的, 而是必须具有非常具体的格式: `ObjectID.property`。

在下面的示例**mainstoryboard.storyboard**中, 你可以看到`UITextField`有一个`placeholder`可以本地化的文本属性;s 具有属性; `UIButton`默认文本使用`normalTitle`设置: `text` `UILabel`

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
> 使用带有大小类的情节提要可能会导致不会在应用程序中出现的翻译。 [Apple 的 Xcode 发行说明](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html)表明, 如果三个条件成立, 则情节提要或 XIB 将不会正确本地化: 它使用大小类, 基本本地化和生成目标设置为通用, 生成目标为 iOS 7.0。 解决方法是将情节提要字符串文件复制到两个相同的文件中:**Mainstoryboard.storyboard ~ iphone. string**和**mainstoryboard.storyboard ~ ipad**, 如以下屏幕截图所示:
>
> ![](images/xs-dup-strings.png "字符串文件")

<a name="appstore" />

## <a name="app-store-listing"></a>App Store 列表

遵循 Apple 的[应用商店本地化](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization)常见问题解答, 为你的应用销售的每个国家/地区输入翻译。 请注意, 如果您的应用程序还包含该语言的本地化的**lproj**目录, 则会出现转换。

## <a name="summary"></a>总结

本文介绍使用内置资源处理和情节提要功能本地化 iOS 应用程序的基础知识。

可在[此跨平台指南](~/cross-platform/app-fundamentals/localization.md)中了解适用于 IOS、Android 和跨平台应用 (包括 Xamarin) 的国际化和 L10n 的详细信息。

## <a name="related-links"></a>相关链接

- [Tasky (在代码中本地化) (示例)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (本地化情节提要) (示例)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Apple 本地化指南](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin. Forms 本地化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Android 本地化](~/android/app-fundamentals/localization.md)
