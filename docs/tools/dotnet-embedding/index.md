---
title: .NET 嵌入
description: '.NET 嵌入允许现有.NET 代码 (C#、 F # 和其他人） 可供其他编程语言编写的代码。'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 23233ea8b06e0db580ba99edf2705e3dae5b931f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107118"
---
# <a name="net-embedding"></a>.NET 嵌入

![预览](~/media/shared/preview.png)

.NET 嵌入允许您从其他编程语言和各种不同的环境中使用现有的.NET 代码（C＃，F＃和其他语言）。

这意味着如果您要从现有 iOS 应用程序中使用.NET 库，您可以这样做。 或者，如果要将其与原生 c + + 库链接，您也可以这样做。 或者从 Java 中使用.NET 代码。

.NET 嵌入基于[Embeddinator 4000](https://github.com/mono/Embeddinator-4000)开源项目。

## <a name="environments-and-languages"></a>环境和语言

该工具既知道它将使用的环境，也知道将使用它的语言。 例如，iOS 平台不允许实时（JIT）编译，因此.NET 嵌入将您的.NET 代码静态编译为可在iOS中使用的原生代码。 其他环境允许 JIT 编译，在这些环境中，我们选择 JIT 编译。

它支持多种语言使用者，因此它将.NET 代码表示为目标语言中的惯用代码。 这是目前支持的语言列表：

- [**Objective C** ](objective-c/index.md) – 将.NET 映射到惯用的 Objective-C API
- [**Java** ](android/index.md) – 将.NET 映射到惯用 Java API
- [**C** ](get-started/c.md) – 将.NET 映射到面向对象的C API

稍后将推出更多语言。

## <a name="getting-started"></a>入门

如果要开始使用，请查看目前我们针对每种支持的语言的指南：

- [**Objective C** ](get-started/objective-c/index.md) – 涵盖 macOS 和 iOS
- [**Java** ](get-started/java/index.md) – 涵盖 macOS 和 Android
- [**C** ](get-started/c.md) – 涵盖桌面平台上的 C 语言

## <a name="related-links"></a>相关链接

- [GitHub 上的 Embeddinator 4000](https://github.com/mono/Embeddinator-4000)
