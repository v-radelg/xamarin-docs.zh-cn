---
title: System.Exception AMDeviceNotificationSubscribe 已返回...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: d834c06c5fa5ee55e5e3b91bd16b5b4d326c42ee
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528170"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe 已返回...

> [!IMPORTANT]
> 此问题已在最新版本的 Xamarin 中解决。 但是, 如果该软件的最新版本发生问题, 请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。


## <a name="fix"></a>修补程序

1. `usbmuxd`终止进程, 使系统重新启动它:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2. 如果这不能解决问题, 请重新启动 Mac。

## <a name="error-message"></a>错误消息

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

首次启动 Visual Studio for Mac 时, 或在`mtbserver.log` xamarin 生成主机应用 (xamarin 生成主机 **> 查看生成主机日志**) 的文件中, 此消息可能出现在错误对话框中。

请注意, 这是一个不常见的问题。 如果 Visual Studio 在连接到 Mac 生成主机时遇到问题, 则会有其他错误, 更有可能出现在`mtbserver.log`文件中。

### <a name="errors-in-systemlog"></a>系统日志中的错误

在某些情况下, 以下两个错误消息可能还会`/var/log/system.log`在中重复出现:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>其他信息

其中一次推测错误的根本原因是, 在极少数情况下, 负责报告 iOS 设备和模拟器信息的 OS X 系统服务进入意外状态。 在此状态下, Xamarin 无法与系统服务正确交互。 重新启动计算机将重新启动系统服务并解决该问题。

根据出现的错误`system.log` , 此问题可能与 Bonjour (`mDNSResponder`) 相关。 在不同的 WiFi 网络之间进行更改似乎会增加出现问题的几率。

## <a name="references"></a>参考资料

* [Bug 11789-Monotouch.dialog. MobileDevice. MobileDeviceException:AMDeviceNotificationSubscribe 返回:0xe8000063 [已解析 NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
