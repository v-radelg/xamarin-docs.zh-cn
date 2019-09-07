---
title: Xamarin Android 常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: c14c03d4f618644382aa80b5e0e7fc5b7a46fa9b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760881"
---
# <a name="android-frequently-asked-questions"></a>Android 常见问题

## <a name="installation--setup"></a>安装 & 安装程序

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[应安装哪些 Android SDK 包？](install-android-sdk-packages.md)

安装 Android SDK 不会自动包括开发所需的所有最低包。 尽管各个开发人员的需求各不相同，但本指南还讨论了用 Xamarin 进行开发通常需要的包。

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[可以在哪里设置 Android SDK 位置？](android-sdk-location.md)

本指南介绍 Android SDK 的默认设置，该设置适用于大多数设置;以及如何在 Visual Studio for Mac 或 Visual Studio 中更改这些默认值（如果需要）。

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[如何更新 Java Development Kit (JDK) 版本？](update-jdk.md)

本文介绍如何在 Windows 和 Mac 上更新 Java 开发工具包（JDK）版本。

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[能否使用 Java 开发工具包（JDK）版本9或更高版本？](jdk9-errors.md)

Xamarin 需要 JDK 8 或 Microsoft Mobile OpenJDK。 本文列出了在安装 JDK 9 或更高版本时可能会看到的一些常见错误消息，以及检查 JDK 版本的说明。

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[如何手动安装 Xamarin.Android.Support 包所需的 Android 支持库？](install-android-support-library.md)

本指南提供在 Windows & Mac 上`Xamarin.Android.Support.v4`安装支持库的示例步骤。

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[在 Windows 上调试 Android 需要哪些 USB 驱动程序？](android-drivers-debug-windows.md)

在 Windows 中进行开发时在 Android 设备上进行调试;需要安装兼容的 USB 驱动程序。 默认情况下，Android SDK 管理器包含 "Google USB 驱动程序"，它可添加对结点设备的支持。
其他设备需要设备制造商发布的 USB 驱动程序。 本指南提供有关查找这些驱动程序以及备用测试方法的信息。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[是否可以从 Windows VM 连接到在 Mac 上运行的 Android 仿真器？](connect-android-emulator-mac-windows.md)

本指南介绍使用 Android 仿真程序的方法。

## <a name="general-questions"></a>一般问题

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[如何自动化 Android NUnit 测试项目？](automate-android-nunit-test.md)

本指南介绍设置 Android NUnit 测试项目而_不_是 UITest 项目的步骤。 可在[此处](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)找到 UITest 指南。

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[为何我的 Android 发布版本无法连接到 Internet？](android-internet.md)

此问题的最常见原因是： **INTERNET**权限自动包括在调试版本中，但必须为发布版本手动设置。 本指南介绍如何对发布版本启用权限。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[更智能的 Xamarin Android 支持 v4 / v13 NuGet 包](android-support-v4v13-libraries.md)

`Support-v4`和`Support-v13`不能在同一应用中一起使用，也就是说，它们是互斥的。 这是因为`Support-v13`实际包含的所有类型和`Support-v4`实现。 如果尝试同时引用同一项目中的，则会遇到重复的类型错误。

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[如何实现解决 PathTooLongException 错误？](path-too-long-exception.md)

本文介绍如何解决生成 Xamarin Android 项目时可能出现的**PathTooLongException**错误。

## <a name="deprecated"></a>已弃用

> [!NOTE]
> 下面的文章适用于最新版本的 Xamarin 中已解决的问题。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[哪个版本的 Xamarin.Android 添加了 Lollipop 支持？](xa-lollipop.md)

本指南最初是为 Android L 预览版编写的。Xamarin 4.17 添加了 android L 预览版支持 & Xamarin Android 4.20 添加了 Android 棒糖形支持。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - 未找到与给定名称匹配的资源：属性“android:actionModeShareDrawable”](missing-action-mode-share-drawable.md)

如果缺少一些必需的 Android SDK 包，则较早版本的 Xamarin 会出现此错误。

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[调整 Android Designer 的 Java 内存参数](android-designer-java-memory.md)

为 Android 设计器启动`java`进程时使用的默认内存参数可能与某些系统配置不兼容。 从 Xamarin Studio 5.7.2.7 和 Xamarin for Visual Studio 3.9.344 开始，可以基于每个项目自定义这些设置。

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Android Resource.designer.cs 文件不更新](resource-designer-wont-update.md)

Xamarin 5.1 以前损坏的 .csproj 文件中的 bug，通过部分或完全删除 .csproj 文件中的 xml 代码。 这会导致 Android 生成系统（如更新 Android Resource.designer.cs）的重要部分失败。 到7月15日的5.1.4 稳定版本，此 bug 已修复;但在许多情况下，必须手动修复项目文件，如本指南中所述。
