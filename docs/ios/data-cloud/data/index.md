---
title: Xamarin iOS 数据访问
description: 本文档链接到介绍如何在 Xamarin iOS 应用程序中使用本地数据库的指南。 链接内容讨论了 SQLite.NET、ADO.NET 等。
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 163bd88349ee55af5f518a20364bbce7fe6052b0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008441"
---
# <a name="xamarinios-data-access"></a>Xamarin iOS 数据访问

Xamarin 支持数据库访问 Api，例如：

- ADO.NET 框架。
- SQLite-NET 第三方库。

本指南首先提供数据库的高级概述，然后再介绍如何设置 ADO.NET 和 SQLite.NET，以便在 Xamarin iOS 应用程序中访问 SQLite 数据库。 

本文档中的大部分代码都是完全跨平台的，将在 iOS 或 Android 上运行，而无需修改。 讨论了两个示例应用：

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) -简单数据操作将结果写入文本显示控件;
- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) –将数据操作集成到一个小型工作应用程序中，该应用程序列出并编辑了简单的数据结构。

这两个示例解决方案都包含 iOS 和 Android 示例应用程序项目。

对于 Xamarin. Forms 应用程序，请阅读使用[数据库](~/xamarin-forms/data-cloud/data/databases.md)，其中介绍了如何使用 XAMARIN 在 PCL 库中处理 SQLite。

## <a name="sections"></a>各节内容

- [介绍](introduction.md)
- [配置](configuration.md)
- [使用 SQLite.NET ORM](using-sqlite-orm.md)
- [使用 ADO.NET](using-adonet.md)
- [在应用中使用数据](using-data-in-an-app.md)

## <a name="summary"></a>总结

本章介绍了使用 SQLite 作为数据库引擎的 Xamarin 中的数据访问。 可以使用 ADO.NET 语法直接访问数据库，也可以在中C#包含 SQLite.NET ORM 并执行数据操作。

我们回顾了两个示例：一个示例包含非常简单的数据访问代码，该代码输出到一个文本字段，另一个是包含创建、读取、更新和删除功能的简单应用程序。 还介绍了线程处理，以及如何使用预填充的 SQLite 数据库来为应用程序设定种子。

有关跨平台数据访问的其他示例，请参阅我们的[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
