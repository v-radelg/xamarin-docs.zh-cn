---
title: 在 Xamarin.iOS 应用程序中的数据存储空间简介
description: 本文档介绍 Xamarin.iOS 应用程序中的数据存储的各种方式，并提供了有关优势的 SQLite 的特定信息。
ms.prod: xamarin
ms.assetid: B1994468-FD06-4FD9-96B3-FCEBB13A972A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 5df001926512b182c19ccfcf896057e88f5a597a
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650247"
---
# <a name="introduction-to-data-storage-in-xamarinios-apps"></a>在 Xamarin.iOS 应用程序中的数据存储空间简介

## <a name="when-to-use-a-database"></a>何时使用数据库

手机和平板电脑仍递增的移动设备的存储和处理功能，而落后于他们的桌面&amp;便携式计算机对应项。 出于此原因值得花费一些时间来规划您的应用程序的数据存储体系结构而不只假定数据库正确的答案的时间。 有多种不同的选项，满足不同的要求，如：

-  **首选项**– iOS 提供内置的机制，用于存储数据的简单键 / 值对。 如果要存储的少量数据 （如个性化设置信息） 或简单的用户设置然后使用此平台的本机功能来存储此类信息。 适用于 iOS 您还可以充分利用此数据，同时为备份和同步具有多个设备的用户的 iCloud 同步。
-  **文本文件**– 用户输入或的缓存下载的内容 （例如。 可以直接在文件系统上存储 HTML)。 使用适当的文件命名约定来帮助你组织的文件并查找数据。
-  **序列化数据文件**– 对象可保留 XML 或 json 格式在文件系统。 .NET framework 包括进行序列化和反序列化对象轻松的库。 使用合适的名称来组织数据文件。
-  **数据库**– SQLite 数据库引擎是可用的 iOS，非常适合存储结构化的数据，您需要查询、 排序或以其他方式操作。 数据库存储适合于具有许多属性的数据的列表。
-  **图像文件**– 尽管可以在移动设备上的数据库中存储二进制数据，但建议将其存储在文件系统中直接。 如有必要您可以在数据库中以与图像关联与其他数据存储的文件名。 处理大型映像或映像的很多，很好的做法规划删除不再需要避免使用用户的所有存储空间的文件的缓存策略。


如果数据库是您的应用程序的适当的存储机制，此文档的其余部分将讨论如何在 Xamarin 平台上使用 SQLite。

## <a name="advantages-of-using-a-database"></a>使用数据库的优点

有很多优点到移动应用程序中使用的 SQL 数据库：

-  SQL 数据库允许高效地存储结构化数据。
-  可以使用复杂查询提取特定的数据。
-  可以对查询结果进行排序。
-  查询结果可进行聚合。
-  使用现有数据库技术的开发人员可以利用他们的知识来设计数据库和数据访问代码。
-  从连接的应用程序的服务器组件的数据模型可能会重新使用 （整体或部分） 中的移动应用程序。


## <a name="sqlite-database-engine"></a>SQLite 数据库引擎

SQLite 是为其移动平台采用的 Apple 的开源数据库引擎。 因此不，开发人员能够利用它的任何额外工作，SQLite 数据库引擎是内置到 iOS。 SQLite 是非常适用于跨平台移动开发的因为：

-  数据库引擎是小型、 快速且可轻松地移植。
-  数据库存储在单个文件，这是在移动设备上轻松管理。
-  跨平台可以方便使用的文件格式是： 是否 32 位或 64 位和大或小-端系统。
-  它实现大部分标准 sql92 功能。


SQLite 旨在作为小巧快捷，因为它的使用有一些需要注意的问题：

-  不支持某些外部联接语法。
-  仅限于表重命名和 ADDCOLUMN 受支持。 无法执行对您的架构进行其他修改。
-  视图是只读的。


您可以了解有关 SQLite 的网站- [SQLite.org](http://SQLite.org) -但是，您需要通过 Xamarin 使用 SQLite 的所有信息包含在本文档和相关示例。 SQLite 数据库引擎是内置于所有版本的 iOS。
尽管这一章中未涉及，SQLite 也是可在 Windows Phone 和 Windows 应用程序使用。

## <a name="windows-and-windows-phone"></a>Windows 和 Windows Phone

尽管本文档未涵盖这些平台，还可以在 Windows 平台上使用 SQLite。
更多信息，请参阅[Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)并[Tasky Pro](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/case_study%3A_tasky)案例研究，并查看[Tim Heuer 的博客](http://timheuer.com/blog/archive/2012/06/28/seeding-your-metro-style-app-with-sqlite-database.aspx)。



## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 数据访问](~/xamarin-forms/data-cloud/data/databases.md)
