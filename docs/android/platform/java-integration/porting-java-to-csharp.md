---
title: 将 Java 移植C#到 for Xamarin
description: 在 Xamarin Android 应用程序中使用 Java 的第三个选项是将 Java 源代码移植到C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: c6627f585326848c5221729ca94071b00651c59e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511172"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>将 Java 移植C#到 for Xamarin

此方法对于以下组织可能很感兴趣:

- **正在将技术堆栈从 Java 切换C#到。**
- **必须维护同C#一个产品的和 Java 版本。**
- **希望使用 .NET 版本的常用 Java 库。**

可以通过两种方式将 Java 代码移植C#到。 第一种方法是手动移植代码。 这涉及了解 .NET 和 Java, 并熟悉适用于每种语言的适当惯例的技能专家。 此方法最适用于少量代码或希望完全从 Java 移出到C#的组织。

第二种移植方法是使用代码转换器 (如[锐化](https://github.com/mono/sharpen)) 来尝试和自动执行此过程。 [锐化](https://github.com/mono/sharpen)是 Versant 中的开源转换器, 最初用于将*db4o*从 Java 移植到C#的代码。 db4o 是一个面向对象的数据库, 该数据库 Versant 以 Java 开发, 然后移植到 .NET。 对于必须同时存在于这两种语言中并且需要在两者之间进行某种奇偶校验的项目, 使用代码转换器可能非常有用。

可以在[ngit](https://github.com/mono/ngit)项目中查看自动代码转换工具的含义。
Ngit 是 Java 项目[jgit](http://eclipse.org/)的端口。
Jgit 本身是[Git](http://git-scm.com/)源代码管理系统的 Java 实现。 若要C#从 Java 生成代码, ngit 程序员使用自定义自动系统从 jgit 中提取 Java 代码, 应用一些修补程序来容纳转换过程, 然后运行锐化以生成C#代码。 这使 ngit 项目可以从在 jgit 上完成的连续、正在进行的工作中获益。

在引导自动代码转换工具时, 经常会遇到不太简单的工作量, 这可能是使用的障碍。 在许多情况下, 可以更简单、更轻松地将 Java C#移植到。

## <a name="related-links"></a>相关链接

- [锐化转换工具](https://github.com/mono/sharpen)
