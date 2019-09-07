---
title: 配置
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 5ebafa70239305210da631c3e9c34278f83b272b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754667"
---
# <a name="configuration"></a>配置

若要在 Xamarin Android 应用程序中使用 SQLite，需要确定数据库文件的正确文件位置。

## <a name="database-file-path"></a>数据库文件路径

无论使用哪种数据访问方法，都必须先创建数据库文件，然后才能使用 SQLite 存储数据。 根据目标位置，文件位置会有所不同。 对于 Android，你可以使用环境类构造有效路径，如以下代码片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

在确定存储数据库文件的位置时，还需要考虑其他一些事项。 例如，在 Android 上，你可以选择是使用内部存储还是外部存储。

如果要在跨平台应用程序中的每个平台上使用不同的位置，可以使用如下所示的编译器指令为每个平台生成不同的路径：

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

有关在 Android 中使用文件系统的提示，请参阅[浏览文件](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files)食谱。 有关使用编译器指令编写特定于每个平台的代码的详细信息，请参阅[构建跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文档。

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
- [Android 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
