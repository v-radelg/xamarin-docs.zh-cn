---
title: 目标 Sharpie 工具和命令
description: 本文档概述了包含目标 Sharpie 和命令行自变量与其使用的工具。
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 718b5104ddc4593d080b88b062c42d371d9e8e2e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261163"
---
# <a name="objective-sharpie-tools--commands"></a>目标 Sharpie 工具和命令

_包含目标 Sharpie 和命令行参数来使用它们的工具的概述。_

<style type="text/css"> .terminal-blue { color: rgb(10,96,254); } .terminal-green { color: rgb(12,156,26); } .terminal-magenta { color: rgb(152,12,103); } </style>


目标 Sharpie 成功后[安装](~/cross-platform/macios/binding/objective-sharpie/get-started.md)，打开终端并自己应熟悉<em>命令</em>目标 Sharpie 必须提供：

<pre>$ <b>sharpie -help</b>
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
  docs               Open the Objective Sharpie online documentation</pre>

目标 Sharpie 提供以下工具：

|Tool|描述|
|--- |--- |
|**xcode**|提供有关当前 Xcode 安装以及 iOS 和 Mac Sdk 提供的版本信息。 我们将使用此信息更高版本生成我们的绑定时。|
|**pod**|搜索、 配置、 将安装 （在本地目录中），并将绑定 OBJECTIVE-C [CocoaPod](https://cocoapods.org/)规范的主存储库中可用的库。 此工具将评估已安装的 CocoaPod 自动推导出正确的输入要传递给`bind`下列工具。 3.0 中的新增功能 ！|
|**bind**|分析标头文件 (`*.h`) 到初始 Objective C 库中[ApiDefinition.cs 和 StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)文件。|
|**update**|检查目标 Sharpie 的较新版本和下载并启动安装程序，如果有可用。|
|**verify-docs**|显示有关详细的信息`[Verify]`属性。|
|**docs**|导航到默认 web 浏览器中的此文档。|

若要获取特定的目标 Sharpie 工具的帮助，请输入工具的名称和`-help`选项。 例如，`sharpie xcode -help`将返回以下输出：

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

我们可以在绑定过程之前，我们需要通过在终端中输入以下命令获取有关当前已安装 Sdk 的信息`sharpie xcode -sdks`。 你的输出可能会因已安装的 Xcode 版本信息。 目标 Sharpie 寻找 Sdk 安装在任何`Xcode*.app`下`/Applications`目录：

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

综上所述，我们可以看到，我们有`iphoneos9.1`SDK 安装在我们的机器上并且具有`arm64`体系结构支持。 我们将在本部分中的所有示例使用此值。 使用此信息后，我们已准备好将 Objective C 库标头文件分析到初始`ApiDefinition.cs`和`StructsAndEnums.cs`绑定项目。

## <a name="related-links"></a>相关链接

- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)