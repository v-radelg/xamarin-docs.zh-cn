---
title: Xamarin.Android 方面的常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 9c0e6d014f27651710bf3b8e713b2bf80322d628
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58796516"
---
# <a name="android-frequently-asked-questions"></a>Android 方面的常见问题

## <a name="installation--setup"></a>安装和设置

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[应安装哪些 Android SDK 包？](install-android-sdk-packages.md)

安装 Android SDK 不会自动包括用于开发的所有最小所需的包。 虽然各开发人员需要不同，本指南讨论了通常会使用 Xamarin.Android 进行开发所需的包。

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[可以在哪里设置 Android SDK 位置？](android-sdk-location.md)

本指南介绍了 Android SDK，其中应适用于大多数组织; 的默认设置以及如何根据需要更改这些默认值在 Visual Studio for Mac 或 Visual Studio。

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[如何更新 Java Development Kit (JDK) 版本？](update-jdk.md)

本文演示了如何更新 Windows 和 mac 上的 Java 开发工具包 (JDK) 版本

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[可以使用 Java 开发工具包 (JDK) 版本 9 或更高版本？](jdk9-errors.md)

Xamarin.Android 需要 JDK 8 或 Microsoft 移动 OpenJDK。 本文列出了可能会看到是否安装 JDK 9 或更高版本，以及有关签入的 JDK 版本说明一些常见错误消息。


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？](install-android-support-library.md)

本指南提供有关安装示例步骤`Xamarin.Android.Support.v4`支持库上 Windows 和 mac。

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[在 Windows 上调试 Android 需要哪些 USB 驱动程序？](android-drivers-debug-windows.md)

若要在 Android 设备上进行调试时开发 Windows;需要安装兼容的 USB 驱动程序。 Android SDK 管理器默认情况下添加了用于 Nexus 设备的支持包括"Google USB 驱动程序"。
其他设备需要发布的设备制造商的 USB 驱动程序。 本指南提供有关查找这些驱动程序还为其他测试方法的信息。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？](connect-android-emulator-mac-windows.md)

本指南介绍使用 Android 仿真程序时的方法。

## <a name="general-questions"></a>一般问题

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[如何自动化 Android NUnit 测试项目？](automate-android-nunit-test.md)

本指南介绍了用于设置 Android NUnit 测试项目，步骤_不_Xamarin.UITest 项目。 找不到 Xamarin.UITest 参考线[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[为何我的 Android 发布版本无法连接到 Internet？](android-internet.md)

此问题的最常见原因是**INTERNET**权限将自动包括在调试版本，但必须手动设置为发布版本。 本指南介绍如何启用发布版本的权限。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[更智能的 Xamarin Android 支持 v4 / v13 NuGet 包](android-support-v4v13-libraries.md)

`Support-v4` 和`Support-v13`不能使用一起在相同的应用中，即，它们是互相排斥。 这是因为`Support-v13`实际包含的所有类型和实现`Support-v4`。 如果尝试和引用同一项目中，将会遇到重复的类型错误。

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[如何解决 PathTooLongException 错误？](path-too-long-exception.md)

此文章介绍了如何解决**PathTooLongException**生成 Xamarin.Android 项目时可能发生的错误。



## <a name="deprecated"></a>不推荐使用

> [!NOTE]
> 下面的文章适用于 Xamarin 的最新版本中已解决的问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[哪个版本的 Xamarin.Android 添加了 Lollipop 支持？](xa-lollipop.md)

本指南是最初编写 Android L 预览版。Xamarin.Android 4.17 添加 Android L 预览版支持和 Xamarin.Android 4.20 添加了 Android Lollipop 支持。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - 未找到与给定名称匹配的资源：属性“android:actionModeShareDrawable”](missing-action-mode-share-drawable.md)

如果缺少某些所需的 Android SDK 包，在旧版 Xamarin 中可能发生此错误。

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[调整 Android Designer 的 Java 内存参数](android-designer-java-memory.md)

启动时使用的默认内存参数`java`处理有关 Android 设计器可能与某些系统配置不兼容。 开始使用 Xamarin Studio 5.7.2.7 和 Xamarin 的 Visual Studio 3.9.344 这些设置可以在每个项目上自定义。

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Android Resource.designer.cs 文件不更新](resource-designer-wont-update.md)

在 Xamarin.Studio 5.1 中的 bug 以前通过部分或完全删除.csproj 文件中的 xml 代码损坏.csproj 文件。 这将导致重要部分的 Android 生成系统 （例如，更新 Android Resource.designer.cs） 失败。 截至 5.1.4 稳定版本在年 7 月 15 日，已修复此错误;但在许多情况下的项目文件必须按本指南中所述手动修复。



