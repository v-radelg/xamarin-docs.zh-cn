---
title: 客观 Sharpie 工具 & 命令
description: 本文档概述了客观 Sharpie 附带的工具，以及要与它们一起使用的命令行参数。
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: davidortinau
ms.author: daortin
ms.date: 10/05/2015
ms.openlocfilehash: 2179154aa6dc78a8b0b6b418d780e7996f8e557d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015926"
---
# <a name="objective-sharpie-tools--commands"></a>客观 Sharpie 工具 & 命令

_了解目标 Sharpie 中包含的工具，以及用于使用这些工具的命令行参数。_

成功[安装](~/cross-platform/macios/binding/objective-sharpie/get-started.md)目标 Sharpie 后，请打开一个终端，熟悉 Sharpie 必须提供的*命令*：

```
$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

客观 Sharpie 提供以下工具：

|工具|描述|
|--- |--- |
|**xcode**|提供有关当前 Xcode 安装以及可用的 iOS 和 Mac Sdk 版本的信息。 稍后我们将在生成绑定时使用此信息。|
|**盒**|搜索、配置、安装（在本地目录中），并绑定主规范存储库中可用的[CocoaPod](https://cocoapods.org/)库。 此工具将计算已安装的 CocoaPod，以自动推导出正确输入以传递到下面的 `bind` 工具。 3\.0 中的新增项！|
|**bind**|将 ApiDefinition.cs 库中的头文件（`*.h`）分析为初始的[和 StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)文件。|
|**update**|检查目标 Sharpie 和下载的更新版本，并启动安装程序（如果有）。|
|**验证-文档**|显示有关 `[Verify]` 属性的详细信息。|
|**docs**|在默认 web 浏览器中导航到此文档。|

若要获取特定目标 Sharpie 工具的帮助，请输入工具的名称和 `-help` 选项。 例如，`sharpie xcode -help` 返回以下输出：

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

在可以开始绑定过程之前，需要通过在终端 `sharpie xcode -sdks`中输入以下命令来获取有关当前已安装的 Sdk 的信息。 根据您安装的 Xcode 的版本，输出可能会有所不同。 客观 Sharpie 查找 `/Applications` 目录下的任何 `Xcode*.app` 中安装的 Sdk：

```
$ sharpie xcode -sdks
sdk: appletvos9.0    arch: arm64
sdk: iphoneos9.1     arch: arm64   armv7
sdk: iphoneos9.0     arch: arm64   armv7
sdk: iphoneos8.4     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: macosx10.10     arch: x86_64  i386
sdk: watchos2.0      arch: armv7
```

在上面的示例中，我们可以看到，我们已在计算机上安装了 `iphoneos9.1` SDK，并且它具有 `arm64` 体系结构支持。 本部分中的所有示例都将使用此值。 利用此信息，我们就可以将目标 C 库标头文件分析为绑定项目的初始 `ApiDefinition.cs` 和 `StructsAndEnums.cs`。
