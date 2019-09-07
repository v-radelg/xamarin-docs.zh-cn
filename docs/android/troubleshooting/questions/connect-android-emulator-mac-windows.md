---
title: 是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: af32f4af3951eff3b8b5412908e35c4cdef09ae4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757261"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？

若要从 Windows 虚拟机连接到 Mac 上运行的 Android Emulator，请执行以下步骤：

1. 在 Mac 上启动仿真程序。

2. 在 Mac 上终止服务器：`adb`

    ```bash
    adb kill-server
    ```

3. 请注意，模拟器在环回网络接口上侦听2个 TCP 端口：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇数端口是用于连接到`adb`的端口。 另请参阅。 [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)

4. _选项 1_：用法[`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    将从端口5555（或所需的任何其他端口）外部接收的入站 TCP 数据包转发到环回接口（在本例中为**127.0.0.1 5555** ）上的奇数端口，并将出站数据包反向转发：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    `nc`只要命令一直在终端窗口中运行，数据包就会按预期方式转发。 使用模拟器完成后，可以在终端窗口中键入 Control C `nc`来退出命令。

    （选项1通常比选项2更简单，尤其是在**系统首选项 > 安全性 & 隐私 > 防火墙**。） 

    _选项 2_：用法[`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    若要将 TCP 数据包从`5555`端口（或所需的任何其他端口）重定向到环回接口（`127.0.0.1:5555`在本示例中为）上的奇数端口，请在[共享网络](http://kb.parallels.com/en/4948)接口上重定向到该端口：

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令使用`pf packet filter`系统服务设置端口转发。 换行符非常重要。 复制粘贴时，请确保将其保持不变。 如果使用的是平行线，还需要从*vmnet8*调整接口名称。 `vmnet8`是 VMWare 合成中*共享网络*模式的特殊*NAT 设备*的名称。 类似的网络接口可能[vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5. 从 Windows 计算机连接到模拟器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    将 "ip 地址-mac" 替换为 Mac 的 IP 地址，例如，按`ifconfig vmnet8 | grep 'inet '`列出。 如果需要，请`5555`将替换为你在步骤4中所需的其他端口\. （注意：在 Visual Studio 中通过`adb` [**工具 > android > android Adb 命令提示符**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)获取命令行访问的一种方法。）

### <a name="alternate-technique-using-ssh"></a>使用的替代方法`ssh`

如果已在 Mac 上启用_远程登录_，则可以使用`ssh`端口转发连接到模拟器。

1. 在 Windows 上安装 SSH 客户端。 一种选择是安装[适用于 Windows 的 Git](https://git-for-windows.github.io/)。 然后，该 命令将在GitBash命令提示符下`ssh`可用。

2. 按照上述步骤1-3 启动模拟器，在 Mac 上终止`adb`服务器，并标识模拟器端口。

3. 在`ssh` windows 上运行，在 windows 上的本地端口（`localhost:15555`在此示例中）和 Mac 环回接口（`127.0.0.1:5555`在此示例中为）中的奇数模拟器端口之间设置双向端口转发：

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    将`mac-username`替换为列出的`whoami`Mac 用户名。 将`ip-address-of-the-mac`替换为 Mac 的 IP 地址。

4. 使用 Windows 上的本地端口连接到模拟器：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    （注意：可通过`adb` [Visual Studio 中的**工具 > android > android Adb 命令提示符**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)获取命令行访问权限的一种简单方法。）

请注意：如果对本地端口使用`5555`端口， `adb`则会认为模拟器在 Windows 上本地运行。 这不会导致 Visual Studio 中出现任何问题，但在 Visual Studio for Mac 会导致应用程序在启动后立即退出。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>尚不支持`adb -H`使用的替代方法

理论上，另一种方法是使用`adb`内置功能连接`adb`到在远程计算机上运行的服务器（请参阅示例[https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)）。
但 Xamarin IDE 扩展目前不提供配置该选项的方法。

## <a name="contact-information"></a>联系信息

本文档讨论了2016年3月的当前行为。 本文档中所述的技术不是适用于 Xamarin 的稳定测试套件的一部分，因此可能会在将来中断。

如果您注意到该方法不再有效，或者您注意到了文档中的任何其他错误，请在下面的论坛线索上随意添加讨论： [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
谢谢!
