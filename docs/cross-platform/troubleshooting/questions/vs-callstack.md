---
title: 如何收集 Visual Studio 进程的当前调用堆栈？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: 6d9cabdb22425bdc057f6d52de5aa943537dd1ca
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864476"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>如何收集 Visual Studio 进程的当前调用堆栈？

当 GUI 在 Visual Studio 中锁定 （挂起、 冻结） 时，要收集的诊断信息的重要部分是一套 Visual Studio 进程的所有线程的调用堆栈。 若要保存此信息供 Visual Studio 的挂起实例，可以使用 Visual Studio 的第二个实例：

1. 启动 Visual Studio 的第二个实例（新窗口）。

2. 关闭任何打开的解决方案中的 Visual Studio 的新实例。

3. 选择“调试”>“附加到进程”  。

   ![](vs-callstack-images/image1.png "选择调试 > 附加到进程")

4. 从“可用进程”  列表中选择 `devenv.exe` 原始挂起实例。

5. 选择“调试”>“全部中断”  。

   ![](vs-callstack-images/image2.png "选择调试 > 全部中断")

6. 选择**调试 > 另存为转储**。

   ![](vs-callstack-images/image3.png "选择调试 > 另存为转储")

7. 更改**另存为类型**到**小型转储 (\*.dmp)** 。 这将生成较小文件而非**小型转储与堆**，并且在堆通常不适用于诊断冻结。

   ![](vs-callstack-images/image4.png "这将生成较小文件比小型转储与堆，堆通常不与相关诊断冻结")

8. 保存转储文件。 如果提交文件联机，可以压缩它以减小大小。
