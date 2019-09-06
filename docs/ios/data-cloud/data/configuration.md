---
title: 在 Xamarin 中配置 SQLite
description: 本文档介绍如何在 Xamarin iOS 应用程序中确定 SQLite 数据库文件的位置。 无论选择的数据访问机制如何，这些概念都是相关的。
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: c0a8f57e3f4f351cf5b874ded2639b975ea71cad
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281911"
---
# <a name="configuring-sqlite-in-xamarinios"></a>在 Xamarin 中配置 SQLite

若要在 Xamarin iOS 应用程序中使用 SQLite，需要确定数据库文件的正确文件位置。

## <a name="database-file-path"></a>数据库文件路径

无论使用哪种数据访问方法，都必须先创建数据库文件，然后才能使用 SQLite 存储数据。 根据目标位置，文件位置会有所不同。 对于 iOS，你可以使用环境类构造有效路径，如以下代码片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

在确定存储数据库文件的位置时，还需要考虑其他一些事项。 在 iOS 上，你可能希望自动备份（或不）数据库。

如果要在跨平台应用程序中的每个平台上使用不同的位置，可以使用如下所示的编译器指令为每个平台生成不同的路径：

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

有关 iOS 上使用的文件位置的详细信息，请参阅使用[文件系统](~/ios/app-fundamentals/file-system.md)一文。 有关使用编译器指令编写特定于每个平台的代码的详细信息，请参阅[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档。

## <a name="threading"></a>线程

不应跨多个线程使用同一个 SQLite 数据库连接。 请小心打开，使用，然后关闭在同一线程上创建的任何连接。

若要确保代码不尝试同时从多个线程访问 SQLite 数据库，请在每次访问数据库时手动执行锁定，如下所示：

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

所有数据库访问（读取、写入、更新等）都应该使用同一个锁进行包装。 必须小心，以避免死锁情况，方法是确保 lock 子句内的工作保持简单，并且不会调用其他也可能采用锁的方法！


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
