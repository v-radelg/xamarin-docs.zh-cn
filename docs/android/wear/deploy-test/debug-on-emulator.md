---
title: 在模拟器上调试 Android 磨损
description: 这些文章介绍了如何在模拟器上调试 Xamarin 应用程序应用程序。
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: f085aaffbedb2965222b98a22cf6a4bb2393642b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764040"
---
# <a name="debug-android-wear-on-an-emulator"></a>在模拟器上调试 Android 磨损

_这些文章介绍了如何在模拟器上调试 Xamarin 应用程序应用程序。_

## <a name="debug-wear-on-emulator-overview"></a>调试仿真程序的磨损概述

开发 Android 应用程序需要在物理硬件上运行应用程序，或者在使用模拟器或模拟器的情况下运行应用程序。 使用硬件是最好的方法，但并不总是最实用的方法。 在许多情况下，如下面所述，使用模拟器来模拟/模拟 Android 磨损硬件可以更简单、更经济高效。 如果你尚不熟悉部署和运行 Android 应用程序的过程，请参阅[Hello，磨损](~/android/wear/get-started/hello-wear.md)。

## <a name="configure-the-android-emulator"></a>配置 Android Emulator

若要在仿真程序上运行应用程序，必须安装 Android SDK Android Emulator 并对其进行配置以进行 Android 磨损。 有关 Android SDK 模拟器的总体安装和配置信息，请参阅[Android Emulator 安装程序](~/android/get-started/installation/android-emulator/index.md)。

创建磨损虚拟设备时，请选择 Android 磨损设备配置文件（例如**Android 磨损方形**）。 为了提高性能，请使用以下示例中所示的磨损**x86** CPU/ABI：

[![磨损虚拟设备配置示例](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)

## <a name="launch-the-wear-virtual-device"></a>启动磨损虚拟设备 

创建 Android 磨损虚拟设备后，你可以在开始调试之前从 IDE 中的 "设备" 下拉菜单中选择它。 如果设备下拉菜单中的虚拟设备不可用，请验证你的项目是否为 Android 应用程序项目（不是 Android*应用程序项目*）以及其目标 api 级别是否设置为与虚拟设备相同的 api 级别。 例如:

[![在 Visual Studio 设备菜单中选择磨损 AVD](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Android 仿真程序启动后，Xamarin 会将此应用程序部署到模拟器。 仿真器会使用配置的虚拟设备映像运行此应用。

如果你首先看到此（或另一个插播式屏幕），请不要惊讶。 监视模拟器可能需要一段时间才能启动： 

![监视模拟器只显示一分钟 。](debug-on-emulator-images/please-wait.png)

可以一直运行仿真器；无需关闭仿真器并在每次运行应用时重启。

## <a name="summary"></a>总结

本指南介绍了如何配置用于磨损开发的 Android Emulator，并启动了用于调试的磨损虚拟设备。
