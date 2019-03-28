---
title: Xamarin.Android 设计器材料设计功能
description: 本主题介绍了设计器功能，可简化开发人员创建材料设计符合的布局。 本部分介绍，并说明如何使用材料网格、 材料颜色调色板、 版式小数位数和主题编辑器。
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 59c61cc637a435f8107256ddcd6bfacfb0466867
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507014"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Xamarin.Android 设计器材料设计功能

_本主题介绍了设计器功能，可简化开发人员创建材料设计符合的布局。本部分介绍，并说明如何使用材料网格、 材料颜色调色板、 版式小数位数和主题编辑器。_

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016：每个人都可以使用材料设计创建美观的应用**

## <a name="overview"></a>概述

Xamarin.Android 设计器包括功能，使你更轻松地创建材料设计符合布局。 如果您不熟悉 Material Design，请参阅[材料设计简介](https://material.io/design/introduction)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在本指南中，我们将我们来看一下以下的设计器功能：

-   *材料网格*&ndash;显示网格、 间距和线条来帮助你将根据 Material Design 准则、 布局小组件放在设计图面上的覆盖。

-   *主题编辑器*&ndash;小颜色资源编辑器，您可以设置主题的子集的颜色信息。 例如，您可以预览，并修改材料的颜色，如`colorPrimary`， `colorPrimaryDark`，和`colorAccent`。

我们将看上述每项功能，并举例说明如何使用它们。

## <a name="material-design-grid"></a>材质设计网格

材料设计网格菜单会显示在设计器的顶部工具栏中：

[![材质设计网格](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

单击材料设计网格图标时，在设计器包括以下元素在设计图面上显示一个覆盖区：

-   线条 （橙色行）

-   间距 （绿色区域）

-   网格 （蓝色线）

可以在上面的屏幕截图中看到这些元素。 其中每个覆盖项目进行配置。 当您单击材料设计网格菜单旁边的省略号时，将打开对话框弹出框，您可以禁用/启用网格、 配置的线条，位置和设置间距。 请注意，所有值都以都表示`dp`（密度无关像素为单位）：

[![网格、 准线和间距配置](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

若要添加新的准线，请输入中的新偏移量的值**偏移量**框中，选择一个位置 (**左**，**顶部**，**右**，或**底部**) 并单击 + 图标以添加新的准线。 同样，若要添加新的间距，输入的大小和偏移量 （以 dp) 到**大小**并**偏移量**框，分别。 选择一个位置 (**左**，**顶部**，**右**，或者**底部**) 单击 + 图标以添加新的间距。

更改这些配置值时，它们保存在布局 XML 文件中并重复使用时重新打开该布局。


## <a name="theme-editor"></a>主题编辑器

**Theme Editor** ，可以自定义主题属性的子集的颜色信息。 若要打开**Theme Editor**，单击工具栏上的画笔图标：

[![主题编辑器图标](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

尽管**Theme Editor**只可以使用如下所述的功能的子集如果目标 API 级别低于 API 21 版 (Android 5.0 是可从工具栏访问的所有目标 Android 版本和 API 级别棒糖形）。

左侧的面板**Theme Editor**显示构成了当前所选主题颜色的列表 (在此示例中，我们将使用`Default Theme`):

[![主题编辑器](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

如果选择颜色位于左侧，右侧面板中提供了以下选项卡中，可以帮助您编辑该颜色：

-   **继承**&ndash;显示所选颜色的样式继承关系图，并列出已解析的颜色和颜色代码分配给该主题颜色。

-   **颜色选取器** &ndash; ，可以更改所选的颜色为任意值。

-   **材质调色板**&ndash;允许将所选的颜色更改为符合到 Material Design 的值。

-   **资源** &ndash; ，可以更改所选的颜色为一个主题中的其他现有的颜色资源。

让我们看看每个详细信息中的这些选项卡。

### <a name="inherit-tab"></a>继承选项卡

下面的示例中所示**Inherit**选项卡上列出的样式继承**背景**的颜色**默认主题**:

[![继承选项卡](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

在此示例中，**默认主题**从一种使用样式继承`@color/background_material_light`但将重写它与`color/material_grey_50`，其中包含的颜色代码值`#fffafafa`。
有关样式继承的详细信息，请参阅[样式和主题](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>颜色选取器

以下屏幕截图演示了**颜色选取器**:

[![颜色选取器](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

在此示例中，**背景**颜色可以更改通过不同的方式的任何值为：

-   直接单击一种颜色。
-   输入色调、 饱和度和亮度值。
-   输入以十进制表示的 RGB （红色、 绿色、 蓝色） 值。
-   设置所选颜色的 alpha （透明度）。
-   直接输入十六进制颜色代码。

在颜色选取器中选择的颜色是*不*限制到 Material Design 准则、 或的可用颜色资源集。

### <a name="resources"></a>资源

**资源**选项卡提供了一系列主题中已存在的颜色资源：

[![资源](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

使用**资源**选项卡可限制您的选择为此颜色列表。 请记住，如果您选择颜色资源已分配给另一个部分的主题，UI 的两个相邻元素可能"一起运行"（因为它们具有相同的颜色） 且变得困难的用户来区分。

### <a name="material-palette"></a>材质调色板

**材料调色板**选项卡随即打开**材料设计调色板**。 从调色板选择颜色值限制您的颜色选择，以便与材料设计指南一致：

[![材质调色板](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

调色板的顶部显示主 Material Design 颜色的调色板的底部显示一系列所选的主要颜色的色调。 例如，选择**Indigo**，一系列**Indigo**色调显示在对话框的底部。
时选择了 hue，该属性的颜色更改为所选的色调。 在以下示例中，`Background Tint`的按钮将变为*Indigo 500*:

![选择 Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` 设置为的颜色代码*Indigo 500* (`#ff3f51b5`)，并在设计器更新以反映此更改的背景色：

[![背景色彩更改](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

Material Design 的调色板的详细信息，请参阅 Material Design[颜色调色板指南](https://material.io/design/color/)。

### <a name="creating-a-new-theme"></a>创建新的主题

在以下示例中，我们将使用材料调色板可创建一个新的自定义主题。 首先，我们将更改**背景**颜色*蓝色 900*:

![将背景更改为蓝色 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

更改颜色资源后，弹出一条消息的消息，*当前主题具有未保存的更改*:

[![警告未保存的更改](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

**背景**颜色在设计器中的已更改为新的颜色选择，但尚未保存此更改。 此时，可以执行以下操作：

-   单击**放弃更改**放弃新的颜色选择 （或选择） 并恢复到其原始状态的主题。

-   按<kbd>CTRL + S</kbd>保存到所做的更改当前主题。

在以下示例中， <kbd>CTRL + S</kbd> ，以便所做的更改保存到按下**AppTheme**:

[![更改保存到 AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>总结

本主题介绍 Xamarin.Android 设计器中提供的材料设计功能。 本文介绍了如何启用和配置材料设计网格，并介绍了如何使用主题编辑器来创建符合 Material Design 准则的新自定义主题。
有关对 Material Design Xamarin.Android 支持的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在本指南中，我们将我们来看一下以下的设计器功能：

-   *材质设计网格*&ndash;显示网格、 间距和线条来帮助你将根据 Material Design 准则、 布局小组件放在设计图面上的覆盖。

-   *材料设计调色板*&ndash;属性填充对话框，可帮助您从官方的 Material Design 调色板中选择一种颜色。

-   *版式规模*&ndash;材料设计符合性设置为一个选择为您提供的属性填充对话框`textAppearance`文本字段的属性。

-   *主题编辑器*&ndash;小颜色资源编辑器，您可以设置主题的子集的颜色信息。 例如，您可以预览，并修改材料的颜色，如`colorPrimary`， `colorPrimaryDark`，和`colorAccent`。

我们将看上述每项功能，并举例说明如何使用它们。

## <a name="material-design-grid"></a>材质设计网格

材料设计网格菜单会显示在设计器的顶部工具栏中：

[![材质设计网格](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

单击材料设计网格图标时，在设计器包括以下元素在设计图面上显示一个覆盖区：

-   线条 （橙色行）

-   间距 （绿色区域）

-   网格 （蓝色线）

以下屏幕截图中，可以看到这些元素：

[![准线、 间距和网格](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

其中每个覆盖项目进行配置。 单击省略号 (&hellip;) 旁边的材料设计网格菜单中，对话框弹出框将打开，您可以禁用/启用网格、 配置的线条，位置和设置间距。 请注意，所有值都以都表示`dp`（密度无关像素为单位）：

[![网格、 准线和间距配置](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

若要添加新的准线，请输入中的新偏移量的值**偏移量**框中，选择一个位置 (**左**，**顶部**，**右**，或**底部**) 并单击 + 图标 （显示为右输入值） 以添加新的准线。 同样，若要添加新的间距，输入的大小和偏移量 （以 dp) 到**大小**并**偏移量**框，分别。 选择一个位置 (**左**，**顶部**，**右**，或者**底部**) 单击 + 图标以添加新的间距。

更改这些配置值时，它们保存在布局 XML 文件中并重复使用时重新打开该布局。

## <a name="material-design-color-palette"></a>材料设计调色板

现在接受一种颜色的每个属性面板项具有一个附加调色板图标，你可用于打开材料设计调色板，如以下屏幕截图中所示：

[![颜色图标](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

单击此图标时，将打开对话框弹出框，它使您可以配置该属性从 Material Design 调色板的颜色：

[![材料设计调色板](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

调色板的顶部显示主 Material Design 颜色的调色板的底部显示一系列所选的主要颜色的色调。 例如，选择**Indigo**，一系列**Indigo**色调显示在对话框的底部。
时选择了 hue，该属性的颜色更改为所选的色调。 在以下示例中，`Background Tint`的按钮将变为*Indigo 500*:

[![选择 Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` 设置为的颜色代码*Indigo 500* (`#ff3f51b5`)，并在设计器更新以反映此更改的按钮的背景色：

[![背景色彩更改](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

Material Design 的调色板的详细信息，请参阅 Material Design[颜色调色板指南](https://material.io/design/color/)。

## <a name="typographic-scale"></a>版式规模

**文本外观**一部分**属性**pad**样式**选项卡都有一个图标，可选择从`TextAppearance`符合 Material Design 的样式规范：

[![样式选项卡](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

单击此图标时，它会打开**版式规模**对话框弹出框，显示可供选择的预配置的文本样式的列表：

[![文本样式选择器](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

在以下示例中，单击**显示 1**更改为更大的字体的按钮的文本**显示 1**:

[![显示 1 样式](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

中的文本样式**版式规模**对话遵循**主题**设置。 例如，如果**Light**在设计器中，可用的文本样式镜像的列表中选择主题**Light**主题：

[![浅色主题](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>主题编辑器

**Theme Editor** ，可以自定义主题属性的子集的颜色信息。 若要打开**Theme Editor**，单击工具栏上的画笔图标：

[![主题编辑器图标](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

尽管**Theme Editor**只可以使用如下所述的功能的子集如果目标 API 级别低于 API 21 版 (Android 5.0 是可从工具栏访问的所有目标 Android 版本和 API 级别棒糖形）。

左侧的面板**Theme Editor**显示构成了当前所选主题颜色的列表 (在此示例中，我们将使用`Default Theme`):

[![主题编辑器](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

如果选择颜色位于左侧，右侧面板中提供了以下选项卡中，可以帮助您编辑该颜色：

-   **继承**&ndash;显示所选颜色的样式继承关系图，并列出已解析的颜色和颜色代码分配给该主题颜色。

-   **颜色选取器** &ndash; ，可以更改所选的颜色为任意值。

-   **材质调色板**&ndash;允许将所选的颜色更改为符合到 Material Design 的值。

-   **资源** &ndash; ，可以更改所选的颜色为一个主题中的其他现有的颜色资源。

让我们看看每个详细信息中的这些选项卡。

### <a name="inherit-tab"></a>继承选项卡

下面的示例中所示**Inherit**选项卡上列出的样式继承**背景**的颜色**默认主题**:

[![继承选项卡](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

在此示例中，**默认主题**从一种使用样式继承`@color/background_material_dark`但将重写它与`color/material_grey_850`，其中包含的颜色代码值`#ff303030`。
有关样式继承的详细信息，请参阅[样式和主题](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>颜色选取器

以下屏幕截图演示了**颜色选取器**:

[![颜色选取器](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)


在此示例中，**背景**颜色可以更改通过不同的方式的任何值为：

-   直接单击一种颜色。
-   输入色调、 饱和度和亮度值。
-   输入以十进制表示的 RGB （红色、 绿色、 蓝色） 值。
-   设置所选颜色的 alpha （透明度）。
-   直接输入十六进制颜色代码。

在颜色选取器中选择的颜色是*不*限制到 Material Design 准则、 或的可用颜色资源集。

### <a name="resources"></a>资源

**资源**选项卡提供了一系列主题中已存在的颜色资源：

[![资源](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

使用**资源**选项卡可限制您的选择为此颜色列表。 请记住，如果您选择颜色资源已分配给另一个部分的主题，UI 的两个相邻元素可能"一起运行"（因为它们具有相同的颜色） 且变得困难的用户来区分。

### <a name="material-palette"></a>材质调色板

**材料调色板**选项卡随即打开**材料设计调色板**所述[早期](#material-design-color-palette)。 从调色板选择颜色值约束颜色选择，以便与材料设计指南一致。

[![材质调色板](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>创建新的主题

在以下示例中，我们将使用材料调色板可创建一个新的自定义主题。 首先，我们将更改**背景**颜色*蓝色 900*:

[![将背景更改为蓝色 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

更改颜色资源后，弹出一条消息的消息，*当前主题具有未保存的更改*:

[![警告未保存的更改](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

已在设计器中的颜色更改，但尚未保存此更改。 此时，可以执行以下操作：

-   单击**放弃更改**放弃新的颜色选择 （或选择） 并恢复到其原始状态的主题。

-   按 **&#8984; + S**若要将所做的更改保存到新的主题名为**自定义**。


## <a name="summary"></a>总结

本主题介绍 Xamarin.Android 设计器中提供的材料设计功能。 它介绍了如何启用和配置材料设计网格，如何使用材料设计调色板编辑颜色属性，以及如何使用版式规模选择器来配置文本属性。 它还演示了如何使用主题编辑器来创建符合 Material Design 准则的新自定义主题。 有关对 Material Design Xamarin.Android 支持的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。

-----


## <a name="related-links"></a>相关链接

- [材料主题](~/android/user-interface/material-theme.md)
- [材料设计简介](https://material.io/design/introduction)
