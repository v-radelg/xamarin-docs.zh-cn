---
title: 将 SQLite.NET 与 Xamarin 配合使用
description: SQLite.NET PCL NuGet 库为 Xamarin iOS 应用提供了简单的数据访问机制。 本文档概述了如何使用此库。
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: e229ad37e8cd5ff940fb5abece7b782b84336d50
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008166"
---
# <a name="using-sqlitenet-with-xamarinios"></a>将 SQLite.NET 与 Xamarin 配合使用

Xamarin 推荐的 SQLite.NET 库是一个基本的 ORM，可用于在 iOS 设备上的本地 SQLite 数据库中存储和检索对象。
ORM 代表对象关系映射–一种 API，可让你从数据库保存和检索 "对象"，而无需编写 SQL 语句。

<a name="Usage"/>

## <a name="usage"></a>用法

若要在 Xamarin 应用中包括 SQLite.NET 库，请将以下 NuGet 包添加到项目中：

- **包名称：** sqlite 网络-pcl
- **作者：** Frank Krueger
- **ID：** sqlite net pcl
- **Url：** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![SQLite.NET NuGet 包](using-sqlite-orm-images/image1a-sml.png "SQLite.NET NuGet 包")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> 有许多不同的 SQLite 包可用，请确保选择正确的 SQLite 包（它可能不是搜索的最大结果）。

获得可用的 SQLite.NET 库后，请执行以下三个步骤以使用它来访问数据库：

1. **添加 using 语句**-将以下语句添加到需要数据C#访问的文件中：

    ```csharp
    using SQLite;
    ```

1. **创建空白数据库**-可以通过将文件路径传递给 SQLiteConnection 类构造函数来创建数据库引用。 如果文件已存在，则不需要检查该文件是否已存在–如果需要，将自动创建该文件，否则将打开现有的数据库文件。

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    应按照本文档前面讨论的规则来确定 dbPath 变量。

1. **保存数据**-创建 SQLiteConnection 对象后，将通过调用其方法来执行数据库命令，如 CreateTable 和 Insert，如下所示：

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **检索数据**-若要检索对象（或对象列表），请使用以下语法：

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>基本数据访问示例

在 iOS 上运行时，此文档的*DataAccess_Basic*示例代码如下所示。 此代码演示如何执行简单的 SQLite.NET 操作并在应用程序的主窗口中以文本形式显示结果。

**Android**

 [![iOS SQLite.NET 示例](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

下面的代码示例演示使用 SQLite.NET 库来封装基础数据库访问的整个数据库交互。 它显示：

1. 创建数据库文件
1. 通过创建对象并保存来插入一些数据
1. 查询数据

需要包含以下命名空间：

```csharp
using SQLite; // from the github SQLite.cs class
```

这要求您已将 SQLite 添加到项目中[，如下所示。](#Usage) 请注意，通过将属性添加到类（`Stock` 类）而不是 CREATE TABLE 命令来定义 SQLite 数据库表。

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

如果在未指定表名称参数的情况下使用 `[Table]` 属性，则会导致基础数据库表具有与类相同的名称（在本例中为 "Stock"）。 如果直接针对数据库编写 SQL 查询，而不是使用 ORM 数据访问方法，则实际的表名非常重要。 同样，`[Column("_id")]` 属性是可选的，如果不存在，列将添加到与类中的属性同名的表中。

## <a name="sqlite-attributes"></a>SQLite 特性

可应用于类以控制它们在基础数据库中的存储方式的常用特性包括：

- **[PrimaryKey]** –此特性可应用于整数属性，以强制其成为基础表的主键。 不支持组合主键。
- **[自动增量]** –此特性将导致插入到数据库中的每个新对象的整数属性值为自动增量
- **[Column （name）]** –提供可选 `name` 参数将重写基础数据库列的名称（与属性相同）的默认值。
- **[表（名称）]** –标记该类，使其能够存储在基础 SQLite 表中。 指定可选的 name 参数将重写基础数据库表名称（与类名相同）的默认值。
- **[MaxLength （值）]** –当尝试插入数据库时，限制文本属性的长度。 在插入对象之前，使用代码应进行验证，因为在尝试执行数据库插入或更新操作时，仅 "选中" 此属性。
- **[Ignore]** –使 SQLite.NET 忽略此属性。 这对于类型不能存储在数据库中的属性或无法自动解析的模型集合的属性特别有用。
- **[Unique]** –确保基础数据库列中的值是唯一的。

其中的大多数属性是可选的，SQLite 将使用表名和列名的默认值。 应始终指定整数主键，以便可以对数据高效地执行选择和删除查询。

## <a name="more-complex-queries"></a>更复杂的查询

`SQLiteConnection` 上的以下方法可用于执行其他数据操作：

- **Insert** –将新的对象添加到数据库。
- **获取\<t >** –尝试使用主键检索对象。
- **表\<t >** –返回表中的所有对象。
- **删除**–使用其主键删除对象。
- **查询\<t >** -执行返回多行（作为对象）的 SQL 查询。
- **执行**–当您不希望从 SQL 返回行时（例如插入、更新和删除说明），请使用此方法（而不是 `Query`）。

### <a name="getting-an-object-by-the-primary-key"></a>通过主键获取对象

SQLite.Net 提供 Get 方法，以根据其主键检索单个对象。

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>使用 Linq 选择对象

返回集合的方法支持 IEnumerable\<T > 以便您可以使用 Linq 查询或排序表的内容。 下面的代码演示了一个示例，该示例使用 Linq 筛选出以字母 "A" 开头的所有条目：

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>使用 SQL 选择对象

尽管 SQLite.Net 可以提供对你的数据的基于对象的访问权限，但有时你可能需要执行比 Linq 允许更复杂的查询（或者可能需要更快的性能）。 可以将 SQL 命令与 Query 方法一起使用，如下所示：

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> 直接编写 SQL 语句时，会创建数据库中表和列的名称的依赖关系，这些表和列是从你的类及其属性生成的。 如果在代码中更改这些名称，则必须记住更新所有手动写入的 SQL 语句。

### <a name="deleting-an-object"></a>删除对象

主密钥用于删除行，如下所示：

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

您可以检查 `rowcount` 以确认受影响的行数（在此示例中已删除）。

## <a name="using-sqlitenet-with-multiple-threads"></a>将 SQLite.NET 用于多个线程

SQLite 支持三个不同的线程模式：*单线程*、*多线程*和*序列化*。 如果要从多个线程访问数据库，而不受任何限制，则可以将 SQLite 配置为使用**序列化**的线程模式。 在应用程序的早期设置此模式很重要（例如，在 `OnCreate` 方法的开头）。

若要更改线程模式，请在 `Mono.Data.Sqlite` 命名空间中调用 `SqliteConnection.SetConfig`。 例如，下面这行代码将为**序列化**模式配置 SQLite：

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
