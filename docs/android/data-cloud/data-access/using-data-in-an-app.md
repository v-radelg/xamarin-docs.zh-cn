---
title: 在 Android 应用中使用数据
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 922b1fa411a176df580050384e7555120fd68137
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754457"
---
# <a name="using-data-in-an-app"></a>在应用中使用数据

**DataAccess_Adv**示例显示允许用户输入和 CRUD （创建、读取、更新和删除）数据库功能的工作应用程序。 该应用程序包含两个屏幕：一个列表和一个数据输入窗体。 所有数据访问代码在 iOS 和 Android 中可重复使用，而无需修改。

添加一些数据后，应用程序屏幕上的应用程序屏幕将如下所示：

![Android 示例列表](using-data-in-an-app-images/image11.png "Android 示例列表")

![Android 示例详细信息](using-data-in-an-app-images/image12.png "Android 示例详细信息")

Android 项目显示在下面 &ndash; 此部分中所示代码包含在**Orm**目录中：

![Android 项目树](using-data-in-an-app-images/image14.png "Android 项目树")

Android 中的活动的本机 UI 代码超出了本文档的范围。 有关 UI 控件的详细信息，请参阅[Android listview 和适配器](~/android/user-interface/layouts/list-view/index.md)指南。

## <a name="read"></a>读取

示例中有两个读取操作：

- 阅读列表
- 读取单个记录

@No__t_0 类中的两个方法是：

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

Android 以 `ListView` 的形式呈现数据。

## <a name="create-and-update"></a>创建和更新

为了简化应用程序代码，提供了一个 save 方法，该方法根据 PrimaryKey 是否已设置，执行插入或更新。 由于 `Id` 属性使用 `[PrimaryKey]` 属性进行标记，因此不应在代码中对其进行设置。 此方法将检测是否之前已保存值（通过检查主键属性），并相应地插入或更新对象：

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```

实际的应用程序通常需要进行一些验证（例如，必填字段、最小长度或其他业务规则）。 良好的跨平台应用程序在共享代码中实现尽可能多的验证逻辑，将验证错误传递给 UI，以根据平台的功能显示。

## <a name="delete"></a>删除

与 `Insert` 和 `Update` 方法不同，`Delete<T>` 方法只接受主键值而不接受完整的 `Stock` 对象。 在此示例中，将一个 `Stock` 对象传递到方法中，但仅将 Id 属性传递到 `Delete<T>` 方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用预先填充的 SQLite 数据库文件

某些应用程序附带已填充数据的数据库。 可以在移动应用程序中轻松完成此项工作，方法是：将现有 SQLite 数据库文件与应用程序一起传送，并将其复制到可写目录，然后再访问该文件。 由于 SQLite 是在许多平台上使用的标准文件格式，因此可以使用多种工具来创建 SQLite 数据库文件：

- **SQLite 管理器 Firefox 扩展**&ndash; 在 Mac 和 Windows 上工作，并生成与 IOS 和 Android 兼容的文件。

- **命令行**&ndash; 参阅[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。

创建与应用程序一起分发的数据库文件时，请注意表和列的命名，以确保它们与代码所需的名称匹配，尤其是在使用 SQLite.NET 时，这会希望名称与C#类和属性匹配（或关联的自定义特性）。

若要确保某些代码在 Android 应用中的其他任何位置之前运行，可以将其放在第一个要加载的活动中，也可以创建在任何活动之前加载的 `Application` 子类。 下面的代码演示了一个 `Application` 子类，该子类将现有数据库文件**数据**复制到 **/Resources/Raw/** 目录中。

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
