---
title: 调试的仿真程序上的 Android 穿戴设备
description: 这些文章介绍如何调试的仿真程序上的 Xamarin.Android 穿戴设备应用程序。
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 699fb3cc3a5730e8ab2c677feb7cdfbdcf106aeb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308249"
---
# <a name="debug-android-wear-on-an-emulator"></a>调试的仿真程序上的 Android 穿戴设备

_这些文章介绍如何调试的仿真程序上的 Xamarin.Android 穿戴设备应用程序。_

## <a name="debug-wear-on-emulator-overview"></a>调试穿戴设备仿真程序概述

开发 Android Wear 应用程序需要运行该应用程序，在物理硬件上或使用仿真器或模拟器。 使用硬件是最好的方法，但并不总是最实用的方法。 在许多情况下，它可以是更简单且更具成本效益模拟/仿真 Android Wear 硬件使用仿真器，如下所述。 如果你尚不熟悉的部署和运行过程 Android Wear 应用，请参阅[你好，穿戴设备](~/android/wear/get-started/hello-wear.md)。

## <a name="configure-the-android-emulator"></a>配置 Android 仿真器

若要在仿真器上运行穿戴设备应用，必须安装 Android SDK Android 仿真器，并将其配置为 Android Wear。 有关整体的 Android SDK 仿真程序安装和配置信息，请参阅[Android 仿真程序安装程序](~/android/get-started/installation/android-emulator/index.md)。

当创建 Wear 虚拟设备时，选择 Android Wear 设备配置文件 (如**Android Wear 正方形**)。 为改进性能，使用 Wear **x86** CPU/ABI 在此示例中所示：

[![Wear 虚拟设备配置示例](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>启动 Wear 虚拟设备 

创建 Android Wear 虚拟设备后，你可以选择它从设备下拉列表菜单在 IDE 中开始调试之前。 如果你的虚拟设备不可用的设备下拉列表中，验证你的项目是 Android *Wear*应用程序项目 （不 Android 应用程序项目），且，其目标 API 级别设置为相同的 API 级别设置为虚拟设备。 例如：

[![在 Visual Studio 设备菜单中选择 Wear AVD](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Android 仿真程序启动后，则 Xamarin.Android 将 Wear 应用部署到仿真程序。 仿真器会使用配置的虚拟设备映像运行此应用。

不会感到惊讶，如果看到此错误 （或另一个插播式屏幕） 在第一个。 观看仿真程序可能需要一段时间才能启动： 

![观看仿真程序将显示只需一分钟的时间...](debug-on-emulator-images/please-wait.png)

可以一直运行仿真器；无需关闭仿真器并在每次运行应用时重启。

 
## <a name="summary"></a>总结
 
本指南介绍了如何配置 Android 仿真器以穿戴设备开发和启动 Wear 虚拟设备以进行调试。
