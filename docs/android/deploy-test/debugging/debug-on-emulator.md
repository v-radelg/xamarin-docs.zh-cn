---
title: 在 Android Emulator 上调试
description: 本指南介绍如何使用 Android Emulator 在 Visual Studio 中启动和调试应用。
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 2bc8f82db29ed3c07c67293a83e6874f0cc6acb2
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556517"
---
# <a name="debug-on-the-android-emulator"></a>在 Android Emulator 上调试

本指南介绍如何在 Android Emulator 中启动虚拟设备以调试和测试应用。 

可使用各种配置运行 Android Emulator（作为“使用 .NET 进行移动开发”工作负荷的一部分进行安装）来模拟各种 Android 设备  。 其中的每个配置都创建为虚拟设备  。 本指南介绍如何从 Visual Studio 启动模拟器以及如何在虚拟设备中运行应用。 有关配置 Android Emulator 和创建新的虚拟设备的信息，请参阅 [Android Emulator 设置](~/android/get-started/installation/android-emulator/index.md)。

## <a name="using-a-pre-configured-virtual-device"></a>使用预配置的虚拟设备

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 包含预配置虚拟设备，可以在“设备”下拉菜单中查看。 例如，在下面的 Visual Studio 2017 屏幕截图中，多个预配置虚拟设备都可用：

- **VisualStudio\_android-23\_arm\_phone**

- **VisualStudio\_android-23\_arm\_tablet**

- **VisualStudio\_android-23\_x86\_phone** 

- **VisualStudio\_android-23\_x86\_tablet** 

[![虚拟设备](debug-on-emulator-images/win/01-virtual-devices-sml.png)](debug-on-emulator-images/win/01-virtual-devices.png#lightbox)

通常都会选择使用“VisualStudio\_android-23\_x86\_phone”  虚拟设备来测试和调试手机应用。 如果这些预配置虚拟设备中有一个能够满足需求（即与应用的目标 API 级别一致），请跳转到[启动仿真器](#launching)，开始在仿真器中运行应用。 （如果尚不熟悉 Android API 级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。）

如果 Xamarin.Android 项目使用的目标框架级别与可用虚拟设备都不兼容，不可用的虚拟设备会在下拉菜单中的“不支持的设备”下列出  。 例如，以下项目的目标框架设为 Android 7.1 Nougat (API 25)，这与此示例中所列的 Android 6.0 虚拟设备不兼容   ：

[![不兼容的虚拟设备](debug-on-emulator-images/win/02-incompatible-level-sml.png)](debug-on-emulator-images/win/02-incompatible-level.png#lightbox)

可以单击“更改最低 Android 目标”  ，更改项目的最低 Android 版本，使其与可用虚拟设备的 API 级别一致。 也可以使用 [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) 新建支持目标 API 级别的虚拟设备。
必须先安装新 API 级别对应的系统映像（请参阅[设置用于 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md)），然后才能为虚拟设备配置此 API 级别。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Visual Studio for Mac 包含预配置虚拟设备，可在“设备”下拉菜单中查看这些设备。 例如，在下面的屏幕截图中，两个预配置虚拟设备都可用：

- Android\_Accelerated\_x86 

- Android\_ARMv7a 

[![虚拟设备](debug-on-emulator-images/mac/01-virtual-devices-sml.png)](debug-on-emulator-images/mac/01-virtual-devices.png#lightbox)

通常会选择使用“Android\_Accelerated\_x86”  虚拟设备来测试和调试手机应用。 如果此预配置虚拟设备满足需求（即与应用的目标 API 级别一致），请跳转到[启动仿真器](#launching)，开始在仿真器中运行应用。 （如果尚不熟悉 Android API 级别，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。）

-----

## <a name="editing-virtual-devices"></a>编辑虚拟设备

若要修改虚拟设备（或创建新的虚拟设备），必须使用 [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)。

<a name="launching" />

## <a name="launching-the-emulator"></a>启动仿真器

在 Visual Studio 的顶部附近，有一个可用于选择“调试”或“发布”模式的下拉菜单   。 选择“调试”可在应用启动后将调试器附加到仿真器内运行的应用程序进程  。 选择“发布”模式可禁用调试器（但是，仍可运行应用并使用 LOG 语句进行调试）  。 在设备下拉菜单中选择虚拟设备后，选择“调试”或“发布”模式，然后单击“播放”按钮运行应用程序   ：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![“调试”和“发布”模式以及“播放”按钮](debug-on-emulator-images/win/17-debug-release-sml.png)](debug-on-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![“调试”和“发布”模式以及“播放”按钮](debug-on-emulator-images/mac/16-debug-release-sml.png)](debug-on-emulator-images/mac/16-debug-release.png#lightbox)

-----

仿真器启动后，Xamarin.Android 将应用部署到仿真器。 仿真器会使用配置的虚拟设备映像运行此应用。 Android Emulator 的示例屏幕截图如下所示。 在此示例中，仿真器在运行名为 MyApp 的空白应用  ：

![运行空白应用的仿真器](debug-on-emulator-images/emulator-running.png)

可以一直运行仿真器：无需关闭仿真器并在每次启动应用时等待重启。 当 Xamarin.Android 应用首次在仿真器中运行时，将会先安装面向目标 API 级别的 Xamarin.Android 共享运行时，再安装应用。 运行时安装过程可能需要一段时间，请耐心等待。 仅当首次向仿真器部署 Xamarin.Android 应用时，才会安装运行时 &ndash; 后续部署速度更快，因为仅将应用复制到仿真器。

<a name="quick-boot" />

## <a name="quick-boot"></a>快速启动

较新版本的 Android Emulator 包含一个名为“快速启动”的功能，只需几秒钟即可启动模拟器  。 关闭仿真器时，它会拍摄虚拟设备状态的快照，以便在重启时从该状态快速还原。
要使用此功能，需要以下工具：

- Android Emulator 版本 27.0.2 或更高版本
- Android SDK Tools 版本 26.1.1 或更高版本

安装了上述版本的仿真器和 SDK 工具后，默认情况下会启用“快速启动”功能。 

由于尚未创建快照，虚拟设备的首次冷启动没有加快速度：

![冷启动屏幕截图](debug-on-emulator-images/cold-boot.png)

退出仿真器时，“快速启动”在快照中保存仿真器的状态：

![在关闭时保存状态](debug-on-emulator-images/saving-state.png)

之后，启动虚拟设备的速度大幅加快，因为仿真器只需还原关闭仿真器时的状态。

![在重启时加载状态](debug-on-emulator-images/loading-state.png)

## <a name="troubleshooting"></a>疑难解答

有关常见模拟器问题的技巧和解决方法，请参阅 [Android Emulator 疑难解答](~/android/get-started/installation/android-emulator/troubleshooting.md)。

## <a name="summary"></a>总结

本指南介绍如何配置 Android Emulator，以便能够运行和测试 Xamarin.Android 应用。 其中介绍了使用预配置的虚拟设备启动仿真器的步骤，并提供了将应用程序从 Visual Studio 部署到仿真器的步骤。 

若要深入了解如何使用 Android Developer，请参阅以下 Android 开发者主题：

- [屏幕导航](https://developer.android.com/studio/run/emulator.html#navigate)

- [在仿真器中执行基本任务](https://developer.android.com/studio/run/emulator.html#tasks)

- [使用扩展控件、设置和帮助](https://developer.android.com/studio/run/emulator.html#extended)

- [使用快速启动运行仿真器](https://developer.android.com/studio/run/emulator#quickboot)
