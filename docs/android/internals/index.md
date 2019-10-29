---
title: 高级概念和内部机制
description: Xamarin 和 it 的 API 设计的基础体系结构。
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/21/2018
ms.openlocfilehash: 97382243ac5f767d94a782b895401c1f2f8ae554
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027860"
---
# <a name="advanced-concepts-and-internals"></a>高级概念和内部机制

_本部分包含的主题介绍了 Xamarin 的体系结构、API 设计和限制。此外，它还包括一些主题，这些主题说明了其垃圾收集实现以及 Xamarin 中可用的程序集。因为 Xamarin 是[开放源代码](https://github.com/xamarin/xamarin-android)，所以也可以通过检查其源代码来了解 xamarin 的内部工作原理。_

## <a name="architectureandroidinternalsarchitecturemd"></a>[体系结构](~/android/internals/architecture.md)

本文介绍 Xamarin Android 应用程序背后的基础体系结构。 它介绍了 Xamarin Android 应用程序如何在 Mono 执行环境中与 Android 运行时虚拟机一起运行，并将此类关键概念解释为 Android 可调用包装器和托管可调用包装器。 

## <a name="api-designandroidinternalsapi-designmd"></a>[API 设计](~/android/internals/api-design.md)

除了包含 Mono 的核心基类库外，Xamarin 还附带了各种 Android Api 的绑定，以允许开发人员通过 Mono 创建本机 Android 应用程序。

Xamarin 的核心是一个互操作引擎，它将C#世界与 java 世界桥梁，并为开发人员提供了从C#或其他 .Net 语言访问 Java api 的权限。

## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[程序集](~/cross-platform/internals/available-assemblies.md)

Xamarin 附带多个程序集。 正如 Silverlight 是桌面 .NET 程序集的扩展子集一样，Xamarin 也是多个 Silverlight 和桌面 .NET 程序集的扩展子集。 
