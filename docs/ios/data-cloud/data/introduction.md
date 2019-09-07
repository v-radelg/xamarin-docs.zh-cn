---
title: Xamarin iOS 应用中的数据存储简介
description: 本文档介绍了在 Xamarin iOS 应用程序中存储数据的各种方法，并提供有关 SQLite 优点的具体信息。
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 4000e4cc5d260457c0e0da275e3a7beecafd1a98
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767017"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>Xamarin iOS 应用中的数据存储简介

## <a name="when-to-use-a-database"></a>何时使用数据库

虽然移动设备的存储和处理能力正在增加，但手机和平板电脑仍然会滞后于&amp;其桌面便携式计算机的背面。 出于此原因，需要花费一些时间来规划应用的数据存储体系结构，而不是仅仅假设数据库始终是正确的答案。 有多种不同的选项可满足不同的要求，例如：

- **首选项**– iOS 提供用于存储简单的键值对的内置机制。 如果要存储简单的用户设置或少量数据（如个性化信息），请使用平台的本机功能来存储此类信息。 对于 iOS，还可以利用此数据的 iCloud 同步，这两种数据都适用于具有多个设备的用户的备份和同步。
- **文本文件**-已下载内容的用户输入或缓存（例如 HTML）可以直接存储在文件系统上。 使用适当的文件命名约定来帮助您组织文件和查找数据。
- **序列化数据文件**–对象可以在文件系统上保存为 XML 或 JSON。 .NET framework 包含用于使对象序列化和反序列化的库。 使用适当的名称来组织数据文件。
- **数据库**– SQLite 数据库引擎可用，适用于存储需要查询、排序或以其他方式操作的结构化数据。 数据库存储适用于具有多个属性的数据列表。
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

SQLite 是一个开源数据库引擎，它已被 Apple 用于其移动平台。 SQLite 数据库引擎内置于 iOS 中，因此开发人员无需再使用它。 SQLite 适用于跨平台移动开发，因为：

- 数据库引擎的规模非常小，可以快速轻松地进行移植。
- 数据库存储在一个文件中，该文件在移动设备上易于管理。
- 文件格式易于跨平台使用：无论是32还是64位，以及大或小字节序系统。
- 它实现了大部分 SQL92 功能标准。

因为 SQLite 的设计速度非常小，但使用时有一些注意事项：

- 某些外部联接语法不受支持。
- 仅支持表重命名和 ADDCOLUMN。 不能对架构执行其他修改。
- 视图为只读。

你可以在网站上了解有关 SQLite 的详细信息- [SQLite.org](http://SQLite.org) -但是，此文档和关联的示例中包含了使用 sqlite 的所有所需信息。 SQLite 数据库引擎内置于所有版本的 iOS。
尽管本章未涵盖，但也可在 Windows Phone 和 Windows 应用程序上使用 SQLite。

## <a name="windows-and-windows-phone"></a>Windows 和 Windows Phone

SQLite 还可以在 Windows 平台上使用，但本文档中未介绍这些平台。
有关详细信息，请参阅[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)和[Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky)案例研究，并查看[Tim Heuer 的博客](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
