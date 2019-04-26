---
title: Xamarin.iOS 数据访问
description: 本文档所链接到介绍如何在 Xamarin.iOS 应用程序使用本地数据库的指南。 链接的内容讨论 SQLite.NET、 ADO.NET 和的详细信息。
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 8d2513ba1c2ae2769e81659c98f3897f33d83fbf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218033"
---
# <a name="xamarinios-data-access"></a>Xamarin.iOS 数据访问

Xamarin.iOS 支持数据库访问 Api，如：

-  ADO.NET 框架。
-  SQLite NET 第三方库。

本指南描述如何设置 ADO.NET 和 SQLite.NET 访问 SQLite 数据库在 Xamarin.iOS 应用程序中的之前通常提供数据库的高级概述。 

本文档中的代码大部分是完全跨平台，并将在 iOS 或 Android 上运行而无需修改。 有两个示例应用介绍：

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – 简单的数据操作写入结果显示为文本显示的控件;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – 集成到一个小的工作应用程序，列出并编辑简单的数据结构的数据操作。

这两个示例解决方案包含 iOS 和 Android 示例应用程序项目。

有关 Xamarin.Forms 应用程序，请阅读[使用数据库](~/xamarin-forms/app-fundamentals/databases.md)其中解释了如何使用 SQLite PCL 库中使用 Xamarin.Forms。

## <a name="sections"></a>部分

-  [介绍](introduction.md)
-  [配置](configuration.md)
-  [使用 SQLite.NET ORM](using-sqlite-orm.md)
-  [使用 ADO.NET](using-adonet.md)
-  [在应用中使用数据](using-data-in-an-app.md)

## <a name="summary"></a>总结

本章讨论了 Xamarin.iOS 使用 SQLite 作为数据库引擎中的数据访问。 可以使用 ADO.NET 语法"直接"访问数据库或可包含 SQLite.NET ORM，并在 C# 中执行数据操作。

我们讨论了两个示例： 一个包含非常简单的数据访问代码的输出到文本字段，并包括一个简单应用程序创建、 读取、 更新和删除的功能。 我们还讨论了线程处理，以及如何使用预填充的 SQLite 数据库应用程序设定种子。

有关跨平台数据访问的其他示例，请参阅我们[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
