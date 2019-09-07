---
title: 在 Windows 上调试 Android 需要哪些 USB 驱动程序？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8be3f1b8803aa7e052ebc89af51dad3b659f95f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757333"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>在 Windows 上调试 Android 需要哪些 USB 驱动程序？

## <a name="finding-usb-drivers"></a>查找 USB 驱动程序

在 Windows 中进行开发时在 Android 设备上进行调试;需要安装兼容的 USB 驱动程序。 默认情况下，Android SDK 管理器包含 "Google USB 驱动程序"，它添加了对站点设备的支持，如下所述：[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

其他设备需要由设备制造商专门发布的 USB 驱动程序。 本指南中包括了最常见制造商的一些链接：[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>替代项

根据 manfacturer，可能很难跟踪所需的确切 USB 驱动程序。 用于测试 Windows 中开发的 Android 应用的一些替代项，包括使用 Android 模拟器或使用外部测试服务。 其中包括：

- [App Center 测试](https://docs.microsoft.com/appcenter/test-cloud/)-云测试服务在数百个真实的 Android 设备上运行。

- [适用于 Android 的 Visual Studio 模拟器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [在 Android Emulator 上调试](~/android/deploy-test/debugging/debug-on-emulator.md)
