---
title: Android 本地化
description: 本文档介绍 Android SDK 的本地化功能, 以及如何使用 Xamarin 访问这些功能。
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c76eb8c6553768af68687ab6a45447a16775940e
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119139"
---
# <a name="android-localization"></a>Android 本地化

_本文档介绍 Android SDK 的本地化功能, 以及如何使用 Xamarin 访问这些功能。_

## <a name="android-platform-features"></a>Android 平台功能

本部分介绍 Android 的主要本地化功能。 跳到[下一节](#basics), 查看具体的代码和示例。

### <a name="locale"></a>区域设置

用户在 "设置" 中选择语言 **> 语言 & 输入**。 此选择控制所显示的语言和所使用的区域设置 (例如 对于日期和数字格式)。

当前区域设置可以通过当前上下文`Resources`进行查询:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

此值将是一个区域设置标识符, 其中包含用下划线分隔的语言代码和区域设置代码。 下面是通过 StackOverflow 提供[的 Java 区域设置](https://www.oracle.com/technetwork/java/javase/locales-137662.html)和[支持 Android 的区域设置](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android)列表。

常见示例包括：

- `en_US`对于英语 (美国)
- `es_ES`西班牙语 (西班牙)
- `ja_JP`对于日语 (日本)
- `zh_CN`适用于中文 (中国)
- `zh_TW`中文 (台湾)
- `pt_PT`对于葡萄牙语 (葡萄牙)
- `pt_BR`对于葡萄牙语 (巴西)

### <a name="locale_changed"></a>LOCALE_CHANGED

当用户`android.intent.action.LOCALE_CHANGED`更改其语言选择时, Android 会生成。

活动可以通过设置`android:configChanges`活动的属性来选择处理此操作, 如下所示:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Android 中的国际化基础知识

Android 的本地化策略包含以下关键部分:

- 要包含本地化的字符串、图像和其他资源的资源文件夹。

- `GetText`方法, 用于在代码中检索本地化字符串

- `@string/id`在 MAIN.AXML 文件中, 用于自动将本地化的字符串置于布局中。

### <a name="resource-folders"></a>资源文件夹

Android 应用程序管理资源文件夹中的大部分内容, 例如:

- **layout** -包含 main.axml 布局文件。
- **绘制**-包含图像和其他图形资源。
- **values** -包含字符串。
- **raw** -包含数据文件。

大多数开发人员都熟悉了如何在可**绘制**目录中使用**dpi**后缀来提供多个版本的映像, 同时让 Android 为每个设备选择正确的版本。 使用相同的机制, 通过使用语言和区域性标识符 suffixing 资源目录来提供多种语言翻译。

![多个区域性标识符的资源/绘制和资源/值文件夹的屏幕截图](localization-images/resources.png)

> [!NOTE]
> 如果需要指定`es`仅有两个字符的顶级语言, 则必须指定完整的区域设置, 目录名称格式要求使用短划线和小写**r**来分隔两部分, 例如**rBR**或**zh-chs-rCN**。 将此与代码中返回的值 (例如, `pt_BR`）格式模式中出现的位置生成。 这两个值与 .net `CultureInfo`类使用的值不同, 后者仅包含一条破折号 (例如 `pt-BR`）格式模式中出现的位置生成。 跨 Xamarin 平台工作时, 请记住这些差异。

#### <a name="stringsxml-file-format"></a>String .xml 文件格式

本地化**值**目录 (例如 **值-es**或**values-rBR**) 应包含一个名为**Strings**的文件, 该文件将包含该区域设置的已翻译文本。

每个可转换字符串都是一个 XML 元素, 其资源 ID 指定`name`为属性, 并将转换后的字符串作为值:

```xml
<string name="app_name">TaskyL10n</string>
```

需要根据普通 XML 规则进行转义, 并且`name`必须是有效的 Android 资源 ID (不含空格或短划线)。 下面是示例的默认 (英语) 字符串文件的示例:

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

西班牙语目录**值-es**包含一个文件, 该文件具有相同的名称 (**string .xml**), 其中包含以下翻译:

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

![多个值文件夹的屏幕截图, 其中每个文件夹都包含一个 String .xml 文件](localization-images/values.png)

在设置了字符串文件后, 可以在布局和代码中引用已转换的值。

### <a name="axml-layout-files"></a>MAIN.AXML 布局文件

若要在布局文件中引用本地化字符串, `@string/id`请使用语法。 示例中的此 XML 代码片段`text`显示了使用本地化资源 id 设置的属性 (已省略某些其他属性):

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

若要在代码中检索已转换的`GetText`字符串, 请使用方法并传递资源 ID:

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>数量字符串

通过 Android 字符串资源, 还可以创建*数量字符串*, 以允许翻译人员提供不同数量的不同翻译, 如:

- "剩下1个任务。"
- "仍有2个任务。"

(而不是泛型 "存在 n 个任务")。

在**字符串 .xml**中

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

若要呈现完整的字符串, `GetQuantityString`请使用方法, 传递要显示的资源 ID 和值 (传递两次)。 Android 使用第二个参数来确定要使用*的* `quantity`字符串, 第三个参数是实际替换为字符串的值 (两者都是必需的)。

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

有效`quantity`的开关包括:

- 零
- one
- 双向
- 小
- many
- 其他

[Android 文档](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals)中更详细地介绍了这些内容。如果给定语言不需要 "特殊" 处理, 则将忽略`quantity`这些字符串 (例如, 英语仅使用`one`和`other`; 指定`zero`字符串将不会产生任何影响, 不会使用)。

### <a name="images"></a>映像

本地化的图像与字符串文件遵循相同的规则: 应用程序中引用的所有映像都应放置在可**绘制**目录中, 因此存在回退。

然后, 应将特定于区域设置的图像放置在限定的可绘制文件夹 (如可**绘制的**或可**绘制-ja** ) 中 (也可以添加 dpi 说明符)。

在此屏幕截图中, 四个图像保存在可**绘制**目录中, 但只有一个**标记为 png**, 并在其他目录中包含本地化副本。

![多个被绘制文件夹的屏幕截图, 其中每个文件夹包含一个或多个本地化的 .png 文件](localization-images/drawable.png)


#### <a name="other-resource-types"></a>其他资源类型

还可以提供其他类型的特定于语言的其他类型的资源, 包括布局、动画和原始文件。 这意味着你可以为一个或多个目标语言提供特定的屏幕布局, 例如, 你可以创建专门用于德语的布局, 以允许使用非常长的文本标签。

如果你设置了应用程序设置`android:supportsRtl="true"`, Android 4.2 引入了对[从右到左 (RTL) 语言](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html)的支持。 资源限定符`"ldrtl"`可以包含在目录名称中, 以包含专为 RTL 显示而设计的自定义布局。

有关资源目录命名和回退的详细信息, 请参阅 Android 文档,[提供备用资源](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。


### <a name="app-name"></a>应用名称

`@string/id`在`MainLauncher`活动中使用中的可以轻松地本地化应用程序名称:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>从右到左 (RTL) 语言

Android 4.2 及更高版本提供对 RTL 布局的完全支持, 如[本机 RTL 支持博客](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html)中所述。

使用 Android 4.2 (API 级别 17) 和更高版本时, 可以使用`start`和`end`而不是`left`和`right` (例如`android:paddingStart`) 来指定对齐值。 还有新的 api (如`LayoutDirection` `TextDirection`、和`TextAlignment` ) 可帮助生成适应 RTL 读取器的屏幕。

以下屏幕截图显示了阿拉伯语中的[本地化的**Tasky**示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n):

[![阿拉伯语中的 Tasky 应用程序的屏幕截图](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

下一屏幕截图显示了希伯来语中的[本地化的**Tasky**示例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n):

[![希伯来语 Tasky 应用程序的屏幕截图](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

使用**字符串 .xml**文件对 RTL 文本进行本地化, 其方式与 LTR 文本相同。

## <a name="testing"></a>正在测试

请确保彻底测试默认区域设置。 如果由于某种原因而无法加载默认资源 (即缺少这些资源), 则应用程序会崩溃。

### <a name="emulator-testing"></a>模拟器测试

有关如何使用 ADB shell 将仿真程序设置为特定区域设置的说明, 请参阅 Google 对 Android Emulator 部分的[测试](https://developer.android.com/guide/topics/resources/localization.html#testing)。

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>设备测试

若要在设备上进行测试, 请在 "**设置**" 应用中更改语言。

> [!TIP]
> 记下菜单项的图标和位置, 以便可以将语言恢复为原始设置。


## <a name="summary"></a>总结

本文介绍使用内置资源处理对 Android 应用程序进行本地化的基本知识。 可在[此跨平台指南](~/cross-platform/app-fundamentals/localization.md)中了解有关适用于 IOS、Android 和跨平台 (包括 Xamarin) 应用的国际化和 L10n 的详细信息。



## <a name="related-links"></a>相关链接

- [Tasky (在代码中本地化) (示例)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Android 本地化资源](https://developer.android.com/guide/topics/resources/localization.html)
- [跨平台本地化概述](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin. Forms 本地化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS 本地化](~/ios/app-fundamentals/localization/index.md)
