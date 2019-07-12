---
title: Xamarin Profiler 故障排除
description: 本文档提供与 Xamarin Profiler 相关的故障排除信息。 它描述与日志记录和诊断、 IDE 和其他主题相关的问题。
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: e4a4376291ff56433c8cd9785989af2983a80c1c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832152"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler 故障排除

## <a name="logging-and-diagnostics"></a>日志记录和诊断

Xamarin 团队可帮助跟踪问题，如果你向我们提供的信息，包括：

- 截屏视频中的问题、 崩溃或故障以及导致它在工作流。
- 日志会输出 （见下文）。
- **.Mlpd**正在生成的分析会话 （见下文）。

### <a name="getting-log-outputs"></a>获取日志输出

在 Mac 上日志会保存到`~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`。

在 Windows 上这些保存到`%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log`每当提交问题时请包括最新的日志。

我们添加了更多记录，我们看一下，因此此输出应增长，并随着时间的推移变得更加有用。

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>生成.mlpd 文件

**.Mlpd**文件是压缩的 mono 运行时探查器输出。 Xamarin Profiler GUI 中读取的数据 **.mlpd** ，并为用户显示。 **.mlpd**文件可用于 Xamarin 调试工具，因为它们可帮助诊断 Profiler 可能会遇到与你的数据的问题，我们的工程师。

**.Mlpd**的当前会话自动保存在你的 Mac`/tmp`目录，并可以确定按时间戳。 如果在启用日志记录，第一个输出将的路径 **.mlpd**文件。 **.Mlpd**文件通常保存在目录中启动 ~/var/文件夹...

**.Mlpd**也可以通过选择保存当前会话的**文件 > 另存为...** 从 Profiler 的菜单：

**Visual Studio for Mac**:

![](troubleshooting-images/image17.png ".Mlpd 文件保存在 Visual Studio for Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "在 Visual Studio 中保存.mlpd 文件")

务必要注意 **.mlpd**包含大量信息，并将大文件大小。

## <a name="troubleshooting"></a>疑难解答

以下列表显示常见难题、 解决方法，以及提示和技巧使用 Profiler。

> [!NOTE]
> 您需要 Visual Studio**企业**订阅服务器才能解锁此功能在 Windows 上的任一 Visual Studio Enterprise 或 Visual Studio for mac。

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>看不到 iOS 探查器选项，或灰显 [Visual Studio 和 Visual Studio for Mac]

检查以下设置来解决此问题：

- 确保使用调试配置
- 请确保使用 SGen 垃圾回收器。
- 请确保该平台是[支持](~/tools/profiler/index.md#Profiler_Support)。
- 确保具有合适的许可证。
- 确保记录您在和正确的身份验证。
- [Visual Studio]您必须使用[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/)以及是否具有有效的企业许可证。

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>尝试启动探查器时遇到错误

如果您遇到此错误框在 Visual Studio 中使用探查器时：

![](troubleshooting-images/error.png "在 Visual Studio 中使用探查器时的错误框")

它通常是由于无法启动到模拟器 / 仿真程序。 请尝试并正常运行应用，修复的问题，它提供了，然后尝试再次使用 Profiler。

#### <a name="to-watch-a-specific-thread"></a>若要观看特定线程

如果你有想要专门观看的线程，则会命名为其创建以获取最开头的线程的理想之选`ThreadName`而不是`0x0`。 例如设置线程名称作为`UI`，可以使用以下代码：

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>相关链接

- [演练-使用 Xamarin Profiler](~/tools/profiler/index.md)
- [内存和性能的最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [发行说明](https://developer.xamarin.com/releases/profiler/preview/)
