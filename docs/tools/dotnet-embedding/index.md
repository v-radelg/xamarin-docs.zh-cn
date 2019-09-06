---
title: .NET 嵌入
description: .Net 嵌入允许使用其他编程语言编写C#的F#代码使用现有的 .net 代码（、和其他）。
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: af068e5a09cc11eec33508a4f2eb33186168aae6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290227"
---
# <a name="net-embedding"></a>.NET 嵌入

![预览](~/media/shared/preview.png)

利用 .NET 嵌入，便可在其他编程语言和各种不同环境中使用现有 .NET 代码（C＃、F＃ 和其他语言）。

这意味着，如果要从现有 iOS 应用中使用 .NET 库，则可以实现。   或者，如果要将其与本机 C++ 库进行链接，也可以实现。   此外，还可以从 Java 使用 .NET 代码。

.NET 嵌入基于 [Embeddinator 4000](https://github.com/mono/Embeddinator-4000) 开源项目。

## <a name="environments-and-languages"></a>环境和语言

该工具既知道其要使用的环境，也知道要使用其的语言。   例如，iOS 平台不允许实时 (JIT) 编译，因此 .NET 嵌入将静态编译 .NET 代码为可在 iOS 中使用的本机代码。  其他环境允许 JIT 编译，在这些环境中，我们选择 JIT 编译。

它支持各种语言使用者，因此可将 .NET 代码表示为目标语言中的惯用代码。   以下是当前支持的语言列表：

- [**Objective C** ](objective-c/index.md) – 将 .NET 映射到惯用的 Objective-C API
- [**Java** ](android/index.md) – 将 .NET 映射到惯用 Java API
- [**C** ](get-started/c.md) – 将 .NET 映射到面向对象的 C API 等

稍后会提供更多语言。

## <a name="getting-started"></a>入门

若要入门，请查看有关每种当前支持语言的指南之一：

- [**Objective C** ](get-started/objective-c/index.md) – 涵盖了 macOS 和 iOS
- [**Java** ](get-started/java/index.md) – 涵盖了 macOS 和 Android
- [**C** ](get-started/c.md) – 涵盖了桌面平台上的 C 语言

## <a name="related-links"></a>相关链接

- [GitHub 上的 Embeddinator-4000](https://github.com/mono/Embeddinator-4000)
