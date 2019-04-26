---
title: 开始使用目标 Sharpie
description: 本文档提供目标 Sharpie，用来自动创建的工具的高级概述C#绑定到 OBJECTIVE-C 代码。
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: c1831467ca0cbb4329a1e77fb355698f2d16cd6a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199775"
---
# <a name="getting-started-with-objective-sharpie"></a>开始使用目标 Sharpie

> [!IMPORTANT]
> 目标 Sharpie 是经验丰富的 Xamarin 开发人员的高级知识的 Objective C （并扩展到 C） 的工具。 然后再尝试绑定 OBJECTIVE-C 库应具有 solid 了解如何在命令行 （和本机库的工作方式更好地理解） 上生成本机库。

<a name="installing" />

## <a name="installing-objective-sharpie"></a>安装目标 Sharpie

目标 Sharpie 目前为独立的命令行工具为 Mac OS X 10.10 及更高，_不是完全受支持的 Xamarin 产品_。 它只应由高级开发人员可以帮助创建一个绑定项目中的第三方 OBJECTIVE-C 的库。

目标 Sharpie 可作为标准的 OS X 包安装程序下载。
运行安装程序并按所有屏幕上从安装向导的提示：

- **当前版本：3.4**
  - [下载最新版本](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [论坛公告](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> 使用`sharpie update`命令以更新到最新版本。

## <a name="basic-walkthrough"></a>基本演练

目标 Sharpie 是由 Xamarin，可协助创建绑定到第三方 Objective C 库所需的定义提供一个命令行工具C#。
即使使用目标 Sharpie，开发人员*将*需要目标 Sharpie 完成以解决任何问题，可能不会自动处理通过该工具后修改生成的文件。

在可能的情况下，目标 Sharpie 将批注与它对一些不确定如何正确地将绑定的 Api （在本机代码中的许多构造是不明确）。
这些批注将显示为[`[Verify]`属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)。

目标 Sharpie 的输出是一对文件- [ `ApiDefinition.cs`并`StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -可用于创建绑定项目编译到一个库，可以使用 Xamarin 应用程序中。

> [!IMPORTANT]
> 目标 Sharpie 附带了一个**主要**适当的使用情况的规则： 必须绝对传递它正确 clang 编译器命令行参数以确保能够正确分析。 这是因为分析阶段目标 Sharpie 是只是一种工具[针对 clang libtooling API 实现](http://clang.llvm.org/docs/LibTooling.html)。

这意味着目标 Sharpie Clang 的全部功能 (C/OBJECTIVE-C /C++实际编译的本机库的编译器会将绑定) 和所有绑定的标头文件及其内部知识。
而不是转换的已分析[AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree)为对象代码目标 Sharpie 转换到 ASTC#绑定"搭建基架"适用于输入到`bmac`并`btouch`Xamarin 绑定工具。

如果目标 Sharpie 错误出在分析过程，表示出错期间该 clang 其分析阶段尝试构造 AST，并且您需要找出原因。

**新增功能！** 版本 3.0 尝试解决这种复杂性的一些通过直接支持的 Xcode 项目。 如果本机库具有有效的 Xcode 项目，目标 Sharpie 可以计算指定的目标和配置，以推断出必要输入标头文件和编译器标志的项目。

如果不没有提供任何 Xcode 项目，需要通过再推断正确的输入标头文件、 标头文件搜索路径和其他必要的编译器标志将更熟悉该项目。 请务必意识到用来生成本机库的编译器标志是一样的必须传递给目标 Sharpie。 这是熟悉的手动程度更高的过程，而且需要少量编译使用 Clang 工具链在命令行上的本机代码。

**新增功能！** 版本 3.0 还引入了一个工具，用于轻松地绑定[CocoaPods](https://cocoapods.org)通过`sharpie pod`命令。
如果您感兴趣的库作为 CocoaPod 不可用，我们建议首先尝试将与目标 Sharpie CocoaPod 绑定 （而不是试图直接绑定对源）。

## <a name="related-links"></a>相关链接

- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)