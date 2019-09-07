---
title: 在 iOS 应用中使用数据
description: 本文档介绍了 DataAccess_Adv 示例，该示例演示如何在 Xamarin iOS 应用程序中收集用户输入并执行创建、读取、更新和删除（CRUD）数据库操作。
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 9441596cd457c3cc3a881e5db319ec3bbfc5a312
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766848"
---
# <a name="using-data-in-an-ios-app"></a>在 iOS 应用中使用数据

**DataAccess_Adv**示例显示允许用户输入和*CRUD* （创建、读取、更新和删除）数据库功能的工作应用程序。 该应用程序包含两个屏幕：一个列表和一个数据输入窗体。 所有数据访问代码在 iOS 和 Android 中可重复使用，而无需修改。

添加一些数据后，应用程序屏幕上的应用程序屏幕将如下所示：

 ![](using-data-in-an-app-images/image9.png "iOS 示例列表")

 ![](using-data-in-an-app-images/image10.png "iOS 示例详细信息")

IOS 项目如下所示–此部分中所示的代码包含在**Orm**目录中：

 ![](using-data-in-an-app-images/image13.png "iOS 项目树")

IOS 中的 ViewControllers 的本机 UI 代码超出了本文档的范围。
有关 UI 控件的详细信息，请参阅[使用表和单元格的 iOS](~/ios/user-interface/controls/tables/index.md)指南。

## <a name="read"></a>读取

示例中有两个读取操作：

- 阅读列表
- 读取单个记录

`StockDatabase`类中的两个方法是：

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

iOS 以不同的`UITableView`形式呈现数据。

## <a name="create-and-update"></a>创建和更新

为了简化应用程序代码，提供了一个 save 方法，该方法根据 PrimaryKey 是否已设置，执行插入或更新。 由于属性`Id`是`[PrimaryKey]`用特性标记的，因此不应在代码中对其进行设置。
此方法将检测是否之前已保存值（通过检查主键属性），并相应地插入或更新对象：

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

实际的应用程序通常需要进行一些验证（例如，必填字段、最小长度或其他业务规则）。
良好的跨平台应用程序在共享代码中实现尽可能多的验证逻辑，将验证错误传递给 UI，以根据平台的功能显示。

## <a name="delete"></a>删除

`Delete<T>` `Stock`与和方法`Update`不同，方法只接受主键值而不接受完整的对象。 `Insert`
在此示例`Stock`中，将对象传递到方法，但仅将 Id 属性传递`Delete<T>`给方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用预先填充的 SQLite 数据库文件

某些应用程序附带已填充数据的数据库。
可以在移动应用程序中轻松完成此项工作，方法是：将现有 SQLite 数据库文件与应用程序一起传送，并将其复制到可写目录，然后再访问该文件。 由于 SQLite 是在许多平台上使用的标准文件格式，因此可以使用多种工具来创建 SQLite 数据库文件：

- **SQLite 管理器 Firefox 扩展**–适用于 Mac 和 Windows，并生成与 IOS 和 Android 兼容的文件。
- **命令行**–请参阅[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。

创建与应用程序一起分发的数据库文件时，请注意表和列的命名，以确保它们与代码所需的名称匹配，尤其是在使用 SQLite.NET 时，这会希望名称与C#类和属性匹配（或关联的自定义特性）。

对于 iOS，请在应用程序中包含 sqlite 文件，并确保其标记**为 "生成操作"：内容**。 将代码放入， `FinishedLaunching`以将文件复制到可写目录，*然后*调用任何数据方法。 下面的代码将复制名为**数据**的现有数据库，仅当它尚不存在时。

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

在此完成后执行的任何数据访问代码（无论是 ADO.NET 还是使用 SQLite.NET）都将有权访问预填充的数据。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
