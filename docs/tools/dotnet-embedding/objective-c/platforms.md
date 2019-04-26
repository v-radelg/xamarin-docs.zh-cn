---
title: Objective C 的平台
description: 本文档介绍了.NET 嵌入使用 Objective C 代码时可以面向各种平台。 它讨论 macOS、 iOS、 tvOS 和 watchOS。
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8091fb4e8328f61f1471d061b51b4735de3c089c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230719"
---
# <a name="objective-c-platforms"></a>Objective C 的平台

.NET 嵌入可以面向各种平台生成 Objective C 代码时：

* macOS
* iOS
* tvOS
* watchOS [尚未实现]

通过选择平台`--platform=<platform>`.NET 嵌入到命令行参数。

时构建适用于 iOS、 tvOS 和 watchOS 的平台，.NET 嵌入将始终创建嵌入 Xamarin.iOS，因为 Xamarin.iOS 包含大量所需在这些平台上运行时支持代码的框架。

但是，为 macOS 平台生成时，就可以选择是否生成的框架是否应嵌入 Xamarin.Mac。 如果绑定的程序集不引用 Xamarin.Mac.dll （直接或间接），并且此选项将被传递嵌入 Xamarin.Mac 可以`--platform=macOS`.NET 嵌入工具。

如果绑定的程序集包含对 Xamarin.Mac.dll 的引用，它必须嵌入 Xamarin.Mac、，此外 embeddinator 必须知道要使用的目标框架。

有三个可能的 Xamarin.Mac 目标框架： `modern` (以前称为`mobile`)，`full`并`system`(每个之间的区别所述 Xamarin.Mac 的[目标框架][ 1]文档)，并且每个已传递选定`--platform=macOS-modern`，`--platform=macOS-full`或`--platform=macOS-system`.NET 嵌入工具。

[1]: ~/mac/platform/target-framework.md
