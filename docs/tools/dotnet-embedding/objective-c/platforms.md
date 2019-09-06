---
title: 目标-C 平台
description: 本文档介绍了在使用客观 C 代码时，.NET 嵌入可面向的各种平台。 它讨论了 macOS、iOS、tvOS 和 watchOS。
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: f97b595f129cb1ad1ea56e3ae43b0f0a477fef5a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282733"
---
# <a name="objective-c-platforms"></a>目标-C 平台

生成目标 C 代码时，.NET 嵌入可以面向各种平台：

* macOS
* iOS
* tvOS
* watchOS [尚未实现]

通过将`--platform=<platform>`命令行自变量传递给 .net 嵌入来选择平台。

为 iOS、tvOS 和 watchOS 平台进行生成时，.NET 嵌入将始终创建一个嵌入 Xamarin 的框架，因为 Xamarin 包含许多运行时支持代码，这些代码在这些平台上是必需的。

但是，在为 macOS 平台生成时，可以选择生成的框架是否应嵌入 Xamarin。 如果绑定的程序集不引用 xamarin .dll （直接或间接），则可以不嵌入 xamarin，这是通过传递`--platform=macOS`到 .net 嵌入工具来选择的。

如果绑定程序集包含对 Xamarin .dll 的引用，则必须嵌入 Xamarin，另外，embeddinator 必须知道要使用的目标框架。

有三种可能的 Xamarin `modern`目标框架：（以前称为`mobile`）、 `full`和`system` （在 Xamarin 的[目标框架][1]文档中介绍了二者之间的差异），每个通过将`--platform=macOS-modern` `--platform=macOS-full`或传递到.net嵌入工具来选择。`--platform=macOS-system`

[1]: ~/mac/platform/target-framework.md
