---
title: 在设备和仿真器上调试 Xamarin.Android
description: 如何测试和调试 Xamarin.Android 应用
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 0f873f69de7f85a77bdd0ca7aafa33bff1d9b961
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021514"
---
# <a name="debugging"></a>调试

本节讨论如何在设备或仿真器上调试 Xamarin.Android 应用。

## <a name="debugging-overview"></a>调试概述

开发 Android 应用程序需要在物理硬件上或使用仿真器运行应用程序。 使用硬件是最好的方法，但并不总是最实用的方法。 在许多情况下，使用如下所述的仿真器之一模拟/仿真 Android 硬件，这可简化操作且更具成本效益。

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[在 Android Emulator 上调试](~/android/deploy-test/debugging/debug-on-emulator.md)

本文介绍如何从 Visual Studio 启动 Android Emulator 并在虚拟设备中运行应用。

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[在设备上进行调试](~/android/deploy-test/debugging/debug-on-device.md)

本文介绍如何配置物理 Android 设备，以便可以直接从Visual Studio 或 Visual Studio for Mac 将 Xamarin.Android 应用程序部署到此设备。

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Android 调试日志](~/android/deploy-test/debugging/android-debug-log.md)

开发人员用于调试应用程序的一个非常常见的技巧是使用 `Console.WriteLine`。 但是，在移动平台（如 Android）上没有控制台。 Android 设备会提供日志，你在编写应用时可能需要利用它。 这有时称为 **logcat**，原因在于为检索它而输入的命令。 本文介绍如何使用 **logcat**。

> [!WARNING]
> 请注意，**Xamarin Android Player** 已弃用。 有关详细信息，请参阅[此博客文章中的公告](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/)。 此外，自 Visual Studio 2017 起，已弃用 **Visual Studio Android Emulator**。
