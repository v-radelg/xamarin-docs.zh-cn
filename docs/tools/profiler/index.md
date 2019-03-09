---
title: Xamarin Profiler
description: 本指南介绍 Xamarin Profiler 的主要功能。 它查找在探查器、 分析和时应使用它们，而标准的工作流是在分析 Xamarin 应用程序。
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
---

# <a name="xamarin-profiler"></a>Xamarin Profiler

_本指南介绍 Xamarin Profiler 的主要功能。它查找在探查器、 分析和时应使用它们，而标准的工作流是在分析 Xamarin 应用程序。_

应用程序的成功取决于最终用户体验。 作为开发人员，您可能已实现一些真正强大的功能在应用中，但如果应用是缓慢或完整的故障，用户将有可能将其删除。

以前，Mono 具有功能强大的命令行探查器来收集有关 Mono 运行时中运行的程序名为[Mono 日志探查器](https://www.mono-project.com/docs/debug+profile/profile/profiler/)。 Xamarin Profiler Mono 日志探查器和分析 Android、 iOS、 tvOS 和 Mac 应用程序在 Mac 和 Android，iOS 和 tvOS 应用程序在 Windows 上支持的图形界面中。

Xamarin Profiler 有许多可用于分析的 instruments-分配、 周期和时间 Profiler。 本指南介绍了这些仪表的度量，以及它们如何分析应用程序，并阐明了每个屏幕上显示的数据的含义。

本指南将检查常见分析方案，并介绍探查器，此工具可帮助分析和优化 iOS 和 Android 应用程序。

## <a name="download-and-install"></a>下载并安装

> [!NOTE]
> 将需要进行[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/)解锁此功能在 Windows 上的任一 Visual Studio Enterprise 或 Visual Studio for Mac 在 mac 上的订阅服务器

Xamarin Profiler 是独立的应用程序，并与 Visual Studio for Mac 和 Visual Studio 若要启用分析从 IDE 内集成。

下载你的平台的安装程序包：

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

下载完成后，启动安装程序将 Xamarin Profiler 添加到您的系统。

## <a name="profilers-and-profiling"></a>探查器和分析

分析是一个重要且经常被忽略的步骤，应用程序开发中。 分析是一种**动态程序分析**-它将分析该程序时它正在运行，并且在使用中。 探查器是一个数据挖掘工具，收集时间复杂性、 特定方法的使用情况和分配的内存有关的信息。 探查器，您可以深入钻取并分析这些指标以查明代码中的问题区域。

当设计和开发的应用程序时，务必提前; 优化也就是说，花时间开发将很少访问的区域中的代码。 这是分析的强大功能。 探查器提供了深入了解最常用的部分基本代码和可帮助查找的区域应花费时间进行改进的地方。 开发人员应请务必了解其中大部分时间花费在应用程序中以及如何使用你的应用程序的内存。

分析是有帮助的所有类型的开发，但它是移动开发中尤其重要。 未优化的代码是更为明显比台式计算机上的移动平台上，您的应用程序的成功取决于美观和优化代码，它可以有效地运行。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler 为开发人员提供了一种方式配置文件中的应用程序在 Visual Studio for Mac 或 Visual Studio 中。 探查器收集并显示有关应用程序，然后可由开发人员分析应用程序的行为的信息。 有多种不同方式配置文件的应用程序与 Xamarin Profiler，即内存分析和统计采样。 这些通过执行的分配和时间 Profiler 分别检测。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

目前，Xamarin Profiler 可用于测试 Mac (通过 Visual Studio for Mac) 上的 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 应用程序。 探查器是在 IDE 中的单独进程，因此，除了启动从 Visual Studio for Mac，可以将使用它作为独立应用程序来检查.exe 和`.mlpd`已从生成的文件[mono 日志探查器](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

目前，Xamarin Profiler 可用于测试 （通过 Visual Studio 和 Visual Studio for Mac） 的 Windows 上的 Xamarin.Android 应用。 探查器是在 IDE 中的单独进程，因此，除了从 Visual Studio 启动，可以将使用它作为独立应用程序来检查.exe 和`.mlpd`已从生成的文件[mono 日志探查器](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Profiler 支持

Xamarin Profiler 的支持均可在以下平台上：

 - Visual Studio for Mac (macOS，使用企业许可证)
    - Android
        - 设备和仿真程序
    - iOS
        - 设备和模拟器
    - tvOS （不支持时间检测）
        - 设备和模拟器
    - Mac

 - Visual Studio (仅**企业**版本)
    - Android
        - 设备和仿真程序
    - iOS [实验性]
        - 设备和模拟器
    - tvOS
        - 设备和模拟器

请注意，你可以**仅**配置文件**调试**配置。

## <a name="profiler-basics"></a>Profiler 基础知识

本部分介绍 Xamarin Profiler 的组成部分，并 tours 及其功能。

### <a name="allow-profiling-in-your-app"></a>允许在您的应用程序中进行分析

您可以成功进行分析您的应用程序之前，需要允许在应用程序的项目选项中使用性能分析。

 - iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  **生成 > iOS 调试 > 启用分析**

  ![](images/ios-options-mac.png "iOS 在 Visual Studio for Mac 中的选项对话框")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **属性 > iOS 生成 > 启用分析**

  ![](images/ios-project-options-vs.png "iOS 在 Visual Studio 中的选项对话框")

-----

 - Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  **生成 > Android 调试 > 启用开发者检测**

  ![在 Visual Studio for Mac 的 android 选项对话框](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **生成 > Android 调试 > 启用开发者检测**

  ![在 Visual Studio for Mac 的 android 选项对话框](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>启动 Profiler

从 IDE 时正在分析你的 iOS 或 Android 应用程序，或作为独立应用程序，可以启动 Xamarin Profiler。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>从 Visual Studio for Mac 启动

1. 首先，请确保已为 Mac，在 Visual Studio 中加载应用程序并选择 （默认值） 调试配置。
2. 浏览到**运行 > 开始分析**Visual Studio for Mac 中或**分析 > Xamarin Profiler**在 Visual Studio 中，打开 Profiler，如下面的关系图中所示：

  ![](images/start-profiling-xs.png "启动 Profiler 从 Visual Studio for Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>从 Visual Studio 启动

1.  首先，请确保已在 Visual Studio 中加载应用程序并选择 （默认） 的调试配置，如上所示。
2.  浏览到**分析 > Xamarin Profiler**在 Visual Studio 中，打开 Profiler，如下面的关系图中所示：

![启动从 Visual Studio Profiler](images/start-profiling-vs.png)

-----

如果未出现的菜单项，请参阅[故障排除指南](~/tools/profiler/troubleshooting.md)。

这将启动 Profiler 和分析应用程序将自动启动。

Profiler 可用于测量内存和性能。 它来实现此目标通过分配和时间 Profiler instruments，我们将探讨下一节中详细介绍。

#### <a name="saving-and-loading-profiler-sessions"></a>保存和加载 Profiler 会话

若要保存在任何时间分析会话，请选择**文件 > 另存为...** Profiler 菜单栏中。 这将保存在文件_mlpd_格式、 分析数据的特殊的且经过高度压缩的格式。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

已安装后 Xamarin Profiler 可在你的应用程序的文件夹中的屏幕截图中所示：

![](images/applications.png "从 Mac 中打开独立 Profiler")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

已安装后可以在应用程序目录中找到 Xamarin Profiler 应用程序：

![](images/applications-vs.png "从 Windows 中打开独立 Profiler ")

-----

您可以加载 *.mlpd*到通过打开独立应用程序，Profiler 的文件选择**选择目标**和加载该文件。

有关详细信息，请参阅[生成.mlpd 文件](~/tools/profiler/troubleshooting.md#gen_mlpd)。

## <a name="profiler-features"></a>Profiler 功能

Xamarin Profiler 是如下图所示的五个部分组成：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](images/profiler-mac-sml.png "在 Visual Studio for Mac 的 Profiler 部分")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "在 Visual Studio 中的 Profiler 部分")](images/profiler-vs.png#lightbox)

-----

- **工具栏**– 位于顶部的探查器，这提供选项来启动/停止分析，选择目标进程、 查看应用程序中的运行时间和选择构成探查器应用程序的拆分视图。
- **检测列表**– 这将列出所有 instruments 分析会话中加载。
- **绘制图表**– 这些图表水平与相关 instruments 检测列表中。 可以使用滑块 （时间 Profiler 下方所示） 来更改其小数位数。
- **检测详细信息区域**-包含要显示由当前检测所选视图数据。 我们将介绍以下部分中的更多详细信息中的这些视图。
- **检查器视图**– 这包含分段控件可以选择的部分。 部分依赖于选择的工具，包括：配置设置、 统计、 堆栈跟踪信息和根的路径。

### <a name="allocations"></a>分配

分配检测提供有关在应用程序中的对象的详细的信息，如创建并进行垃圾回收。

在探查器的顶部是内存的分配图，后者将显示在分析期间按固定间隔分配量。 目前分配关系图是总分配数而不是堆的大小在某个时间点。 在某种意义上，它将永远不会停机，则只会增加。 这包括在堆栈上分配的对象。 具体取决于使用的运行时版本，该图表可以查看不同 – 甚至为同一应用。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](images/allocations1.png "分配检测")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "分配检测")](images/allocations1-vs.png#lightbox)

-----

有不同的数据视图中的分配工具，允许开发人员能够分析其应用程序是如何使用并释放内存。 这些视图如下所述：

 -   **分配**– 这将显示所有分配的列表和类名按其进行分组。 这提供了精彩概述类和方法正在使用、 使用频率和使用的类的总体大小。 双击一个类，将显示分配的内存： 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/allocations3.png "分配选项卡")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "分配选项卡")](images/allocations2-vs.png#lightbox)

-----

分配的检查器视图的堆栈跟踪和路径提供筛选和分组对象、 内存分配，提供统计信息和选项顶部分配，以及视图到根。

 -   **调用关系树**– 这在应用程序中显示所有线程的整个调用树，并包括有关每个节点上分配的内存的信息。 所有的同级节点时在列表中选择元素时，将显示灰色。 可以展开树，或双击要向下钻取到它的元素。在显示此数据视图时，可以使用显示设置检查器视图更改显示的方式。 目前有两个选项：
    1.  **反转调用树**– 这会考虑从上到下的堆栈跟踪。 这是一个方便访问的视图选项指示最深的方法上 CPU 具有已花费其时间。
    2.  **单独的线程**– 此选项按线程组织的调用关系树。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/allocations2.png "调用树选项卡")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "调用树选项卡")](images/allocations3-vs.png#lightbox)

-----

 -   **快照**– 此窗格显示有关内存快照的信息。 若要生成这些分析实时应用程序时，请单击_照相机_中你想要查看哪些内存是保留和释放每个点上的工具栏按钮。 然后可以单击每个快照，若要浏览实质上发生了什么情况。 请注意在实时分析应用程序时，可以仅拍摄快照。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/allocations4.png "快照选项卡")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "快照选项卡")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>时间 Profiler

