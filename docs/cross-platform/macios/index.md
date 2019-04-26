---
title: Apple 平台 （iOS 和 Mac）
description: 本文档介绍了与 Xamarin.iOS 和 Xamarin.Mac 开发相关的各种主题： 代码共享、 统一的 API、 绑定 OBJECTIVE-C 库、 本机引用、 本机类型，和的详细信息。
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b40758fa562e57415cd3c0818763ef0a7ce5dcca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199762"
---
# <a name="apple-platform-ios-and-mac"></a>Apple 平台 （iOS 和 Mac）

## <a name="code-sharing"></a>代码共享

具有共享的最佳方法的没有用户界面元素的元素的代码的 iOS 和 Mac 间的代码仍是使用[的可移植类库](~/cross-platform/app-fundamentals/pcl.md)。

必须执行一些用户界面工作的代码并且尚未，你想要共享，应使用[共享的项目](~/cross-platform/app-fundamentals/shared-projects.md)，用于放置共享单个项目中并将其与 Mac 和 iOS 引用时编译的代码。

##  <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

用于 iOS 和 Mac 项目的统一 API 框架使用相同的命名空间，以便在同一代码文件可用于在这两个平台之间无缝的代码共享。 它还使 32 位和 64 位版本。 Unified API 模板默认值已从早期 2015，并建议所有新项目-*仅*Unified API 项目可以提交到 App Store。

### <a name="classic-apis"></a>经典 Api

> [!NOTE]
> **经典的配置文件不推荐使用：** 在 Xamarin.iOS 中添加新的平台时我们开始逐渐淘汰经典的配置文件 (monotouch.dll) 中的功能。 例如，已删除的非 NRC （新引用计数） 选项。 NRC 始终已启用的所有统一应用程序 （即非 NRC 永远不会是一个选项） 并且没有已知的问题。 未来版本将删除作为垃圾回收器使用 Boehm 的选项。 这也是永远不会用于统一应用程序的选项。 经典支持完全删除的计划为 fall 2016 版本的 Xamarin.iOS 10.0。

原始 （非统一） Xamarin.iOS 和 Xamarin.Mac Api 进行代码共享更难因为本机框架有其中一个`MonoTouch.`或`MonoMac.`命名空间前缀。  我们提供一些空命名空间，开发人员可以通过添加共享代码`using`引用在同一文件中，但这 MonoMac 和 MonoTouch 命名空间的语句是有点费解之处。 经典 API 应仅将继续使用旧的应用程序，内部分布中 （升级到 Unified API 建议）。


### <a name="updating-from-classic-to-the-unified-api"></a>更新从经典部署模型到 Unified API

有任何应用程序更新从经典部署模型到 Unified API 的详细的说明。

## <a name="binding-objective-c-librariesbindingindexmd"></a>[绑定 Objective-C 库](binding/index.md)

Xamarin，可使用绑定到你的应用将本机库。 本部分介绍：

- 绑定的工作原理
- 如何手动构建，可将 OBJECTIVE-C 代码到 Xamarin，一个绑定项目和
- 如何使用我们**目标 Sharpie**工具，可帮助自动执行该过程。

## <a name="native-referencesnative-referencesmd"></a>[本机引用](native-references.md)

##  <a name="macios-native-typesnativetypesmd"></a>[Mac/iOS 本机类型](nativetypes.md)

若要支持 32 位和 64 位代码，以透明方式从C#和F#，我们引入了新的数据类型。   了解有关内容。

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[生成 32 位和 64 位应用程序](32-and-64/index.md)

您需要知道要支持 32 位和 64 位应用程序。

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[使用跨平台应用中的本机类型](native-types-cross-platform.md)

本文介绍如何使用新的 iOS 统一 API 本机类型 (`nint`， `nuint`， `nfloat`): 与非 iOS 设备，例如 Android 或 Windows Phone 操作系统共享代码的跨平台应用程序中。
它提供了深入了解何时使用本机类型，并提供新的类型必须用于跨平台代码的情况下几个可能的解决方案。

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[HttpClient 堆栈和 SSL/TLS 实现选择器](http-stack.md)

新的 HttpClient 堆栈选择器控制要使用 Xamarin.iOS、 Xamarin.tvOS 和 Xamarin.Mac 应用程序中的 HttpClient 实现。 TvOS 的或 OS X 本机传输现在切换到使用 iOS 的实现，(`NSUrlSession`或`CFNetwork`具体取决于操作系统)。

SSL （安全套接字层） 和其后续版本 TLS （传输层安全性） 支持 HTTP 和其他网络连接通过`System.Net.Security.SslStream`。 新的 SSL/TLS 实现生成选项 Mono 的 TLS 堆栈和一个由 Apple 的 TLS 堆栈位于 Mac 和 iOS 之间切换。
