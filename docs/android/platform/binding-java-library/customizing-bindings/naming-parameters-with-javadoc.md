---
title: 用 Javadoc 命名参数
description: 本文介绍如何使用从 Java 项目生成的 Javadoc 恢复 Java 绑定项目中的参数名称。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027670"
---
# <a name="naming-parameters-with-javadoc"></a>用 Javadoc 命名参数

_本文介绍如何使用从 Java 项目生成的 Javadoc 恢复 Java 绑定项目中的参数名称。_

## <a name="overview"></a>概述

绑定现有 Java 库时，某些有关绑定 API 的元数据将丢失。 特定于方法的参数名称。 参数名称将显示为 `p0`、`p1`等。这是因为 Java `.class` 文件不会保留 Java 源代码中使用的参数名称。 

如果一个 Xamarin Java 绑定项目有权访问原始库中的 Javadoc HTML，则可以提供这些参数的名称。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>将 Javadoc HTML 集成到 Java 绑定项目

将 Javadoc HTML 集成到 Java 绑定项目是一个手动过程，其中包括以下步骤： 

1. 下载库的 Javadoc
2. 编辑 `.csproj` 文件并添加 `<JavaDocPaths>` 属性：
3. 清除并重新生成项目

完成此操作后，最初的 Java 参数名称应存在于由 Java 绑定项目绑定的 Api 中。 

> [!NOTE]
> JavaDoc 输出有很大差异。 此.JAR 绑定工具链不支持每个可能的排列，因此某些参数可能未正确命名。

## <a name="summary"></a>总结

本文介绍如何在 Java 绑定项目中使用 Javadoc 为绑定 Api 提供含义参数名称。 
