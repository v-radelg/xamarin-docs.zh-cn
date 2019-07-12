---
title: Android 本地化
description: 本文档介绍了本地化功能的 Android SDK 以及如何使用 Xamarin 访问它们。
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f6a7f8859340dcc8e48b6a4e6f56847168f4b71e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829763"
---
# <a name="android-localization"></a>Android 本地化

_本文档介绍了本地化功能的 Android SDK 以及如何使用 Xamarin 访问它们。_

## <a name="android-platform-features"></a>Android 平台功能

本部分介绍 Android 的主要本地化功能。 请跳到[下一节](#basics)若要查看特定的代码和示例。

### <a name="locale"></a>区域设置

用户选择在其语言**设置 > 语言和输入**。 选择此选项控制显示的语言和区域设置使用 （例如。 适用于日期和数字格式设置）。

可通过当前上下文的查询的当前区域设置`Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

此值将为包含语言代码和区域设置代码，以下划线分隔的区域设置标识符。 有关参考，下面是[Java 区域设置列表](https://www.oracle.com/technetwork/java/javase/locales-137662.html)并[StackOverflow 通过 Android 支持区域设置](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android)。

常见示例包括：

* `en_US` 英语 （美国）
* `es_ES` 西班牙语 （西班牙）
* `ja_JP` 日语 （日本）
* `zh_CN` 中文 （中国）
* `zh_TW` 中文 （台湾）
* `pt_PT` 葡萄牙语 （葡萄牙）
* `pt_BR` 葡萄牙语 （巴西）

### <a name="localechanged"></a>LOCALE_CHANGED

Android 生成`android.intent.action.LOCALE_CHANGED`当用户更改其语言选择。

可以选择活动来处理此情况设置`android:configChanges`属性上的活动，如下：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>在 Android 中的国际化基础知识

Android 的本地化策略具有以下主要部分：

- 若要包含本地化的字符串、 图像和其他资源的资源文件夹。

- `GetText` 方法，用于检索代码中的已本地化的字符串

- `@string/id` 在 AXML 文件，以自动放置在布局中的已本地化的字符串。

### <a name="resource-folders"></a>资源文件夹

Android 应用程序管理资源文件夹中的大多数内容，例如：

* **布局**-包含 AXML 布局文件。
* **可绘制**-包含图像和其他可绘制资源。
* **值**-包含字符串。
* **原始**-包含数据文件。

大多数开发人员已熟悉使用**dpi**上后缀**drawable** directory 来提供多个版本的映像，让 Android 选择每个设备的正确版本。 相同的机制用于后缀与语言和区域性标识符的资源目录中提供多个语言翻译。

![多个区域性标识符资源/drawable 和资源中的值文件夹的屏幕截图](localization-images/resources.png)

> [!NOTE]
> 指定等顶级语言时`es`只有两个字符是必需的; 但在指定完整的区域设置时，目录名称格式需要短划线和小写**r**来分隔两个部分，例如**pt rBR**或**zh rCN**。 这与在代码中，其中包含下划线 （例如返回的值。 `pt_BR`）格式模式中出现的位置生成。 这两种是不同的值.NET`CultureInfo`类使用，其中包含短划线仅 （例如。 `pt-BR`）格式模式中出现的位置生成。 在 Xamarin 平台上工作时，请记住这些差异。

#### <a name="stringsxml-file-format"></a>Strings.xml 文件格式

本地化**值**目录 （例如。 **值 es**或**值 pt rBR**) 应包含名为的文件**Strings.xml**将包含对该区域设置翻译后的文本。

每个翻译的字符串是具有 ID 指定为资源的 XML 元素`name`属性和已翻译的字符串的值：

```xml
<string name="app_name">TaskyL10n</string>
```

需要根据正常 XML 规则，转义和`name`必须是有效的 Android 资源 ID （没有空格或短划线）。 下面是该示例的默认 （英语） 字符串文件的示例：

**values/Strings.xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

西班牙语目录**值 es**包含具有相同名称的文件 (**Strings.xml**)，其中包含翻译：

**values-es/Strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![屏幕截图的多个值的文件夹，每个都包含 Strings.xml 文件](localization-images/values.png)

与字符串文件设置后，可以在布局和代码中引用已翻译的值。

### <a name="axml-layout-files"></a>AXML 布局文件

若要引用布局文件中的已本地化的字符串，使用`@string/id`语法。 此 XML 代码段的示例所示`text`属性所设置的本地化资源 Id （已省略某些其他属性）：

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>GetText 方法

若要检索已翻译的字符串在代码中，使用`GetText`方法并传入的资源 ID:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>数量字符串

Android 字符串资源，您还可以创建*数量字符串*这使翻译人员提供不同的翻译，对于不同的数量，例如：

* "没有剩余 1 个任务。"
* "有 2 任务仍将执行操作。"

（而不是泛型"有 n 个任务左"）。

在**Strings.xml**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

若要呈现完整字符串使用`GetQuantityString`方法，传递的资源 ID 以及要显示的值 （其中传递两次）。 Android 使用第二个参数来确定*这*`quantity`要使用的字符串，第三个参数是实际代入 （两者都是必需的） 的字符串值。

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

有效`quantity`开关是：

* 零
* one
* 两个
* 几个
* many
* 其他

中的更多详细信息中所述[Android 文档](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals)。如果给定的语言不需要特殊处理时，那些`quantity`字符串将被忽略 (例如，英语仅使用`one`并`other`; 指定`zero`字符串会产生任何效果，将不使用它)。

### <a name="images"></a>映像

本地化的映像遵循相同的规则作为字符串文件： 应用程序中引用的所有图像应都置于**drawable**目录，因此没有回退。

特定于区域设置的映像应随后可以放置在限定可绘制文件夹等**drawable es**或**drawable-ja** （还可以添加 dpi 说明符）。

在此屏幕截图，四个映像保存在**drawable**目录中，但只有一个分区**flag.png**，已本地化其他目录中的副本。

![多个可绘制文件夹，每个都包含一个或多个本地化的.png 文件的屏幕截图](localization-images/drawable.png)


#### <a name="other-resource-types"></a>其他资源类型

您还可以提供其他类型的替代方法，包括布局、 动画和原始文件的特定于语言的资源。 这意味着，您可以提供特定的屏幕布局的一个或多个目标语言，例如你可以创建一个布局专门为德语，允许很长的文本标签。

Android 4.2 引入了对支持[从右到左 (RTL) 语言](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html)如果你设置的应用程序设置`android:supportsRtl="true"`。 资源限定符`"ldrtl"`可以包含在要包含专为从右向左显示的自定义布局目录名称。

有关资源目录命名和故障回复的详细信息，请参阅 Android 文档[提供替代资源](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。


### <a name="app-name"></a>应用名称

应用程序名称很容易通过使用本地化`@string/id`中为`MainLauncher`活动：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>右到左 (RTL) 语言

Android 4.2 及更高版本提供完全支持对于 RTL 布局，详见[本机 RTL 支持博客](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html)。

使用 Android 4.2 （API 级别 17） 时和更高版本，可以使用指定值的对齐方式`start`并`end`而不是`left`并`right`(例如`android:paddingStart`)。 此外，还有新的 Api，如`LayoutDirection`， `TextDirection`，和`TextAlignment`以帮助您构建适应屏幕从右向左读取器。

以下屏幕截图显示[本地化**Tasky**示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)在阿拉伯语中：

[![在阿拉伯语 Tasky 应用的屏幕截图](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

下一步的屏幕截图显示[本地化**Tasky**示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)希伯来语版本：

[![希伯来语版本 Tasky 应用的屏幕截图](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

使用本地化的文本按 RTL **Strings.xml** LTR 文本的方式相同的文件。

## <a name="testing"></a>正在测试

请务必全面测试的默认区域设置。 如果出于某种原因 （即它们是缺少） 无法加载默认资源，你的应用程序将崩溃。

### <a name="emulator-testing"></a>模拟器测试

请参阅 Google [Android 模拟器上测试](https://developer.android.com/guide/topics/resources/localization.html#testing)部分，了解如何将模拟器设置为使用 ADB 命令行程序的特定区域设置的说明。

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>设备测试

若要在设备上测试，更改所使用的语言**设置**应用。

> [!TIP]
> 请记下的图标和菜单项的位置，以便可以还原到原始设置的语言。


## <a name="summary"></a>总结

本文介绍如何本地化使用内置的资源处理的 Android 应用程序的基础知识。 针对 iOS、 Android 和跨平台 （包括 Xamarin.Forms) 中的应用程序，可以了解更多有关 i18n 和 L10n[此跨平台指南](~/cross-platform/app-fundamentals/localization.md)。



## <a name="related-links"></a>相关链接

- [Tasky （在代码中已本地化） （示例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Android 资源与本地化](https://developer.android.com/guide/topics/resources/localization.html)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 本地化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS 本地化](~/ios/app-fundamentals/localization/index.md)
