---
title: 使用目标 Sharpie 创建绑定
description: 本部分提供目标 Sharpie，Xamarin 的命令行工具来自动执行创建绑定到 OBJECTIVE-C 库的过程的简介
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: cbe0be19e38892d06a79831d7974fbddcb71a9ac
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978476"
---
# <a name="creating-bindings-with-objective-sharpie"></a>使用目标 Sharpie 创建绑定

_本部分提供目标 Sharpie，Xamarin 的命令行工具来自动执行创建绑定到 OBJECTIVE-C 库的过程的简介_

- [概述](#overview) & [历史记录](#history)
- [入门](get-started.md)
- [工具和命令](tools.md)
- [功能](platform/index.md)
- [示例](examples/index.md)
- [完整的演练](~/ios/platform/binding-objective-c/walkthrough.md)
- [发布历史记录](releases.md)

## <a name="overview"></a>概述

目标 Sharpie 是一个命令行工具来帮助启动绑定的第一次传递。
其工作原理是分析要映射到的公共 API 的本机库的标头文件[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)（之前已手动执行该过程）。

目标 Sharpie 使用 Clang 分析标头文件，因此绑定是为确切并尽可能详尽。 这可以极大地减少时间和精力去生成质量绑定。

> [!IMPORTANT]
> 目标 Sharpie 是经验丰富的 Xamarin 开发人员的高级知识的 Objective C （并扩展到 C） 的工具。 然后再尝试绑定 OBJECTIVE-C 库应具有 solid 了解如何在命令行 （和本机库的工作方式更好地理解） 上生成本机库。

## <a name="history"></a>历史记录

我们已发展，目标 Sharpie 在内部使用 Xamarin 在过去三年里。 这就证明了目标 Sharpie 的强大功能，作为 Api Xamarin.iOS 和 Xamarin.Mac 中引入自 iOS 8、 Mac OS X 10.10，和 watchOS 2.0 已完全使用目标 Sharpie 引导。 Xamarin 严重依赖目标 Sharpie 在内部用于构建其自己的产品。

但是，目标 Sharpie 是一个非常先进的工具，要求精通 Objective C 和 C、 如何在命令行中，使用 clang 编译器和通常如何本机库被放在一起。 由于此高栏中，我们认为具有 GUI 向导设置错误的期望，并在这种情况下，目标 Sharpie 目前仅可用作命令行工具。

## <a name="related-links"></a>相关链接

- [目标 Sharpie 下载](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [演练：绑定 OBJECTIVE-C 库](~/ios/platform/binding-objective-c/walkthrough.md)
- [绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [绑定详细信息](~/cross-platform/macios/binding/overview.md)
- [绑定类型参考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [面向 Objective-C 开发人员的 Xamarin](~/ios/get-started/objective-c-developers/index.md)

