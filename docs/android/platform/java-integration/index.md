---
title: Java 集成概述
description: Java 生态系统包括组件的不同和巨大的集合。 许多这些组件可用于减少开发 Android 应用程序所需的时间。 本文档将介绍并提供一些开发人员可以使用这些现有的 Java 组件以提高其 Xamarin.Android 应用程序的开发体验的方式的高级概述。
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 3ab31fb7cac97fbae3315f51daf3dd4b1edbcc1d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085280"
---
# <a name="java-integration-overview"></a>Java 集成概述

_Java 生态系统包括组件的不同和巨大的集合。许多这些组件可用于减少开发 Android 应用程序所需的时间。本文档将介绍并提供一些开发人员可以使用这些现有的 Java 组件以提高其 Xamarin.Android 应用程序的开发体验的方式的高级概述。_


## <a name="overview"></a>概述

给定的 Java 生态系统的程度，它是很有可能在 Java 中已编码为 Xamarin.Android 应用程序所需的任何给定的功能。 因此，很吸引人尝试并创建 Xamarin.Android 应用程序时重复使用这些现有的库。 

有三个可能的方法可重复使用 Xamarin.Android 应用程序中的 Java 库： 

-   **创建 Java 绑定库**&ndash;使用此技术，Xamarin.Android 项目用于创建C#Java 类型的包装。 然后，Xamarin.Android 应用程序可以引用C#包装器创建此项目，然后使用`.jar`文件。 

-   **Java 本机接口** &ndash; *Java 本机* *接口*(JNI) 是一个框架，允许非 Java 代码 (如 c + + 或C#) 调用或调用通过运行的 Java 代码JVM 内部。 

-   **将代码移植**&ndash;此方法包括获取 Java 源代码，并再将其转换为C#。 这可以手动或通过使用如锐化的自动化的工具。 

前两个技术的核心是*Java 本机接口*(JNI)。 JNI 是一个框架，允许不以 Java 编写的应用程序的 Java 虚拟机中运行的 Java 代码进行交互。 Xamarin.Android 使用 JNI 来创建*绑定*为C#代码。 

第一种方法是绑定 Java 库的自动化程度更高、 声明性方法。 它涉及到使用 Visual Studio for Mac 或 Visual Studio 项目类型提供的 Xamarin.Android &ndash; Java 绑定库。 若要成功创建这些绑定，Java 绑定库可能仍需要一些手动修改，但没有那么多一样将纯 JNI 方法。 请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)有关 Java 绑定库的详细信息。 

第二种方法，使用 JNI，在更低级别工作，但可以提供更精细的控制和访问通常不能通过 Java 绑定库可访问的 Java 方法。 

第三种方法是从以前的两个全然不同： 移植到 Java 中的代码C#。 移植到另一种语言中的代码是一个非常费力的过程，但可以减少工作量的一种工具帮助调用*锐化*。 提升是开放源代码工具，它是 Java-到-C#转换器。 



## <a name="summary"></a>总结

本文档提供的一些不同的方式通过 Java 库，可以在 Xamarin.Android 应用程序中重复使用的高级概述。 它引入了绑定的概念和管理可调用包装器，并探讨了用于 Java 将代码移植到选项C#。 


## <a name="related-links"></a>相关链接

- [体系结构](~/android/internals/architecture.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [锐化](https://github.com/slluis/sharpen)
- [Java 本机接口](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
