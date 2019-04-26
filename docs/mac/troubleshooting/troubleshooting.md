---
title: Xamarin.Mac 故障排除提示
description: 本文档介绍用于解决开发 Xamarin.Mac 应用程序时遇到的问题的方法。 它还讨论了如何获取支持。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f498aab5bfaffc08a22f62a318f8f9f73ab0afca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61237416"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin.Mac 故障排除提示

## <a name="overview"></a>概述

有时，我们都就会处理一个项目，可以在无法得到我们所希望的方式协同工作的 API 或在尝试解决 bug 时停留。 若要成功地编写你的移动和桌面应用程序，是 Xamarin 的目标，我们提供了一些资源，以帮助。

与任何这些资源，有可能需要帮助他们快速解决问题的准备工作的一些步骤：

- 确定最佳尽可能接近报告崩溃的问题的根本原因：
 
     - "我的应用程序崩溃"难以进行诊断。 "我的应用程序崩溃时我对此调用返回一个空数组"可以更轻松地解决工作。

     - "无法获得 NSTable 若要运行"是不太有用不是"我 NSTableDelegate 上的方法都不在此情况下调用。"

- 如有可能提供显示问题的小型示例程序。 获知页查找问题的源代码的花数量级的更多时间和精力。

- 了解这些内容所做的更改应用程序来导致出现问题才会显示可以快速缩小问题来源。 注意是否最近已升级版本的 Xamarin.Mac，剪裁掉的节的应用程序以查找导致出现此问题的部分或测试上一个版本，使其发现了哪些更改的问题可以是非常有帮助。


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>要执行的操作时在应用程序崩溃且无输出

在大多数情况下，Visual Studio for Mac 中的调试程序将捕获异常和崩溃应用程序中并帮助你跟踪的根本原因。 但是有某些情况下你的应用程序将弹跳的停靠面板上，然后退出其中的很少或没有输出。 这些条件包括：

- 代码签名问题。
- 某些 mono 运行时崩溃。
- 某些 Objective c 异常和崩溃。
- 一些故障非常早的进程生存期。
- 某些堆栈溢出。
- 中列出的 macOS 版本你**Info.plist**更高，你当前安装的 macOS 版本或其无效。

调试这些程序可能令人沮丧，以查找所需的信息可能很困难。 下面是可能会帮助的几个方法：

- 确保中列出的 macOS 版本**Info.plist**是同一个作为 macOS 计算机上当前安装的版本。
- 检查 Visual Studio 的 Mac 应用程序输出 (**视图** -> **面板** -> **应用程序输出**) 的堆栈跟踪，或从红色输出可能描述输出的 Cocoa。
- 从命令行运行应用程序并查看输出时 (在**终端**应用) 使用： 

     `MyApp.app/Contents/MacOS/MyApp` (其中`MyApp`是你的应用程序的名称)
- 可以通过将"MONO_LOG_LEVEL"添加到你的命令在命令行中，例如增加输出： 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 你可以将本机调试器附加 (`lldb`) 到进程以查看是否有提供任何 （这需要付费的许可证） 的详细信息。 例如，执行以下操作：

    1. 输入`lldb MyApp.app/Contents/MacOS/MyApp`在终端中。
    2. 输入`run`在终端中。
    3. 输入`c`在终端中。
    4. 完成调试后退出。
- 作为最后的手段，之前调用`NSApplication.Init`在您`Main`方法 （或在所需的其他位置），可以将文本写入文件中的已知位置来跟踪在哪些步骤启动过程中遇到问题。

## <a name="known-issues"></a>已知问题

以下部分介绍了已知的问题及解决方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>无法连接到沙盒应用程序中的调试器

调试器连接到 Xamarin.Mac 应用的整个 TCP，这意味着，默认情况下启用沙盒机制，时，无法连接到该应用程序，因此如果尝试运行该应用程序而无需启用适当的权限，你将收到一个错误 *"无法连接到调试器"*。 

[![编辑权利](troubleshooting-images/debug01.png "编辑权利")](troubleshooting-images/debug01-large.png#lightbox)

**允许传出网络连接 （客户端）** 权限是所需的调试器，使此将允许调试正常。 由于没有它进行调试时不能我们已更新`CompileEntitlements`目标进行`msbuild`为进行调试的沙箱处理的任何应用程序仅生成自动将该权限添加到权利。 发布版本应使用未修改的形式在权利文件中指定的权利。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>没有数据是可用于编码 437 System.NotSupportedException:
 
Xamarin.Mac 应用程序中包括第三方库，你可能会遇到错误，在窗体"System.NotSupportedException:没有数据是可用于编码 437"尝试编译和运行应用时。 例如，库，如`Ionic.Zip.ZipFile`，可能会引发此异常在操作过程。

这可以通过打开 Xamarin.Mac 项目中，转到用于解决**Mac 生成** > **国际化**并检查**西部**国际化：

[![编辑生成选项](troubleshooting-images/issue01.png "编辑生成选项")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>未能编译 (mm5103)

在 Xcode 的新版本是版本且已安装新版本，而不是尚未运行它，通常被导致此错误。 在尝试使用新的 Xcode 版本进行编译之前, 必须先至少一次运行该版本。

首次运行新版本的 Xcode，它会安装多个所需的 Xamarin.Mac 的命令行工具。 此外，您应该更新 Xcode 或你的 Xamarin.Mac 版本后为干净的生成。

如果您不能解决此问题，请[提交 bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>缺少 entitlements.plist

Visual Studio for Mac 的最新版本已删除的权利部分**Info.plist**编辑器并放入单独**Entitlements.plist** （适用于更好的跨平台支持的编辑器使用 Xamarin.iOS)。

与新的 Visual Studio 对已安装的 Mac，当创建新的 Xamarin.Mac 应用项目， **Entitlements.plist**文件将自动添加到项目树：

![选择权利](troubleshooting-images/entitlements01.png "选择权利")

如果您双击**Entitlements.plist**将显示文件，权利编辑器：

[![编辑权利](troubleshooting-images/entitlements02.png "编辑权利")](troubleshooting-images/entitlements02-large.png#lightbox)

对于现有的 Xamarin.Mac 项目，你将需要手动创建**Entitlements.plist**右键单击项目中的文件**Solution Pad** ，然后选择**添加**  > **新文件...**.接下来，选择**Xamarin.Mac** > **空属性列表**:

![添加新的属性列表](troubleshooting-images/entitlements03.png "添加新的属性列表")

输入`Entitlements`的名称，然后单击**新建**按钮。 如果你的项目之前包含授权文件，系统将提示将其添加到项目而不是创建一个新的文件：

[![验证文件的覆盖](troubleshooting-images/entitlements04.png "验证的文件覆盖")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>在论坛上的社区支持

使用 Xamarin 产品的开发人员社区提供了令人惊叹，许多访问我们[Xamarin.Mac 论坛](http://forums.xamarin.com/categories/mac)共享体验和他们的专业知识。 此外，Xamarin 工程师定期访问论坛，帮助。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>归档 bug

你的反馈对我们很重要。 如果发现任何问题与 Xamarin.Mac:

- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues) 
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。 

请尽可能多地包含以下内容：                                                                                                                                          

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用。 
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。 
