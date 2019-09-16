---
title: 入门与目标 Sharpie
description: 本文档提供了客观 Sharpie 的高级概述，该工具用于自动创建到目标-C 代码C#的绑定。
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: c34a6c09bf1298fd710e3e39a244294821a714ae
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290736"
---
# <a name="getting-started-with-objective-sharpie"></a>入门与目标 Sharpie

> [!IMPORTANT]
> 客观 Sharpie 是一款面向经验丰富的 Xamarin 开发人员的工具，具有客观-C （和 extension，C）的高级知识。 在尝试绑定目标 C 库之前，您应该大致了解如何在命令行上生成本机库（并且充分了解本机库的工作方式）。

<a name="installing" />

## <a name="installing-objective-sharpie"></a>安装目标 Sharpie

目标 Sharpie 目前是一个独立的命令行工具，适用 Mac OS X 于10.10 和更高版本，并且_不是完全受支持的 Xamarin 产品_。 只有高级开发人员才能使用它来帮助创建到第三方目标-C 库的绑定项目。

目标 Sharpie 可作为标准 OS X 包安装程序下载。
运行安装程序并按照安装向导中的所有屏幕提示操作：

- **当前版本：3.4**
  - [下载最新版本](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [论坛公告](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> `sharpie update`使用命令更新到最新版本。

## <a name="basic-walkthrough"></a>基本演练

客观 Sharpie 是 Xamarin 提供的命令行工具，可帮助创建将第三方目标-C 库绑定到C#所需的定义。
即使使用客观 Sharpie，开发人员*也*需要在客观 Sharpie 完成后修改生成的文件，以解决无法由该工具自动处理的任何问题。

在可能的情况下，客观 Sharpie 将对 Api 进行注释，以确定如何正确绑定（本机代码中的很多构造不明确）。
这些批注将显示为[ `[Verify]`属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)。

目标 Sharpie 的输出是一对[ `ApiDefinition.cs`文件`StructsAndEnums.cs` ，可](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)用于创建绑定项目，该项目可编译到可在 Xamarin 应用中使用的库。

> [!IMPORTANT]
> 客观 Sharpie 附带了一个用于正确使用的**主要**规则：必须将正确的 clang 编译器命令行参数传递给它，才能确保正确地进行分析。 这是因为目标 Sharpie 分析阶段只是[针对 clang LIBTOOLING API 实现](http://clang.llvm.org/docs/LibTooling.html)的工具。

这意味着目标 Sharpie 具有 Clang （实际编译要绑定的本机库的 C/目标 CC++ /编译器）及其所有用于绑定的标头文件的内部知识的全部功能。
客观 Sharpie 将 ast 转换为适用于C# `bmac`输入和`btouch` Xamarin 绑定工具的绑定 "基架"，而不是将已分析的[ast](https://en.wikipedia.org/wiki/Abstract_syntax_tree)转换为对象代码。

如果目标 Sharpie 在分析过程中出现错误，则意味着在分析阶段尝试构造 AST 时 clang 误码，而您需要找出原因。

**新增功能！** 版本3.0 尝试直接支持 Xcode 项目，从而解决了某些复杂性。 如果本机库具有有效的 Xcode 项目，目标 Sharpie 可以计算指定目标和配置的项目来推导必需的输入头文件和编译器标志。

如果没有可用的 Xcode 项目，则需要通过推断出正确的输入头文件、头文件搜索路径和其他必要的编译器标志来更熟悉此项目。 必须认识到用于生成本机库的编译器标志与必须传递给目标 Sharpie 的编译器标志相同。 这是一个更手动的过程，并且需要对 Clang 工具链的命令行上的本机代码进行熟悉。

**新增功能！** 版本3.0 还引入了一个工具，通过 `sharpie pod` 命令轻松绑定 [CocoaPods](https://cocoapods.org)。
如果你感兴趣的库以 CocoaPod 的形式提供，我们建议你首先尝试将 CocoaPod 与目标 Sharpie 绑定（与尝试直接绑定源）。
