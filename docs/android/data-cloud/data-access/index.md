---
title: Xamarin.Android Data Access
description: 大多数应用程序具有一些要求将数据保存在本地设备上。 除非数据量非常小，这通常需要一个数据库和管理数据库的访问权限的应用程序中的数据层。  Android 包含 SQLite 数据库引擎内置和 Xamarin 的平台通过简化的存储和检索数据的访问权限。 本文档演示如何跨平台的方式访问 SQLite 数据库。
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 6858e290d93007d6054ba0ef63dce86e6e2e53e3
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649621"
---
# <a name="xamarinandroid-data-access"></a>Xamarin.Android Data Access

_大多数应用程序具有一些要求将数据保存在本地设备上。除非数据量非常小，这通常需要一个数据库和管理数据库的访问权限的应用程序中的数据层。Android 包含 SQLite 数据库引擎内置和 Xamarin 的平台通过简化的存储和检索数据的访问权限。本文档演示如何跨平台的方式访问 SQLite 数据库。_

## <a name="data-access-overview"></a>数据访问概述

大多数应用程序具有一些要求将数据保存在本地设备上。 除非数据量非常小，这通常需要一个数据库和管理数据库的访问权限的应用程序中的数据层。 Android 这两个具有"内置"SQLite 数据库引擎和 Xamarin 的平台，其中随附了 SQLite 数据提供程序简化对数据的访问。

Xamarin.Android 支持数据库访问 Api，如：

- ADO.NET 框架。
- SQLite NET 第三方库。

在本部分中的代码大部分是完全跨平台，并将在 iOS 或 Android 上运行而无需修改。 有两个示例应用介绍：

- [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash;简单的数据操作写入结果显示为文本显示的控件;

- [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash;集成到一个小的工作应用程序，列出并编辑简单的数据结构的数据操作。

这两个示例解决方案包含 iOS 和 Android 示例应用程序项目。

有关 Xamarin.Forms 应用程序，请阅读[使用数据库](~/xamarin-forms/data-cloud/data/databases.md)其中解释了如何使用 SQLite PCL 库中使用 Xamarin.Forms。

在本部分中的主题讨论 Xamarin.Android 使用 SQLite 作为数据库引擎中的数据访问。 可以通过使用 ADO.NET 语法"直接"访问数据库或可包含 SQLite.NET ORM，并在 C# 中执行数据操作。

查看两个示例： 一个包含非常简单的数据访问代码的输出到文本字段，并包括一个简单应用程序创建、 读取、 更新和删除的功能。 线程处理以及如何播种使用预填充的 SQLite 数据库应用程序还讨论了。

有关跨平台数据访问的其他示例，请参阅我们[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据方案](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin.Forms 数据访问](~/xamarin-forms/data-cloud/data/databases.md)
