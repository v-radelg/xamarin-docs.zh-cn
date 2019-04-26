---
title: 实时编译之前 Xamarin.Mac
description: 本文档介绍了之前在 Xamarin.Mac 中实时编译。 它比较 AOT 编译为 JIT 编译，介绍如何启用 AOT，并介绍了混合 AOT。
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e155a394afd68d9970ee32785f6d0aeda6e2d129
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034199"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>实时编译之前 Xamarin.Mac

## <a name="overview"></a>概述

继续操作的预先 (aot) 编译为提高启动性能的功能强大的优化技术。 但是，它还会影响生成时，应用程序大小和程序执行中影响深远的方式。 若要了解其施加的权衡，我们将更深入的编译和执行应用程序。

编写的代码托管语言，如C#和F#，编译到名为 IL 的中间表示形式。 此 IL，存储在您的库和程序程序集，是相对紧凑且可移植处理器体系结构之间。 IL，但是，是仅设置的说明，在某个时间点的 IL 将需要转换为特定于处理器的计算机代码设置的中间。

有两个点，可以在其中完成这一处理：

- **只需在实时 (JIT)** – 在启动和执行 IL 编译为机器代码的内存中应用程序的过程。
- **预先 (aot) 的预**– 生成 IL 编译并写出到本机库和应用程序捆绑包中存储过程。

每种方法有多种优点和缺点：

- **JIT**
  - **启动时间**– 必须在启动时执行 JIT 编译。 对于大多数应用程序这是大约为 100 毫秒，但对于大型应用程序这一次可以大得多。
  - **执行**– 可以针对正在使用的特定处理器优化为 JIT 代码，可以生成略有更好的代码。 在大多数应用程序这是速度更快的几个百分比点最多。
- **AOT**
  - **启动时间**– 正在加载预编译的 dylib 是明显快于 JIT 的程序集。
  - **磁盘空间**– 这些 dylib 不过可能需要相当大的磁盘空间。 具体取决于哪个程序集是 AOTed，它可以双击或更多应用程序的代码部分的大小。
  - **生成时间**– AOT 编译是要慢得多的 JIT 将使用它生成的速度较慢。 这种减速秒最多一分钟或更多，具体取决于大小和数量编译的程序集的范围。
  - **模糊处理**– 作为 IL，这是要实施反向工程比机器代码简单得多，不一定需要可以去除来帮助对敏感代码进行模糊处理。 这需要"混合"选项下面所述。

## <a name="enabling-aot"></a>启用 AOT

AOT 选项将添加到将来的更新中的 Mac 生成窗格。 在此之前，启用 AOT 需要通过在 Mac 生成的"其他 mmp 参数"字段中传递的命令行参数。 提供了以下选项：


      --aot[=VALUE]          Specify assemblies that should be AOT compiled
                               - none - No AOT (default)
                               - all - Every assembly in MonoBundle
                               - core - Xamarin.Mac, System, mscorlib
                               - sdk - Xamarin.Mac.dll and BCL assemblies
                               - |hybrid after option enables hybrid AOT which
                               allows IL stripping but is slower (only valid
                               for 'all')
                                - Individual files can be included for AOT via +
                               FileName.dll and excluded via -FileName.dll

                               Examples:
                                 --aot:all,-MyAssembly.dll
                                 --aot:core,+MyOtherAssembly.dll,-mscorlib.dll



## <a name="hybrid-aot"></a>混合 AOT

在 macOS 应用程序运行时的执行期间默认情况下的使用计算机代码从生成的 AOT 编译的本机库加载。 有，但是，某些区域的代码如 trampolines，JIT 编译可能会产生显著更优化的结果。 这需要可用的托管程序集 IL。 任何使用 JIT 编译; 在 iOS 上，限制应用程序这些部分中的代码有 AOT 也编译。

混合选项中指示到这两个编译编译器 （如 iOS) 这些部分但也要认为 IL 不会在运行时可用。 然后可以去除此 IL 中发布版本。 如上文所述，将强制运行时在某些位置使用较低优化的例程。

## <a name="further-considerations"></a>更多注意事项

使用大小和数量的处理程序集的 AOT 缩放负面结果。 完整[目标框架](~/mac/platform/target-framework.md)示例包含明显变大基类库 (BCL) 比现代，并因此 AOT 将花费很长并生成更大的捆绑包。 这很复杂，通过使用链接的完整目标框架不兼容性，其中剥离出未使用的代码。 请考虑在应用程序迁移到新式并启用链接为获得最佳结果。

一个 AOT 的另一个好处附带了与本机调试和分析工具链的改进了交互。 由于将提前编译绝大多数的基本代码，它将具有的函数名称和更轻松地阅读内部本机故障报告、 分析和调试的符号。 生成的 JIT 函数不具有这些名称，并通常显示为未命名很难解决的十六进制偏移量。
