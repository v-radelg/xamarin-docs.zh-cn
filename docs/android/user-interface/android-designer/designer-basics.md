---
title: Xamarin.Android 设计器基础知识
description: 本主题介绍 Xamarin.Android 设计器功能，说明如何启动设计器中，介绍设计图面上，并详细介绍了如何使用属性窗格编辑小组件属性。
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: fe909d72f3c6d6733318b5dcbd1858a1a9e28b37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61164924"
---
# <a name="xamarinandroid-designer-basics"></a>Xamarin.Android 设计器基础知识

_本主题介绍 Xamarin.Android 设计器功能，说明如何启动设计器中，介绍设计图面上，并详细介绍了如何使用属性窗格编辑小组件属性。_


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>启动设计器

创建布局，或通过双击现有的布局文件，它可以启动时，在设计器将自动启动。 例如，双击**activity_main.axml**中**资源 > 布局**文件夹将加载在设计器，如以下屏幕截图中所示：

[![在 Visual Studio 中的设计器屏幕](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

同样，右键单击可以添加新的布局**布局**中的文件夹**解决方案资源管理器**，然后选择**添加 > 新建项...> Android 布局**:

[![添加新项对话框](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

这将创建一个新 **.axml**布局文件并将其加载到设计器。

## <a name="designer-features"></a>设计器功能

在设计器由几个部分，支持它的各种功能，如以下屏幕截图中所示：

[![设计器窗格的关系图](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

当您编辑设计器中的布局时，使用以下功能来创建和调整你的设计：

-   **设计图面**&ndash;可简化用户界面可视方式构建过程，为您提供的可编辑表示形式如何布局将显示在设备上。 **设计图面**内显示**设计窗格**(使用**设计器工具栏**位于其上方)。

-   **源窗格**&ndash;提供的基础上显示的设计与相对应的 XML 源视图**设计图面**。

-   **设计器工具栏**&ndash;显示的选择器列表：**设备**，**版本**，**主题**，布局配置和操作栏设置。 **设计器工具栏**还包括图标用于启动主题编辑器和用于启用材料设计网格。

-   **工具箱**&ndash;提供了一系列小组件和布局，您可以拖放到**设计图面**。

-   **属性窗口**&ndash;列出了用于查看和编辑所选小组件的属性。

-   **文档大纲**&ndash;显示构成布局小组件的树。 可以单击以使其上所选的树中的项**设计图面**。 此外，单击树中的项目加载项的属性存储到**属性**窗口。

## <a name="design-surface"></a>设计图面

在设计器使您可以从工具箱拖到拖放小组件**设计图面**。 当与设计器中的小组件交互 （通过添加新的小组件或重新定位现有的） 时，将显示垂直和水平线以标记可插入点。 在以下示例中，一个新`Button`要小组件拖动到**设计图面**:

[![在设计图面上的示例中插入行](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

此外，可以将小组件复制： 您可以使用复制和粘贴将复制小组件，也可以拖放时按现有小组件<kbd>CTRL</kbd>密钥。

### <a name="designer-toolbar"></a>设计器工具栏

**设计器工具栏**(之上**设计图面**) 提供配置选择器和工具菜单：

[![设计器工具栏中的关系图](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

**设计器工具栏**可以访问以下功能：

-   **备用布局选择器**&ndash;允许你选择从不同的布局版本。

-   **设备选择器**&ndash;定义的限定符 （如屏幕尺寸、 分辨率和键盘可用性） 的一组与特定设备关联。 此外可以添加和删除新的设备。

-   **Android 版本选择器**&ndash;布局所面向的 Android 版本。 在设计器将呈现根据所选的 Android 版本的布局。

-   **主题选择器**&ndash;选择布局的用户界面主题。

-   **配置选择器**&ndash;选择该设备的配置，例如*纵向*或*横向*。

-   **资源限定符选项**&ndash;将打开一个对话框，其中提供了用于选择下拉列表菜单*语言*， *UI 模式*，*夜间模式*，和*圆角屏幕*选项。

-   **操作栏设置**&ndash;配置布局的操作栏设置。

-   **主题编辑器**&ndash;会打开*Theme Editor*，从而有可能用于自定义的所选主题元素。

-   **材质设计网格**&ndash;启用或禁用*材料设计网格*。 材料设计网格与相邻的下拉列表菜单项打开一个对话框，允许你自定义网格。

这些主题中的更详细地解释每个功能：

-   [资源限定符和可视化效果选项](~/android/user-interface/android-designer/resource-qualifiers.md)提供有关的详细的信息**设备选择器**， **Android 版本选择器**，**主题选择器**，**配置选择器**，**资源限定选项**，并**操作栏设置**。

-   [备选布局视图](~/android/user-interface/android-designer/alternative-layout-views.md)介绍了如何使用**备用布局选择器**。

-   [Xamarin.Android 设计器材料设计功能](~/android/user-interface/android-designer/material-design-features.md)提供的全面概述**Theme Editor**并且**材料设计网格**。

### <a name="context-menu-commands"></a>上下文菜单命令

上下文菜单中同时**设计图面**并在**文档大纲**。 此菜单会显示可用于所选小组件和其容器，使你更轻松地对容器执行操作的命令 (这并不总是容易上选择**设计图面**)。 下面是在上下文菜单的示例：

[![右键单击设计图面上时的示例上下文菜单](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

在此示例中，右键单击`TextView`打开上下文菜单，提供多个选项：

-   **LinearLayout** &ndash;将打开用于编辑的子菜单`LinearLayout`的父级`TextView`。

-   **删除**，**副本**，和**剪切**&ndash;适用于在右击操作`TextView`。


### <a name="zoom-controls"></a>缩放控件

**设计图面**支持缩放通过多个控件，如所示：

[![关系图的设计图面上缩放控件](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

这些控件使其更轻松地查看特定区域的设计器中的用户界面：

-   **突出显示容器**&ndash;上将突出显示容器**设计图面**，以便可以更轻松地找到时放大和缩小。

-   **常规尺寸**&ndash;将布局的-像素呈现，以便您可以查看所选设备分辨率布局外观。

-   **适合窗口**&ndash;设置缩放级别，使整个布局位于设计图面上可见。

-   **放大**&ndash;放大布局每次单击将以增量方式放大。

-   **缩小**&ndash;以增量方式缩小每次单击，使显示在设计图面上较小的布局。

请注意，所选的缩放设置不影响在运行时应用程序的用户界面。

## <a name="switching-between-design-and-source-panes"></a>设计和源窗格之间切换

之间的中心条带中**设计**并**源**窗格中，有几个按钮用于修改如何**设计**和**源**窗格将显示：

[![窗格中显示按钮的位置](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

这些按钮执行以下步骤：

-   **设计**&ndash;此最顶部的按钮**设计**，选择**设计**窗格。 单击此按钮时，**工具箱**并**属性**启用窗格和**文本编辑器工具栏**不会显示。 当**折叠**（见下文），在单击按钮**设计**而无需单独显示窗格**源**窗格。

-   **交换窗格**&ndash;此按钮 （类似于两个对立的箭头） 交换**设计**并**源**窗格，以便**源**窗格位于左侧并**设计**窗格位于右侧。 然后再次单击该交换两个窗格重新添加到其原始位置。

-   **源**&ndash;此按钮 （类似于两个对立的尖括号） 选择**源**窗格。 单击此按钮时，**工具箱**和**属性**窗格处于禁用状态并**文本编辑器工具栏**由 Visual Studio 顶部显示。 当**折叠**单击按钮后 （请参阅下文），单击**源**按钮将显示**源**窗格中，而不是**设计**窗格。

-   **垂直拆分** &ndash; （这类似于垂直条） 此按钮，将显示**设计**并**源**窗格并行。 这是默认排列方式。

-   **水平拆分**&ndash;此按钮 （这类似于水平栏），将显示**设计**上面**源**窗格。 **交换窗格**可通过单击放置**源**上面**设计**窗格。

-   **折叠窗格**&ndash;此按钮 （类似于两个右尖括号）"折叠"的双窗格显示**设计**并**源**到之一的一个视图两个窗格。
    此按钮将变为**展开窗格**按钮 （类似于两个左尖括号），可通过单击该视图将返回到双窗格 (**设计**和**源**) 显示模式。

当**折叠窗格**单击时，仅**设计**显示窗格。 但是，您可以单击**源**按钮，而不是视图仅**源**窗格。 单击**设计**按钮将再次返回到**设计**窗格。

## <a name="source-pane"></a>源窗格

**源**窗格中显示基础上显示的设计的 XML 源**设计图面**。 因为这两个视图在同一时间不可用，则可以通过该设计的可视表示形式和设计的基础 XML 源之间来回创建 UI 设计：

[![在源窗格中的示例 XML 源](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

XML 源所做的更改立即呈现上**设计图面**; 上所做的更改**设计图面**会导致 XML 源中显示**源**窗格相应地更新。 当对中的 XML 进行更改**源**窗格、 自动完成和 IntelliSense 功能可供下一步所述的基于 XML 的 UI 开发速度。

对于更高版本中方便地导航时使用的长 XML 文件，**源**窗格支持 Visual Studio 滚动条 （如上直接在上面的屏幕截图中所示）。 有关滚动条的详细信息，请参阅[如何通过自定义滚动条来跟踪代码](https://msdn.microsoft.com/library/dn237345.aspx)。


### <a name="autocompletion"></a>自动完成功能

当您开始键入某个小组件属性的名称时，你可以按<kbd>CTRL + 空格键</kbd>若要查看可能的完成列表。 例如，输入后`android:lay`在下面的示例 (键入后跟<kbd>CTRL + 空格键</kbd>)，显示以下列表：

[![自动完成的布局属性](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

按<kbd>ENTER</kbd>接受第一个列出的完成时，或使用箭头键来滚动到所需的完成，并按<kbd>ENTER</kbd>。 或者，可以使用鼠标滚动到并单击所需的完成。

### <a name="intellisense"></a>IntelliSense

小组件的输入的新属性并开始将其分配一个值后，IntelliSense 会弹出后触发器字符类型化，并提供要用于该属性的有效值的列表。 例如，在第一个双引号输入`android:layout_width`在以下示例中，自动完成功能选择器会弹出来提供针对此宽度的有效选项的列表：

[![布局宽度的 IntelliSense 示例](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

在此弹出窗口的底部两个按钮 （如上面的屏幕截图中红色中所述）。 单击**Project 资源**左侧的按钮将列表限制对资源的同时单击应用程序项目的一部分**框架资源**右侧按钮限制到列表显示框架中的可用资源。
这些按钮来切换打开或关闭： 你可以单击它们，以便禁用筛选操作，每个都提供。



## <a name="properties-pane"></a>属性窗格

设计器支持通过小组件属性的编辑**属性**窗格： 

![属性窗口的屏幕截图](designer-basics-images/vs/08-property-pad.png)

中列出的属性**属性**根据该小组件选择窗格中更改**设计图面**。

### <a name="default-values"></a>默认值

大多数小组件的属性中将为空白**属性**窗口由于它们的值继承自 Android 所选主题。
**属性**窗口将仅显示显式设置为所选小组件的值; 它不会从主题继承的值。

### <a name="referencing-resources"></a>引用资源

某些属性可以引用布局以外的文件中定义的资源 **.axml**文件。 此类型的最常见的用例都`string`和`drawable`资源。 但是，引用可还用于其他资源，如`Boolean`值和维度。 如果某个属性支持资源引用，属性的文本项旁边显示浏览图标 （方形）。 此按钮将打开一个资源选择器，单击时。

例如，下面的屏幕截图显示了可用的选项时单击右侧的文本字段的变暗的正方形`Text`中的小组件**属性**窗口：

[![文本选项的示例列表](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

当**资源...** 单击时，**选择资源**的对话框：

[![示例资源中列出的多个资源的屏幕截图](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

可以从此列表中，选择要用于该小组件，而不是硬编码中的文本的文本资源**属性**窗格。 下一步的示例所示的资源选择器`Src`属性的`ImageView`:

[![ImageView 列表图标资源的资源选择器](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

单击右侧的空方块`Src`属性将打开**选择资源**对话框，其中包含从颜色 （如上所示） 到绘图的资源的列表。


### <a name="boolean-property-references"></a>布尔值属性的引用

*布尔*属性通常选择作为属性窗口中的属性旁边的复选标记。 可以指定`true`或`false`值通过选中或取消选中此复选框，也可以通过单击属性右侧的填充深的方块选择的属性引用。 在以下示例中，文本更改为全部大写通过单击**文本的所有大写**关联与所选的布尔属性引用`TextView`:

![设置布尔属性的示例](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>编辑属性内联

Android 设计器支持上直接编辑某些属性**设计图面**（因此您无需搜索属性列表中的这些属性）。 可以直接编辑的属性包括文本、 边距和大小。

### <a name="text"></a>Text

一些小组件的文本属性 (如`Button`并`TextView`)，可以直接上编辑**设计图面**。 双击某个小组件将其置于编辑模式下，如下所示：

![你好字符串的文本资源](designer-basics-images/vs/12-text-resource.png "文本资源")

可以输入新的文本值，也可以输入新的资源字符串。 在以下示例中，`@string/hello`资源将被替换为文本， `CLICK THIS BUTTON`:

![Shift + Enter 以自动将文本链接到新的资源](designer-basics-images/vs/13-shift-enter-resource.png)

此更改存储在该小组件`text`属性; 它不会修改分配给的值`@string/hello`资源。
当你键入新的文本字符串中时，可以按<kbd>Shift</kbd> +
<kbd>Enter</kbd>自动链接到新的资源的输入的文本。

### <a name="margin"></a>边距

当选择小组件时，设计器会显示句柄，可用于以交互方式更改的大小或边距的小组件。 处于选定状态时单击小组件边距编辑模式和大小编辑模式之间切换。

当您第一次单击小组件时，显示边距句柄。 如果将鼠标移动到一个句柄，设计器会显示该句柄将更改的属性 (如下所示的`layout_marginLeft`属性):

![在设计器中处理显示边距的屏幕截图](designer-basics-images/vs/15-margin-handles.png)

如果已设置了边距，会显示虚线，指示边距占用的空间：

![点线将标记一个按钮周围的空间的示例](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>大小

前面曾提到，可以通过单击小组件，它已处于选定状态时切换到大小编辑模式。 单击要设置到的指示维度的大小的三角形句柄`wrap_content`:

![自动换行的内容和大小调整句柄](designer-basics-images/vs/17-wrap-content.png)

单击**包装内容**句柄收缩该维度中的小组件，这样就不大于所需包装包含的内容。 在此示例中，按钮文本中的下一步的屏幕截图所示的水平缩小。

如果大小值设置为**内容包装**，在设计器将显示指向中更改到大小的相反方向的三角形句柄`match_parent`:

![匹配父句柄](designer-basics-images/vs/18-match-parent.png)

单击**匹配父**句柄将还原该维度中的大小，以便它与父级小组件相同。

此外，您可以拖动的循环重设大小句柄 （如上面的屏幕截图中所示） 以调整大小小组件设为任意`dp`值。 当你执行此操作，同时**内容包装**并**匹配父**句柄显示为该维度：

![循环的调整大小图柄](designer-basics-images/vs/19-resize-dp.png)

并非所有容器都允许编辑`Size`的小组件。 例如，请注意，在下面的屏幕截图与`LinearLayout`选择，调整大小图柄不会显示：

![没有重设大小句柄](designer-basics-images/vs/20-no-resize-handles.png)


## <a name="document-outline"></a>文档大纲

**文档大纲**显示布局的小组件层次结构。
在下面的示例，其中包含`LinearLayout`选择小组件：

![文档大纲示例](designer-basics-images/vs/21-document-outline.png)

轮廓的所选小组件 (在这种情况下， `LinearLayout`) 上也会突出显示**设计图面**。 在文档大纲中的所选小组件保持在与同步**设计图面**。 这可用于选择查看组，并不总是容易上选择**设计图面**。

**文档大纲**支持复制和粘贴，或您可以使用拖放。 拖放功能支持从**文档大纲**到**设计图面**以及从**设计图面**到**文档大纲**. 此外，右键单击中的项**文档大纲**显示该项的上下文菜单 (右键单击该同一个小组件时，将显示相同的上下文菜单**设计图面**)。




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="launching-the-designer"></a>启动设计器

创建布局，或通过双击现有.axml 文件，它可以启动时，在设计器将自动启动。 例如，双击**Main.axml**中**资源 > 布局**文件夹将加载在设计器，如下所示：

[![Visual Studio for Mac 中的设计器屏幕](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

同样，右键单击可以添加新的布局**布局**中的文件夹**Solution Pad** ，然后选择**添加 > 新文件 > Android > 布局**:

[![添加新文件对话框](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

这将创建一个新的.axml 文件并将其加载到设计图面上。

## <a name="designer-features"></a>设计器功能

在设计器由几个部分，支持它的各种功能，如以下屏幕截图中所示：

[![设计器窗格的关系图](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

当您编辑设计器中的布局时，使用以下功能来创建和调整你的设计：

-   **设计图面**&ndash;可简化用户界面可视方式构建过程，为您提供的可编辑表示形式如何布局将显示在设备上。

-   **工具栏**&ndash;显示的选择器列表：**设备**，**版本**，**主题**，布局配置和操作栏设置。 工具栏还包括图标用于启动主题编辑器和用于启用材料设计网格。

-   **工具箱**&ndash;提供了一系列小组件和可以拖放到设计图面上的布局。

-   **属性面板**&ndash;列出了用于查看和编辑所选小组件的属性。

-   **文档大纲**&ndash;显示构成布局小组件的树。 您可以单击以使其可在设计器中选择的树中的项。 此外，单击树中的项目将项目的属性加载到属性面板。

## <a name="toolbar"></a>Toolbar

工具栏 （位于设计图面上方） 提供配置选择器和工具菜单：

[![设计器工具栏中的关系图](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

工具栏提供了对以下功能的访问：

-   **备用布局选择器**&ndash;允许你选择从不同的布局版本。

-   **设备选择器**&ndash;定义一组与特定设备如屏幕尺寸、 分辨率和键盘可用性相关联的限定符。 此外可以添加和删除新的设备。

-   **Android 版本选择器**&ndash;布局所面向的 Android 版本。 在设计器将呈现根据所选的 Android 版本的布局。

-   **主题选择器**&ndash;选择布局的用户界面主题。

-   **配置选择器**&ndash;选择该设备的配置，例如*纵向*或*横向*。

-   **资源限定符选项**&ndash;将打开一个对话框，其中提供了用于选择下拉列表菜单*语言*， *UI 模式*，*夜间模式*，和*圆角屏幕*选项。

-   **操作栏设置**&ndash;配置布局的操作栏设置。

-   **主题编辑器**&ndash;会打开*Theme Editor*，从而有可能用于自定义的所选主题元素。

-   **材质设计网格**&ndash;启用或禁用*材料设计网格*。 材料设计网格与相邻的下拉列表菜单项打开一个对话框，允许你自定义网格。

这些主题中的更详细地解释每个功能：

[资源限定符和可视化效果选项](~/android/user-interface/android-designer/resource-qualifiers.md)提供有关的详细的信息**设备选择器**， **Android 版本选择器**，**主题选择器**，**配置选择器**，**资源限定选项**，并**操作栏设置**。

[备选布局视图](~/android/user-interface/android-designer/alternative-layout-views.md)介绍了如何使用**备用布局选择器**。

[材料设计功能](~/android/user-interface/android-designer/material-design-features.md)提供的全面概述**Theme Editor**并且**材料设计网格**。

## <a name="design-surface"></a>设计图面

在设计器使您可以拖放小组件从工具箱拖到设计图面。 当与设计器中的小组件交互 （通过添加新的小组件或重新定位现有的） 时，将显示垂直和水平线以标记可插入点。 在以下示例中，新`Button`小组件正被拖动到设计图面：

[![在设计图面上的示例中插入行](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

此外，可以将小组件复制： 您可以使用复制和粘贴将复制小组件，也可以拖放时按现有小组件<kbd>Ctrl</kbd>密钥。

### <a name="context-menu-commands"></a>上下文菜单命令

在设计图面上并在文档大纲中提供上下文菜单。 此菜单会显示可用于所选小组件和其容器，使你更轻松地在容器 （这并不总是可以轻松选择设计图面上） 上执行操作的命令。 下面是在上下文菜单的示例：

[![右键单击设计图面上时的示例上下文菜单](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

在此示例中，右键单击`Button`打开上下文菜单，提供多个选项：

-   **LinearLayout** &ndash;将打开用于编辑的子菜单`LinearLayout`的父级`Button`。

-   **剪切**，**副本**，和**删除**&ndash;适用于在右击操作`Button`。

### <a name="zoom-controls"></a>缩放控件

在设计图面支持缩放通过多个控件，如所示：

[![关系图的设计图面上缩放控件](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

这些控件使其更轻松地查看特定区域的设计器中的用户界面：

-   **突出显示容器**&ndash;突出显示在设计图面上的容器，以便可以更轻松地找到时放大和缩小。

-   **常规尺寸**&ndash;将布局的-像素呈现，以便您可以查看所选设备分辨率布局外观。

-   **适合窗口**&ndash;设置缩放级别，使整个布局位于设计图面上可见。

-   **放大**&ndash;放大布局每次单击将以增量方式放大。

-   **缩小**&ndash;以增量方式缩小每次单击，使显示在设计图面上较小的布局。

请注意，所选的缩放设置不影响在运行时应用程序的用户界面。

## <a name="property-pad"></a>属性面板

设计器支持通过小组件属性的编辑**属性板**。 根据该小组件选择设计器图面中的属性填充更改中列出的属性。 当`Button`选择上一示例中，为该属性`Button`显示小组件：

[![属性面板的屏幕截图](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>属性面板节

属性面板划分为相似的属性组合在一起的多个部分&ndash;这使得更轻松地找到感兴趣的属性：

-   **小组件** &ndash; Main 属性的小组件，如`id`， `visibility`， `text`，等等。用于管理小组件的内容的属性通常放在此处。

-   **样式**&ndash;属性更改的可视外观的小组件，例如`font`， `text color`， `background`，等等。

-   **布局**&ndash;设置的位置和大小的小组件的属性。

-   **滚动**&ndash;滚动属性。

-   **行为**&ndash;设置小组件的行为方式的标志。

### <a name="default-values"></a>默认值

大多数小组件的属性中将为空白**属性板**由于它们的值继承自 Android 所选主题。 **属性板**将仅显示显式设置为所选小组件的值; 它不会从主题继承的值。

### <a name="referencing-resources"></a>引用资源

某些属性可以引用布局以外的文件中定义的资源 **.axml**文件。 此类型的最常见的用例都`string`和`drawable`资源。 但是，引用可还用于其他资源，如`Boolean`值和维度。
当某个属性支持资源引用，浏览图标 (一个省略号， &hellip;) 属性的文本项旁边所示。
单击此按钮将打开，资源选择器。

例如，下面的屏幕截图显示了可用的资源时单击文本字段右侧的省略号`Button`中的小组件**属性板**:

[![示例资源中列出的两个资源的屏幕截图](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

下一步的示例所示的资源选择器`Src`属性的`ImageView`:

[![ImageView 列表图标资源的资源选择器](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>布尔值属性的引用

*布尔*属性通常显示为属性面板中的复选框。 当`Boolean`属性支持资源引用，该属性旁边显示的小复选框。 选中的复选框意味着`true`意味着在空框`false`。 直接可以输入一个值，例如`true`或`false`。 将鼠标悬停在输入引出了一个小文本字段图标。 如果你想要手动输入此值，可以单击它。

[![设置布尔属性的示例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>分组的属性

一些小组件具有组合在一起的多值属性 (如`Padding`，例如)。 中列出了这些属性值**属性板**单个、 可展开行中。 其中一些属性可以直接在编辑分组的行，如`Padding`属性如下所示：

[![Padding 属性的设置示例](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>编辑属性内联

Android 设计器支持直接编辑的设计图面上的某些属性 （这样就不必搜索属性列表中的这些属性）。 可以直接编辑的属性包括文本、 边距和大小。

### <a name="text"></a>Text

一些小组件的文本属性 (如`Button`和`TextView`)，可以直接在设计图面上进行编辑。 双击某个小组件将其置于编辑模式下，如下所示：

[![你好字符串的文本资源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

可以输入新的文本值，也可以输入新的资源字符串。 在以下示例中，`@string/hello`资源将被替换为文本， `CLICK THIS BUTTON`:

[![Shift + Enter 以自动将文本链接到新的资源](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

此更改存储在该小组件`text`属性; 它不会修改分配给的值`@string/hello`资源。
当你键入新的文本字符串中时，可以按<kbd>Shift</kbd> +
<kbd>Enter</kbd>自动链接到新的资源的输入的文本。

### <a name="margin"></a>边距

当选择小组件时，设计器会显示句柄，可用于以交互方式更改的大小或边距的小组件。 处于选定状态时单击小组件边距编辑模式和大小编辑模式之间切换。

当您第一次单击小组件时，显示边距句柄。 如果将鼠标移动到一个句柄，设计器会显示该句柄将更改的属性 (如下所示的`layout_marginLeft`属性):

[![在设计器中处理显示边距的屏幕截图](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

如果已设置了边距，会显示虚线，指示边距占用的空间：

[![点线将标记一个按钮周围的空间的示例](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>大小

前面曾提到，可以通过单击小组件，它已处于选定状态时切换到大小编辑模式。 单击要设置到的指示维度的大小的三角形句柄`wrap_content`:

[![自动换行的内容和大小调整句柄](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

单击**包装内容**句柄收缩这样就不能大于包装包含的内容所需的维度中的小组件。 在此示例中，按钮文本中的下一步的屏幕截图所示的水平缩小。

如果大小值设置为**内容包装**，在设计器将显示指向中更改到大小的相反方向的三角形句柄`match_parent`:

[![匹配父句柄](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

单击**匹配父**句柄将还原该维度中的大小，以便它与父级小组件相同。

此外，您可以拖动的循环重设大小句柄 （如上面的屏幕截图中所示） 以调整大小小组件设为任意`dp`值。 当你执行此操作，同时**内容包装**并**匹配父**句柄显示为该维度：

[![循环的调整大小图柄](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

并非所有容器都允许编辑`Size`的小组件。 例如，请注意，在下面的屏幕截图与`LinearLayout`选择，调整大小图柄不会显示：

[![没有重设大小句柄](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>文档大纲

**文档大纲**显示布局的小组件层次结构。
在下面的示例，其中包含`LinearLayout`选择小组件：

[![文档大纲](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

轮廓的所选小组件 (在这种情况下， `LinearLayout`) 也会突出显示在设计图面上。 在文档大纲中所选小组件与保持同步对应的设计图面上。 这可用于选择查看组，并不总是可以轻松选择设计图面上。

文档大纲支持复制和粘贴，或可以使用拖放。 拖放功能支持从文档大纲到设计图面和文档大纲设计图面。 此外，右键单击文档大纲中的项显示该项 （的同一上下文菜单中右键单击设计图面上的同一个小组件） 的上下文菜单。

-----
