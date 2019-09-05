---
title: iOS 高级概念和内部机制
description: 本主题介绍 .NET 基类库（BCL）中的 Monotouch.dialog API 设计、程序集和类，以及 Visual Studio for Mac 如何与 Xcode 的 Interface Builder 和 Apple 的工具链集成。
ms.prod: xamarin
ms.assetid: 951713CD-D6AD-981C-A09E-4F2C98588D8B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: 4f6043190087d34ccaa4a63fcc801843194273ad
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291882"
---
# <a name="ios-advanced-concepts-and-internals"></a>iOS 高级概念和内部机制

_本主题介绍 .NET 基类库（BCL）中的 Monotouch.dialog API 设计、程序集和类，以及 Visual Studio for Mac 如何与 Xcode 的 Interface Builder 和 Apple 的工具链集成。_

## <a name="api-designiosinternalsapi-designindexmd"></a>[API 设计](~/ios/internals/api-design/index.md)

介绍 API 绑定背后的设计原则。

## <a name="available-assembliescross-platforminternalsavailable-assembliesmd"></a>[可用程序集](~/cross-platform/internals/available-assemblies.md)

列出 .NET 基类库（BCL）中可用的程序集和类。

## <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[XIB 代码生成](~/ios/internals/xib-code-generation.md)

还介绍了 Visual Studio for Mac 和 Xcode 的 Interface Builder 如何允许使用 Interface Builder 来设计 UI。

> [!IMPORTANT]
> 本文档说明 Visual Studio for Mac 仅与 Xcode 的 Interface Builder 集成。 有关 iOS 设计器的详细信息，请查看[Ios 设计器](~/ios/user-interface/designer/index.md)文档。

## <a name="ios-architectureiosinternalsarchitecturemd"></a>[iOS 体系结构](~/ios/internals/architecture.md)

Xamarin iOS 应用程序在 Mono 执行环境中运行，并使用完全提前（AOT）编译将代码编译C#为 ARM 汇编语言。 本指南以较低的级别探究 Xamarin

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Objective-C 的选择器](~/ios/internals/objective-c-selectors.md)

说明并且直接调用 Objective-C 的选择器 （方法） 的使用情况。

## <a name="limitationslimitationsmd"></a>[限制](limitations.md)

要了解的有关 Xamarin 的缺陷和限制。
