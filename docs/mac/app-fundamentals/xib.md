---
title: 在 Xamarin.Mac 中.xib 文件
description: 本文介绍如何在 Xcode 的 Interface Builder 来创建和维护一个 Xamarin.Mac 应用程序的用户界面中创建的.xib 文件。
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 45eeee745b133646aef0f775bc879fa6a5d867c7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062515"
---
# <a name="xib-files-in-xamarinmac"></a>在 Xamarin.Mac 中.xib 文件

_本文介绍如何在 Xcode 的 Interface Builder 来创建和维护一个 Xamarin.Mac 应用程序的用户界面中创建的.xib 文件。_

> [!NOTE]
> 创建 Xamarin.Mac 应用的用户界面的首选的方法是使用演示图板。 本文档具有已保留在原处，出于历史原因以及使用较早的 Xamarin.Mac 项目。 有关详细信息，请参阅我们[情节提要简介](~/mac/platform/storyboards/index.md)文档。

## <a name="overview"></a>概述

当使用C#和.NET 在 Xamarin.Mac 应用程序中，有权访问相同的用户界面元素和工具使用的开发人员*Objective C*并*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护你的用户界面 （或选择通过 C# 代码中直接创建）。

MacOS 使用.xib 文件在 Xcode 的 Interface Builder 中以图形方式定义应用程序的用户界面的元素 （如菜单、 Windows、 视图、 标签、 文本字段） 的创建和维护。

[![正在运行的应用程序示例](xib-images/intro01.png "的正在运行的应用示例")](xib-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用.xib 文件中的 Xamarin.Mac 应用程序的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)第一次，一文，它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性使用绑定于 C# 类对 OBJECTIVE-C 对象和 UI 元素。


## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 简介

作为 Xcode 的一部分，Apple 已创建名为 Interface Builder，可以在设计器中直观地创建您的用户界面的工具。 Xamarin.Mac 与 Interface Builder，您可以使用 OBJECTIVE-C 的用户执行的操作的相同工具创建 UI 集成非常流畅。


### <a name="components-of-xcode"></a>Xcode 组件

当您从Visual Studio for Mac在Xcode中打开.xib文件时，它将在左侧打开一个 **Project Navigator**，中间的 **Interface Hierarchy** 和 **Interface Editor** 以及右侧的 **Properties＆Utilities** 部分：