时间 Profiler 检测测量应用程序的每个方法花费多少时间是完全。 按固定间隔暂停应用程序，且每个活动线程上运行时堆栈跟踪。 检测详细信息区域中的每一行显示了已遵从的执行路径。

图表绘图，如下面的屏幕截图中所示显示在运行的应用接收到的样本数：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![时间 Profiler 检测](images/time1.png)](images/time1.png#lightbox) 

[![时间 Profiler 检测 – 示例列表](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![时间 Profiler 检测](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![时间 Profiler 检测 – 示例列表](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **调用关系树**– 显示所用的时间中的每个方法：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/time2.png "时间 Profiler 检测 – 调用关系树")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "时间 Profiler 检测 – 调用关系树")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>循环

使用C#和F#托管代码，它可以是很常见，并且遗憾的是很容易创建对将永远不会被释放的对象的引用。 此检测可以找出这些对象，并显示在你的应用程序中引用的周期。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![循环检测](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![循环检测](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>分析应用程序

目前，默认的调试配置可以进行分析。

如果分析具有任何其他配置的应用，你将看到以下消息对话框：


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![分析错误对话框](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "分析错误对话框")](images/image1vs.png#lightbox) 

-----

选择**更新**以继续。

### <a name="sgen-garbage-collector-and-profiling"></a>SGen 垃圾回收器和分析

[SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)垃圾回收器用于所有 Xamarin 平台。

SGen 是分代 GC，分配的应用程序分成以下三个堆对象 — 小堆、 主堆和大型对象空间。 这允许垃圾回收的速度执行。 SGen 目前默认 GC 以供 Xamarin.Android 和 Xamarin.iOS 统一应用程序。

使用经典 API 的 Xamarin.iOS 应用程序使用 Boehm GC-保守、 非分代垃圾回收器。 由于它是保守，就很难释放可用内存，这可能会导致不准确的结果时使用探查器。 出于此原因，分配检测不能用于 Boehm 垃圾回收器。

时系统将提示一个消息对话框，如果您的应用程序使用 Boehm GC，Xamarin 不建议切换到 SGen Boehm 使用而无需仔细研究和全面的测试的现有 iOS 应用程序。 Xamarin 也不建议用于探查，然后，根据这些结果将不会提供准确的内存使用率的基准切换回来，切换到 SGen。

有关内存管理的详细信息，请参阅[内存和性能最佳实践](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南。

## <a name="summary"></a>总结

在本指南还介绍了哪些分析以及如何为开发人员很有益处。 然后，我们引入了 Xamarin Profiler，提供一些历史记录和到其工作原理的信息。 最后我们随 Xamarin Profiler 的功能，并探讨了分配和时间 Profiler Instruments。

## <a name="related-links"></a>相关链接

- [内存和性能的最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [发行说明](https://developer.xamarin.com/releases/profiler/preview/)
