---
title: Xamarin.Forms 要求
description: Xamarin.Forms 平台和开发系统要求。
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
ms.openlocfilehash: 46a72534fba7a45323a82ad121e5844410472812
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72584348"
---
# <a name="xamarinforms-requirements"></a>Xamarin.Forms 要求

_Xamarin.Forms 平台和开发系统要求。_

请参阅[安装](installation/index.md)一文中跨平台应用的安装和设置概述。

## <a name="target-platforms"></a>目标平台

可对以下操作系统编写 Xamarin.Forms 应用程序：

- iOS 9 或更高版本
- Android 4.4 (API 19) 或更高版本（[详细信息](#android)）
- Windows 10 通用 Windows 平台（[详细信息](#windows10)）

但建议使用 Android 5.0 （API 21）作为最小 API。 这可确保与所有 Android 支持库完全兼容，同时仍以大多数 Android 设备为目标。

假定开发人员熟悉[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)。

### <a name="additional-platform-support"></a>其他平台支持

这些平台状态可在 [Xamarin.Forms GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support) 上找到：

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="android"></a>Android

应安装最新的 Android SDK 工具和 Android API 平台。 可使用 [Android SDK 管理器](~/android/get-started/installation/android-sdk.md)更新到最新版本。

此外，Android 项目的目标/编译版本必须设置为“使用最新安装的平台”。 但是，最低版本可设置为 API 19，因此可继续支持使用 Android 4.4（以及更高版本）的设备。 在“项目选项”中可设置这些值：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

“项目选项”>“应用程序”>“应用程序属性”

![Visual Studio 中的 Android 生成选项](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

“生成”>“常规”

![选择最新的目标框架](requirements-images/options-general-sml.png)

“生成”>“Android 应用程序”

![选择应用的最低和目标 Android 版本](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>开发系统要求

可在 macOS 和 Windows 上开发 Xamarin.Forms 应用。 但是，生成 Windows 版本的应用需要使用 Windows 和 Visual Studio。

## <a name="mac-system-requirements"></a>Mac 系统要求

你可以使用 Visual Studio for Mac 在 macOS 高塞拉利昂（10.13）或更高版本上开发 Xamarin。 若要开发 iOS 应用，建议使用最新版本的 Xcode、iOS 和 macOS。 有关特定版本要求，请参阅最新的[Xamarin 发行说明](/xamarin/ios/release-notes/)。

> [!NOTE]
> 不能在 macOS 上开发 Windows 应用。

## <a name="windows-system-requirements"></a>Windows 系统要求

任何支持 Xamarin 开发的 Windows 安装上都可生成适用于 iOS 和 Android 的 Xamarin.Forms 应用。 若要完全支持当前平台功能，请使用最新版本的 Visual Studio。 

使用最新版本的 Xcode 和 Apple 指定的最低 macOS 版本的 iOS 开发需要使用联网的 Mac。

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

为 UWP 开发 Xamarin.Forms 应用需要：

- Windows 10 （建议使用最新版本，秋季创建者更新）

- 建议使用 Visual Studio 2019

- [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

可以在现有的 Xamarin.Forms 解决方案中随时[添加通用 Windows 平台 (UWP) 应用](~/xamarin-forms/platform/windows/installation/index.md)。

## <a name="deprecated-platforms"></a>弃用的平台

使用 Xamarin 3.0 或更高版本时，不支持这些平台：

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
