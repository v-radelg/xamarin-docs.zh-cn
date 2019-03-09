---
title: 是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 35bfdb92ccfffe54f0ca10dc001d8919703a5bd8
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668148"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？

若要连接到从 Windows 虚拟机的 Mac 上运行的 Android 仿真程序，请使用以下步骤：

1.  在 mac 上启动仿真程序

2.  终止`adb`在 Mac 上的服务器：

    ```bash
    adb kill-server
    ```

3.  请注意在仿真程序正在侦听环回网络接口上的 2 个 TCP 端口：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇数端口将用于连接到`adb`。 另请参阅[ https://developer.android.com/tools/devices/emulator.html#emulatornetworking ](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4.  _选项 1_:使用 [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    若要转发的入站 TCP 数据包收到外部在端口 5555 上 （或您喜欢的任何其他端口） 到环回接口上的奇数端口 (**127.0.0.1 5555**在此示例中)，将转发出站数据包重新另一种方法：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要`nc`命令运行保持在终端窗口中，将按预期方式转发数据包。 可以在终端窗口中退出键入控制 C`nc`命令完成后使用仿真程序。

    (选项 1 通常要比容易选项 2，尤其是当**系统首选项 > 安全性和隐私 > 防火墙**已打开。) 

    _选项 2_:使用 [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    若要重定向来自端口的 TCP 数据包`5555`（或您喜欢的任何其他端口） 上[共享网络](http://kb.parallels.com/en/4948)接口的环回接口上的奇数端口 (`127.0.0.1:5555`在此示例中):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令将设置端口转发使用`pf packet filter`系统服务。 换行很重要。 请确保以使它们保持不变时复制粘贴。 您还需要调整接口名称从*vmnet8*如果您使用 Parallels。 `vmnet8` 是特殊名称*NAT 设备*有关*共享网络*VMWare Fusion 中的模式。 在 Parallels 中适当的网络接口很可能[vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5.  从 Windows 计算机连接到模拟器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    替换为"ip-地址---mac"与 Mac 的 IP 地址例如作为由列出`ifconfig vmnet8 | grep 'inet '`。 如果需要将为`5555`与您喜欢第 4 步中的其他端口\. (注意： 一种方法来获取命令行访问权`adb`是通过[**工具 > Android > Android Adb 命令提示符**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) Visual Studio 中。)

### <a name="alternate-technique-using-ssh"></a>备用技术使用 `ssh`

如果已启用_远程登录名_在 Mac 上则可以使用`ssh`端口转发以连接到模拟器。

1.  在 Windows 上安装 SSH 客户端。 一种方法是安装[Git 的 Windows](https://git-for-windows.github.io/)。 `ssh`命令将推出**Git Bash**命令提示符。

2.  按照步骤 1-3 上述若要启动仿真程序，请终止`adb`在 Mac 上的服务器和识别的仿真程序端口。

3.  运行`ssh`若要设置 Windows 上的本地端口之间的双向端口转发的 Windows 上 (`localhost:15555`在此示例中) 和 Mac 的环回接口上的奇数的仿真程序端口 (`127.0.0.1:5555`在此示例中):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    替换`mac-username`与你的 Mac 用户名列出的`whoami`。 替换为`ip-address-of-the-mac`mac 的 IP 地址

4.  连接到仿真程序在 Windows 上使用的本地端口：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (注意： 一种简单方法获取命令行访问权`adb`是通过[**工具 > Android > Android Adb 命令提示符**在 Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。)

小小的警告： 如果使用端口`5555`对于本地端口`adb`会想在 Windows 上本地运行仿真程序。 这不会时出现问题导致在 Visual Studio 中，但在 Visual Studio for Mac 会导致应用启动后立即退出。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>一种替代方式使用`adb -H`尚不支持

从理论上讲，另一种方法是使用`adb`的内置功能，可连接到`adb`远程计算机上运行的服务器 (请参阅示例[ https://stackoverflow.com/a/18551325 ](https://stackoverflow.com/a/18551325))。
但 Xamarin.Android IDE 扩展当前不提供某种方法来配置该选项。

## <a name="contact-information"></a>联系信息

本文档讨论截至 2016 年 3 月的当前行为。 本文档中所述的技术不是 xamarin，稳定的测试套件的一部分，因此它在将来可能会损坏。

如果您注意到，该技术将不再适用，或者如果您注意到文档中的任何其他错误，随意添加下面的论坛主题的讨论： [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
谢谢!

