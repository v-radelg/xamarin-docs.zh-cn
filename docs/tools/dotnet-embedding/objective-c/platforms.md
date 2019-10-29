---
title: 目标-C 平台
description: 本文档介绍了在使用客观 C 代码时，.NET 嵌入可面向的各种平台。 它讨论了 macOS、iOS、tvOS 和 watchOS。
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: dbc2c40e06f14ec636b4aa4cc11fc4f2d230ee6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006427"
---
# <a name="objective-c-platforms"></a>目标-C 平台

生成目标 C 代码时，.NET 嵌入可以面向各种平台：

* macOS
* iOS
* tvOS
* watchOS [尚未实现]

通过向 .NET 嵌入传递 `--platform=<platform>` 命令行参数来选择平台。

为 iOS、tvOS 和 watchOS 平台进行生成时，.NET 嵌入将始终创建一个嵌入 Xamarin 的框架，因为 Xamarin 包含许多运行时支持代码，这些代码在这些平台上是必需的。

但是，在为 macOS 平台生成时，可以选择生成的框架是否应嵌入 Xamarin。 如果绑定的程序集不引用 Xamarin .dll （直接或间接），则可以不嵌入 Xamarin，这是通过将 `--platform=macOS` 传递到 .NET 嵌入工具来选择的。

如果绑定程序集包含对 Xamarin .dll 的引用，则必须嵌入 Xamarin，另外，embeddinator 必须知道要使用的目标框架。

有三种可能的 Xamarin 目标框架： `modern` （以前称为 `mobile`）、`full` 和 `system` （Xamarin 的[目标框架][1]文档中描述了每个内容之间的差异），并通过传递`--platform=macOS-modern`、`--platform=macOS-full` 或 `--platform=macOS-system` 到 .NET 嵌入工具。

[1]: ~/mac/platform/target-framework.md
