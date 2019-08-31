---
title: Apple 平台 (iOS 和 Mac)
description: '本文档介绍与 Xamarin 和 Xamarin 开发相关的各种主题: 代码共享、Unified API、绑定目标-C 库、本机引用、本机类型等。'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57e69f3cb7662b1ff6e1e7fe1645605d7861b9
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199418"
---
# <a name="apple-platform-ios-and-mac"></a>Apple 平台 (iOS 和 Mac)

## <a name="code-sharing"></a>代码共享

对于您的代码中没有用户界面元素的元素, 在 iOS 和 Mac 之间共享代码的最佳方式仍是[可移植类库](~/cross-platform/app-fundamentals/pcl.md)。

对于必须执行一些用户界面工作, 并且你想要共享的代码, 你应使用[共享项目, 该共享项目](~/cross-platform/app-fundamentals/shared-projects.md)允许你将代码置于单个项目中, 并在被引用时使用 Mac 和 iOS 进行编译。

## <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

IOS 和 Mac 项目的 Unified API 为框架使用相同的命名空间, 以便可以在这两个平台上使用相同的代码文件, 以实现无缝代码共享。 它还支持32和64位版本。 从2015年初开始, Unified API 是模板默认值, 建议为所有新项目*仅*Unified API 项目提交到 App Store。

### <a name="classic-apis"></a>经典 Api

> [!NOTE]
> **经典配置文件弃用:** 由于在 Xamarin 中添加了新的平台, 因此我们开始逐渐弃用经典配置文件 (monotouch.dialog) 的功能。 例如, 已删除非 NRC (新引用计数) 选项。 始终为所有统一的应用程序启用了 NRC (即, 不能使用 NRC), 并且没有已知问题。 未来版本将删除使用 Boehm 作为垃圾回收器的选项。 这也是对统一应用程序不可用的选项。 在版本的 10.0 Xamarin 版本2016中, 计划完全删除经典支持。

由于本机框架具有`MonoTouch.`或`MonoMac.`命名空间前缀, 因此原始 (非统一) Xamarin 和 xamarin api 使得代码共享变得更加困难。  我们提供了一些空命名空间, 使开发人员可以通过`using`在同一文件中添加引用 MonoMac 和 monotouch.dialog 命名空间的语句来共享代码, 但这有点不好。 只能在内部分发的旧版应用中继续使用 Classic API (建议升级到 Unified API)。


### <a name="updating-from-classic-to-the-unified-api"></a>从经典升级到 Unified API

有关将任何应用程序从经典升级到 Unified API 的详细说明。

## <a name="binding-objective-c-librariesbindingindexmd"></a>[绑定 Objective-C 库](binding/index.md)

Xamarin 使你可以通过绑定将本机库引入你的应用程序。 本部分介绍：

- 绑定的工作原理
- 如何手动生成绑定项目, 使你能够将目标为 C 的代码引入 Xamarin, 以及
- 如何使用我们的**客观 Sharpie**工具帮助自动执行此过程。

## <a name="native-referencesnative-referencesmd"></a>[本机引用](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Mac/iOS 本机类型](nativetypes.md)

为了以透明的方式从C#和F#中支持32和64位代码, 我们引入了新的数据类型。   在此处了解相关信息。

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[生成32和64位应用](32-and-64/index.md)

支持32和64位应用程序所需了解的内容。

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[使用跨平台应用中的本机类型](native-types-cross-platform.md)

本文介绍如何在跨平台应用程序中使用新`nint`的`nuint`iOS `nfloat`Unified API 本机类型 (、、), 该应用程序中的代码与 Android 或 Windows Phone 操作系统等非 iOS 设备共享。
它可让你深入了解本机类型的使用情况, 并为新类型必须与跨平台代码一起使用的情况提供多种可能的解决方案。

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[HttpClient 堆栈和 SSL/TLS 实现选择器](http-stack.md)

新的 HttpClient Stack 选择器控制要在 Xamarin、tvOS 和 Xamarin 应用中使用的 HttpClient 实现。 现在, 你可以切换到使用 iOS、tvOS 或 os X 本机传输 (`NSUrlSession`或`CFNetwork`取决于 OS) 的实现。

SSL (安全套接字层) 及其后续版本 TLS (传输层安全性) 通过`System.Net.Security.SslStream`提供对 HTTP 和其他网络连接的支持。 新的 SSL/TLS 实现生成选项在 Mono 的 TLS 堆栈和在 Mac 和 iOS 中提供的 Apple TLS 堆栈提供支持的选项之间切换。
