---
title: Android 上的数据存储简介
ms.prod: xamarin
ms.assetid: FDAC0771-4749-4758-865A-F1BD190CA54B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 69d5222bb6c50870d0c42bea6ff71236e3d1580c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754555"
---
# <a name="introduction"></a>介绍

## <a name="when-to-use-a-database"></a>何时使用数据库

虽然移动设备的存储和处理能力增加，但手机和平板电脑仍然会滞后于其桌面和便携式计算机。 出于此原因，需要花费一些时间来规划应用的数据存储体系结构，而不是仅仅假设数据库始终是正确的答案。 有多种不同的选项可满足不同的要求，例如：

- **首选项**– Android 提供用于存储简单的键值对的内置机制。 如果要存储简单的用户设置或少量数据（如个性化信息），请使用平台的本机功能来存储此类信息。
- **文本文件**-已下载内容的用户输入或缓存（例如 HTML）可以直接存储在文件系统上。 使用适当的文件命名约定来帮助您组织文件和查找数据。
- **序列化数据文件**–对象可以在文件系统上保存为 XML 或 JSON。 .NET framework 包含用于使对象序列化和反序列化的库。 使用适当的名称来组织数据文件。
- **数据库**– SQLite 数据库引擎在 Android 平台上可用，适用于存储需要查询、排序或以其他方式操作的结构化数据。 数据库存储适用于具有多个属性的数据列表。
- **映像文件**–尽管可以在移动设备上的数据库中存储二进制数据，但建议将它们直接存储在文件系统中。 如果需要，可以将文件名存储在数据库中，以将图像与其他数据相关联。 处理大型映像或大量映像时，最好计划一个缓存策略，以删除不再需要的文件，避免使用用户的所有存储空间。

如果数据库是适用于你的应用程序的正确存储机制，则本文档的其余部分将讨论如何在 Xamarin 平台上使用 SQLite。

## <a name="advantages-of-using-a-database"></a>使用数据库的优点

在移动应用中使用 SQL 数据库有许多优点：

- SQL 数据库允许有效存储结构化数据。
- 可以通过复杂的查询来提取特定数据。
- 可以对查询结果进行排序。
- 可以聚合查询结果。
- 具有现有数据库技能的开发人员可以利用他们的知识来设计数据库和数据访问代码。
- 可以在移动应用程序中重复使用（整体或部分）已连接应用程序的服务器组件中的数据模型。

## <a name="sqlite-database-engine"></a>SQLite 数据库引擎

SQLite 是 Google 为其移动平台采用的开源数据库引擎。 SQLite 数据库引擎内置于两个操作系统中，因此开发人员无需再使用它。 SQLite 适用于跨平台移动开发，因为：

- 数据库引擎的规模非常小，可以快速轻松地进行移植。
- 数据库存储在一个文件中，该文件在移动设备上易于管理。
- 文件格式易于跨平台使用：无论是32还是64位，以及大或小字节序系统。
- 它实现了大部分 SQL92 功能标准。

因为 SQLite 的设计速度非常小，但使用时有一些注意事项：

- 某些外部联接语法不受支持。
- 仅支持表重命名和 ADDCOLUMN。 不能对架构执行其他修改。
- 视图为只读。

你可以在网站上了解有关 SQLite 的详细信息- [SQLite.org](http://SQLite.org) -但是，此文档和关联的示例中包含了使用 sqlite 的所有所需信息。 自 Android 2 以来，Android 中已支持 SQLite 数据库引擎。
尽管本章未涵盖，但也可在 Windows Phone 和 Windows 应用程序上使用 SQLite。

## <a name="windows-and-windows-phone"></a>Windows 和 Windows Phone

SQLite 还可以在 Windows 平台上使用，但本文档中未介绍这些平台。
有关详细信息，请参阅[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)和[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究，并查看[Tim Heuer 的博客](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
