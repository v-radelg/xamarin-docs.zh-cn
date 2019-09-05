---
title: Xamarin 中的情节提要–快速入门
description: 本文档提供了使用 Xamarin 中的情节提要构建 macOS 用户界面的快速入门简介。 它介绍了如何创建 segue 并创建首选项窗口。
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/02/2017
ms.openlocfilehash: 498f73d6e5942fe305dc19effc83b9d976d86ac5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291920"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Xamarin 中的情节提要–快速入门

若要快速了解如何使用情节提要定义 Xamarin 应用程序的用户界面，请启动新的 Xamarin Mac 项目。 选择“Mac” > “App” > “Cocoa 应用”，然后单击“下一步”按钮：

[![](quickstart-images/qs01.png "添加新的 Cocoa 应用")](quickstart-images/qs01.png#lightbox)

使用**应用名称** `MacStoryboard` ，并单击 "**下一步**" 按钮：

[![](quickstart-images/qs02.png "设置应用名称")](quickstart-images/qs02.png#lightbox)

使用默认的 "**项目名称**" 和 "**解决方案名称**"，然后单击 "**创建**" 按钮：

[![](quickstart-images/qs03.png "项目和解决方案名称")](quickstart-images/qs03.png#lightbox)

在**解决方案资源管理器**中，双击该`Main.storyboard`文件以将其打开，以便在 Xcode 的 Interface Builder 中进行编辑：

[![](quickstart-images/qs04.png "在 Xcode 中编辑情节提要")](quickstart-images/qs04.png#lightbox)

如上图所示，默认情节提要同时定义了应用的菜单栏及其主窗口，同时查看控制器和视图。 对于我们的示例应用，我们将创建一个 UI，该 UI 在一侧有一个主要_内容视图_，第二个是_检查器视图_。

为此，我们需要首先删除情节提要附带的默认视图控制器和视图，方法是在 Interface Builder 中选择它并按**Delete**键：

[![](quickstart-images/qs05.png "删除默认视图控制器")](quickstart-images/qs05.png#lightbox)

接下来， `split`在 "**筛选器**" 区域中键入，选择垂直拆分视图控制器并将其拖到_Design Surface_：

[![](quickstart-images/qs06.png "搜索拆分视图控制器")](quickstart-images/qs06.png#lightbox)

请注意，控制器自动包含两个子视图控制器（及其相关视图），并将其连接到拆分视图的左侧和右侧。 若要将拆分视图绑定到它的父窗口，请按**ctrl**键，单击窗口控制器（窗口控制器的帧中的蓝色圆圈），然后将线条拖到拆分视图控制器。 从弹出窗口中选择 "**窗口内容**"：

[![](quickstart-images/qs07.png "设置 windows 内容视图")](quickstart-images/qs07.png#lightbox)

这会使用 Segue 将两个 interface 元素绑定在一起：

[![](quickstart-images/qs08.png "窗口和内容之间的 Segue")](quickstart-images/qs08.png#lightbox)

我们想要在拆分视图的左侧放置一个文本视图，并使其在窗口或拆分视图调整大小时自动填充可用区域。 将一个文本视图拖到附加到拆分视图的顶部视图控制器上，并单击 "**固定**自动布局" 约束（位于 Design Surface 底部右侧的第二个图标）。

[![](quickstart-images/qs09.png "配置约束")](quickstart-images/qs09.png#lightbox)

从这里，我们将单击 "约束" Segue 顶部的边界框周围的所有四个**I 形**图标，然后单击底部的 "**添加4个约束**" 按钮以添加所需的约束。

如果返回 Visual Studio for Mac 并运行项目，请注意，文本视图会自动调整大小，以在窗口或拆分调整大小时填充拆分视图的左侧：

[![](quickstart-images/qs10.png "运行的应用的示例")](quickstart-images/qs10.png#lightbox)

由于我们要使用拆分视图的右侧作为检查器区域，因此，我们希望它具有较小的大小并允许折叠。 返回到 Xcode，并通过在 Design Surface 中选择并单击**大小检查器**来编辑右侧的视图。 从此处输入**宽度** `250`：

[![](quickstart-images/qs11.png "设置宽度")](quickstart-images/qs11.png#lightbox)

接下来，选择表示右侧的拆分项，设置较高的**保存优先级**，并单击 "**用户可以折叠**" 复选框：

[![](quickstart-images/qs12.png "编辑保存优先级")](quickstart-images/qs12.png#lightbox)

如果我们返回 Visual Studio for Mac 并立即运行该项目，请注意，右端会保持较小的大小，并调整窗口的大小：

[![](quickstart-images/qs13.png "运行的应用的示例")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>定义演示 Segue

我们将在拆分视图的右侧布局，作为所选文本属性的检查器。 我们会将一些控件拖动到底部视图，以布局检查器的 UI。 对于最后一个控件，我们想要显示允许用户从四个预设字符样式中进行选择的 segue。

我们会将一个按钮添加到检查器，并向 Design Surface 添加一个视图控制器。 我们会将视图控制器的大小调整为 Segue 的大小，并向其添加四个按钮。 接下来，我们将**控制**"检查器" 视图中的按钮，并将其拖至将表示 Segue 的视图控制器：

[![](quickstart-images/qs14.png "拖动以创建新的 segue")](quickstart-images/qs14.png#lightbox)

从弹出菜单中，选择**segue**： 

[![](quickstart-images/qs15.png "选择 segue 类型")](quickstart-images/qs15.png#lightbox)

最后，在 Design Surface 中选择 "Segue"，并将**首选边缘**设置为 "**左**"。 然后，将行从**定位点视图**拖动到要将 segue 附加到的按钮：

[![](quickstart-images/qs16.png "拖动以创建新的 segue")](quickstart-images/qs16.png#lightbox)

如果返回 Visual Studio for Mac，请运行应用程序，然后单击 "检查器" 中的 "**无**" 按钮，将显示 segue：

[![](quickstart-images/qs17.png "运行 segue 的示例")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>创建应用首选项

大多数标准 macOS 应用程序都提供了一个_首选项对话框_，该对话框允许用户定义多个选项，这些选项控制应用程序的各个方面，例如外观或用户帐户。

若要定义标准首选项对话框窗口，请先将选项卡视图控制器拖动到 Design Surface：

[![](quickstart-images/qs18.png "在 Xcode 中编辑情节提要")](quickstart-images/qs18.png#lightbox)

同样，这将自动附带两个子视图控制器。 例如，我们会将一个标签添加到将在其中居中的每个视图：

[![](quickstart-images/qs19.png "设置约束")](quickstart-images/qs19.png#lightbox)

接下来，我们希望在用户选择 "**首选项 ...** " 菜单项时显示 "首选项" 窗口。 从菜单栏中，选择 "首选项" 菜单项，然后**按住 ctrl**键并将线条拖到选项卡视图控制器：

[![](quickstart-images/qs20.png "拖动以创建 segue")](quickstart-images/qs20.png#lightbox)

在弹出窗口中，选择 "**模式**"，将此窗口显示为模式对话框：

[![](quickstart-images/qs21.png "选择 segue 类型")](quickstart-images/qs21.png#lightbox)

如果保存更改，请返回到 Visual Studio for Mac，运行应用并选择 "首选项 **...** " 菜单项，将显示新的 "首选项" 对话框：

[![](quickstart-images/qs22.png "运行 segue 的示例")](quickstart-images/qs22.png#lightbox)

你可能会注意到，这看起来不像标准的 macOS 应用首选项对话框窗口。 若要解决此问题，请在`Resources` **解决方案资源管理器**中包括 Xamarin 应用程序文件夹中的两个图像文件，并返回到 Xcode 的 Interface Builder。

选择选项卡视图控制器，并将其**样式**切换到**工具栏**： 

[![](quickstart-images/qs23.png "设置选项卡样式")](quickstart-images/qs23.png#lightbox)

选择每个选项卡并为其指定一个**标签**，并选择其中一个图像来表示它：

[![](quickstart-images/qs24.png "在 Xcode 中配置每个选项卡")](quickstart-images/qs24.png#lightbox)

如果保存更改，请返回 Visual Studio for Mac，运行应用并选择 "**首选项 ...** " 菜单项，此时将显示该对话框，如标准 macOS 应用：

[![](quickstart-images/qs25.png "运行首选项窗口的示例")](quickstart-images/qs25.png#lightbox)

有关详细信息，请参阅[使用图像](~/mac/app-fundamentals/image.md)、[菜单](~/mac/user-interface/menu.md)、[窗口](~/mac/user-interface/window.md)和[对话框](~/mac/user-interface/dialog.md)文档。

## <a name="related-links"></a>相关链接

- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [macOS 人机界面指南](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
