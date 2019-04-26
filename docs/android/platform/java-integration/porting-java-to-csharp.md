---
title: 将 Java 移植到 C#
description: 第三个选项为 Xamarin.Android 应用程序中使用 Java Java 源代码移植到C#。
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: 9beb6d59c9376a404c06af7f0cd1efd985929843
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075136"
---
# <a name="porting-java-to-c"></a>将 Java 移植到 C#

_第三个选项为 Xamarin.Android 应用程序中使用 Java Java 源代码移植到C#。_

## <a name="overview"></a>概述

此方法可能感兴趣的组织的：

-  **从 Java 到切换技术堆栈C#。**
-  **必须维护C#和相同的产品的 Java 版本。**
-  **想要有常用的 Java 库的.NET 版本。**


有两种方法以 Java 代码移植到C#。 第一种方法是手动将代码移植。 这涉及到熟练的开发人员了解.NET 和 Java 和熟悉每种语言的正确惯例。 这种方法有意义的最少量的代码，或希望完全摆脱 Java 到组织C#。

第二个迁移方法是尝试并自动完成该过程使用的代码转换器，如[锐化](https://github.com/mono/sharpen)。 [锐化](https://github.com/mono/sharpen)是从 Versant 最初用于端口的代码已开放源代码转换器*db4o*从 Java 到C#。 db4o 是一种面向对象的数据库 Versant 开发在 Java 中，并在然后移植到.NET。 使用代码转换器可能会有意义的项目，必须存在于这两种语言，并且需要一些两者之间的奇偶校验。

当自动化的代码转换工具有意义的示例所示[ngit](https://github.com/mono/ngit)项目。
Ngit 是 Java 项目的端口[jgit](http://eclipse.org/)。
Jgit 本身是 Java 实现[Git](http://git-scm.com/)源代码管理系统。 若要生成C#通过 Java，ngit 程序员使用自定义自动系统来从 jgit 提取 Java 代码，应用一些修补程序，以容纳转换过程中，然后运行锐化，生成的代码C#代码。 这允许 ngit 项目能够受益于 jgit 完成的持续、 正在进行工作。

通常非完成大量工作所涉及的引导的自动化的代码转换工具，并且这可能会证明是要使用的障碍。 在许多情况下，它可能是更简单、 更轻松地为端口 JavaC#手动。



## <a name="related-links"></a>相关链接

- [通过转换工具](https://github.com/mono/sharpen)