[![Xcode UI 的组件](xib-images/xcode03.png "Xcode UI 的组件")](xib-images/xcode03-large.png#lightbox)

让我们看其中每个 Xcode 部分作用以及如何将使用它们来创建 Xamarin.Mac 应用程序的界面。


#### <a name="project-navigation"></a>项目导航

当您打开一个用于在Xcode中进行编辑的.xib文件时，Visual Studio for Mac会在后台创建一个Xcode项目文件，以在其自身与Xcode之间传递更改。 更高版本，当你切换回 Visual Studio for Mac 从 Xcode，对此项目进行任何更改与同步 Xamarin.Mac 项目由 Visual Studio for Mac。

**项目导航**部分可用于所有组成此文件之间导航_填充程序_Xcode 项目。 通常情况下，你只会对感兴趣的.xib 文件，例如此列表中**MainMenu.xib**并**MainWindow.xib**。


#### <a name="interface-hierarchy"></a>界面层次结构

**界面层次结构**选项，可以轻松地访问用户界面的多个关键属性，例如其**占位符**和主**窗口**。 本部分还可用于您的用户界面和调整通过在层次结构内拖动这些嵌套方式访问进行的各个元素 （视图）。


#### <a name="interface-editor"></a>界面编辑器

**界面编辑器**部分在其上提供的设计面您布局在用户界面以图形方式。 您要将元素从**库**一部分**属性和实用程序**部分，以创建您的设计。 当将用户界面元素 （视图） 添加到设计图面中时，它们将被添加到**界面层次结构**部分中出现的顺序**界面编辑器**。


#### <a name="properties--utilities"></a>属性和实用程序

**属性和实用程序**部分细分为两个主要部分，我们将使用的分别**属性**（也称为检查器） 和**库**:

![属性检查器](xib-images/xcode04.png "属性检查器")

本部分中最初不过是几乎为空，如果选择中的元素**界面编辑器**或**界面层次结构**，则**属性**部分内会填充使用有关的特定的元素和属性，可以调整的信息。

“属性”部分内具有 8 个不同的检查器选项卡，如下所示：

[![所有检查器概述](xib-images/xcode05.png "的所有检查器概述")](xib-images/xcode05-large.png#lightbox)

由左至右，这些选项卡依次为：

-   **文件检查器** – 文件检查器显示文件信息，例如正在编辑的 Xib 文件的文件名称和位置。
-   **快速帮助** – 快速帮助选项卡基于 Xcode 中所选内容提供相应背景帮助。
-   **标识检查器** – 标识检查器提供有关所选控件/视图的信息。
-   **属性检查器**– 通过属性检查器可用于自定义的所选控件/视图的各种属性。
-   **大小检查器**– 通过大小检查器，可控制的大小和重设大小行为的所选控件/视图。
-   **连接检查器**– 连接检查器显示所选控件的输出口和操作连接。 我们将稍后介绍输出口和操作。
-   **绑定检查器**– 通过绑定检查器，可配置控件，以便其值自动绑定到数据模型。
-   **视图效果检查器**– 通过视图效果检查器允许您指定的控件，如动画效果。

在中**库**部分中，可以找到控件和对象以将放入到设计器以图形方式生成用户界面：

![库检查器的示例](xib-images/xcode06.png "库检查器的示例")

现在，您已熟悉的 Xcode IDE 和 Interface Builder，让我们看在使用它来创建用户界面。


## <a name="creating-and-maintaining-windows-in-xcode"></a>创建和维护在 Xcode 中的 windows

创建 Xamarin.Mac 应用的用户界面的首选的方法是使用演示图板 (请参阅我们[情节提要简介](~/mac/platform/storyboards/index.md)文档了解详细信息)，因此，任何新项目在中并启动 Xamarin.Mac 将默认情况下使用情节提要。

若要切换到使用.xib 基于 UI，执行以下操作：

1. 打开 Visual Studio for Mac，并启动新 Xamarin.Mac 项目。
2. 在中**Solution Pad**，右键单击该项目并选择**添加** > **新文件...**
3. 选择**Mac** > **Windows 控制器**:

    ![添加新的窗口控制器](xib-images/setup00.png "添加新的窗口控制器")
4. 输入`MainWindow`的名称，然后单击**新建**按钮：

    ![添加新的主窗口](xib-images/setup01.png "添加新的主窗口")
5. 再次右键单击该项目并选择**外** > **新文件...**
6. 选择**Mac** > **主菜单**:

    ![添加新的主菜单](xib-images/setup02.png "添加新的主菜单")
7. 保留的名称作为`MainMenu`然后单击**新建**按钮。
8. 在中**Solution Pad**选择**Main.storyboard**文件，右键单击然后选择**删除**:

    ![选择主要情节提要](xib-images/setup03.png "选择主要情节提要")
9. 在删除对话框中，单击**删除**按钮：

    ![确认删除](xib-images/setup04.png "确认删除")
10. 在中**Solution Pad**，双击**Info.plist**文件将其打开进行编辑。
11. 选择`MainMenu`从**主界面**下拉列表中：

    [![设置主菜单](xib-images/setup05.png "设置主菜单")](xib-images/setup05-large.png#lightbox)
12. 在中**Solution Pad**，双击**MainMenu.xib**文件以打开在 Xcode 的 Interface Builder 中进行编辑。
13. 在中**库检查器**，类型`object`搜索字段中将一个新**对象**拖到设计图面：

    [![编辑主菜单](xib-images/setup06.png "编辑主菜单")](xib-images/setup06-large.png#lightbox)
14. 在中**标识检查器**，输入`AppDelegate`有关**类**:

    [![选择应用程序委托](xib-images/setup07.png "选择应用程序委托")](xib-images/setup07-large.png#lightbox)
15. 选择**文件的所有者**从**界面层次结构**，切换到**连接检查器**拖动线条，将从委托到`AppDelegate` **对象**刚添加到项目：

    [![连接应用程序委托](xib-images/setup08.png "连接应用程序委托")](xib-images/setup08-large.png#lightbox)
16. 保存更改并返回到 Visual Studio for Mac。

与所有这些更改后，编辑**AppDelegate.cs**文件，并使其看起来如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

现在，应用程序的主窗口中定义 **.xib**添加窗口控制器时，会自动包含在项目中的文件。 若要编辑您的 windows 设计、 在**Solution Pad**，双击**MainWindow.xib**文件：

![选择 MainWindow.xib 文件](xib-images/edit01.png "选择 MainWindow.xib 文件")

这将在 Xcode 的 Interface Builder 中打开的窗口设计：

[![编辑 MainWindow.xib](xib-images/edit02.png "编辑 MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>标准的窗口的工作流

用于创建和使用 Xamarin.Mac 应用程序中的任何窗口，该过程基本上是相同：

1. 对于不自动添加到你的项目是默认值的新窗口，向项目添加新的窗口中定义。
2. 双击要打开的窗口设计在 Xcode 的 Interface Builder 中进行编辑的.xib 文件。
3. 在中设置任何所需的窗口的属性**属性检查器**并**大小检查器**。
4. 在构建您的接口和配置中对其所需的控件中拖动**属性检查器**。
5. 使用**大小检查器**来处理 UI 元素的大小调整。
6. 公开到窗口的 UI 元素C#通过输出口和操作的代码。
7. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。


### <a name="designing-a-window-layout"></a>设计窗口布局

布置界面生成器中的用户界面的过程基本上是相同的添加的每个元素：

1. 查找中所需的控制**库检查器**将其拖到**界面编辑器**并将其置于。
2. 在中设置任何所需的窗口的属性**属性检查器**。
3. 使用**大小检查器**来处理 UI 元素的大小调整。
4. 如果使用的自定义类，则将其设置**标识检查器**。
5. 公开到 UI 元素C#通过输出口和操作的代码。
6. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

例如：

1. 在 Xcode 中，从“库”部分拖动“Push Button”：

    [![从库中选择一个按钮](xib-images/xcode07.png "从库中选择一个按钮")](xib-images/xcode07-large.png#lightbox)
2. 此按钮拖放**窗口**中**界面编辑器**:

    [![将按钮添加到窗口](xib-images/xcode08.png "向窗口中添加一个按钮")](xib-images/xcode08-large.png#lightbox)
3. 单击“属性检查器”中的 **Title** 属性，将此按钮的标题更改为 `Click Me`：

    ![设置按钮属性](xib-images/xcode09.png "设置按钮属性")
4. 从“库”部分拖动“标签”：

    [![在库中选择的标签](xib-images/xcode10.png "在库中选择一个标签")](xib-images/xcode10-large.png#lightbox)
5. 将此标签拖放到“界面编辑器”中此按钮旁的“窗口”中：

    [![将标签添加到窗口](xib-images/xcode11.png "将标签添加到窗口")](xib-images/xcode11-large.png#lightbox)
6. 按住此标签上的右控点，将其拖动至靠近窗口边缘的位置：

    [![调整标签的大小](xib-images/xcode12.png "调整标签的大小")](xib-images/xcode12-large.png#lightbox)
7. 在仍处于选中状态的标签**界面编辑器**，切换到**大小检查器**:

    ![选择大小检查器](xib-images/xcode13.png "选择大小检查器")
8. 中**自动调整大小框**单击**Dim Red 括号**右侧并**Dim Red 水平箭头**中心：

    ![编辑自动调整大小属性](xib-images/xcode14.png "编辑自动调整大小属性")
9. 这可确保该标签将拉伸以扩展和收缩调整窗口大小中运行的应用程序。 **红色方括号**顶部和左侧**自动调整大小框**框告知标签处于其给定的 X 和 Y 位置。
10. 将所做的更改保存到用户界面

已调整大小和移动控件时，您应该已经注意到，Interface Builder 为您提供了有用的快捷提示基于[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 这些指南将帮助您创建将具有 Mac 用户的熟悉的外观和感觉的高质量应用程序。

如果查看**界面层次结构**部分中，请注意，布局和层次结构构成了我们的用户界面的元素的显示方式：

![界面层次结构中选择元素](xib-images/xcode15.png "接口层次结构中选择元素")

在这里可以选择要编辑或通过拖动对 UI 元素进行重新排序，如果所需的项。 例如，如果 UI 元素已被另一元素覆盖，你可以将其拖到要使其成为窗口上最顶层的项的列表的底部。

有关如何使用 Windows 中的 Xamarin.Mac 应用程序的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)文档。


## <a name="exposing-ui-elements-to-c-code"></a>公开到 UI 元素C#代码

完成对 Interface Builder 中用户界面的外观进行布局，您将需要公开 UI 的元素，以便它们可以从访问C#代码。 若要执行此操作，您将使用操作和输出口。


### <a name="setting-a-custom-main-window-controller"></a>设置自定义主窗口控制器

为了能够创建输出口和操作公开给 C# 代码的 UI 元素，Xamarin.Mac 应用将需要使用自定义窗口控制器。

请执行以下操作：

1. 在 Xcode 的 Interface Builder 中打开应用的情节提要。
2. 选择`NSWindowController`设计图面中。
3. 切换到**标识检查器**查看，并输入`WindowController`作为**类名**:

    [![编辑类名](xib-images/windowcontroller01.png "编辑的类名")](xib-images/windowcontroller01-large.png#lightbox)
4. 保存所做的更改并返回到 Visual Studio for Mac 进行同步。
5. 一个 **WindowController.cs** 文件将被添加到Visual Studio for Mac中 **解决方案板** 中的项目中：

    ![Visual Studio for Mac中的新类名称](xib-images/windowcontroller02.png "Visual Studio for Mac") 中的新类名称
6. 重新打开在 Xcode 的 Interface Builder 情节提要。
7. **WindowController.h**文件将可供使用：

    [![在 Xcode 中匹配的.h 文件](xib-images/windowcontroller03.png "在 Xcode 中匹配的.h 文件")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>输出口和操作

那么什么是输出口和操作呢？ 在传统的 .NET 用户界面编程中，用户界面中的控件在添加时会自动作为属性公开。 然而在 Mac 中情况则不同，如果仅将控件添加到视图，代码不可对其进行访问。 开发人员必须对代码显式公开 UI。 为此，Apple 为我们提供了两个选项：

-  **输出口** – 输出口类似于属性。 如果控件绑定到插座，被公开到你的代码通过属性，因此可以执行诸如附加事件处理程序，对等调用方法。
-  **操作** - 操作类似于 WPF 中的命令模式。 例如，当控件上执行某个操作，比如单击按钮，控件将自动在代码中调用方法。 操作是强大、 更方便，因为可以对同一操作连接多个控件。

在 Xcode 中，输出口和操作添加通过代码中直接*拖动控件*。 具体而言，这意味着，若要创建的电源插座或操作，你选择想要添加的电源插座或操作，请按下哪个控件元素**控制**按钮在键盘上，该控件将直接拖到你的代码。

对于 Xamarin.Mac 开发人员，这意味着您将拖入到对应的 OBJECTIVE-C 存根 （stub） 文件C#你想要创建的电源插座或操作的文件。 Visual Studio for Mac创建了一个名为 **MainWindow.h** 的文件，作为使用Interface Builder生成的Shim Xcode项目的一部分：

[![在 Xcode 中的.h 文件的示例](xib-images/xcode16.png "在 Xcode 中的.h 文件的示例")](xib-images/xcode16-large.png#lightbox)

此存根 （stub）.h 文件反映**MainWindow.designer.cs** ，将自动添加到新的 Xamarin.Mac 项目`NSWindow`创建。 此文件将用于同步 Interface Builder 所做的更改，并且是在其中我们将创建输出口和操作，以便 UI 元素公开给C#代码。


#### <a name="adding-an-outlet"></a>添加输出口

使用基本掌握输出口和操作的是，让我们看看创建输出口公开到 UI 元素在C#代码。

请执行以下操作：

1. 在 Xcode 中屏幕右上角，单击“双圆”按钮，打开“助手编辑器”：

    [![选择在助手编辑器](xib-images/outlet01.png "选择助手编辑器")](xib-images/outlet01-large.png#lightbox)
2. 此时 Xcode 会切换为拆分视图模式，“界面编辑器”位于一边，“代码编辑器”位于另一边。
3. 请注意，Xcode 已自动选取**MainWindowController.m**中的文件**代码编辑器**，这是不正确。 如果你记得从什么上面输出口和操作是我们讨论，我们需要能够**MainWindow.h**选定。
4. 在顶部**代码编辑器**上单击**自动链接**，然后选择**MainWindow.h**文件：

    [![选择正确的.h 文件](xib-images/outlet02.png "选择正确的.h 文件")](xib-images/outlet02-large.png#lightbox)
5. 此时 Xcode 应选择了正确的文件：

    [![选中正确的文件](xib-images/outlet03.png "选中正确的文件")](xib-images/outlet03-large.png#lightbox)
6. **最后一步非常重要！** 如果您没有选中正确的文件，您将无法创建输出口和操作也会公开给中错误的类C#！
7. 在中**界面编辑器**，按住**控件**键键盘上，单击并拖动到代码编辑器上面创建的标签下方`@interface MainWindow : NSWindow { }`代码：

    [![拖动以创建新的电源插座](xib-images/outlet04.png "拖动以创建新的电源插座")](xib-images/outlet04-large.png#lightbox)
8. 会显示一个对话框。 将保留**连接**将设置到插座并输入`ClickedLabel`有关**名称**:

    [![设置电源插座属性](xib-images/outlet05.png "设置电源插座属性")](xib-images/outlet05-large.png#lightbox)
9. 单击**Connect**按钮以创建输出口：

    ![已完成的插座](xib-images/outlet06.png "已完成的输出口")
10. 保存对文件所做的更改。


#### <a name="adding-an-action"></a>添加操作

接下来，让我们看看创建要公开到 UI 元素与用户交互的操作在C#代码。

请执行以下操作：

1. 请确保我们仍处于**助手编辑器**并**MainWindow.h**文件会显示在**代码编辑器**。
2. 在中**界面编辑器**，按住**控件**键盘上键并单击拖动我们在上面创建到代码编辑器按钮正下方`@property (assign) IBOutlet NSTextField *ClickedLabel;`代码：

    [![若要创建操作的拖动](xib-images/action01.png "拖动以创建操作")](xib-images/action01-large.png#lightbox)
3. 更改**连接**类型设置为操作：

    [![选择一种操作类型](xib-images/action02.png "选择一种操作类型")](xib-images/action02-large.png#lightbox)
4. 输入 `ClickedButton` 作为**名称**：

    [![配置操作](xib-images/action03.png "配置操作")](xib-images/action03-large.png#lightbox)
5. 单击**Connect**按钮以创建操作：

    ![已完成的操作](xib-images/action04.png "已完成的操作")
6. 保存对文件所做的更改。

与用户界面连接和公开给C#代码中，切换回 Visual Studio for Mac，使其同步来自 Xcode 和 Interface Builder 的更改。


### <a name="writing-the-code"></a>编写代码

使用用户界面创建和公开给代码通过输出口和操作其 UI 元素，已准备好编写代码将您的程序转换为现实。 例如，打开**了构成**文件进行编辑中双击**Solution Pad**:

[![了构成文件](xib-images/code01.png "了构成文件")](xib-images/code01-large.png#lightbox)

并将以下代码添加到`MainWindow`类以使用上面创建的输出示例设备：

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

请注意，`NSLabel`中访问C#通过直接分配的名称，它在 Xcode 中时在 Xcode 中创建其电源插座，在这种情况下，它称为`ClickedLabel`。 您可以访问任何方法或属性公开的对象相同的方式任何正常C#类。

> [!IMPORTANT]
> 您需要使用`AwakeFromNib`，而不是等其他方法`Initialize`，因为`AwakeFromNib`称为_后_OS 已加载并实例化用户界面的.xib 文件。 如果您尝试访问标签控件前的.xib 文件完全加载和实例化，则会遇到`NullReferenceException`错误由于会尚未创建标签控件。

接下来，添加到下面的分部类`MainWindow`类：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此代码将附加到在 Xcode 和 Interface Builder 中创建并将在用户单击按钮时调用的操作。

一些 UI 元素自动具有内置的操作，例如，默认的菜单栏中的项如**打开...** 菜单项 (`openDocument:`)。 在中**Solution Pad**，双击**AppDelegate.cs**文件以打开进行编辑，并添加以下代码`DidFinishLaunching`方法：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

关键行是`[Export ("openDocument:")]`，它会告诉`NSMenu`的**AppDelegate**有一个方法`void OpenDialog (NSObject sender)`来响应`openDocument:`操作。

使用菜单的详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)文档。


## <a name="synchronizing-changes-with-xcode"></a>与 Xcode 同步更改

时，切换回 Visual Studio for Mac 从 Xcode，则将自动与 Xamarin.Mac 项目同步在 Xcode 中所做的任何更改。

如果选择**MainWindow.designer.cs**中**Solution Pad**你将能够看到如何我们的输出口和操作具有已关联我们C#代码：

[![与 Xcode 同步更改](xib-images/sync01.png "与 Xcode 同步更改")](xib-images/sync01-large.png#lightbox)

请注意如何在两个定义**MainWindow.designer.cs**文件：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

中的定义与对齐**MainWindow.h**在 Xcode 中的文件：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所见，Visual Studio for Mac会监听.h文件的更改，然后自动同步各个 **.designer.cs** 文件中的这些更改以将它们公开给您的应用程序。 你可能注意**MainWindow.designer.cs**是一个分部类，以便 Visual Studio for Mac 不必修改**了构成**它会覆盖我们已对类进行任何更改。

通常情况下将永远不需要打开**MainWindow.designer.cs**自己，此处提供仅为了。

> [!IMPORTANT]
> 在大多数情况下，Visual Studio for Mac将自动查看Xcode中所做的任何更改并将其同步到Xamarin.Mac项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。


## <a name="adding-a-new-window-to-a-project"></a>向项目添加一个新的窗口

主文档窗口中，除了 Xamarin.Mac 应用程序可能需要向用户，如首选项或检查器面板显示其他类型的窗口。 将一个新的窗口添加到你的项目时应始终使用**与控制器的 Cocoa 窗口**选项时，这使得文件更容易从.xib 加载窗口的过程。

若要添加新的窗口，请执行以下操作：

1. 在中**Solution Pad**，右键单击该项目并选择**添加** > **新文件...**.
2. 在新文件对话框中，选择**Xamarin.Mac** > **与控制器的 Cocoa 窗口**:

    ![添加新的窗口控制器](xib-images/new01.png "添加新的窗口控制器")
3. 对“名称”输入 `PreferencesWindow`，然后单击“新建”按钮。
4. 双击**PreferencesWindow.xib**文件以打开 Interface Builder 中为进行编辑：

    [![编辑在 Xcode 中的窗口](xib-images/new02.png "编辑在 Xcode 中的窗口")](xib-images/new02-large.png#lightbox)
5. 设计你的接口：

    [![设计 windows 布局](xib-images/new03.png "设计 windows 布局")](xib-images/new03-large.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

将以下代码添加到**AppDelegate.cs**以显示新窗口：

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

`var preferences = new PreferencesWindowController ();`行创建从.xib 文件加载窗口并增大它的窗口控制器的新实例。 `preferences.Window.MakeKeyAndOrderFront (this);`行向用户显示新窗口。

如果你运行代码并选择**首选项...** 从**应用程序菜单**，此时将显示窗口：

![运行示例应用](xib-images/new04.png "运行示例应用")

有关如何使用 Windows 中的 Xamarin.Mac 应用程序的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)文档。


## <a name="adding-a-new-view-to-a-project"></a>向项目添加新视图

有些的时候时更轻松地将窗口的设计分解为多个更易于管理的.xib 文件。 例如，要选择中的工具栏项时切换出主窗口的内容**首选项窗口**或换出内容以响应**源列表**所选内容。

将新视图添加到你的项目时应始终使用**与控制器的 Cocoa 视图**选项时，这使得文件更容易从.xib 加载视图的过程。

若要添加新视图，请执行以下操作：

1. 在中**Solution Pad**，右键单击该项目并选择**添加** > **新文件...**.
2. 在新文件对话框中，选择**Xamarin.Mac** > **与控制器的 Cocoa 视图**:

    ![添加新视图](xib-images/view01.png "添加新视图")
3. 对“名称”输入 `SubviewTable`，然后单击“新建”按钮。
4. 双击**SubviewTable.xib**文件将其打开进行编辑 Interface Builder 中设计用户界面：

    [![设计在 Xcode 中的新视图](xib-images/view02.png "设计在 Xcode 中的新视图")](xib-images/view02-large.png#lightbox)
5. 连接任何所需的操作和输出口。
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来编辑**SubviewTable.cs**并将以下代码添加到**AwakeFromNib**文件以在加载时填充的新视图：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

将枚举添加到项目来跟踪哪些视图当前正在显示。 例如， **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

编辑窗口，将使用该视图并显示它的.xib 文件。 添加**的自定义视图**作为容器的视图后加载到内存中的C#的代码并将其公开到名为插座`ViewContainer`:

[![创建所需的电源插座](xib-images/view03.png "创建所需的输出口")](xib-images/view03-large.png#lightbox)

保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

接下来，编辑.cs 文件中将显示新视图的窗口 (例如，**了构成**) 并添加以下代码：

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

当我们需要显示新视图从窗口的容器中的.xib 文件加载 (**的自定义视图**前面添加)，此代码处理删除任何现有视图并将其扩展为新交换。 它会检查它已有视图显示，如果它因此从屏幕上将其删除。 已传递的视图所需的下一步中 （如从视图控制器加载） 调整其大小以容纳在内容区域中并将其添加到所显示的内容。

若要显示新视图，请使用以下代码：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

这将创建要显示新视图的视图控制器的新实例，设置它的类型 （如枚举添加到项目中所指定） 并使用`DisplaySubview`方法添加到窗口的类，以实际显示的视图。 例如：

[![运行示例应用](xib-images/view04.png "运行示例应用")](xib-images/view04-large.png#lightbox)

有关如何使用 Windows 中的 Xamarin.Mac 应用程序的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)并[对话框](~/mac/user-interface/dialog.md)文档。


## <a name="summary"></a>总结

本文已详细的介绍了使用 Xamarin.Mac 应用程序中的.xib 文件。 我们已了解创建应用程序的用户界面、 如何创建和维护.xib 文件在 Xcode 的接口生成器以及如何处理的.xib 文件的不同类型和.xib 文件使用C#代码。


## <a name="related-links"></a>相关链接

- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [菜单](~/mac/user-interface/menu.md)
- [对话框](~/mac/user-interface/dialog.md)
- [使用图像](~/mac/app-fundamentals/image.md)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
