---
title: 独立发布
ms.prod: xamarin
ms.assetid: 6FB4DEF2-01AD-C5FE-0950-CE1BF088A9C6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: dee49d556173ec0b9544820f129eef8a9cce404f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021311"
---
# <a name="publishing-independently"></a>独立发布

可在不使用任何现有 Android 市场的情况下发布应用程序。 本部分将介绍其他发布方法和 Xamarin.Android 的许可级别。

## <a name="xamarin-licensing"></a>Xamarin 许可

可使用多个许可证来开发、部署和分发 Xamarin.Android 应用：

- Visual Studio Community &ndash; 面向使用 Windows 的学生、小型团队和 OSS 开发者  。

- **Visual Studio Professional** &ndash; 面向独立开发者或小型团队（仅限 Windows）。 该许可证提供标准订阅或云订阅，并且没有使用限制。

- **Visual Studio Enterprise** &ndash; 面向任何规模的团队（仅限 Windows）。 此许可包括企业功能（标准或云订阅）。

若要下载社区版或深入了解如何购买 Professional 版和 Enterprise 版，请访问 [visualstudio.com](https://visualstudio.microsoft.com/xamarin/)。

## <a name="allow-installation-from-unknown-sources"></a>允许来自未知源的安装

默认情况下，Android 仅允许用户下载和安装来自 Google Play 的应用程序。 若要允许来自非应用商店的安装，用户必须先在设备上启用未知源  设置，才可尝试安装应用程序。 可通过“设置”>“安全”  找到此设置，如下图所示：

[![安全设置屏幕](publishing-independently-images/settings.png)](publishing-independently-images/settings.png#lightbox)

> [!IMPORTANT]
> 无论是否启用此设置，某些网络提供商可能都会阻止安装来自未知源的应用程序。

## <a name="publishing-by-e-mail"></a>通过电子邮件发布

若要向用户分发应用程序，一种简单快捷的方式是将发布 APK 附加到电子邮件。 用户在 Android 设备上打开电子邮件时，Android 将识别 APK 附件并显示“安装”  按钮，如下图所示：

[![附件安装按钮](publishing-independently-images/publishing-via-email.png)](publishing-independently-images/publishing-via-email.png#lightbox)

尽管通过电子邮件进行分发非常简单，但其在防止盗版或未授权分发方面提供的保护较少。 因此，最好在很少用户接收该应用程序且其肯定不会分发应用程序的情况下采用此方式。

## <a name="publishing-by-web"></a>通过 Web 发布

可通过 Web 服务器分发应用程序。 为此，需将应用程序上传到 Web 服务器，然后向用户提供下载链接。 Android 设备浏览到链接并下载应用程序时，该程序将在下载完成后自动安装。

## <a name="manually-installing-an-apk"></a>手动安装 APK

手动安装是安装应用程序的第 3 个选择。 若要手动安装应用程序：

1. **向用户分发 APK 副本** &ndash; 例如可通过 CD 或 U 盘分发此副本。
1. **（用户）在 Android 设备上安装应用程序** &ndash; 使用命令行 *Android Debug Bridge* (**adb**) 工具。 **adb** 是一种多功能的命令行工具，支持与仿真器实例或 Android 设备进行通信。 Android SDK 内附有 adb；其位于 \<sdk>/platform-tools/ 目录中   。

Android 设备必须使用 USB 接口线连接到计算机。
Windows 计算机可能还需要电话供应商提供额外的 USB 驱动程序，使其能够被 adb 识别  。 本文未介绍其他 USB 驱动程序的安装说明。

发出任何 **adb** 命令之前，有必要了解连接了哪些仿真器实例或设备（如有）。 可使用 `devices` 命令查看附加对象的列表，如以下代码段所示：

```shell
$ adb devices
List of devices attached
        0149B2EC03012005device
```

确认已连接设备后，可通过 **adb** 发出 `install` 命令安装应用程序：

```shell
$ adb install <path-to-apk>
```

以下代码段演示了将应用程序安装到已连接设备：

```shell
$ adb install helloworld.apk
3772 KB/s (3013594 bytes in 0.780s)
        pkg: /data/local/tmp/helloworld.apk
Success
```

如已安装应用程序，则 `adb install` 无法安装 APK 并会报告错误，如下例所示：

```shell
$ adb install helloworld.apk
4037 KB/s (3013594 bytes in 0.728s)
        pkg: /data/local/tmp/helloworld.apk
Failure [INSTALL_FAILED_ALREADY_EXISTS]
```

此时必须从设备中卸载该应用程序。 首先，发出 `adb uninstall` 命令：

```shell
adb uninstall <package_name>
```

以下代码段是一个卸载应用程序的示例：

```shell
$ adb uninstall mono.samples.helloworld
Success
```
