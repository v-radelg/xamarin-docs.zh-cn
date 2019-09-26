---
title: 资源限定符和可视化效果选项
description: 本主题说明如何定义将仅在某些限定符值匹配时使用的资源。 一个简单的示例是语言限定的字符串资源。 可以将字符串资源定义为默认值，并将其他替代资源定义为用于其他语言。 所有资源类型都可以进行限定，包括布局本身。
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6ae10e73c3f41226560beb8cf8c50608fc6adf3f
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "70762622"
---
# <a name="resource-qualifiers-and-visualization-options"></a>资源限定符和可视化选项

_本主题说明如何定义将仅在某些限定符值匹配时使用的资源。一个简单的示例是语言限定的字符串资源。可以将字符串资源定义为默认值，并将其他替代资源定义为用于其他语言。所有资源类型都可以进行限定，包括布局本身。_

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>资源限定符选项

可以通过单击 "**横向**模式" 按钮右侧的省略号图标来访问**资源限定符选项**：

[![资源限定符选项](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

此对话框显示以下资源限定符的下拉菜单：

- **语言**&ndash;显示可用的语言资源，并提供添加新语言/区域资源的选项。

- **UI 模式**列出显示模式（如**汽车 dock**和**书桌 dock**）以及布局方向。 &ndash;

其中每个下拉菜单都将打开新对话框，您可以在其中选择和配置资源限定符（如下所述）。

### <a name="language"></a>语言

"**语言**" 下拉菜单仅列出了定义了资源（或**所有语言**，这是默认值）的语言。 不过，还可以使用 "**添加语言/区域 ...** " 选项向列表中添加新语言：

[![添加语言/区域](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

单击 "**添加语言/区域 ...** " 时，将打开 "**选择语言**" 对话框以显示可用语言和区域的下拉列表：

![语言列表](resource-qualifiers-images/vs/10-languages.png "语言列表")

在此示例中，我们已选择**fr （法语）** 的语言和**BE** （比利时） 为法语的区域语言。 请注意，"**区域**" 字段是可选的，因为可以在不考虑特定区域的情况下指定多种语言。 当**语言**下拉菜单再次打开时，它会显示新添加的语言/区域资源：

![选择的语言和区域](resource-qualifiers-images/vs/11-language-region-added.png "选择的语言和区域")

请注意，如果添加新的语言，但没有为其创建新资源，则在下次打开该项目时，将不再显示添加的语言。

### <a name="ui-mode"></a>UI 模式

单击 " **UI 模式**" 下拉菜单时，将显示一系列模式，如**正常**、**汽车 dock**、**书桌 Dock**、**电视**、**设备**，以及**观看**：

[![UI 模式菜单](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

此列表的下面是夜景模式，**不晚**于**晚间，** 后跟从**左**到右和从**右到左**的布局方向（有关**从左到右**和从**右到左**的选项的信息，请参阅[Layoutdirection.](xref:Android.Util.LayoutDirection)）。
"**资源限定符选项**" 对话框中的最后一项是**圆形屏幕**（用于 Android 磨损）或**不是圆形**屏幕。
有关圆形和非圆角屏幕的信息，请参阅[布局](https://developer.android.com/training/wearables/ui/layouts.html)。
有关 Android UI 模式的详细信息，请参阅[UiModeManager](xref:Android.App.UiModeManager)。

## <a name="action-bar-settings"></a>操作栏设置

"**操作栏设置**" 图标在 "画笔（主题编辑器）" 图标的左侧可用：

![操作栏设置](resource-qualifiers-images/vs/14-action-bar.png "操作栏设置")

此图标将打开一个对话框 segue，该对话框提供一种方法来选择三个操作栏模式之一：

- **标准**&ndash;包含带有可选副标题的徽标、图标和标题文本。

- **列表**&ndash;列表导航模式。 此模式为在活动中导航提供一个列表菜单（即，可以作为下拉列表显示给用户），而不是静态标题文本。

- **选项卡**&ndash;选项卡导航模式。 此模式为在活动中导航提供一系列选项卡，而不是静态标题文本。

## <a name="themes"></a>主题

"**主题**" 下拉菜单显示在项目中定义的所有主题。 选择 "**更多主题**" 会打开一个对话框，其中包含已安装的 Android SDK 中可用的所有主题的列表，如下所示：

[![更多主题列表](resource-qualifiers-images/vs/15-theme-menu-sml.png "更多主题列表")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

选择主题后，会更新 Design Surface，以显示新主题的效果。 请注意，仅当在 "**主题**" 对话框中单击 **"确定"** 按钮时，此更改才为永久更改。 选择主题后，它将包含在**主题**下拉菜单中，如下所示：

![现在可以使用浅色主题](resource-qualifiers-images/vs/16-light-theme.png "现在可以使用浅色主题")

## <a name="android-version"></a>Android 版本

Android**版本**选择器设置用于在设计器中呈现布局的 android 版本。 选择器将显示与项目的目标 framework 版本兼容的所有版本：

![Android 版本列表](resource-qualifiers-images/vs/17-android-version.png "Android 版本列表")

目标 framework 版本可在项目设置中的 "属性" 下设置 **> 应用程序 > 使用 Android 版本编译**"。 有关 target framework 版本的详细信息，请参阅[了解 ANDROID API 级别](~/android/app-fundamentals/android-api-levels.md)。

"工具箱" 中提供的小组件集由项目的目标框架版本确定。 对于 "**属性" 窗口**中提供的属性也是如此。 可用小组件列表*不*是由工具栏的**版本**选择器中选择的值决定的。 例如，如果将项目的目标版本设置为 Android 4.4，则仍可在工具栏版本选择器中选择 "Android 6.0"，以查看 Android 6.0 中项目的外观，但无法添加特定于 Android 6.0 &ndash;的小组件 你仍将仅限于 Android 4.4 中提供的小组件。

有关资源类型的详细信息，请参阅[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>资源限定符选项

可以通过单击 "**横向**模式" 按钮右侧的省略号图标来访问**资源限定符选项**：

[![资源限定符选项](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

此对话框显示以下资源限定符的下拉菜单：

- **语言**&ndash;显示可用的语言资源，并提供添加新语言/区域资源的选项。

- **UI 模式**列出显示模式（如**汽车 dock**和**书桌 dock**）以及布局方向。 &ndash;

其中每个下拉菜单都将打开新对话框，您可以在其中选择和配置资源限定符（如下所述）。

### <a name="language"></a>语言

"**语言**" 下拉菜单仅列出了定义了资源（或**所有语言**，这是默认值）的语言。 不过，还可以使用 "**添加语言/区域 ...** " 选项向列表中添加新语言：

[![添加语言/区域](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

单击 "**添加语言/区域 ...** " 时，将打开 "**选择语言**" 对话框以显示可用语言和区域的下拉列表：

[![语言列表](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

在此示例中，我们已选择**fr （法语）** 的语言和**BE** （比利时） 为法语的区域语言。 请注意，"**区域**" 字段是可选的，因为可以在不考虑特定区域的情况下指定多种语言。 当**语言**下拉菜单再次打开时，它会显示新添加的语言/区域资源：

[![选择的语言和区域](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

请注意，如果添加新的语言，但没有为其创建新资源，则在下次打开该项目时，将不再显示添加的语言。

### <a name="ui-mode"></a>UI 模式

单击 " **UI 模式**" 下拉菜单时，将显示一系列模式，如**正常**、**汽车 dock**、**书桌 Dock**、**电视**、**设备**，以及**观看**：

[![UI 模式菜单](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

此列表下面是夜景模式，**不晚**于晚间，后跟布局方向**从左到** **右、从** **右到左**。 最后一对选项可让你选择**圆形屏幕**或**矩形屏幕**（适用于 Android 磨损设备）。

有关 Android UI 模式的详细信息，请参阅[UiModeManager](xref:Android.App.UiModeManager)。
有关**从左到右**和向**左**的选项的信息，请参阅[layoutdirection.](xref:Android.Util.LayoutDirection)。

## <a name="action-bar-settings"></a>操作栏设置

"**操作栏设置**" 图标在 "画笔（主题编辑器）" 图标的左侧可用：

[![操作栏设置](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

此图标将打开一个对话框 segue，该对话框提供一种方法来选择三个操作栏模式之一：

- **标准**&ndash;包含带有可选副标题的徽标、图标和标题文本。

- **列表**&ndash;列表导航模式。 此模式为在活动中导航提供一个列表菜单（即，可以作为下拉列表显示给用户），而不是静态标题文本。

- **选项卡**&ndash;选项卡导航模式。 此模式为在活动中导航提供一系列选项卡，而不是静态标题文本。

## <a name="themes"></a>主题

"**主题**" 下拉菜单显示在项目中定义的所有主题。 选择 "**更多主题**" 会打开一个对话框，其中包含已安装的 Android SDK 中可用的所有主题的列表，如下所示：

[![更多主题列表](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

选择主题后，会更新 Design Surface，以显示新主题的效果。 请注意，仅当在 "**主题**" 对话框中单击 **"确定"** 按钮时，此更改才为永久更改。 选择主题后，它将包含在**主题**下拉菜单中，如下所示：

[![现在可以使用浅色主题](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Android 版本

Android**版本**选择器设置用于在设计器中呈现布局的 android 版本。 选择器将显示与项目的目标 framework 版本兼容的所有版本：

[![Android 版本列表](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

可以在项目的设置中设置目标 framework 版本， **> 生成 > 常规**"部分的" 项目选项 "。 有关 target framework 版本的详细信息，请参阅[了解 ANDROID API 级别](~/android/app-fundamentals/android-api-levels.md)。

"工具箱" 中提供的小组件集由项目的目标框架版本确定。 对于**属性板**中提供的属性也是如此。 可用小组件列表*不*是由工具栏的**版本**选择器中选择的值决定的。 例如，如果将项目的目标版本设置为 Android 4.4，则仍可在工具栏版本选择器中选择 "Android 6.0"，以查看 Android 6.0 中项目的外观，但无法添加特定于 Android 6.0 &ndash;的小组件 你仍将仅限于 Android 4.4 中提供的小组件。

有关资源类型的详细信息，请参阅[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)。

-----
