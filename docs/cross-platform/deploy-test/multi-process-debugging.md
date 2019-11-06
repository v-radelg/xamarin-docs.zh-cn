---
title: 多进程调试
description: 本文档介绍如何使用 Visual Studio for Mac 调试同时运行的多个进程。 例如，可以使用此功能同时调试移动应用程序和 Web 服务项目。
ms.prod: xamarin
ms.assetid: 852F8AB1-F9E2-4126-9C8A-12500315C599
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: fb96dab2d9979a365964d4993d9c7fc7fee299f5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016551"
---
# <a name="multi-process-debugging"></a>多进程调试

在 Visual Studio for Mac 中开发的新式解决方案具有针对不同平台的多个项目是十分常见的。 例如，一个解决方案可能具有一个移动应用程序项目，该项目依赖于 Web 服务项目提供的数据。 开发此解决方案时，开发人员可能需要同时运行这两个项目以排查任何错误。 从 [Xamarin Cycle 9 版本](https://releases.xamarin.com/stable-release-cycle-9/)开始，Visual Studio for Mac 现在可以调试正在同时运行的多个进程。 这样便可以在多个正在运行的项目中设置断点、检查变量以及查看线程。 这被称为“多进程调试”  。 

本指南将介绍为支持调试多个进程对 Visual Studio for Mac 所做的一些更改，如何配置解决方案以调试多个进程，以及如何使用 Visual Studio for Mac 附加到现有进程。

## <a name="requirements"></a>要求

调试多个进程需要使用 Visual Studio for Mac。

## <a name="ide-changes"></a>IDE 更改

为了帮助开发者进行多进程调试，Visual Studio for Mac 的用户界面已进行了一些更改。 Visual Studio for Mac 具有更新的“调试工具栏”  ，并且在“解决方案选项”  文件夹中包含了新的“解决方案配置”  部分。 此外，“Threads Pad”  现在将显示正在运行的进程以及每个进程的线程。 Visual Studio for Mac 还将显示多个 Debug Pad（每个进程一个 Pad），用于“应用程序输出”  等内容。

### <a name="solution-configurations"></a>解决方案配置

默认情况下，Visual Studio for Mac 将在“调试工具栏”的“解决方案配置”  区域显示单个项目。 启动某个调试会话时，Visual Studio for Mac 将启动此项目，并将调试程序附加到此项目。

若要在 Visual Studio for Mac 中启动和调试多个进程，必须创建一个解决方案配置  。 解决方案配置将描述通过单击“启动”  按钮或按 ⌘↩ (**Cmd-Enter**) 启动调试会话时，应包括解决方案中的什么项目。 以下屏幕截图是 Visual Studio for Mac 中具有多个解决方案配置的一个解决方案示例：

![](multi-process-debugging-images/mpd01-xs.png "A solution with multiple solution configurations")

### <a name="parts-of-the-debug-toolbar"></a>调试工具栏的各个部分

已将调试工具栏更改为允许通过弹出菜单选择解决方案配置。 此屏幕截图显示调试工具栏的各个部分：

![](multi-process-debugging-images/mpd02-xs.png "The parts of the debug toolbar")

1. **解决方案配置** - 可以通过在调试工具栏中单击“解决方案配置”并从弹出菜单中选择配置以设置解决方案配置：

    ![](multi-process-debugging-images/mpd03-xs.png "A sample popup with solution configurations")

2. **生成目标** - 它标识项目的生成目标。 这与 Visual Studio for Mac 的早期版本相同。
3. **设备目标** - 它选择解决方案将在其上运行的设备。 可以针对每个项目标识单独的设备或仿真器：

    ![](multi-process-debugging-images/mpd04-xs.png "Popup showing the devices for a project")

### <a name="multiple-debug-pads"></a>多个 Debug Pad

启动多个解决方案配置时，某些 Visual Studio for Mac 的 Pad 将显示多次，一次对应一个进程。 例如，下面的屏幕截图显示了正在运行两个项目的一个解决方案的两个“应用程序输出”  Pad：

![](multi-process-debugging-images/mpd05-xs.png "Output Pad for a solution configuration")

### <a name="multiple-processes-and-the-_active-thread_"></a>多个进程和活动线程 

一个进程中遇到断点时，该进程将暂停执行，而其他进程则将继续运行。 在单一进程方案中，Visual Studio for Mac 可轻松在单个 Pad 集中显示线程、局部变量、应用程序输出等信息。 然而，当多个进程具有多个断点并且可能具有多个线程时，会使开发人员在处理尝试一次性显示所有线程（和进程）中全部信息的调试会话中的信息时十分困难。

为了解决此问题，Visual Studio for Mac 一次仅显示一个线程中的信息，这称为“活动线程”  。 在断点处暂停的第一个线程将被视为“活动线程”  。 活动线程指开发人员关注的那个线程。 调试命令（如 **单步执行 (Step Over)** ⇧⌘O (Shift-Cmd-O)）将被发给活动线程。

“Thread Pad”  将显示正在解决方案配置中接受检查的所有进程和线程的信息，并提供有关活动线程内容的视觉提示：

![](multi-process-debugging-images/mpd06-xs.png "Thread pad for a solution configuration")

线程将按承载它们的进程进行分组。 活动线程的项目名称和 ID 将显示为加粗文本，并且指向右侧的箭头将显示在活动线程旁边的槽中。 在上面的屏幕截图中，“进程 ID 48703”  中的“线程 #1”  (**FirstProject**) 为活动线程。

调试多个进程时，可以使用“Thread Pad”  切换活动线程以查看该进程（或线程）的调试信息。 若要切换活动线程，请在“Thread Pad”  中选择所需的线程，然后双击它。

#### <a name="stepping-through-code-when-multiple-projects-are-stopped"></a>在多个项目停止时逐行执行 (Step Through) 代码

当两个（或多个）项目都具有断点时，Visual Studio for Mac 将暂停这两个进程。 仅可以在活动线程中**单步执行 (Step Over)** 代码。 在范围更改使调试器可以从活动线程中切换焦点之前，另一个进程将处于暂停状态。 有关示例，请查看下面显示正在调试两个项目的 Visual Studio for Mac 屏幕截图：

![](multi-process-debugging-images/mpd09-xs.png  "Visual Studio for Mac debugging two projects")

在此屏幕截图中，每个解决方案都具有自己的断点。 启动调试时，将在 **SecondProject** 中 `MainClass` 的**第 10 行**上遇到第一个断点。 由于两个项目都有断点，因此这两个进程都将被暂停。 遇到断点后，“单步执行 (Step Over)”  的每次调用都将导致 Visual Studio for Mac 在活动线程中单步执行代码。

逐行执行 (Step Through) 代码仅限于活动线程，因此，Visual Studio for Mac 每次将逐行执行代码，而另一个进程仍将处于暂停状态。

以上面的屏幕截图为例，`for` 循环结束后，Visual Studio for Mac 将允许 FirstProject  运行，直到遇到 `MainClass` 中的第 11 行  上的断点为止。 对于每个“单步执行 (Step Over)”  命令，调试程序将在 **FirstProject** 中逐行前进，直到 Visual Studio for Mac 的内部启发式算法将活动线程切换回 SecondProject  为止。

如果只有其中一个项目具有断点集，那么仅会暂停该进程。 另一个项目将继续运行，直到开发人员将其暂停或其中添加了断点为止。

### <a name="pausing-and-resuming-a-processes"></a>暂停和继续运行进程

可以通过右键单击进程并从上下文菜单中选择“暂停”  或“继续”  以暂停或继续运行进程：

![](multi-process-debugging-images/mpd08-xs.png "Pause or resume in the Thread pad")

调试工具栏的外观会根据正在调试的项目的状态发生变化。 运行多个项目时，在至少有一个运行的项目和一个暂停的项目的情况下，调试工具栏则将同时显示“暂停”  和“继续”  按钮：

![](multi-process-debugging-images/mpd07-xs.png  "Debug toolbar")

在“调试工具栏”  中单击“暂停”  按钮将暂停所有正在调试的进程，而单击“继续”  按钮将使所有暂停的进程继续运行。

### <a name="debugging-a-second-project"></a>调试第二个项目

在 Visual Studio for Mac 启动了第一个项目后，还可以调试第二个项目。 在第一个项目启动后，在“Solution Pad”  中，*右键单击  项目，然后选择“启动调试项”  ：

![](multi-process-debugging-images/mpd13-xs.png  "Start Debugging Item")

## <a name="creating-a-solution-configuration"></a>创建解决方案配置

在使用“启动”  按钮启动调试会话时，解决方案配置  将告知 Visual Studio for Mac 要运行什么项目。 每个解决方案可能具有多个解决方案配置。 如此便可以指定调试项目时要运行的项目。

在 Xamarin Studio 中创建新的解决方案配置：

1. 在 Visual Studio for Mac 中打开“解决方案选项”  对话框，然后选择“运行”>“配置”  ：

    ![](multi-process-debugging-images/mpd10-xs.png "Solution Configuration in the Solution Options dialog")

2. 单击“新建”  按钮，输入新解决方案配置的名称，然后单击“创建”  。 新的解决方案配置将显示在“配置”  窗口中：

    ![](multi-process-debugging-images/mpd11-xs.png  "Naming a new solution configuration")

3. 在配置列表中选择新的运行配置。 “解决方案选项”  对话框将显示解决方案中的每个项目。 勾选启动调试会话时应启动的每个项目：

    ![](multi-process-debugging-images/mpd12-xs.png "Selecting the project to start")

**MultipleProjects** 解决方案配置现在将显示在“调试工具栏”  中，使开发人员可以同时调试两个项目。

## <a name="summary"></a>总结

本指南介绍在 Visual Studio for Mac 中调试多个进程的相关内容。 它涵盖了为支持多进程调试对 IDE 所做的一些更改，并介绍了一些相关的行为。

## <a name="related-links"></a>相关链接

- [Xamarin Cycle 9 发行说明](https://releases.xamarin.com/stable-release-cycle-9/)
