---
title: Android 仿真器设置
description: 可使用各种配置运行 Android Emulator 来模拟不同的设备。 本指南介绍如何准备好 Android Emulator 以测试应用。
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: cb8dc64a8c40496aaf2ea23a834effb58436618a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759079"
---
# <a name="android-emulator-setup"></a>Android 仿真器设置

本指南介绍如何准备好 Android Emulator 以测试应用  。

## <a name="overview"></a>概述

可使用各种配置运行 Android Emulator 来模拟不同的设备。 每个配置称为虚拟设备  。 在仿真器上部署和测试应用时，选择模拟物理 Android 设备（如 Nexus 或 Pixel 手机）的预配置或自定义虚拟设备。

下面列出的部分介绍了如何加速 Android Emulator 以最大限度提高性能、如何使用 Android Device Manager 创建和自定义虚拟设备，以及如何自定义虚拟设备的配置文件属性。 此外，疑难解答部分说明了常见模拟器问题和解决方法。

## <a name="sections"></a>部分

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[通过硬件加速提高仿真器性能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

如何使用 Hyper-V 或 HAXM 虚拟化技术为计算机准备最大的 Android Emulator 性能。 由于在没有硬件加速的情况下 Android Emulator 的运行可能会极度缓慢，因此，建议在使用模拟器之前在计算机上启用硬件加速。

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[使用 Android Device Manager 管理虚拟设备](~/android/get-started/installation/android-emulator/device-manager.md)

如何使用 Android Device Manager 创建和自定义虚拟设备。

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)

如何使用 Android Device Manager 编辑虚拟设备的配置文件属性。

### <a name="android-emulator-troubleshootingandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Android Emulator 疑难解答](~/android/get-started/installation/android-emulator/troubleshooting.md)

本文介绍运行 Android Emulator 时最常见的警告消息和问题，以及解决方法和相关技巧。

配置 Android Emulator 后，请参阅[使用 Google Android Emulator 进行调试](~/android/deploy-test/debugging/debug-on-emulator.md)，了解如何启动模拟器以及如何使用它测试并调试应用的信息。

> [!NOTE]
> 自 Android SDK 工具版本 26.0.1 和更高版本开始，Google 已删除了对现有 AVD/SDK 管理器的支持，以支持其新的 CLI（命令行接口）工具  。 由于此弃用更改，因此现在将 Xamarin SDK/Device Manager 用于 Android 工具 26.0.1 和更高版本，而不使用 Google SDK/Device Manager。 有关 Xamarin SDK 管理器的详细信息，请参阅[设置 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md)。
