---
title: Xamarin 预编译
description: 本文档预先介绍了 Xamarin 中的编译时间。 它将 AOT 编译与 JIT 编译进行比较，说明如何启用 AOT，并查看混合 AOT。
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 6797428596fddb0361fb307240bf8237a1e8554d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769835"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin 预编译

## <a name="overview"></a>概述

预先（AOT）编译是一种功能强大的优化技术，可提高启动性能。 但是，它还会以深远的方式影响生成时间、应用程序大小和程序执行。 为了理解它所施加的折衷，我们将对应用程序的编译和执行进行一些探讨。

用托管语言（例如C#和F#）编写的代码编译为名为 IL 的中间表示形式。 存储在库和程序程序集中的这一 IL 相对紧凑，并可在处理器体系结构之间移植。 不过，IL 只是一组中间的指令，在某一时刻需要将 IL 转换为特定于处理器的计算机代码。

可以执行以下两个操作：

- 实时 **（JIT）** –在应用程序的启动和执行期间，IL 将在内存中编译为计算机代码。
- **提前（AOT）** –在生成过程中，将编译 IL 并将其写出到本机库，并将其存储在应用程序捆绑包中。

每个选项都有多个优点和折衷：

- **JIT**
  - **启动时间**–必须在启动时执行 JIT 编译。 对于大多数应用程序，这是100ms 的顺序，但对于大型应用程序，这种情况可能会大大增加。
  - **执行**–由于可以针对所使用的特定处理器优化 JIT 代码，因此可以生成更好的代码。 在大多数应用程序中，这是最快的几个百分比点。
- **AOT**
  - **启动时间**–加载预编译的 DYLIB 比 JIT 程序集快得多。
  - **磁盘空间**–这些 dylib 可能会占用大量的磁盘空间。 根据 AOTed 的程序集，它可以使应用程序的代码部分翻倍或更大。
  - **生成时间**–对于 JIT 编译，它的运行速度明显降低，并将使用它来降低生成速度。 这种减速范围可以是几分钟到一分钟或更长时间，具体取决于编译的程序集的大小和数目。
  - **模糊**处理–作为 IL 比机器代码更容易进行反向工程，这并不是必需的，可将其去除以帮助对敏感代码进行模糊处理。 这需要如下所述的 "混合" 选项。

## <a name="enabling-aot"></a>启用 AOT

在将来的更新中，会将 AOT 选项添加到 "Mac 生成" 窗格。 在此之前，启用 AOT 要求通过 Mac Build 中的 "其他 mmp 参数" 字段传递一个命令行参数。 提供了以下选项：

```
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
```

## <a name="hybrid-aot"></a>混合 AOT

在 macOS 应用程序的执行过程中，运行时默认为使用从 AOT 编译生成的本机库中加载的计算机代码。 但有些代码区域（如 trampolines），JIT 编译可以产生更多的优化结果。 这要求托管程序集 IL 可用。 在 iOS 上，应用程序被限制为使用 JIT 编译;这部分代码也是 AOT。

混合选项指示编译器编译这些节（如 iOS），同时还假定在运行时不会提供 IL。 然后，可以将此 IL 去除生成后。 如上所述，在某些位置，运行时将强制使用不太优化的例程。

## <a name="further-considerations"></a>更多注意事项

通过处理的程序集的大小和数量来扩展 AOT 的不利影响。 示例的完整[目标框架](~/mac/platform/target-framework.md)包含比新式更大的基类库（BCL），因此 AOT 会花费很长时间，并生成更大的绑定。 与链接（去除未使用的代码）的整个目标框架不兼容时，这会更加复杂。 请考虑将应用程序移至新式，并启用链接以获得最佳结果。

AOT 的另一个优点是改进了与本机调试和分析工具链的交互。 由于大部分基本代码会提前编译，因此它将具有在本机崩溃报告、分析和调试中更易于阅读的函数名称和符号。 JIT 生成的函数没有这些名称，通常显示为难以解析的未命名十六进制偏移量。
