---
title: Java 与 Xamarin 的集成
description: Java 生态系统包含各种不同的组件集合。 其中的许多组件可用于减少开发 Android 应用程序所需的时间。 本文档将介绍并提供一些开发人员可以使用这些现有 Java 组件来改善其 Xamarin 应用程序开发体验的一些方式的高级概述。
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: a9d239140cee9eb600414a1bfb0733c9af6488bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761426"
---
# <a name="java-integration-with-xamarinandroid"></a>Java 与 Xamarin 的集成

_Java 生态系统包含各种不同的组件集合。其中的许多组件可用于减少开发 Android 应用程序所需的时间。本文档将介绍并提供一些开发人员可以使用这些现有 Java 组件来改善其 Xamarin 应用程序开发体验的一些方式的高级概述。_

## <a name="overview"></a>概述

考虑到 Java 生态系统的范围，很可能是 Xamarin Android 应用程序所需的任何给定功能都已在 Java 中进行编码。 因此，创建 Xamarin Android 应用程序时，最好尝试并重用这些现有库。

可以通过三种方法在 Xamarin Android 应用程序中重复使用 Java 库： 

- **创建 Java 绑定库**使用此方法，Xamarin Android 项目用于创建C#围绕 Java 类型的包装。 &ndash; 然后，Xamarin 应用程序可以引用此项目C#创建的包装，并使用`.jar`该文件。 

- **Java 本机接口**C++ C# Java 本机接口（JNI）是一个框架，它允许非 java 代码（如或）通过在 JVM 内运行的 Java 代码调用或调用。 &ndash; 

- **端口代码**此方法涉及获取 Java 源代码，然后将其转换为C# &ndash; 这可以手动完成，也可以通过使用自动化工具（如锐化）来完成。 

前两种技术的核心是*Java 本机接口*（JNI）。 JNI 是一个框架，它允许不以 Java 编写的应用程序与 Java 虚拟机中运行的 Java 代码进行交互。 Xamarin 使用 JNI 创建C#代码*绑定*。 

第一种方法是绑定 Java 库的一种自动化、声明性更高的方法。 它涉及到使用 Visual Studio for Mac 或 Xamarin 提供的 Visual Studio 项目类型。 &ndash; 若要成功创建这些绑定，Java 绑定库可能仍需要进行一些手动修改，但并不像纯粹的 JNI 方法那么多。 有关 Java 绑定库的详细信息，请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。 

第二种方法是使用 JNI，它的运行速度较低，但可以提供更好的控制和访问 Java 方法，而这些方法通常不能通过 Java 绑定库进行访问。 

第三种方法与前两种不同：将代码从 Java 移植到C#。 将代码从一种语言移植到另一种语言可能是一个非常费力的过程，但是可以通过称为 "*锐化*" 的工具来减少这项工作。 锐化是一种开源工具，它是一个 Java 到C#转换器。 

## <a name="summary"></a>总结

本文档提供了一些在 Xamarin Android 应用程序中可重用 Java 的不同方法的高级概述。 它介绍了绑定和托管可调用包装的概念，并讨论了用于将 Java 代码C#移植到的选项。 

## <a name="related-links"></a>相关链接

- [体系结构](~/android/internals/architecture.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [锐化](https://github.com/slluis/sharpen)
- [Java 本机接口](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
