---
title: 在哪里可以找到符号化 iOS 崩溃日志的 .dSYM 文件？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: 418a0196849099da03983085aca9ceed2077207b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030922"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>在哪里可以找到符号化 iOS 崩溃日志的 .dSYM 文件？

使用 Visual Studio for Mac 或 Visual Studio 2017 生成 iOS 应用时，标志崩溃报告所需的 dSYM 文件将与应用项目文件（.csproj）位于同一目录层次结构中。 具体位置取决于项目的生成设置：

- 如果已启用特定于设备的生成，则可在以下目录中找到 dSYM：

    **&lt;项目目录&gt;/bin/&lt;平台&gt;/&lt;&gt;&lt;&gt;-&lt;&gt;/**

    例如:
  
    **TestApp/bin/iPhone/Release/device-build/iphone 8.4-11.3.1/**

- 如果尚未启用特定于设备的生成，则可在以下目录中找到 dSYM：

    **&lt;项目目录&gt;/bin/&lt;平台&gt;/&lt;配置&gt;/**

    例如:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> 作为生成过程的一部分，Visual Studio 2017 将 dSYM 文件从 Mac 生成主机复制到 Windows。 如果在 Windows 上看不到 dSYM 文件，请确保已将应用的生成设置配置为[创建 ipa 文件](~/ios/deploy-test/app-distribution/ipa-support.md)。

## <a name="see-also"></a>请参阅

- [Symbolicating iOS 崩溃文件（Xamarin）](https://www.jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [揭密 iOS 应用程序崩溃日志](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
