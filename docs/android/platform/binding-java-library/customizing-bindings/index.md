---
title: 自定义绑定
description: 可以通过编辑控制绑定过程的元数据来自定义 Xamarin Android 绑定。 通常需要执行这些手动修改来解决生成错误，并调整生成的 API，使其与C#/.NET. 更一致 这些指南介绍了此元数据的结构，如何修改元数据，以及如何使用 JavaDoc 来恢复方法参数的名称。
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020644"
---
# <a name="customizing-bindings"></a>自定义绑定

_可以通过编辑控制绑定过程的元数据来自定义 Xamarin Android 绑定。通常需要执行这些手动修改来解决生成错误，并调整生成的 API，使其与C#/.NET. 更一致这些指南介绍了此元数据的结构，如何修改元数据，以及如何使用 JavaDoc 来恢复方法参数的名称。_

## <a name="overview"></a>概述

Xamarin 会自动执行许多绑定过程;但是，在某些情况下，需要手动修改才能解决以下问题：

- 解决由于缺少类型、模糊类型、重复名称、类可见性问题以及无法由 Xamarin Android 工具解决的其他情况导致的生成错误。 

- 更改 Xamarin 使用的映射，将 Android API 绑定到中C#的不同类型（例如，许多开发人员更喜欢将 Java `int`常量映射到C#`enum`常量）。

- 删除不需要绑定的未使用的类型。 

- 添加在基础 Java API 中没有对应项的类型。 

您可以通过修改控制绑定过程的元数据来进行部分或全部更改。

## <a name="guides"></a>参考线

以下指南介绍了控制绑定过程的元数据，并说明了如何修改此元数据以解决这些问题：

- [Java 绑定元数据](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供进入 Java 绑定的元数据的概述。
    它介绍了完成 Java 绑定库时有时需要执行的各种手动步骤，并说明如何对绑定公开的 API 进行调整，以更严格地遵循 .NET 设计准则。

- [使用 Javadoc 命名参数](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md)介绍了如何使用从绑定 java 项目生成的 Javadoc 来恢复 Java 绑定项目中的参数名称。
