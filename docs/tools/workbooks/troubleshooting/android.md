---
title: 在 Android 上的故障排除 Xamarin 工作簿
description: 本文档提供有关使用 Xamarin 在 Android 上的工作簿的故障排除提示。 它讨论了仿真程序支持、 不会加载的工作簿和其他主题。
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: a93288829ff99027a4b33e7720a7f849df37e9b1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423785"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>在 Android 上的故障排除 Xamarin 工作簿

## <a name="emulator-support"></a>仿真程序支持

若要运行的 Android 工作簿，Android 模拟器必须可供使用。 不支持物理 Android 设备。

如果您的计算机支持，我们建议以及 HAXM Google 仿真器。
如果你必须拥有你的系统上启用 HYPER-V，转而使用 Visual Studio Android 仿真程序。

您必须具有一个运行 Android 5.0 或更高版本的仿真程序。 不支持 ARM 仿真程序。 使用`x86`或`x86_64`仅设备。

请阅读[我们的文档上设置 Android 仿真器][ android-emu]如果不熟悉此过程。

> [!NOTE]
> 1.1 及更早版本的工作簿将尝试 （以及失败 ！） 如果可用，使用 ARM 仿真程序。 若要解决此问题，启动 x86 仿真程序打开或创建一个 Android 工作簿之前所选。 工作簿将始终优先使用连接到运行的仿真程序，只要它是兼容。

## <a name="workbooks-wont-load"></a>不会加载工作簿

### <a name="workbook-window-spins-forever-never-loads-windows"></a>工作簿窗口下去，旋转永远不会加载 (Windows)

首先，检查在仿真程序通过仿真程序的 web 浏览器中测试任何网站具有完全工作网络访问权限。

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Visual Studio Android 仿真程序无法连接到 internet

如果在仿真程序不具有网络访问权限，您可能需要执行这些步骤可以解决您的 HYPER-V 网络交换机。 如果频繁的 Wi-fi 网络之间切换，则可能需要定期重复此过程：

0. **请确保任何关键的网络操作都已完成，因为这可能会暂时断开与 internet 连接 Windows。**
1. 关闭仿真程序。
2. 打开 `Hyper-V Manager`。
3. 下`Actions`，打开`Virtual Switch Manager...`。
4. 删除所有虚拟交换机。
5. 单击 `OK`。
6. 启动 VS Android 仿真程序。 系统可能会提示重新创建虚拟网络交换机。
7. 测试与 Android 仿真程序的浏览器可以访问 internet。

[android-emu]: https://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debug-on-emulator/


## <a name="related-links"></a>相关链接

- [报告 Bug](~/tools/workbooks/install.md#reporting-bugs)
