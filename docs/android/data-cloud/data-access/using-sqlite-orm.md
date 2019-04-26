---
title: 与 Android 一起使用 SQLite.NET
description: SQLite.NET PCL NuGet 库提供了一种简单的数据访问机制适用于 Xamarin.Android 应用。
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/18/2018
ms.openlocfilehash: 6525cb321537a7cefb24feb1e77b532068b098ef
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019647"
---
# <a name="using-sqlitenet-with-android"></a>与 Android 一起使用 SQLite.NET

Xamarin 建议 SQLite.NET 库是非常基本的 ORM，你可以轻松地存储和检索 Android 设备上的本地 SQLite 数据库中的对象。 ORM 代表对象关系映射&ndash;API，你可以保存和检索数据库中的"对象"，而无需编写 SQL 语句。

若要在 Xamarin 应用中包含 SQLite.NET 库，请向项目添加以下 NuGet 包：

- **包名称：** sqlite net pcl
- **创建者：** Frank A. Krueger
- **ID：** sqlite net pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![SQLite.NET NuGet 包](using-sqlite-orm-images/image1a-sml.png "SQLite.NET NuGet 包")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> 有多种不同的 SQLite 包-请确保选择正确的订阅 （它可能不是在搜索结果）。

可用的 SQLite.NET 库后，请执行以下三个步骤来使用它来访问数据库：

1.  **添加 using 语句**&ndash;添加以下语句到C#数据访问是必需的文件：

    ```csharp
    using SQLite;
    ```

2.  **创建空数据库**&ndash;可以通过将文件路径传递 SQLiteConnection 类构造函数创建的数据库引用。 不需要检查文件是否已存在&ndash;它会自动创建必要，否则将打开现有的数据库文件。 `dbPath`变量，必须根据本文档前面所述的规则来确定：

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3.  **将数据保存**&ndash;创建 SQLiteConnection 对象后，通过调用其方法，例如 CreateTable 和插入此类执行数据库命令：

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4.  **检索数据**&ndash;若要检索一个对象 （或一系列对象） 使用以下语法：

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>基本数据访问示例

*DataAccess_Basic*本文档的示例代码如下所示，在 Android 上运行时。 该代码演示了如何执行简单的 SQLite.NET 操作，然后为应用程序的主窗口中的文本显示中的结果。


**Android**

![Android SQLite.NET 示例](using-sqlite-orm-images/image3.png "Android SQLite.NET 示例")

下面的代码示例显示了使用 SQLite.NET 库封装基础数据库访问权限的整个数据库交互。
显示：

1.  创建数据库文件

2.  通过创建对象，然后保存它们插入一些数据

3.  查询数据

你将需要包含这些命名空间：

```csharp
using SQLite; // from the github SQLite.cs class
```

最后一个需要向项目添加了 SQLite。 请注意，通过将特性添加到类定义的 SQLite 数据库表 (`Stock`类) 而不是 CREATE TABLE 命令。

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

使用`[Table]`属性而无需指定表名称参数将导致基础数据库表具有与类相同的名称 （在本例中为"Stock"）。 如果编写直接针对数据库的 SQL 查询，而不是使用 ORM 数据访问方法，是重要的实际表名称。 同样`[Column("_id")]`属性是可选的并且如果不存在列将添加到具有与类中的属性相同的名称的表。

## <a name="sqlite-attributes"></a>SQLite 属性

您可以将应用于您的类来控制如何在基础数据库中存储的常见属性包括：

-   **[PrimaryKey]** &ndash;此特性可以应用于一个整数属性，以强制其为基础表的主键。 不支持复合主键。

-   **[自动增量]** &ndash;此属性会导致一个整数属性的值是自动递增每个新对象插入到数据库

-   **[Column(name)]** &ndash;提供可选`name`参数将替代基础数据库列的名称 （这是与属性相同） 的默认值。

