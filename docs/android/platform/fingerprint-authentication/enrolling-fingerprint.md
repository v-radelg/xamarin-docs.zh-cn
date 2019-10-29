---
title: 注册指纹
description: 如何设置屏幕锁定并在 Android 设备或模拟器上注册指纹。
ms.prod: xamarin
ms.assetid: 52092F63-00EE-4F8B-A49F-65C9CCBA7EF2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c0290dfa3b4aa301a07a589f78577899e8282158
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027597"
---
# <a name="enrolling-a-fingerprint"></a>注册指纹

## <a name="enrolling-a-fingerprint-overview"></a>注册指纹概述

仅当已使用指纹身份验证配置了设备时，Android 应用程序才可以使用指纹身份验证。 本指南将介绍如何在 Android 设备或模拟器上注册指纹。 仿真器没有实际硬件来执行指纹扫描，但可以使用 Android Debug Bridge 的帮助模拟指纹扫描（如下所述）。  本指南将讨论如何在 Android 设备上启用屏幕锁定并注册用于身份验证的指纹。

## <a name="requirements"></a>要求

若要注册指纹，你必须有一个 Android 设备或一个运行 API 级别23（Android 6.0）的模拟器。

Android Debug Bridge （ADB）的使用需要熟悉命令提示符，并且**ADB**可执行文件必须位于 Bash、PowerShell 或命令提示符环境的路径中。

## <a name="configuring-a-screen-lock-and-enrolling-a-fingerprint"></a>配置屏幕锁定并注册指纹 

若要设置屏幕锁定，请执行以下步骤：

1. 请参阅 "**设置" > 安全**"，然后选择"**屏幕锁定**"：

    ![屏幕锁定选择在 "安全" 屏幕上的位置](enrolling-fingerprint-images/testing-01.png)

2. 随后出现的屏幕将允许您选择和配置屏幕锁定安全方法之一： 

    ![选择轻扫、模式、PIN 或密码](enrolling-fingerprint-images/testing-02.png)

   选择并完成一个可用的屏幕锁方法。

3. 配置 screenlock 后，请返回到 "**设置" > 安全**"页，然后选择"**指纹**"：

    ![指纹选择在 "安全" 屏幕上的位置](enrolling-fingerprint-images/testing-03.png)

4. 在该处，按照顺序向设备添加指纹：

    [用于向设备添加指纹的屏幕截图![序列](enrolling-fingerprint-images/testing-04-sml.png)](enrolling-fingerprint-images/testing-04.png#lightbox)

5. 在最后一个屏幕中，系统会提示你将手指置于指纹扫描器上： 

    ![提示你将手指置于传感器上的屏幕](enrolling-fingerprint-images/testing-05.png)

    如果使用 Android 设备，请通过触摸手指触摸扫描仪来完成此过程。 

### <a name="simulating-a-fingerprint-scan-on-the-emulator"></a>模拟模拟器上的指纹扫描

在 Android 模拟器上，可以使用 Android Debug Bridge 来模拟指纹扫描。 在操作系统上，在 Windows 上启动终端会话启动命令提示符或 Powershell 会话并运行 `adb`：

```shell
$ adb -e emu finger touch 1
```

值**1**为 "已扫描" 手指 _\_id_ 。 它是为每个虚拟指纹分配的唯一整数。 以后当应用程序运行时，你可以在每次模拟器提示你提供指纹时运行此相同的 ADB 命令，你可以运行 `adb` 命令并向其传递_finger\_id_以模拟指纹扫描。

指纹扫描完成后，Android 会通知你已添加指纹：  

![显示指纹的屏幕已增加！](enrolling-fingerprint-images/testing-06.png)

## <a name="summary"></a>总结 

本指南介绍了如何在 Android 设备或 Android 模拟器中设置屏幕锁定并注册指纹。 
