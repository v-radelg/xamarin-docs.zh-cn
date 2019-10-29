---
title: .NET 嵌入
description: .Net 嵌入允许使用其他编程语言编写C#的F#代码使用现有的 .net 代码（、和其他）。
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5a0e7eeaee9b3189de63d0b82a3822cc68023505
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006950"
---
# <a name="net-embedding"></a>.NET 嵌入

![预览](~/media/shared/preview.png)

.Net 嵌入允许从其他编程语言和C#不同F#环境使用现有的 .net 代码（、和其他）。

这意味着，如果你有一个要在现有 iOS 应用中使用的 .NET 库，可以这样做。   或者，如果想要将其链接到本机C++库，还可以执行此操作。   或从 Java 使用 .NET 代码。

.NET 嵌入基于[Embeddinator-4000](https://github.com/mono/Embeddinator-4000)开源项目。

## <a name="environments-and-languages"></a>环境和语言

该工具既能识别它将使用的环境，也能识别将使用它的语言。   例如，iOS 平台不允许实时（JIT）编译，因此 .NET 嵌入会将 .NET 代码静态编译为可在 iOS 中使用的本机代码。  其他环境确实允许 JIT 编译，在这些环境中，我们选择 JIT 编译。

它支持各种语言使用者，因此它在目标语言中以惯用代码的形式呈现 .NET 代码。   这是目前支持的语言列表：

- [**目标-c**](objective-c/index.md) -将 .net 映射到惯用目标-C api
- [**Java**](android/index.md) –将 .net 映射到惯用 Java api
- [**C**](get-started/c.md) -将 .net 映射到面向对象的类似于 C api

稍后会提供更多语言。

## <a name="getting-started"></a>入门

若要开始，请检查每个当前支持的语言的指南：

- [**目标-C**](get-started/objective-c/index.md) -涵盖 MacOS 和 iOS
- [**Java**](get-started/java/index.md) –涵盖 MacOS 和 Android
- [**C**](get-started/c.md) -介绍桌面平台上的 c 语言

## <a name="related-links"></a>相关链接

- [GitHub 上的 Embeddinator-4000](https://github.com/mono/Embeddinator-4000)
