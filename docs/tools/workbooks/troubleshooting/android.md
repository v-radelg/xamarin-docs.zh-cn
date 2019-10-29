---
title: Android 上的 Xamarin Workbooks 疑难解答
description: 本文档提供有关在 Android 上使用 Xamarin Workbooks 的疑难解答技巧。 它讨论了模拟器支持、无法加载的工作簿以及其他主题。
ms.prod: xamarin
ms.assetid: F1BD293B-4EB7-4C18-A699-718AB2844DFB
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: be19005ab1125c060ab0111e9f37568d5f4abe45
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029597"
---
# <a name="troubleshooting-xamarin-workbooks-on-android"></a>Android 上的 Xamarin Workbooks 疑难解答

## <a name="emulator-support"></a>模拟器支持

若要运行 Android 工作簿，必须可以使用 Android 仿真程序。 不支持物理 Android 设备。

如果你的计算机支持，则建议将 Google 的模拟器连同 HAXM 一起提供。
如果你必须在系统上启用 Hyper-v，请转到 Visual Studio Android Emulator。

你必须具有运行 Android 5.0 或更高版本的模拟器。 不支持 ARM 模拟器。 仅使用 `x86` 或 `x86_64` 设备。

如果你不熟悉此过程，请参阅[有关设置 Android 仿真][android-emu]程序的文档。

> [!NOTE]
> 工作簿1.1 及更早版本将尝试（并失败！）使用 ARM 模拟器（如果可用）。 若要解决此情况，请在打开或创建 Android 工作簿之前启动所选的 x86 模拟器。 只要工作簿兼容，工作簿将始终更喜欢连接到正在运行的模拟器。

## <a name="workbooks-wont-load"></a>工作簿无法加载

### <a name="workbook-window-spins-forever-never-loads-windows"></a>工作簿窗口永远旋转，从不加载（Windows）

首先，通过在模拟器的 web 浏览器中测试任何网站，检查模拟器是否具有完全工作的网络访问权限。

### <a name="visual-studio-android-emulator-cannot-connect-to-the-internet"></a>Visual Studio Android Emulator 无法连接到 internet

如果模拟器没有网络访问权限，可能需要执行以下步骤来修复 Hyper-v 网络交换机。 如果经常在 Wi-fi 网络之间切换，可能需要定期重复此操作：

1. **请确保所有关键网络操作都已完成，因为这可能会暂时断开 Windows 与 internet 的连接。**
1. 关闭模拟器。
1. 打开 `Hyper-V Manager`。
1. 在 "`Actions`" 下，打开 `Virtual Switch Manager...`。
1. 删除所有虚拟交换机。
1. 单击 `OK`。
1. 启动 VS Android Emulator。 系统可能会提示你重新创建虚拟网络交换机。
1. 与 Android Emulator 的浏览器相比，测试是否可以访问 internet。

[android-emu]: ~/android/deploy-test/debugging/debug-on-emulator.md

## <a name="related-links"></a>相关链接

- [报告 Bug](~/tools/workbooks/install.md#reporting-bugs)
