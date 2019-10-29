---
title: 使用 CocoaPods 的实际示例
description: 本文档演示如何使用客观 Sharpie 自动生成 CocoaPod 的C#绑定定义。
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: cf117880eb46b028d709a44aa453e111b007b441
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016264"
---
# <a name="real-world-example-using-cocoapods"></a>使用 CocoaPods 的实际示例

> [!NOTE]
> 此示例使用[AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking)。

版本3.0 中的新增内容，目的 Sharpie 支持绑定 CocoaPods，甚至包含命令（`sharpie pod`），使下载、配置和构建 CocoaPods 非常简单。 在使用此功能之前，您应[熟悉 CocoaPods](https://cocoapods.org) 。

## <a name="creating-a-binding-for-a-cocoapod"></a>为 CocoaPod 创建绑定

`sharpie pod` 命令有一个全局选项和两个子命令：

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

`init` 子命令还提供了一些有用的帮助：

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

可以向 `init`提供多个 CocoaPod 名称和 subspec 名称。

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

设置 CocoaPod 后，现在可以创建绑定：

```bash
$ sharpie pod bind
```

这会生成 CocoaPod Xcode 项目，并通过目标 Sharpie 对其进行评估和分析。 将生成大量控制台输出，但应在末尾产生绑定定义：

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>后续步骤

生成**ApiDefinitions.cs**和**StructsAndEnums.cs**文件后，请查看以下文档，生成要在应用中使用的程序集：

- [绑定目标-C 概述](~/cross-platform/macios/binding/overview.md)
- [绑定目标-C 库](~/cross-platform/macios/binding/objective-c-libraries.md)
- [演练：绑定 iOS 目标-C 库](~/ios/platform/binding-objective-c/walkthrough.md)
