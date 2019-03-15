---
title: 设置设备进行开发
description: 本文介绍了如何设置 Android 设备并将其连接到计算机，使设备可用于运行和调试 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: e009ec60abc5afd06e43122424c325bbb1bf42ac
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668863"
---
# <a name="set-up-device-for-development"></a>设置设备进行开发

_本文介绍如何设置 Android 设备并将其连接到计算机，使设备可用于运行和调试 Xamarin.Android 应用程序。_

目前为止，你可能已经看到了新应用程序在 Android 仿真程序上运行，并希望看到它在闪亮的 Android 设备上运行。 以下步骤涉及如何将设备连接到计算机以进行调试：

1.  **在设备上启用调试** - 默认情况下，无法在 Android 设备上调试应用程序。

2.  **安装 USB 驱动程序** - 对于 OS X 计算机，无需此步骤。 Windows 计算机可能需要安装 USB 驱动程序。

3.  **将设备连接到计算机** - 通过 USB 或 WiFi 将设备连接到计算机，这是最后一步。

上述每个步骤将在以下部分中详细介绍。


## <a name="enable-debugging-on-the-device"></a>在设备上启用调试

可使用 Android 设备测试 Android 应用程序。 但是，必须先正确配置设备，才能开始调试。 所涉及的步骤略有不同，具体取决于设备上运行的 Android 版本。


### <a name="android-40-to-android-41"></a>Android 4.0 到 Android 4.1

针对 Android 4.0.x 到 Android 4.1.x，请按照以下步骤启用调试：

1.  转到“设置”屏幕。
2.  选择“开发人员选项”。
3.  选中“USB 调试”选项。

此屏幕截图显示运行 Android 4.0.3 的设备上的“开发人员选项”屏幕：

