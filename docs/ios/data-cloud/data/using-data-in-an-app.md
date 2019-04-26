---
title: 使用 iOS 应用程序中的数据
description: 本文档介绍了 DataAccess_Adv 示例中，该示例演示了如何收集用户输入并执行创建、 读取、 更新和删除 (CRUD) 数据库中的 Xamarin.iOS 应用程序的操作。
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: e3127f85841c13422d9674bcf12373af9222afba
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153717"
---
# <a name="using-data-in-an-ios-app"></a>使用 iOS 应用程序中的数据

**DataAccess_Adv**示例演示的工作应用程序允许用户输入并*CRUD* （创建、 读取、 更新和删除） 的数据库功能。 此应用程序包含两个屏幕： 列表和数据输入窗体。 所有数据访问代码都是在 iOS 和 Android 中无需修改即可重复使用。

添加一些数据后的应用程序屏幕如下所示在 iOS 上：

 ![](using-data-in-an-app-images/image9.png "iOS 示例列表")

 ![](using-data-in-an-app-images/image10.png "iOS 示例详细信息")

在 iOS 项目如下所示 – 在本部分中所示的代码包含在**Orm**目录：

 ![](using-data-in-an-app-images/image13.png "iOS 项目树")

在 iOS 中 ViewControllers 的本机 UI 代码不在本文范围之内。
请参阅[iOS 使用表和单元格](~/ios/user-interface/controls/tables/index.md)指南以获取对 UI 控件的详细信息。

## <a name="read"></a>读取

有几个示例中的读取操作：

-  读取列表
-  读取单个记录


中的两个方法`StockDatabase`类：

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

iOS 导致的数据以不同的方式为`UITableView`。

## <a name="create-and-update"></a>创建和更新

为了简化应用程序代码，一条存储方法是提供执行插入或更新取决于是否已设置 PrimaryKey。 因为`Id`属性是否标记有`[PrimaryKey]`属性不应在代码中设置它。
此方法将检测是否值已被以前保存 （通过检查主键属性），并插入或相应地更新该对象：

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



实际应用程序通常需要一些 （如必填的字段，最小长度或其他业务规则） 的验证。
尽可能多的验证逻辑作为在共享的代码，传递验证错误，备份到平台的功能根据显示的 UI，可以实现很好的跨平台应用程序。

## <a name="delete"></a>删除

与不同`Insert`并`Update`方法，`Delete<T>`方法可接受只是主密钥值而不是一个完整`Stock`对象。
在此示例中`Stock`对象传递给该方法，但仅 Id 属性传递给`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用预填充的 SQLite 数据库文件

某些应用程序附带已用数据填充数据库。
您可以轻松地完成此操作在移动应用程序中传送你的应用与现有的 SQLite 数据库文件并对其进行访问之前将其复制到可写目录。 由于 SQLite 是多个平台使用的标准文件格式，有许多工具可用于创建一个 SQLite 数据库文件：

-  **SQLite Manager Firefox 扩展**– 适用于 Mac 和 Windows，并生成与 iOS 和 Android 兼容的文件。
-  **命令行**– 请参阅[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。


时使用您的应用程序创建分发数据库文件，请注意使用命名的表和列，确保它们与您的代码的预期，尤其是如果您使用 SQLite.NET 这将要求要与 C# 类和属性匹配的名称 （或关联的自定义属性）。

对于 iOS，应用程序中包含 sqlite 文件并确保将标有**生成操作：内容**。 中的代码放`FinishedLaunching`若要将文件复制到可写目录*之前*调用任何数据的方法。 下面的代码将名为将现有数据库复制**data.sqlite**，仅当它尚不存在。

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

任何数据访问代码 (是否 ADO.NET 或使用 SQLite.NET)，可执行此操作已完成的将有权访问的预填充数据后。


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
