---
title: 在 Xamarin.Mac – 快速入门中的情节提要
description: 本文档提供构建在 Xamarin.Mac 中的情节提要具有用户界面的 macOS 的快速入门介绍。 它介绍如何创建 segue，并创建一个首选项窗口。
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7f7d23a01a3c3c6567d6bab45d0abbfb078fb512
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033411"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>在 Xamarin.Mac – 快速入门中的情节提要

作为使用演示图板定义 Xamarin.Mac 应用的用户界面的快速介绍，让我们启动新 Xamarin.Mac 项目。 选择“Mac” > “App” > “Cocoa 应用”，然后单击“下一步”按钮：

[![](quickstart-images/qs01.png "添加新 Cocoa 应用")](quickstart-images/qs01.png#lightbox)

使用**应用名称**的`MacStoryboard`然后单击**下一步**按钮：

[![](quickstart-images/qs02.png "设置应用名称")](quickstart-images/qs02.png#lightbox)

使用默认**项目名称**并**解决方案名称**然后单击**创建**按钮：

[![](quickstart-images/qs03.png "项目和解决方案名称")](quickstart-images/qs03.png#lightbox)

在中**解决方案资源管理器**，双击`Main.storyboard`文件以打开在 Xcode 的 Interface Builder 中进行编辑：

[![](quickstart-images/qs04.png "编辑在 Xcode 中的情节提要")](quickstart-images/qs04.png#lightbox)

如上所示的如默认情节提要用于定义应用程序的菜单栏和与之其主窗口视图控制器和视图。 对于我们的示例应用，我们将创建一个用户界面，具有一个 main_内容视图_一面上和一个_检查器视图_在第二个。

若要执行此操作，我们需要首先删除默认视图控制器和附带的情节提要视图中，选择它中 Interface Builder 并按**删除**密钥：

[![](quickstart-images/qs05.png "删除默认视图控制器")](quickstart-images/qs05.png#lightbox)

接下来，键入`split`成**筛选器**区域中，选择垂直拆分视图控制器，并将其拖动到_设计图面_:

[![](quickstart-images/qs06.png "正在搜索拆分视图控制器")](quickstart-images/qs06.png#lightbox)

请注意，控制器会自动包含两个子视图控制器 （和其相关的视图），有线-最多的左侧和右侧的拆分视图。 若要将绑定到父窗口拆分视图，按**控制**键，单击窗口控制器 （在窗口控制器的帧中的蓝色圆圈） 并拖动线条到拆分视图控制器。 选择**窗口内容**从弹出窗口：

[![](quickstart-images/qs07.png "设置 windows 内容视图")](quickstart-images/qs07.png#lightbox)

这会将绑定一起使用 Segue 的两个界面元素：

[![](quickstart-images/qs08.png "在窗口和内容之间的 Segue")](quickstart-images/qs08.png#lightbox)

我们想要将文本视图放置在左侧和右侧的拆分视图，并将其窗口或拆分视图在调整大小时自动填充的可用区域。 将文本视图拖动到顶部附加到拆分视图的视图控制器上，单击**Pin**自动布局约束 （从底部的设计图面右侧的第二个图标）。

[![](quickstart-images/qs09.png "配置约束")](quickstart-images/qs09.png#lightbox)

在这里，我们将单击所有四个**i 型光标**周围的边框的图标框在约束弹出框的顶部，然后单击**添加 4 个约束**底部添加所需的约束的按钮。

如果我们返回到 Visual Studio for Mac，并运行项目，请注意，在文本视图自动调整大小以填充左侧和右侧的拆分视图窗口或调整大小的拆分：

[![](quickstart-images/qs10.png "运行应用程序示例")](quickstart-images/qs10.png#lightbox)

由于我们要作为检查器区域使用拆分视图的右侧，我们希望它具有较小的大小，并允许它为进行折叠。 返回到 Xcode 并通过在设计图面中选择它并单击编辑该视图的左右两边**大小检查器**。 在此处输入**宽度**的`250`:

[![](quickstart-images/qs11.png "设置宽度")](quickstart-images/qs11.png#lightbox)

下一步选择表示右侧窗格中，拆分项设置更高**持有优先级**然后单击**用户可以折叠**复选框：

[![](quickstart-images/qs12.png "编辑持有锁优先级")](quickstart-images/qs12.png#lightbox)

如果我们回到 Visual Studio for Mac，并且现在运行项目，请注意，在右侧保留它是较小大小和窗口调整大小：

[![](quickstart-images/qs13.png "运行应用程序示例")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>定义的表示形式 Segue

我们将向布局拆分视图，使其作为所选的文本属性的检查器的右侧。 我们会将某些控件到下部的视图上拖动到布局该检查器的 UI。 对于最后一个控件，我们想要显示允许用户选择从四个预设的字符样式的弹出框。

我们将向该检查器和视图控制器到设计图面添加一个按钮。 我们将调整大小视图控制器的大小，我们希望我们弹出框，并向其添加四个按钮。 接下来我们将**控制**键并单击检查器视图中的按钮拖到视图控制器将表示我们弹出框：

[![](quickstart-images/qs14.png "拖动以创建新的 segue")](quickstart-images/qs14.png#lightbox)

从弹出菜单中，我们将选择**弹出框**: 

[![](quickstart-images/qs15.png "选择 segue 类型")](quickstart-images/qs15.png#lightbox)

最后，我们将在设计图面上选择 Segue 并设置**首选边缘**到**左侧**。 然后，我们会将行从**定位点视图**到我们想要附加到弹出框的按钮：

[![](quickstart-images/qs16.png "拖动以创建新的 segue")](quickstart-images/qs16.png#lightbox)

如果我们回到 Visual Studio for Mac，请运行应用并单击**None**将显示在检查器弹出窗口中的按钮：

[![](quickstart-images/qs17.png "举例说明如何运行 segue")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>创建应用程序首选项

大多数标准 macOS 应用程序提供了_首选项对话框_，使用户可以定义多个控制应用程序中，如外观或用户帐户的各个方面的选项。

若要定义一个标准的首选项对话框窗口，请首先拖到设计图面上拖动选项卡视图控制器：

[![](quickstart-images/qs18.png "编辑在 Xcode 中的情节提要")](quickstart-images/qs18.png#lightbox)

同样，这将自动附带两个子视图控制器连接。 例如起见，我们将添加一个标签到将中心内每个视图：

[![](quickstart-images/qs19.png "设置约束")](quickstart-images/qs19.png#lightbox)

接下来，我们想要显示首选项窗口中，当用户选择**首选项...** 菜单项。 从菜单栏中，选择首选项菜单项，**控制**密钥单击和拖动线条到选项卡视图控制器：

[![](quickstart-images/qs20.png "拖动以创建 segue")](quickstart-images/qs20.png#lightbox)

从弹出窗口中，我们将选择**模式**以显示此窗口为模式对话框：

[![](quickstart-images/qs21.png "选择 segue 类型")](quickstart-images/qs21.png#lightbox)

如果我们将保存更改，返回到 Visual Studio for Mac 中，运行应用并选择**首选项...** 菜单项，将显示对话框我们新的首选项：

[![](quickstart-images/qs22.png "举例说明如何运行 segue")](quickstart-images/qs22.png#lightbox)

您可能注意到这看上去像标准 macOS 应用首选项对话框窗口。 若要解决此问题，包括两个图像文件在 Xamarin.Mac 应用的`Resources`文件夹中的**解决方案资源管理器**并返回到 Xcode 的 Interface Builder。

选择的选项卡视图控制器和交换机及其**样式**到**工具栏**: 

[![](quickstart-images/qs23.png "设置选项卡栏样式")](quickstart-images/qs23.png#lightbox)

选择每个选项卡，并为其提供**标签**，然后选择代表它的映像之一：

[![](quickstart-images/qs24.png "在 Xcode 中配置每个选项卡")](quickstart-images/qs24.png#lightbox)

如果我们将保存更改，返回到 Visual Studio for Mac 中，运行应用并选择**首选项...** 菜单项，标准 macOS 应用等设备现在将显示该对话框：

[![](quickstart-images/qs25.png "正在运行的首选项窗口的一个示例")](quickstart-images/qs25.png#lightbox)

有关详细信息，请参阅我们[处理图像](~/mac/app-fundamentals/image.md)，[菜单](~/mac/user-interface/menu.md)， [Windows](~/mac/user-interface/window.md)并[对话框](~/mac/user-interface/dialog.md)文档。

## <a name="related-links"></a>相关链接

- [MacStoryboard （示例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
