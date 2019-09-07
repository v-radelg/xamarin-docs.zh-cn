---
title: Xamarin. Android Designer 材料设计功能
description: 本主题介绍设计器功能，使开发人员可以更轻松地创建符合材料设计标准的布局。 本部分介绍和说明如何使用材料网格、材料调色板、排字比例和主题编辑器。
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 67755baf18ad5277e7c2fe8be705522a9b668ff3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756827"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Xamarin. Android Designer 材料设计功能

_本主题介绍设计器功能，使开发人员可以更轻松地创建符合材料设计标准的布局。本部分介绍和说明如何使用材料网格、材料调色板、排字比例和主题编辑器。_

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016：每个人都可以创建具有实质性设计的精美应用**

## <a name="overview"></a>概述

Android Designer 包含一些功能，使您可以更轻松地创建符合内容设计标准的布局。 如果不熟悉材料设计，请参阅[材料设计简介](https://material.io/design/introduction)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在本指南中，我们将介绍以下设计器功能：

- *材料网格*&ndash; Design Surface 上的覆盖区，它显示网格、间距和 keylines，以帮助您根据材料设计准则放置布局小组件。

- *主题编辑器*&ndash;一种小型颜色资源编辑器，可用于设置主题子集的颜色信息。 例如，您可以预览和修改材料颜色`colorPrimary`，例如、 `colorPrimaryDark`和`colorAccent`。

我们将介绍其中的每项功能，并提供有关如何使用这些功能的示例。

## <a name="material-design-grid"></a>材料设计网格

设计器顶部的工具栏中提供了 "材料设计网格" 菜单：

[![材料设计网格](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

单击 "材料设计网格" 图标后，设计器将在 Design Surface 上显示一个覆盖区，其中包含以下元素：

- Keylines （橙色线条）

- 间距（绿色区域）

- 网格（蓝线）

这些元素可在前面的屏幕截图中查看。 每个覆盖项都可配置。 单击 "材料设计网格" 菜单旁边的省略号时，将打开一个对话框 segue，允许您禁用/启用该网格，配置 keylines 的位置，并设置间距。 请注意，所有值都以`dp` （与密度无关的像素）表示：

[![网格、keyline 和间距配置](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

若要添加新的 keyline，请在 "**偏移量**" 框中输入一个新的偏移值，选择一个位置（**左侧**、**顶部**、**右侧**或**底部**），并单击 + 图标添加新的 keyline。 同样，若要添加新的间距，请分别在 "**大小**" 和 "**偏移量**" 框中输入大小和偏移量（在 dp 中）。 选择一个位置（**左**、**上**、**右**或**下**），然后单击 + 图标以添加新的间距。

当你更改这些配置值时，它们将保存在布局 XML 文件中，并在再次打开该布局时重用。

## <a name="theme-editor"></a>主题编辑器

**主题编辑器**允许您为主题属性的子集自定义颜色信息。 若要打开**主题编辑器**，请单击工具栏上的 "画笔" 图标：

[![主题编辑器图标](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

尽管可以从适用于所有目标 Android 版本和 API 级别的工具栏访问**主题编辑器**，但如果目标 API 级别早于 API 21 （Android 5.0 棒糖），则只有下述功能的子集可用。

**主题编辑器**的左侧面板显示组成当前选定主题的颜色列表（在本示例中，我们使用的`Default Theme`是）：

[![主题编辑器](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

选择左侧的颜色时，右侧面板会提供下列选项卡，以帮助你编辑该颜色：

- **继承**&ndash;显示选定颜色的样式继承关系图，并列出分配给该主题颜色的已解析颜色和颜色代码。

- **颜色选取器**&ndash;允许您将所选颜色更改为任意值。

- **材料调色板**&ndash;允许您将所选颜色更改为符合材料设计的值。

- **资源**&ndash;允许您将所选颜色更改为主题中的其他现有颜色资源之一。

让我们详细了解其中的每一个选项卡。

### <a name="inherit-tab"></a>继承选项卡

如以下示例中所示，"**继承**" 选项卡列出了**默认主题** **背景**色的样式继承：

[![继承选项卡](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

在此示例中，**默认主题**继承自使用`@color/background_material_light`的样式`color/material_grey_50`，但会使用将其替换为（颜色代码值为`#fffafafa`）的样式。
有关样式继承的详细信息，请参阅[样式和主题](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>颜色选取器

以下屏幕截图演示了**颜色选取器**：

[![颜色选取器](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

在此示例中，可以通过不同的方式将**背景**色更改为任意值：

- 直接单击颜色。
- 输入色相、饱和度和亮度值。
- 输入十进制的 RGB （红、绿、蓝）值。
- 设置所选颜色的 alpha （不透明度）。
- 直接输入十六进制颜色代码。

在颜色选取器中选择的颜色并*不*限于材料设计准则或可用颜色资源集。

### <a name="resources"></a>资源

"**资源**" 选项卡提供主题中已经存在的颜色资源列表：

[![中心](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

使用 "**资源**" 选项卡可将选项限制为此颜色列表。 请记住，如果你选择的颜色资源已分配给主题的另一部分，则该 UI 的两个相邻元素可能会 "一起运行" （因为它们具有相同的颜色），因此用户难以区分。

### <a name="material-palette"></a>材料调色板

"**材料调色板**" 选项卡将打开**材料设计调色板**。 从此调色板选择颜色值会限制您的颜色选择，使其与材料设计准则一致：

[![材料调色板](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

调色板顶部显示主要的材料设计颜色，而调色板的底部显示选定主颜色的一系列色相。 例如，当你选择 "**靛蓝色**" 时，该对话框的底部将显示一个**靛蓝色**色相的集合。
选择色调时，属性的颜色将更改为所选色调。 在下面的示例中， `Background Tint`按钮的将更改为*靛蓝色 500*：

![选择靛蓝色500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint`设置为*靛蓝色 500* （`#ff3f51b5`）的颜色代码，设计器将更新背景色以反映此更改：

[![背景淡色已更改](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

有关材料设计调色板的详细信息，请参阅材料设计[调色板指南](https://material.io/design/color/)。

### <a name="creating-a-new-theme"></a>创建新主题

在下面的示例中，我们将使用材料调色板创建新的自定义主题。 首先，我们将**背景**色更改为*蓝色 900*：

![将背景更改为蓝色900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

更改颜色资源时，会弹出一条消息，其中包含该消息，*当前主题具有未保存的更改*：

[![未保存的更改警告](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

设计器中的**背景**色已更改为新的颜色选择，但尚未保存此更改。 此时，您可以执行下列操作之一：

- 单击 "**放弃更改**" 以放弃新的颜色选择（或选项），并将主题还原到其原始状态。

- 按<kbd>CTRL + S</kbd>将更改保存到当前主题。

在下面的示例中，按下了<kbd>CTRL + S</kbd> ，以便将更改保存到**AppTheme**中：

[![更改已保存到 AppTheme](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>总结

本主题介绍了 Xamarin. Android Designer 中提供的材料设计功能。 它介绍了如何启用和配置材料设计网格，并说明了如何使用主题编辑器来创建符合材料设计准则的新自定义主题。
有关对材料设计的 Xamarin Android 支持的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在本指南中，我们将介绍以下设计器功能：

- *材料设计网格*&ndash; Design Surface 上的覆盖区，它显示网格、间距和 keylines，以帮助您根据材料设计准则放置布局小组件。

- *材料设计调色板*&ndash; "属性板" 对话框，可帮助您从官方材料设计调色板中选择颜色。

- *排字比例*"属性板" `textAppearance`对话框，可为文本字段的属性提供与材料设计兼容的设置。 &ndash;

- *主题编辑器*&ndash;一种小型颜色资源编辑器，可用于设置主题子集的颜色信息。 例如，您可以预览和修改材料颜色`colorPrimary`，例如、 `colorPrimaryDark`和`colorAccent`。

我们将介绍其中的每项功能，并提供有关如何使用这些功能的示例。

## <a name="material-design-grid"></a>材料设计网格

设计器顶部的工具栏中提供了 "材料设计网格" 菜单：

[![材料设计网格](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

单击 "材料设计网格" 图标后，设计器将在 Design Surface 上显示一个覆盖区，其中包含以下元素：

- Keylines （橙色线条）

- 间距（绿色区域）

- 网格（蓝线）

可以在以下屏幕截图中查看这些元素：

[![Keyline、间距和网格](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

每个覆盖项都可配置。 单击 "材料设计网格"&hellip;菜单旁边的省略号（）时，将打开一个对话框 segue，允许您禁用/启用该网格，配置 keylines 的位置，并设置间距。 请注意，所有值都以`dp` （与密度无关的像素）表示：

[![网格、keyline 和间距配置](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

若要添加新的 keyline，请在 "**偏移量**" 框中输入一个新的偏移值，选择一个位置（**左侧**、**顶部**、**右侧**或**底部**），并单击 + 图标（输入值时显示在右侧）添加新的 keyline。 同样，若要添加新的间距，请分别在 "**大小**" 和 "**偏移量**" 框中输入大小和偏移量（在 dp 中）。 选择一个位置（**左**、**上**、**右**或**下**），然后单击 + 图标以添加新的间距。

当你更改这些配置值时，它们将保存在布局 XML 文件中，并在再次打开该布局时重用。

## <a name="material-design-color-palette"></a>材料设计调色板

现在，接受颜色的每个属性面板项都有一个附加调色板图标，你可以使用它来打开材料设计颜色调色板，如以下屏幕截图所示：

[![颜色图标](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

单击此图标时，将打开一个对话框 segue，使你能够从材料设计调色板配置该属性的颜色：

[![材料设计调色板](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

调色板顶部显示主要的材料设计颜色，而调色板的底部显示选定主颜色的一系列色相。 例如，当你选择 "**靛蓝色**" 时，该对话框的底部将显示一个**靛蓝色**色相的集合。
选择色调时，属性的颜色将更改为所选色调。 在下面的示例中， `Background Tint`按钮的将更改为*靛蓝色 500*：

[![选择靛蓝色500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint`设置为*靛蓝色 500* （`#ff3f51b5`）的颜色代码，设计器将更新按钮的背景色，以反映此更改：

[![背景淡色更改](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

有关材料设计调色板的详细信息，请参阅材料设计[调色板指南](https://material.io/design/color/)。

## <a name="typographic-scale"></a>排字比例

"**属性**板**样式**" 选项卡的 "**文本外观**" 部分提供了一个图标，可`TextAppearance`用于从符合材料设计规范的样式中进行选择：

[![样式选项卡](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

单击此图标时，将打开 "**排字刻度**" 对话框 segue，其中显示了可供选择的预配置文本样式列表：

[![文本样式选择器](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

在以下示例中，单击 "**显示 1** " 将按钮的文本更改为**显示 1**的更大字体：

[![显示1样式](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

"**排字刻度**" 对话框中的文本样式遵循**主题**设置。 例如，如果在设计器中选择了**浅色**主题，则可用文本样式的列表将反映**浅色**主题：

[![浅色主题](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>主题编辑器

**主题编辑器**允许您为主题属性的子集自定义颜色信息。 若要打开**主题编辑器**，请单击工具栏上的 "画笔" 图标：

[![主题编辑器图标](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

尽管可以从适用于所有目标 Android 版本和 API 级别的工具栏访问**主题编辑器**，但如果目标 API 级别早于 API 21 （Android 5.0 棒糖），则只有下述功能的子集可用。

**主题编辑器**的左侧面板显示组成当前选定主题的颜色列表（在本示例中，我们使用的`Default Theme`是）：

[![主题编辑器](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

选择左侧的颜色时，右侧面板会提供下列选项卡，以帮助你编辑该颜色：

- **继承**&ndash;显示选定颜色的样式继承关系图，并列出分配给该主题颜色的已解析颜色和颜色代码。

- **颜色选取器**&ndash;允许您将所选颜色更改为任意值。

- **材料调色板**&ndash;允许您将所选颜色更改为符合材料设计的值。

- **资源**&ndash;允许您将所选颜色更改为主题中的其他现有颜色资源之一。

让我们详细了解其中的每一个选项卡。

### <a name="inherit-tab"></a>继承选项卡

如以下示例中所示，"**继承**" 选项卡列出了**默认主题** **背景**色的样式继承：

[![继承选项卡](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

在此示例中，**默认主题**继承自使用`@color/background_material_dark`的样式`color/material_grey_850`，但会使用将其替换为（颜色代码值为`#ff303030`）的样式。
有关样式继承的详细信息，请参阅[样式和主题](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>颜色选取器

以下屏幕截图演示了**颜色选取器**：

[![颜色选取器](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

在此示例中，可以通过不同的方式将**背景**色更改为任意值：

- 直接单击颜色。
- 输入色相、饱和度和亮度值。
- 输入十进制的 RGB （红、绿、蓝）值。
- 设置所选颜色的 alpha （不透明度）。
- 直接输入十六进制颜色代码。

在颜色选取器中选择的颜色并*不*限于材料设计准则或可用颜色资源集。

### <a name="resources"></a>资源

"**资源**" 选项卡提供主题中已经存在的颜色资源列表：

[![中心](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

使用 "**资源**" 选项卡可将选项限制为此颜色列表。 请记住，如果你选择的颜色资源已分配给主题的另一部分，则该 UI 的两个相邻元素可能会 "一起运行" （因为它们具有相同的颜色），因此用户难以区分。

### <a name="material-palette"></a>材料调色板

"**材料调色板**" 选项卡将打开[前面](#material-design-color-palette)所述的**材料设计调色板**。 从此调色板选择颜色值会限制您的颜色选择，使其与材料设计准则保持一致。

[![材料调色板](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>创建新主题

在下面的示例中，我们将使用材料调色板创建新的自定义主题。 首先，我们将**背景**色更改为*蓝色 900*：

[![将背景更改为蓝色900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

更改颜色资源时，会弹出一条消息，其中包含该消息，*当前主题具有未保存的更改*：

[![未保存的更改警告](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

设计器中的颜色更改已进行，但尚未保存此更改。 此时，您可以执行下列操作之一：

- 单击 "**放弃更改**" 以放弃新的颜色选择（或选项），并将主题还原到其原始状态。

- 按 **&#8984; + S**将更改保存到名为 "**自定义**" 的新主题。

## <a name="summary"></a>总结

本主题介绍了 Xamarin. Android Designer 中提供的材料设计功能。 它介绍了如何启用和配置材料设计网格，如何使用材料设计颜色调色板编辑颜色属性，以及如何使用 "排字刻度" 选择器来配置文本属性。 它还演示了如何使用主题编辑器来创建符合内容设计准则的新的自定义主题。 有关对材料设计的 Xamarin Android 支持的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。

-----

## <a name="related-links"></a>相关链接

- [材料主题](~/android/user-interface/material-theme.md)
- [材料设计简介](https://material.io/design/introduction)