[![开发人员选项](set-up-device-for-development-images/developer-options-sml.png)](set-up-device-for-development-images/developer-options.png#lightbox)


### <a name="android-42-and-higher"></a>Android 4.2 及更高版本

从 Android 4.2 及更高版本开始，默认情况下，“开发人员选项”是隐藏的。 若要启用，请转到“设置”>“关于手机”，然后点击七次“内部版本号”项以显示“开发人员选项”选项卡：

[![内部版本号项](set-up-device-for-development-images/about-phone-sml.png)](set-up-device-for-development-images/about-phone.png#lightbox)

“开发人员选项”选项卡可用后，请在“设置”>“系统”下将其打开，以显示开发人员设置：

[![开发人员设置屏幕](set-up-device-for-development-images/developer3.png)](set-up-device-for-development-images/developer3.png#lightbox)

从此处可启用开发人员选项，例如 USB 调试和保持唤醒状态模式。


## <a name="install-usb-drivers"></a>安装 USB 驱动程序

对于 OS X，无需此步骤。只需通过 USB 线将设备连接到 Mac。

可能需要先安装一些额外的驱动程序，Windows 计算机才能识别通过 USB 连接的 Android 设备。

> [!NOTE]
> 这些是设置 Google Nexus 设备的步骤，将作为参考提供。 适用于特定设备的步骤可能有所不同，但遵循的模式是类似的。 如果遇到问题，请在 Internet 上搜索你的设备。

在 **[Android SDK install path]\tools** 目录中，运行 **android.bat** 应用程序。 默认情况下，Xamarin.Android 安装程序会将 Android SDK 放置在 Windows 计算机上的以下位置中：

    C:\Users\[username]\AppData\Local\Android\android-sdk


### <a name="download-the-usb-drivers"></a>下载 USB 驱动程序

Google Nexus 设备（Galaxy Nexus 除外）需要 Google USB 驱动程序。 Galaxy Nexus 的驱动程序[由 Samsung 分发](http://www.samsung.com/us/support/downloads/)。
所有其他 Android 设备应使用[来自其各自制造商的 USB 驱动程序](https://developer.android.com/tools/extras/oem-usb.html#Drivers)。

通过启动 Android SDK 管理器并展开“附加程序”文件夹，安装 **Google USB 驱动程序**包，如下面的屏幕截图所示：

[![选择的 Google USB 驱动程序包](set-up-device-for-development-images/usbdriverpackage.png)](set-up-device-for-development-images/usbdriverpackage.png#lightbox)

选中“Google USB 驱动程序”框，然后单击“安装”按钮。
驱动程序文件将下载到以下位置：

    [Android SDK install path]\extras\google\usb\_driver

Xamarin.Android 安装的默认路径为：

    C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver



### <a name="installing-the-usb-driver"></a>安装 USB 驱动程序

USB 驱动程序下载完成后，请将其安装。
在 Windows 7 上安装驱动程序：

1.  通过 USB 线，将设备连接到计算机。

2.  从桌面或 Windows 资源管理器，右键单击“计算机”，然后选择“管理”。

3.  在左窗格中，选择“设备”。

4.  在右窗格中，找到并展开“其它设备”。

5.  右键单击设备名，并选择“更新驱动程序软件”。
    这将启动硬件更新向导。

6.  选择“浏览计算机以查找驱动程序软件”，然后单击“下一步”。

7.  单击“浏览”，找到 USB 驱动程序文件夹（Google USB 驱动程序位于 **[Android SDK install path]\extras\google\usb_driver**）。

8.  单击“下一步”安装驱动程序。


### <a name="installing-unverified-drivers-in-windows-8"></a>在 Windows 8 中安装未经验证的驱动程序

若要在 Windows 8 中安装未经验证的驱动程序，可能需要执行额外步骤。 以下步骤介绍如何安装 Galaxy Nexus 的驱动程序：

1.  **访问 Windows 8 高级启动选项** - 此步骤包括重启计算机以访问高级启动选项。 通过使用以下命令，启动命令行提示符和重启计算机：

        shutdown.exe /r /o

2.  **连接设备** - 将设备连接到计算机

3.  **启动设备管理器** -运行 **devmgmt.msc**；应可看到你的设备已列出，其上有一个黄色的三角形。

4.  **安装设备驱动程序** - 安装设备驱动程序，如上所述。



## <a name="connect-the-device-to-the-computer"></a>将设备连接到计算机

最后一步是将设备连接到计算机。 有两种方法可以实现此目的：

-   **USB 线** -这是最简单、最常见的方式。 只需将 USB 线插入设备，然后插入计算机。

-   **WiFi** - 可以通过 WiFi 将 Android 设备连接到计算机，无需使用 USB 线。 使用此技术需要一些操作，但在没有 USB 线或没有用于设备的足够长的 USB 线的情况，此技术会有帮助。 下一部分中将介绍如何通过 WiFi 连接。


### <a name="connecting-over-wifi"></a>通过 WiFi 连接

默认情况下，[Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) 配置为通过 USB 与 Android 设备进行通信。 可将其重新配置为使用 TCP/IP，而不是使用 USB。 为此，设备和计算机必须处于同一 WiFi 网络上。 若要通过 WiFi 设置调试环境，请从命令行执行以下步骤：

1.  确定 Android 设备的 IP 地址。 查找 IP 地址的一种方法是在“设置”>“Wi-Fi”下查看，然后点击设备所连接的 WiFi 网络。 此时会弹出设置屏幕，显示网络连接的相关信息，类似下面的屏幕截图中所示：

    ![IP 地址](set-up-device-for-development-images/ip-settings.png)

    某些版本的 Android 上不会列出 IP 地址，但可在“设置”>“关于手机”>“状态”下找到 IP 地址。

2.  通过 USB 将 Android 设备连接到计算机。

3.  接下来，重启 ADB，以便可在端口 5555 上使用 TCP。 在命令提示符处，键入以下命令：

        adb tcpip 5555

    发出此命令后，计算机不能侦听通过 USB 连接的设备。

4.  断开将设备连接到计算机的 USB 线连接。

5.  配置 ADB，使其可在上面步骤 1 中指定的端口上连接 Android 设备：

        adb connect 192.168.1.28:5555

    此命令完成后，Android 设备即可通过 WiFi 连接到计算机。

通过 WiFi 完成调试后，可通过以下命令将 ADB 重置回 USB 模式：

    adb usb

可要求 ADB 列出连接到计算机的设备。 无论设备通过何种方式连接，可在命令提示符发出以下命令，查看连接的设备：

    adb devices


## <a name="summary"></a>总结

本文介绍如何通过在设备上启用调试，配置用于开发的 Android 设备。 还介绍了如何使用 USB 或 WiFi 将设备连接到计算机。


## <a name="related-links"></a>相关链接

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [使用硬件设备](https://developer.android.com/tools/device.html)
- [Samsung 驱动程序下载](http://www.samsung.com/us/support/downloads/)
- [OEM USB 驱动程序](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Google USB 驱动程序](https://developer.android.com/sdk/win-usb.html)
- [XDA 开发人员：已解决 Windows 8 - ADB/快速启动驱动程序问题](http://forum.xda-developers.com/showthread.php?t=1583801)
