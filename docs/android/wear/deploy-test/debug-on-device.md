---
title: 在穿戴设备上调试
description: 本文介绍如何调试在穿戴设备上的 Xamarin.Android 穿戴设备应用程序。
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 232fcd1d369eba1daad170986f2e2c4c913a3649
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307858"
---
# <a name="debug-on-a-wear-device"></a>在穿戴设备上调试

_本文介绍如何调试在穿戴设备上的 Xamarin.Android 穿戴设备应用程序。_


## <a name="overview"></a>概述

如果具有 Android Wear Smartwatch 如 Android Wear 设备，你可以而不是使用仿真程序在设备上运行应用。 (如果你尚不熟悉的部署和运行过程 Android Wear 应用，请参阅[你好，穿戴设备](~/android/wear/get-started/hello-wear.md)。)

## <a name="prepare-the-wear-device"></a>准备在穿戴设备：

使用以下步骤启用 Android Wear 设备上调试：

1.  打开**设置**Android Wear 设备上的菜单。

2.  滚动到底部的菜单和点击**有关**。

3.  点击内部版本号七倍。

4.  上**设置**菜单中，点击**开发人员选项**。

5.  确认**ADB 调试**已启用。


## <a name="debugging-over-usb"></a>通过 USB 调试

如果在穿戴设备有 USB 端口，可以在穿戴设备连接到计算机，将部署到它，并运行/调试应用程序是使用 Android 手机 (有关详细信息，请参阅[设备上进行调试](~/android/deploy-test/debugging/debug-on-device.md))。


## <a name="debugging-over-bluetooth"></a>通过蓝牙调试

如果在穿戴设备不具有 USB 端口，可以应用部署到在穿戴设备通过蓝牙通过应用程序的调试输出路由到 Android 手机连接到您的计算机。 

### <a name="prepare-your-phone"></a>准备你的手机

使用以下步骤准备你的电话以进行蓝牙连接到在穿戴设备： 

1.  如果尚未这样做，设置手机以供 Xamarin.Android 开发中所述[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

2.  下载并安装免费[Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app)来自 Google Play 商店的应用。

### <a name="connect-the-device"></a>将设备连接

使用以下步骤连接到你的手机穿戴设备：

1.  在手机上，将充当蓝牙中间 （上文中配置），启动 Android Wear 应用程序。 

2.  点击**设置**图标。

3.  启用**调试通过蓝牙**。 您应该看到显示的屏幕上的 Android Wear 应用的以下状态：

        Host: disconnected
        Target: connected

4.  通过 USB 连接到您的计算机的电话。 在计算机上，输入以下命令：

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    如果端口 4444 不可用，可以使用有权访问的任何其他可用端口。 

    **说明**：如果你重启 Visual Studio 或 Visual Studio for Mac，您必须运行以下命令以建立与在穿戴设备。

5.  当在穿戴设备提示您时，请确认你允许**ADB 调试**。 在 Android Wear 应用中，您应看到状态更改为：

        Host: connected
        Target: connected

6.  完成上述步骤后，运行`adb devices`显示手机和 Android Wear 设备的状态：

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

此时，您可以将应用部署到在穿戴设备。

<a name="screenshots" />

### <a name="taking-screenshots"></a>对于拍摄屏幕快照

您可以通过输入以下命令来执行在穿戴设备的屏幕截图： 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

将屏幕快照复制到您的计算机，通过输入以下命令：

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

通过输入以下命令来删除设备上的屏幕截图：

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>卸载应用

可以从在穿戴设备卸载应用，通过输入以下命令：

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

例如，若要删除的包名称应用`com.xamarin.weartest`，输入以下命令：

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

有关调试 Android Wear 设备通过蓝牙的详细信息，请参阅[调试通过蓝牙](https://developer.android.com/training/wearables/apps/bt-debugging.html)。


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>调试包含辅助电话应用程序的 Wear 应用

与 Google Play 上的分发辅助 Android 手机应用程序打包在 android Wear 应用 (有关详细信息，请参阅[使用打包](~/android/wear/deploy-test/packaging.md))。 但是，您仍开发穿戴设备应用和其辅助应用程序分开。 释放通过 Google Play 应用商店应用程序时，将与辅助应用程序一起打包 Wear 应用，并自动安装在可能的情况。

若要调试穿戴设备应用程序与辅助应用程序： 

1.  生成并部署到手机的辅助应用程序。

2.  右键单击穿戴设备项目并将其设置为默认启动项目。

3.  Wear 项目部署到可穿戴设备。

4.  运行和调试 Wear 应用在设备上。

 
## <a name="summary"></a>总结

本文介绍如何配置 Android Wear 穿戴设备通过蓝牙，Visual Studio 中调试的设备，以及如何调试使用辅助电话应用程序的穿戴设备应用。 它还提供有关调试通过蓝牙 Wear 应用的常见调试的提示。
