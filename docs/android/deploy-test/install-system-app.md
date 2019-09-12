---
title: 将 Xamarin.Android 安装为系统应用
description: 本指南将讨论系统应用和用户应用之间的区别，以及如何将 Xamarin.Android 应用程序作为系统应用程序进行安装。 本指南适用于自定义 Android ROM 映像的作者。 它将说明如何创建自定义 ROM。
ms.prod: xamarin
ms.assetid: 0113143B-7D8D-4C4C-B2F5-B966A2E7CE1F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 9ef13816dc1bc11eab0105ec223c2ef98366e4e5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754006"
---
# <a name="installing-xamarinandroid-as-a-system-app"></a>将 Xamarin.Android 安装为系统应用

_本指南将讨论系统应用和用户应用之间的区别，以及如何将 Xamarin.Android 应用作为系统应用安装。本指南适用于自定义 Android ROM 映像的作者。它将说明如何创建自定义 ROM。_

## <a name="system-app"></a>系统应用

自定义 Android ROM 映像的作者或 Android 设备制造商在分发 ROM 或设备时，可能希望将 Xamarin.Android 应用包含为一个系统应用  。 系统应用是被认为对设备运行非常重要或提供自定义 ROM 作者始终希望可用的功能的一款应用。

系统应用安装在“/system/app/”  文件夹中（文件系统的一个只读目录），用户无法将其删除或移动，除非该用户具有根目录访问权限。 与此相反，由用户安装的应用程序（通常是从 Google Play 或通过旁加载应用进行安装）称为“用户应用”  。 用户应用可以被用户删除，并且在许多情况下可以移动到设备上的其他位置（如某类外部存储）。

系统应用的行为与用户应用的完全一致，但具有以下值得注意的例外情况：

- 系统应用可以像正常用户应用  一样进行升级。 但是，因为应用副本一直存在于“/system/app/”  中，该应用始终可以回退到原始版本。

- 系统应用可能被授予对用户应用不可用的某些仅限系统的权限。 仅限系统权限的一个示例是 [`BLUETOOTH_PRIVILEGED`](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)，它允许应用程序与蓝牙设备配对，而无需与任何用户进行交互。

可以将 Xamarin.Android 应用作为系统应用程序分发。 除了向自定义 ROM 提供 APK，还有两个共享库，libmonodroid.so  和 libmonosgen 2.0.so  ，必须将其手动从 APK 复制到 ROM 映像的文件系统。 本指南将说明所涉及的步骤。

## <a name="restrictions"></a>限制

本指南适用于自定义 Android ROM 映像的作者。 它将说明如何创建自定义 ROM。

本指南假定你熟悉[打包适用于 Xamarin.Android 的发布 APK](~/android/deploy-test/publishing/index.md)，并了解 Android 应用程序的 [CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)。

## <a name="install-a-xamarinandroid-app-as-a-system-app"></a>将 Xamarin.Android 应用安装为系统应用

以下步骤介绍如何将 Xamarin.Android 应用作为系统应用进行安装。

1. 打包 Xamarin.Android 应用的发布 APK  &ndash; [发布应用程序](~/android/deploy-test/publishing/index.md)指南对此进行了详述。

2. 从 APK 提取共享库  &ndash; 使用任何 ZIP 实用程序，打开 APK 文件并检查 /lib/  文件夹的内容。 此文件夹的每个应用程序二进制接口  (ABI) 都将有一个受该应用程序支持的子目录；此文件夹内容将包含该特定 ABI 上应用程序所需的所有共享库：

    ![taskypro.zip 的 armeabi-v7a 文件夹中 .so 文件的屏幕截图](install-system-app-images/install-system-app-01.png)

   在上面的屏幕截图中，只有一个支持的 ABI (armeabi-v7a  ) 持有两个应用所需的 .so  文件。 请注意，它只用来提取适用于设备或设备 ROM 目标体系结构的 ABI 文件，也就是说，不将 .so  文件从 x86  文件夹复制到 armeabi-v7a  设备或 ROM。

3. 将 .so 文件复制到 /system/lib  &ndash; 将上一步中从 APK 提取的 .so  文件复制到自定义 ROM 上的 /system/lib/  文件夹中。

4. 将 APK 文件复制到 /system/app &ndash; 最后一步是将 APK 文件复制到 ROM 上的 /system/app 文件夹   。

## <a name="summary"></a>总结

本指南讨论了系统应用  和用户应用  之间的区别，并介绍了如何将 Xamarin.Android 应用作为系统应用安装。

## <a name="related-links"></a>相关链接

- [发布应用程序](~/android/deploy-test/publishing/index.md)
- [CPU 体系结构](~/android/app-fundamentals/cpu-architectures.md)
- [BLUETOOTH_PRIVILEGED](https://developer.android.com/reference/android/Manifest.permission.html#BLUETOOTH_PRIVILEGED)
- [ABI 管理](https://developer.android.com/ndk~/abis.html)
