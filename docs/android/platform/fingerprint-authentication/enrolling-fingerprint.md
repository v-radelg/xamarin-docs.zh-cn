---
title: 注册指纹
description: 如何设置屏幕锁定并注册 Android 设备或仿真程序上的指纹。
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 18903a7d8f6c4033dc3ac7c4c0187a247d023bc1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026911"
---
# <a name="enrolling-a-fingerprint"></a>注册指纹

## <a name="enrolling-a-fingerprint-overview"></a>注册指纹概述

才有可能利用指纹身份验证，如果已使用指纹身份验证配置该设备的 Android 应用程序。 本指南介绍了如何注册 Android 设备或仿真程序上的指纹。 仿真程序不具有实际的硬件执行指纹扫描，但可以模拟指纹扫描 Android Debug Bridge （如下所述） 的帮助。  本指南介绍了如何启用 Android 设备上的屏幕锁定和注册身份验证的指纹。

## <a name="requirements"></a>要求

若要注册一个指纹，必须具有 Android 设备或仿真程序运行 API 级别 23 (Android 6.0)。

使用的 Android Debug Bridge (ADB)，都需要熟悉命令提示符处，并**adb**可执行文件必须是路径中的 Bash、 PowerShell 或命令提示环境。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>配置屏幕锁定和注册指纹 

若要设置屏幕锁定，请执行以下步骤：

1. 转到**设置 > 安全**，然后选择**屏幕锁定**:

    ![屏幕锁定安全屏幕上的选定内容的位置](enrolling-fingerprint-images/testing-01.png)

2. 将显示下一个屏幕将允许选择并配置屏幕锁定安全方法之一： 

    ![选择轻扫、 模式、 PIN 或密码](enrolling-fingerprint-images/testing-02.png)

   选择并完成其中一个可用的屏幕锁定方法。

3. Screenlock 配置后，返回到**设置 > 安全**页，然后选择**指纹**:

    ![安全屏幕上的指纹选定内容的位置](enrolling-fingerprint-images/testing-03.png)

4. 在这里，请执行用于将指纹添加到设备的序列：

    [![将指纹添加到设备的屏幕截图的序列](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. 在最后一个屏幕会提示您若要将手指放在指纹扫描仪上： 

    ![将提示您将手指放在传感器上的屏幕](enrolling-fingerprint-images/testing-05.png)

    如果使用 Android 设备，通过触摸一根手指触扫描程序完成该过程。 
    
    
### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>模拟指纹扫描在仿真程序

在 Android 模拟器，则可以通过使用 Android 调试桥模拟指纹扫描。 在 OS X 启动终端会话中的在 Windows 上启动命令提示符或 Powershell 会话并运行`adb`:

```shell
$ adb -e emu finger touch 1
```

值**1**是_手指\_id_的手指已"扫描"。 它是为每个虚拟指纹分配的唯一整数。 将来当你运行应用时可以运行此相同 ADB 命令每次在仿真程序将提示你对于指纹，可以运行`adb`命令，并将其传递_手指\_id_来模拟指纹扫描.

指纹扫描完成后，Android 会通知您已添加指纹：  

![屏幕上显示的指纹添加 ！](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>总结 

本指南介绍了如何设置屏幕锁定和注册 Android 设备上或在 Android 模拟器中的指纹。 

