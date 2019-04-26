---
title: 配置
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: ac44981c3b82f0fbec6b18396e15502428b9d62f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013109"
---
# <a name="configuration"></a>配置

若要将需要确定您的数据库文件的正确的文件位置在 Xamarin.Android 应用程序中使用 SQLite。

## <a name="database-file-path"></a>数据库文件路径

无论使用哪种数据访问方法，可以使用 SQLite 存储数据之前，必须创建数据库文件。 根据您面向的哪种平台的文件位置将有所不同。 适用于 Android 可用于环境类构造有效的路径，如下面的代码段中所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

没有要确定用于存储数据库文件的位置时需要考虑的其他事项。 例如，在 Android 上你可以选择是否使用内部或外部存储。

如果你想要在跨平台应用程序中的每个平台上使用的其他位置，可用的编译器指令所示来生成每个平台的不同路径：

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

有关在 Android 中使用文件系统上的提示，请参阅[浏览文件](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files)方案。 请参阅[生成跨平台应用程序](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)使用编译器指令将写入每个平台特定代码的详细信息的文档。

## <a name="threading"></a>线程

不应跨多个线程使用相同的 SQLite 数据库连接。 请注意打开、 使用，然后关闭任何同一线程创建的连接。

若要确保你的代码不尝试从多个线程同时访问 SQLite 数据库，手动采用的锁时想要访问数据库时，此类：

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

应使用同一个锁包装所有数据库访问权限 （读取、 写入、 更新等）。 必须格外小心，以避免死锁情况下，通过确保锁子句中的工作保持简单并不会不调用其他方法的可能还需要一个锁 ！


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据方案](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