-   **[Table(name)]** &ndash;将标记为无法存储在基础的 SQLite 表中的类。 指定可选的 name 参数将覆盖基础数据库表的名称 （这是与类名称相同） 的默认值。

-   **[MaxLength(value)]** &ndash;将 text 属性的长度限制时尝试执行数据库插入。 使用代码应验证这一点在为此属性时才检查的数据库插入或更新操作尝试插入对象之前。

-   **[忽略]** &ndash;导致 SQLite.NET 以忽略此属性。
    这是特别适用于具有的类型，不能在数据库中存储的属性或属性，用于模拟 SQLite 无法自动解决的集合。

-   **[Unique]** &ndash;可以确保基础数据库列中的值的唯一性。


大多数这些属性是可选的 SQLite 将使用默认值表和列的名称。 应始终指定整数主键，以便可以对数据有效地执行所选内容和删除查询。

## <a name="more-complex-queries"></a>更复杂的查询

上的以下方法`SQLiteConnection`可用于执行其他数据操作：

-   **插入**&ndash;向数据库添加一个新的对象。

-   **获取&lt;T&gt;**  &ndash;尝试检索使用为主键的对象。

-   **表&lt;T&gt;**  &ndash;返回表中的所有对象。

-   **删除**&ndash;删除对象使用它的主键。

-   **查询&lt;T&gt;**  &ndash;执行 SQL 查询返回的 （作为对象） 的行数。

-   **执行**&ndash;使用此方法 (而不`Query`) 时不希望从 SQL （如 INSERT、 UPDATE 和 DELETE 的说明） 返回的行。


### <a name="getting-an-object-by-the-primary-key"></a>通过主键获取对象

SQLite.Net 提供 Get 方法来检索单个对象基于其主键。

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>选择使用 Linq 对象

返回集合的方法支持`IEnumerable<T>`以便您可以使用 Linq 来查询或对表的内容进行排序。 下面的代码演示如何使用 Linq 来筛选出以字母"A"开头的所有条目：

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>选择使用 SQL 对象

即使 SQLite.Net 可提供基于对象的访问数据，有时您可能需要执行更复杂的查询不是 Linq 允许 （或可能需要更快的性能）。 可以通过查询方法中，使用 SQL 命令，如下所示：

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> 直接编写 SQL 语句创建一个依赖项上的表和列在数据库中，其中已从您的类以及它们的属性生成的名称。 如果在代码中更改这些名称必须记住要更新的任何手动编写的 SQL 语句。

### <a name="deleting-an-object"></a>删除对象

主键用于删除行，如下所示：

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

你可以检查`rowcount`确认多少行受影响 （在这种情况下删除）。

## <a name="using-sqlitenet-with-multiple-threads"></a>通过多个线程使用 SQLite.NET

SQLite 支持三种不同的线程模式：*单线程*，*多线程*，和*序列化*。 如果你想要从多个线程不受任何限制地访问数据库，可以配置要使用的 SQLite**序列化**线程处理模式。 务必尽早在你的应用程序中设置此模式 (例如，在开头`OnCreate`方法)。

若要更改线程的模式，请调用`SqliteConnection.SetConfig`。 例如，这行代码配置适用于 SQLite**序列化**模式：

```csharp
using using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

SQLite 的 Android 版本具有一个限制，即需要几个步骤。 如果在调用`SqliteConnection.SetConfig`如生成 SQLite 异常`library used incorrectly`，则必须使用以下解决方法：

1.  链接的本机**libsqlite.so**库，以便`sqlite3_shutdown`和`sqlite3_initialize`Api 都提供给应用程序：

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```

2.  最开头`OnCreate`方法，将此代码添加到关闭 SQLite，将其用于配置**序列化**模式，并重新初始化 SQLite:

    ```csharp
    using using Mono.Data.Sqlite;
    ...
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

此解决方法也适用于`Mono.Data.Sqlite`库。 有关 SQLite 和多线程处理的详细信息，请参阅[SQLite 和多个线程](https://www.sqlite.org/threadsafe.html)。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
