---
title: 高级概念和内部机制
description: Xamarin.Android 和它的 API 设计背后的基础体系结构。
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: ba804e9757fb1354c39177e6f7cb9c97ea1b8188
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864138"
---
# <a name="advanced-concepts-and-internals"></a>高级概念和内部机制

_本部分包含这些主题介绍体系结构、 API 设计和 Xamarin.Android 的限制。此外，它包含的主题说明其垃圾回收实现以及可在 Xamarin.Android 中的程序集。因为 Xamarin.Android[开放源代码](https://github.com/xamarin/xamarin-android)，则还可以通过检查其源代码了解 Xamarin.Android 的内部工作机制。_


## <a name="architectureandroidinternalsarchitecturemd"></a>[体系结构](~/android/internals/architecture.md)

本文介绍 Xamarin.Android 应用程序的基础体系结构。 它介绍了如何 Xamarin.Android 应用程序为环境中运行 Mono 执行与一起使用 Android 运行时虚拟机，并说明了此类作为 Android 可调用包装器和托管可调用包装器的重要概念。 



## <a name="api-designandroidinternalsapi-designmd"></a>[API 设计](~/android/internals/api-design.md)

除了核心是 Mono 的一部分的基类库，Xamarin.Android 提供与各种 Android Api 允许开发人员使用 Mono 创建本机 Android 应用程序的绑定。

有 Xamarin.Android 的核心是互操作引擎与 Java 领域这样桥 C# 的环境和开发人员提供了访问 Java api 从 C# 或其他.NET 语言。



## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[程序集](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android 附带了几个程序集。 就像 Silverlight 是桌面.NET 程序集的扩展的子集，Xamarin.Android 也是多个 Silverlight 和桌面.NET 程序集的扩展的子集。 

