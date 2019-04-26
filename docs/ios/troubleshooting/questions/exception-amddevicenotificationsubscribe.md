---
title: System.Exception AMDeviceNotificationSubscribe 已返回...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4fb0712366422e8810a2db60d40c3b85d9f4cd82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421939"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe 已返回...

> [!IMPORTANT]
> 最新版本的 Xamarin 中已解决此问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。


## <a name="fix"></a>修补程序

1.  终止`usbmuxd`处理，以便系统将重新启动它：

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  如果这样不会解决问题，重启 mac。

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

此消息可以出现在错误对话框，在首次启动 Visual Studio for Mac 中，或在`mtbserver.log`Xamarin.iOS 生成主机应用程序中的文件 (**Xamarin.iOS 生成主机 > 查看生成主机日志**)。

请注意，这是不常见的问题。 如果 Visual Studio 无法连接到 Mac 生成主机，还有更有可能出现在其他错误`mtbserver.log`文件。

### <a name="errors-in-systemlog"></a>System.log 中的错误

在某些情况下以下两个错误消息可能也会显示重复在`/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>其他信息

一个猜测该错误的根本原因是在 OS X 系统服务负责报告 iOS 设备和模拟器信息可以在极少数情况下，输入意外的状态。 Xamarin 不能与此状态中的系统服务正确交互。 重启计算机重新启动系统服务，解决了问题。

基于出现的错误`system.log`它将出现此问题可能与 Bonjour (`mDNSResponder`)。 不同的 WiFi 网络之间的更改似乎会增加的问题的可能性。

## <a name="references"></a>参考资料

*   [Bug 11789-MonoTouch.MobileDevice.MobileDeviceException:Amdevicenotificationsubscribe 已返回：0XE8000063 [解析 NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
