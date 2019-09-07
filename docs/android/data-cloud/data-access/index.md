---
title: Xamarin Android 数据访问
description: 大多数应用程序都需要在本地将数据保存在设备上。 除非数据量完全小，否则这通常需要应用程序中的数据库和数据层来管理数据库访问。  Android 具有 SQLite 数据库引擎 "内置"，Xamarin 的平台简化了存储和检索数据的访问。 本文档说明如何以跨平台的方式访问 SQLite 数据库。
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2343603199661ea39b1f0af172ce0ccf48a2cd66
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754587"
---
# <a name="xamarinandroid-data-access"></a>Xamarin Android 数据访问

_大多数应用程序都需要在本地将数据保存在设备上。除非数据量完全小，否则这通常需要应用程序中的数据库和数据层来管理数据库访问。Android 具有 SQLite 数据库引擎 "内置"，Xamarin 的平台简化了存储和检索数据的访问。本文档说明如何以跨平台的方式访问 SQLite 数据库。_

## <a name="data-access-overview"></a>数据访问概述

大多数应用程序都需要在本地将数据保存在设备上。 除非数据量完全小，否则这通常需要应用程序中的数据库和数据层来管理数据库访问。 Android 两者都具有 "内置" 的 SQLite 数据库引擎，并通过包含 SQLite 数据提供程序的 Xamarin 平台简化对数据的访问。

Xamarin 支持数据库访问 Api，例如：

- ADO.NET 框架。
- SQLite-NET 第三方库。

本节中的大部分代码都是完全跨平台的，将在 iOS 或 Android 上运行，而无需修改。 讨论了两个示例应用：

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)&ndash;简单的数据操作将结果写入文本显示控件;

- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)&ndash;将数据操作集成到一个小型工作应用程序中，该应用程序可列出和编辑简单的数据结构。

这两个示例解决方案都包含 iOS 和 Android 示例应用程序项目。

对于 Xamarin. Forms 应用程序，请阅读使用[数据库](~/xamarin-forms/data-cloud/data/databases.md)，其中介绍了如何使用 XAMARIN 在 PCL 库中处理 SQLite。

本部分中的主题介绍使用 SQLite 作为数据库引擎的 Xamarin 中的数据访问。 可以通过使用 ADO.NET 语法来直接访问数据库，也可以在中C#包含 SQLite.NET ORM 并执行数据操作。

评审了两个示例：一个示例包含非常简单的数据访问代码，该代码输出到一个文本字段，另一个是包含创建、读取、更新和删除功能的简单应用程序。 此外，还讨论了如何使用预填充的 SQLite 数据库来为应用程序设定种子。

有关跨平台数据访问的其他示例，请参阅我们的[Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)案例研究。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
