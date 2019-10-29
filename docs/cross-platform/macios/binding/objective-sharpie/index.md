---
title: 创建具有目标 Sharpie 的绑定
description: 本部分介绍了客观 Sharpie，Xamarin 的命令行工具，该工具用于自动执行创建到目标 C 库的绑定过程
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 4d6ab6cf48c5c365a4d8d05ef108a4d3a5d16134
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016183"
---
# <a name="creating-bindings-with-objective-sharpie"></a>创建具有目标 Sharpie 的绑定

_本部分介绍了客观 Sharpie，Xamarin 的命令行工具，该工具用于自动执行创建到目标 C 库的绑定过程_

- [概述](#overview) & [历史记录](#history)
- [入门](get-started.md)
- [工具和命令](tools.md)
- [功能](platform/index.md)
- [示例](examples/index.md)
- [完整演练](~/ios/platform/binding-objective-c/walkthrough.md)
- [发布历史记录](releases.md)

## <a name="overview"></a>概述

客观 Sharpie 是一个命令行工具，可帮助启动绑定的第一步。
它的工作原理是分析本机库的标头文件，将公共 API 映射到[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)（以前手动完成的进程）。

客观 Sharpie 使用 Clang 分析标头文件，因此绑定的完全一致。 这可以极大地减少生成质量绑定所要花费的时间和精力。

> [!IMPORTANT]
> 客观 Sharpie 是一款面向经验丰富的 Xamarin 开发人员的工具，具有客观-C （和 extension，C）的高级知识。 在尝试绑定目标 C 库之前，您应该大致了解如何在命令行上生成本机库（并且充分了解本机库的工作方式）。

## <a name="history"></a>历史记录

过去三年，我们一直在 Xamarin 上不断发展并使用目标 Sharpie。 作为证明目标 Sharpie 的强大功能，从 iOS 8、Mac OS X 10.10 和 watchOS 2.0 开始，因为 iOS 8、和在中引入的 Api 与目标 Sharpie 完全相同。 Xamarin 很大程度上依赖于客观 Sharpie 来构建自己的产品。

但是，客观 Sharpie 是一个非常高级的工具，它需要对目标-C 和 C 的高级了解、如何在命令行中使用 clang 编译器，以及如何将本机库组合在一起。 由于这是一个较高的条形，因此，我们认为具有 GUI 向导会设置错误的预期，因此，目标 Sharpie 当前仅可用作命令行工具。

## <a name="related-links"></a>相关链接

- [客观 Sharpie 下载](https://aka.ms/objective-sharpie)
- [演练：绑定目标 C 库](~/ios/platform/binding-objective-c/walkthrough.md)
- [绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [绑定详细信息](~/cross-platform/macios/binding/overview.md)
- [绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [面向 Objective-C 开发人员的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
