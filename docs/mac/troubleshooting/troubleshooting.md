---
title: Xamarin-Mac 故障排除提示
description: 本文档介绍了在开发 Xamarin Mac 应用程序时遇到的问题的解决方法。 它还讨论了获取支持的方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 8714297c4948dbb65c521d6a32bac3e437b40733
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725438"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin-Mac 故障排除提示

## <a name="overview"></a>概述

有时，在处理项目时，所有这些操作都会停滞，因为无法获取 API 来按我们所需的方式工作，也不能尝试解决错误。 Xamarin 的目标是让你能够成功编写移动和桌面应用程序，并提供了一些资源来提供帮助。

使用这些资源中的任何一种，你可以准备一些步骤来帮助他们快速解决你的问题：

- 尽可能最好地确定问题的根本原因，以报告崩溃：

  - "我的应用程序崩溃" 很难诊断。 "当向此调用返回空数组时，应用程序崩溃" 比修复更容易。

  - "无法使 NSTable 工作" 不太有用，因为在这种情况下，似乎不会调用我的 NSTableDelegate 上的任何方法。 "

- 如果可能，请提供一个显示问题的小示例程序。 在查找问题的源代码页面上深入更多的时间和精力。

- 知道您对应用程序所做的更改会导致出现问题，从而快速缩小问题的根源。 请注意，如果你最近升级了 Xamarin 的版本，请修整你的应用程序的各个部分以找出导致问题的部分，或测试以前的版本，以了解引入该问题的更改会非常有帮助。

### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>当应用程序崩溃但没有输出时要执行的操作

在大多数情况下，Visual Studio for Mac 中的调试器将捕获应用程序中的异常和崩溃，并帮助您跟踪根本原因。 但是，在某些情况下，应用程序将在停靠上弹跳，然后在很少或没有输出的情况下退出。 其中包括：

- 代码签名问题。
- 某些 mono 运行时崩溃。
- 某些目标 c 异常和故障。
- 某些情况会在进程生存期内提前崩溃。
- 某些堆栈溢出。
- 你的**信息**中列出的 macOS 版本比你当前安装的 macOS 版本新，或无效。

调试这些程序可能会令人沮丧，因为查找必要的信息可能会很困难。 下面是一些可帮助的方法：

- 确保**info.plist**中列出的 macOS 版本与计算机上当前安装的 macOS 版本相同。
- 检查堆栈跟踪的 Visual Studio for Mac 应用程序输出（**查看** -> **Pad** -> **应用程序输出**），或从可能描述输出的 Cocoa 中以红色输出。
- 从命令行运行应用程序，并使用以下命令查看输出（在**终端**应用中）：

  `MyApp.app/Contents/MacOS/MyApp` （其中，`MyApp` 是应用程序的名称）
- 可以通过在命令行上将 "MONO_LOG_LEVEL" 添加到命令来增加输出，例如：

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 您可以将本机调试器（`lldb`）附加到您的进程，以查看是否提供了更多信息（这需要付费许可证）。 例如，执行以下操作：

  1. 在终端中输入 `lldb MyApp.app/Contents/MacOS/MyApp`。
  2. 在终端中输入 `run`。
  3. 在终端中输入 `c`。
  4. 完成调试后退出。
- 最后一种方法是，在 `Main` 方法（或根据需要在其他位置）调用 `NSApplication.Init` 之前，您可以将文本写入已知位置中的文件，以跟踪正在运行的启动步骤。

## <a name="known-issues"></a>已知问题

以下部分介绍了已知问题及其解决方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>无法在沙盒应用中连接到调试器

调试器通过 TCP 连接到 Xamarin 应用程序，这意味着在默认情况下启用沙盒时，它无法连接到应用，因此，如果你尝试在未启用适当权限的情况下运行应用，则会收到错误 *"无法连接到调试器"* 。

[![编辑权利](troubleshooting-images/debug01.png "编辑权利")](troubleshooting-images/debug01-large.png#lightbox)

"**允许传出网络连接（客户端）** " 权限是调试程序所必需的，启用此项将允许正常调试。 由于不能对其进行调试，因此我们更新了 `msbuild` 的 `CompileEntitlements` 目标，以将该权限自动添加到仅针对调试版本进行沙盒处理的任何应用的权利。 发布版本应使用未修改的权利文件中指定的权利。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>NotSupportedException：没有可用于编码437的数据

如果在 Xamarin 应用程序中包含第三方库，则在尝试编译和运行该应用程序时，可能会出现 "NotSupportedException：无数据可用于编码 437" 形式的错误。 例如，在操作过程中，库（如 `Ionic.Zip.ZipFile`）可能会引发此异常。

为此，可以打开 Xamarin 项目的选项，转到 " **Mac 构建** > **国际化**" 并查看 "**西部**" 国际化：

[![编辑生成选项](troubleshooting-images/issue01.png "编辑生成选项")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>未能编译（mm5103）

此错误通常是在新版本的 Xcode 版本已安装但尚未运行该版本的情况下引发的。 在尝试使用 Xcode 的新版本进行编译之前，首先需要至少运行一次该版本。

首次运行 Xcode 的新版本时，它将安装 Xamarin 所需的几个命令行工具。 此外，还应在更新 Xcode 或 Xamarin 版本后执行干净的生成。

如果你无法解决此问题，请提交[bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>缺少权利。 info.plist

Visual Studio for Mac 的最新版本已从**info.plist**编辑器中删除了 "权利" 部分，并将其放在单独的**info.plist**编辑器中（以获得更好的跨平台支持和 Xamarin）。

安装新的 Visual Studio for Mac 后，当你创建新的 Xamarin Mac 应用项目时，将自动向项目树添加**info.plist**文件：

![选择权利](troubleshooting-images/entitlements01.png "选择权利")

如果双击**info.plist**文件，则将显示 "权利编辑器"：

[![编辑权利](troubleshooting-images/entitlements02.png "编辑权利")](troubleshooting-images/entitlements02-large.png#lightbox)

对于现有的 Xamarin Mac 项目，你将需要手动创建**info.plist**文件，方法是在**Solution Pad**中右键单击该项目，然后选择 "**添加** > **新文件 ...** "。接下来，选择 " **Xamarin** " > **空属性列表**：

![添加新属性列表](troubleshooting-images/entitlements03.png "添加新属性列表")

输入名称的 `Entitlements`，然后单击 "**新建**" 按钮。 如果你的项目以前包含了一个权利文件，则系统会提示你将其添加到项目，而不是创建新文件：

[![验证文件的覆盖](troubleshooting-images/entitlements04.png "验证文件的覆盖")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>论坛上的社区支持

使用 Xamarin 产品的开发人员社区非常惊人，很多人都可以访问我们的[Xamarin 论坛](https://forums.xamarin.com/categories/xamarin-mac)，分享体验和专业知识。 此外，Xamarin 工程师会定期访问论坛来帮助。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>存档 bug

您的反馈对于我们至关重要。 如果发现 Xamarin 的任何问题：

- 搜索[问题存储库](https://github.com/xamarin/xamarin-macios/issues)
- 切换到 GitHub 问题之前，会在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) 中跟踪 Xamarin 问题。 请在其中搜索匹配的问题。
- 如果找不到匹配的问题，请在 [GitHub 问题存储库](https://github.com/xamarin/xamarin-macios/issues/new)中提交一个新问题。

GitHub 的问题是完全公开的。 不能隐藏注释或附件。

请尽可能多地包含以下内容：

- 一个重现此问题的简单示例。 在可能的情况下，这非常有用。
- 故障的完整堆栈跟踪。
- 故障周围的 C# 代码。
