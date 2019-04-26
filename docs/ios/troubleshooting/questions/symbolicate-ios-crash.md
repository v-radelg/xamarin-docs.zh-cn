---
title: 在哪里可以找到符号化 iOS 崩溃日志的 .dSYM 文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/09/2018
ms.openlocfilehash: 0b8f3aa736cba6e70fbf346766499c23a9bbe270
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61418200"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪里可以找到符号化 iOS 崩溃日志的 .dSYM 文件？

在生成 iOS 应用程序使用 Visual Studio for Mac 或 Visual Studio 2017 时，所需标志崩溃报告的.dSYM 文件将位于应用的项目文件 (.csproj) 所在的目录层次结构。 确切位置取决于你的项目的生成设置：

- 如果已启用特定于设备的生成，可以在以下目录中找到.dSYM:

    **&lt;project directory&gt;/bin/&lt;platform&gt;/&lt;configuration&gt;/device-builds/&lt;device&gt;-&lt;os-version&gt;/**

    例如：
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- 如果尚未启用特定于设备的生成，可以在以下目录中找到.dSYM:

    **&lt;项目目录&gt;/bin/&lt;平台&gt;/&lt;配置&gt;/**

    例如：

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> 作为生成过程的一部分，Visual Studio 2017 的.dSYM 文件复制从 Windows 到 Mac 生成主机。 如果看不到 Windows 上的.dSYM 文件，请确保已配置为应用程序的生成设置[创建.ipa 文件](~/ios/deploy-test/app-distribution/ipa-support.md)。

## <a name="see-also"></a>请参阅

- [Symbolicating iOS 崩溃文件 (Xamarin.iOS)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [揭秘 iOS 应用程序崩溃日志](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

