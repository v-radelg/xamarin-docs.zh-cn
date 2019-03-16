---
title: '安装和设置 Wear OS onXamarin.Android '
description: 本文将指导完成安装步骤和准备你的计算机和设备的 Android Wear 开发所需的配置详细信息。 本文结束时，您将有一个有效的 Xamarin.Android 穿戴设备安装到 Visual Studio 中集成 Mac 和/或 Microsoft Visual Studio 中，并且就可以准备好开始构建您的第一个 Xamarin.Android 穿戴设备应用程序。
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 96fd6d32f37dd90422f05caf33cfda9a65683fd2
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071029"
---
# <a name="setup-and-installation"></a>设置和安装

_本文将指导完成安装步骤和准备你的计算机和设备的 Android Wear 开发所需的配置详细信息。本文结束时，您将有一个有效的 Xamarin.Android 穿戴设备安装到 Visual Studio 中集成 Mac 和/或 Microsoft Visual Studio 中，并且就可以准备好开始构建您的第一个 Xamarin.Android 穿戴设备应用程序。_

## <a name="requirements"></a>要求

以下被所创建的基于 Xamarin 的 Android Wear 应用：

-   **Visual Studio 或 Visual Studio for Mac** &ndash; Visual Studio 2017 Community 或更高版本。

-   **Xamarin.Android** &ndash; Xamarin.Android 4.17 或更高版本必须安装并配置与 Visual Studio 或 Visual Studio for mac。

-   **Android SDK** -Android SDK 5.0.1 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin Android 开发需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)如果您是开发的 API 级别 24 或更高版本 （JDK 1.8 还支持 API 级别低于 24）。

你可以继续使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是开发专门针对 API 级别 23 或更早版本。

> [!IMPORTANT]
> Xamarin.Android 不支持 JDK 9。

## <a name="installation"></a>安装

安装 Xamarin.Android 后，执行以下步骤，以便你可以生成和测试 Android Wear 应用： 

1.  安装所需的 Android SDK 和工具。
2.  配置测试设备。
3.  创建第一个 Android Wear 应用。

以下各节中介绍这些步骤。


### <a name="install-android-sdk-and-tools"></a>安装 Android SDK 和工具 

启动**Android SDK 管理器**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![如何启动 Visual Studio 中的 Android SDK 管理器](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![如何启动 Android SDK 管理器在 Visual Studio for Mac](installation-images/xs/sdk-menu.png)

-----


请确保您有以下 Android SDK 和安装工具：

* Android SDK Tools v 24.0.0 或更高版本，并
* Android 4.4W (API20)，或
* Android 5.0.1 (API21) 或更高版本。

如果没有最新 SDK 和安装工具，下载所需的 SDK 工具*并*API 位 (可能需要进行滚动一点，找到它们&ndash;API 选择如下所示): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![启用 Android 5.0.1 的示例 SDK 管理器屏幕截图组件](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![启用 Android 4.4 和 5.0.1 示例 SDK 管理器屏幕截图组件](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>配置

可以使用之前测试你的应用，必须配置 Android Wear 仿真器或实际的 Android Wear 设备。 


### <a name="android-wear-emulator"></a>Android 穿戴设备仿真程序

可以使用 Android Wear 仿真程序之前，必须配置 Android Wear Android 虚拟设备 (AVD) 使用**Google 仿真器管理器**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![如何启动 Android 仿真器管理器从 Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![如何启动 Android 仿真器管理器从 Visual Studio for Mac](installation-images/xs/emulator-menu.png)

-----

有关设置 Android Wear 仿真程序的详细信息，请参阅[仿真器上调试 Android Wear](~/android/wear/deploy-test/debug-on-emulator.md)。


### <a name="android-wear-device"></a>Android 穿戴设备

如果具有 Android Wear Smartwatch 如 Android Wear 设备，则可以调试而不是使用仿真程序此设备上的应用。 有关使用穿戴设备进行开发的信息，请参阅[穿戴设备的设备上进行调试](~/android/wear/deploy-test/debug-on-device.md)。


## <a name="create-your-first-android-wear-app"></a>创建第一个 Android Wear 应用

请按照[你好，穿戴设备](~/android/wear/get-started/hello-wear.md)生成首个监视应用的说明。


## <a name="packaging-your-app"></a>打包应用程序

Android 穿戴设备应用程序始终使用辅助 Android 手机应用程序分发。 

作为您主要的 Android 应用程序的引用添加你的 Android Wear 应用程序时它会自动被假定为 Android Wear 的项目，并将为您生成所有必需的 XML 和元数据。 此外，它将验证包和版本号，因此，您可以轻松地提供您的应用程序，到 Google Play 与匹配。 

若要了解有关打包 Wear 应用的详细信息，请参阅[使用打包](~/android/wear/deploy-test/packaging.md)。


## <a name="related-links"></a>相关链接

- [SkeletonWear （示例）](https://developer.xamarin.com/samples/SkeletonWear/)
