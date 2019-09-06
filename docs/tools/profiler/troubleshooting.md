---
title: Xamarin Profiler 疑难解答
description: 本文档提供与 Xamarin Profiler 相关的疑难解答信息。 它介绍与日志记录和诊断、IDE 和其他主题相关的问题。
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: conceptdev
ms.author: crdun
ms.date: 10/27/2017
ms.openlocfilehash: c6a05e332bf0c08f8c7ea328c2793f7d0bf00fb7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285702"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler 疑难解答

## <a name="logging-and-diagnostics"></a>日志记录和诊断

如果你向我们提供信息，Xamarin 团队可以帮助跟踪问题，其中包括：

- Screencast 的问题、故障或故障，以及你的工作流。
- 日志输出（见下文）。
- 正在为分析会话生成的 **.mlpd** （请参阅下文）。

### <a name="getting-log-outputs"></a>获取日志输出

将保存到`~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`。

在 Windows 上，当你`%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log`提交问题时，它们将保存到，并包括最新的日志。

我们将在此期间添加更多日志记录，因此，此输出会随着时间的推移而变得更加有用。

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>生成 .mlpd 文件

**.Mlpd**文件是 mono 运行时探查器的压缩输出。 Xamarin Profiler GUI 从 **.mlpd**读取数据并为用户显示该数据。 **.mlpd**文件对 Xamarin 有用，因为它们可帮助工程师诊断探查器可能会遇到的数据问题。

当前会话的 **.mlpd**将自动保存在 Mac 的`/tmp`目录中，并且可通过时间戳进行标识。 如果启用日志记录，则第一个输出将是 **.mlpd**文件的路径。 **.Mlpd**文件通常会保存在目录中，从 ~/var/folders。

还可以通过选择 " **File > 另存为 ...** " 来保存当前会话的 **.mlpd** 从探查器菜单：

**Visual Studio for Mac**：

![](troubleshooting-images/image17.png "正在 Visual Studio for Mac 中保存 .mlpd 文件")

**Visual Studio**：

![](troubleshooting-images/image17-vs.png "在 Visual Studio 中保存 .mlpd 文件")

请务必注意， **.mlpd**包含很多信息，文件大小会很大。

## <a name="troubleshooting"></a>疑难解答

下面的列表显示了使用探查器的常见陷阱、解决方法和提示和技巧。

> [!NOTE]
> 你需要成为 Visual Studio **Enterprise**订阅者，才能在 Windows 或 Visual Studio for Mac 上的 Visual Studio Enterprise 中解除此功能的锁定。

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>我看不到 iOS 探查器选项，或该选项灰显 [Visual Studio 和 Visual Studio for Mac]

检查以下设置以解决此问题：

- 确保你使用的是调试配置
- 确保使用的是 SGen 垃圾回收器。
- 确保平台[受支持](~/tools/profiler/index.md#Profiler_Support)。
- 确保你拥有正确的许可证。
- 确保你已登录并正确地进行了身份验证。
- [Visual Studio]您必须使用[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/)并且具有有效的企业许可证。

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>我在尝试启动探查器时收到错误

如果在 Visual Studio 中使用探查器时遇到此错误框：

![](troubleshooting-images/error.png "在 Visual Studio 中使用探查器时出现错误框")

这通常是由于无法启动到模拟器/仿真器。 尝试正常运行应用程序，修复它提供的问题，然后再次尝试使用探查器。

#### <a name="to-watch-a-specific-thread"></a>监视特定线程

如果你有一个想要专门观看的线程，最好将该线程命名为开始创建`ThreadName`的位置， `0x0`而不是。 例如，若要将线程名称设置`UI`为，可以使用以下代码：

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>相关链接

- [演练-使用 Xamarin Profiler](~/tools/profiler/index.md)
- [内存和性能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
