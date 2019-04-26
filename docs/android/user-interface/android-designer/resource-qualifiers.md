---
title: 资源限定符和可视化效果选项
description: 本主题说明如何定义仅某些限定符值匹配时，将使用的资源。 一个简单的示例是一个语言限定字符串资源。 与其他定义要用于其他语言的替代资源，可以为默认值，定义字符串资源。 可限定所有资源类型，包括自身的布局。
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9d99e6a59b57b59d585b32befdadc0890d41448c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60950608"
---
# <a name="resource-qualifiers-and-visualization-options"></a>资源限定符和可视化效果选项

_本主题说明如何定义仅某些限定符值匹配时，将使用的资源。一个简单的示例是一个语言限定字符串资源。与其他定义要用于其他语言的替代资源，可以为默认值，定义字符串资源。可限定所有资源类型，包括自身的布局。_


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>资源限定符选项

**资源限定符选项**可以通过单击右侧的省略号图标可访问**横向**模式按钮：

[![资源限定符选项](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

此对话框将显示下面的资源限定符的下拉式菜单：

-   **语言**&ndash;显示可用的语言资源，并提供了一个选项以添加新语言/区域资源。

-   **用户界面模式**&ndash;列表显示模式 (如**汽车底座**并**桌面底座**) 以及布局方向。

每个这些下拉列表菜单将打开新对话框可以在其中选择并配置的资源限定符 （如下所述）。

### <a name="language"></a>语言

**语言**下拉菜单列出了只有那些具有定义的资源的语言 (或**所有语言**，这是默认设置)。 但是，还有**添加语言/区域...** 选项，可用于将一种新语言添加到列表：

[![添加语言/区域](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

当您单击**添加语言/区域...**，则**选择语言**对话框会打开，显示的可用语言和区域的下拉列表：

![语言的列表](resource-qualifiers-images/vs/10-languages.png "语言的列表")

在此示例中，我们已选择**fr （法语）** 的语言和**BE** （比利时） 为法语的区域语言。 请注意，**区域**字段是可选的因为许多语言可以指定而不考虑特定的区域。 当**语言**再次打开的下拉菜单，则会显示新添加的语言/区域资源：

![语言和区域选择](resource-qualifiers-images/vs/11-language-region-added.png "所选择的语言和区域")

请注意，是否添加新语言，但在不创建新资源，为它添加的语言将不再显示在下一次您打开该项目。

### <a name="ui-mode"></a>用户界面模式

当您单击**用户界面模式**显示的下拉菜单，模式的列表，如**正常**，**汽车底座**，**桌面底座**， **电视**，**装置**，并**监视**:


[![用户界面模式菜单](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

此列表下方是晚上模式**不晚上**和**晚上**后, 跟布局方向**从左到右**并**从右到左**（适用于有关的信息**从左到右**并**从右到左**选项，请参见[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/))。
中的最后一个项**资源限定符选项**对话框均**舍入屏幕**（适用于与 Android Wear 一起使用） 或**非圆角屏幕**。
Round 和非轮屏幕有关的信息，请参阅[布局](https://developer.android.com/training/wearables/ui/layouts.html)。
有关 Android UI 模式的详细信息，请参阅[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。

## <a name="action-bar-settings"></a>操作栏设置

**操作栏设置**图标位于左侧的画笔 （主题编辑器） 图标：

![操作栏设置](resource-qualifiers-images/vs/14-action-bar.png "操作栏设置")

此图标将打开对话框弹出框，使您能够选择三种操作栏模式之一：

-   **标准**&ndash;组成一个徽标或使用可选副标题一个图标和标题的文本。

-   **列表**&ndash;列表导航模式。 此模式而不是静态的标题文本显示在活动中的导航列表菜单 （即，它可以向用户显示与下拉列表）。

-   **选项卡** &ndash; tab 键导航模式。 而不是静态的标题文本，此模式下提供了一系列活动中的导航选项卡。

## <a name="themes"></a>主题

**主题**下拉列表菜单将显示所有项目中定义的主题。 选择**更多主题**打开一个对话框，可以从已安装的 Android SDK 的所有主题的列表，如下所示：

[![更多的主题列表](resource-qualifiers-images/vs/15-theme-menu-sml.png "更主题列表")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

选择一个主题后，设计图面上将更新以显示新的主题的效果。 请注意此更改是永久才**确定**中单击按钮**主题**对话框。 一旦选择了一个主题，它将会包含在**主题**下拉列表菜单操作，如图所示如下：

![浅色主题现已推出](resource-qualifiers-images/vs/16-light-theme.png "浅色主题现已推出")

## <a name="android-version"></a>Android 版本

Android**版本**选择器设置用于呈现设计器中的布局的 Android 版本。 选择器将显示所有与该项目的目标框架版本兼容的版本：

![Android 版本的列表](resource-qualifiers-images/vs/17-android-version.png "列表的 Android 版本")

可以在项目的设置中设置目标框架版本**属性 > 应用程序 > 使用 Android 版本编译**。 有关目标框架版本的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

通过项目的目标框架版本确定的可用工具箱中的小组件集。 这也适用于中的可用属性**属性窗口**。 可用列表的小组件是*不*中选择的值由决定**版本**工具栏的选择器。 例如，如果您设置项目的目标版本为 Android 4.4，仍可以选择 Android 6.0 中工具栏版本选择器来查看该项目在 Android 6.0 中的显示效果，但无法将特定于 Android 6.0 的小组件添加&ndash; 仍将限制为可在 Android 4.4 的小组件。

有关资源类型的详细信息，请参阅[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)。



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>资源限定符选项

**资源限定符选项**可以通过单击右侧的省略号图标可访问**横向**模式按钮：

[![资源限定符选项](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

此对话框将显示下面的资源限定符的下拉式菜单：

-   **语言**&ndash;显示可用的语言资源，并提供了一个选项以添加新语言/区域资源。

-   **用户界面模式**&ndash;列表显示模式 (如**汽车底座**并**桌面底座**) 以及布局方向。

每个这些下拉列表菜单将打开新对话框可以在其中选择并配置的资源限定符 （如下所述）。

### <a name="language"></a>语言

**语言**下拉菜单列出了只有那些具有定义的资源的语言 (或**所有语言**，这是默认设置)。 但是，还有**添加语言/区域...** 选项，可用于将一种新语言添加到列表：

[![添加语言/区域](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

当您单击**添加语言/区域...**，则**选择语言**对话框会打开，显示的可用语言和区域的下拉列表：

[![语言的列表](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

在此示例中，我们已选择**fr （法语）** 的语言和**BE** （比利时） 为法语的区域语言。 请注意，**区域**字段是可选的因为许多语言可以指定而不考虑特定的区域。 当**语言**再次打开的下拉菜单，则会显示新添加的语言/区域资源：

[![语言和区域选择](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

请注意，是否添加新语言，但在不创建新资源，为它添加的语言将不再显示在下一次您打开该项目。

### <a name="ui-mode"></a>用户界面模式

当您单击**用户界面模式**显示的下拉菜单，模式的列表，如**正常**，**汽车底座**，**桌面底座**， **电视**，**装置**，并**监视**:

[![用户界面模式菜单](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

此列表下方是晚上模式**不晚上**和**晚上**后, 跟布局方向**从左到右**并**从右到左**。 选项的最后一个对，可选择**舍入屏幕**或**矩形屏幕**（适用于 Android Wear 设备）。

有关 Android UI 模式的详细信息，请参阅[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。
璝惠**从左到右**并**从右到左**选项，请参见[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)。


## <a name="action-bar-settings"></a>操作栏设置

**操作栏设置**图标位于左侧的画笔 （主题编辑器） 图标：

[![操作栏设置](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

此图标将打开对话框弹出框，使您能够选择三种操作栏模式之一：

-   **标准**&ndash;包含是徽标或图标和标题文本以及可选副标题。

-   **列表**&ndash;列表导航模式。 此模式而不是静态的标题文本显示在活动中的导航列表菜单 （即，它可以向用户显示与下拉列表）。

-   **选项卡** &ndash; tab 键导航模式。 而不是静态的标题文本，此模式下提供了一系列活动中的导航选项卡。

## <a name="themes"></a>主题

**主题**下拉列表菜单将显示所有项目中定义的主题。 选择**更多主题**打开一个对话框，可以从已安装的 Android SDK 的所有主题的列表，如下所示：

[![更多的主题列表](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

选择一个主题后，设计图面上将更新以显示新的主题的效果。 请注意此更改是永久才**确定**中单击按钮**主题**对话框。 一旦选择了一个主题，它将会包含在**主题**下拉列表菜单操作，如图所示如下：

[![浅色主题现已推出](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Android 版本

Android**版本**选择器设置用于呈现设计器中的布局的 Android 版本。 选择器将显示所有与该项目的目标框架版本兼容的版本：

[![Android 版本的列表](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

可以在项目的设置中设置目标框架版本**项目选项 > 生成 > 常规**部分。 有关目标框架版本的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。

通过项目的目标框架版本确定的可用工具箱中的小组件集。 这也适用于中的可用属性**属性板**。 可用列表的小组件是*不*中选择的值由决定**版本**工具栏的选择器。 例如，如果您设置项目的目标版本为 Android 4.4，仍可以选择 Android 6.0 中工具栏版本选择器来查看该项目在 Android 6.0 中的显示效果，但无法将特定于 Android 6.0 的小组件添加&ndash; 仍将限制为可在 Android 4.4 的小组件。

有关资源类型的详细信息，请参阅[Android 资源](~/android/app-fundamentals/resources-in-android/index.md)。

-----
