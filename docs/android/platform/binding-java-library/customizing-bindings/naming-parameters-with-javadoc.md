---
title: 使用 Javadoc 命名参数
description: 此文章介绍了如何使用生成 Java 项目中的 Javadoc 恢复 Java 绑定项目中的参数名称。
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: e394377043953a297afed36a3ce0747a3e6d1512
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60955866"
---
# <a name="naming-parameters-with-javadoc"></a>使用 Javadoc 命名参数

_此文章介绍了如何使用生成 Java 项目中的 Javadoc 恢复 Java 绑定项目中的参数名称。_


## <a name="overview"></a>概述

绑定现有的 Java 库，会丢失一些有关绑定 API 元数据。 特别是对方法的参数的名称。 参数名称将显示为`p0`， `p1`，等等。这是因为 Java`.class`文件不会保留在 Java 源代码中使用的参数名称。 

如果它有权访问 Javadoc HTML 从原始库，Xamarin.Android Java 绑定项目可以提供参数名称。 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>将 Javadoc HTML 集成到 Java 绑定项目

将 Javadoc HTML 集成到 Java 绑定项目是一个手动过程包括以下步骤： 

1.  下载库 Javadoc
2.  编辑`.csproj`文件，并添加`<JavaDocPaths>`属性：
3.  清除并重新生成项目

完成此操作后，原始 Java 参数名称应会出现在由 Java 绑定项目绑定的 Api。 


> [!NOTE]
> 没有大量的 JavaDoc 输出中的变体。 。JAR 绑定工具链不支持每个单个可能的排列，因此某些参数可能未正确命名。


## <a name="summary"></a>总结

本文介绍如何使用 Java 绑定项目中的 Javadoc 提供的绑定 Api 的含义参数名称。 

