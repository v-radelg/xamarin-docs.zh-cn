---
title: 自定义绑定
description: 通过编辑控制绑定过程的元数据，可以自定义的 Xamarin.Android 绑定。 这些手动修改通常是必需的解决生成错误的定型所获得的 API，这样就与更加一致C#/.NET。 这些指南介绍了此元数据的结构、 如何修改的元数据，以及如何使用 JavaDoc 恢复方法参数的名称。
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 44bff372225ee1bf555eb3eeb34da918830980b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957559"
---
# <a name="customizing-bindings"></a>自定义绑定

_通过编辑控制绑定过程的元数据，可以自定义的 Xamarin.Android 绑定。这些手动修改通常是必需的解决生成错误的定型所获得的 API，这样就与更加一致C#/.NET。这些指南介绍了此元数据的结构、 如何修改的元数据，以及如何使用 JavaDoc 恢复方法参数的名称。_


## <a name="overview"></a>概述
 
Xamarin.Android 可自动执行大部分绑定过程中;但是，在某些情况下手动修改被需要解决以下问题：

-   解决生成错误引起的缺少类型、 经过模糊处理的类型、 重复的名称、 类的可见性问题，以及其他无法解析的情况下由 Xamarin.Android 工具。 

-   更改 Xamarin.Android 使用要绑定到中的不同类型的 Android API 映射C#(例如，许多开发人员更喜欢映射 Java`int`常量C#`enum`常量)。

-   正在删除未使用不需要绑定的类型。 

-   添加基础 Java API 中有任何相对应的类型。 

可以通过修改控制绑定过程的元数据进行部分或所有这些更改。


## <a name="guides"></a>参考线

以下指南描述了控制绑定过程的元数据，并解释如何修改此元数据来解决这些问题：

-   [Java 绑定元数据](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供进入 Java 绑定元数据的概述。
    它介绍了完成 Java 绑定库，有时需要的各种手动步骤，并介绍如何来调整一个绑定以更紧密地遵循.NET 设计指南所公开的 API。

-   [使用 Javadoc 命名参数](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)介绍了如何通过使用从绑定的 Java 项目生成的 Javadoc 恢复 Java 绑定项目中的参数名称。


 

