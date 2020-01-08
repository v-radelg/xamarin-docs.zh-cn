---
title: 绑定 Objective-C
description: 本文档提供了一些链接，这些链接指向介绍如何C#创建与目标-C 代码的绑定的各种指南，使开发人员能够在 Xamarin 应用程序中使用现成的库。
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: b7764d63991ec636043982509319e7097ef2091b
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663243"
---
# <a name="binding-objective-c"></a>绑定 Objective-C

本部分包含各种文档，涵盖如何创建到目标 C 库的绑定，因此可以从使用 Xamarin 或 Xamarin C#创建的应用程序中调用它们。

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[概述](~/cross-platform/macios/binding/overview.md)

本文档包含了绑定发生方式的一些内部机制。 这是包含一些技术信息的高级文档。

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[绑定 Objective-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)

本文档介绍了用于创建C#目标-c api 的绑定的过程，以及如何将目标中的惯例映射到 .net 中使用的惯例。
如果只是绑定 C Api，则应将标准 .NET 机制用于此（P/Invoke 框架）。

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[绑定定义参考指南](~/cross-platform/macios/binding/binding-types-reference.md)

这是描述绑定作者用于驱动绑定生成过程的所有可用属性的参考指南。

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[目标 Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

客观 Sharpie 是一个命令行工具，可帮助启动绑定的第一步。 它的工作原理是分析本机库的标头文件，将公共 API 映射到[绑定定义](~/cross-platform/macios/binding/objective-c-libraries.md)（也可以手动执行的过程）。

## <a name="ios"></a>iOS

除了下面的示例外， [iOS 绑定页](~/ios/platform/binding-objective-c/index.md)还会链接回这些公共绑定资源。

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[演练：绑定目标 C 库](~/ios/platform/binding-objective-c/walkthrough.md)

本文提供使用开源[InfColorPicker](https://github.com/InfinitApps/InfColorPicker)目标-C 项目作为示例创建绑定项目的分步演练。 InfColorPicker 库提供了一个可重用的视图控制器，该控制器允许用户根据其 HSB 表示形式选择颜色，使颜色选择更易于用户使用。 目标 Sharpie 将用于帮助进行绑定。

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[绑定示例](https://github.com/mono/monotouch-bindings)

在创建新的绑定项目时可用作引用的第三方绑定的集合。

## <a name="mac"></a>Mac

遵循[Mac 绑定](~/mac/platform/binding.md)说明绑定 macOS 库。 你可以从 "**新建项目**" 窗口中创建新的**Mac 绑定库**：

["![文件" "新建 mac 绑定项目" 对话框](images/new-bindings-library-sml.png)](images/new-bindings-library.png#lightbox)

## <a name="related-links"></a>相关链接

- [iOS 绑定](~/ios/platform/binding-objective-c/index.md)
- [Mac 绑定](~/mac/platform/binding.md)
