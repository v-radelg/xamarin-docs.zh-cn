---
title: 使用 ADO.NET 和 Xamarin.iOS
description: 本文档介绍如何使用 ADO.NET 作为一种方法在 Xamarin.iOS 应用程序中访问 SQLite。 它讨论了程序集引用、 Mono.Data.Sqlite 和 BasicDataAccess 示例。
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 9314e1b69df4a5965dfd045d0b4ca3e44f1b9de6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153665"
---
# <a name="using-adonet-with-xamarinios"></a>使用 ADO.NET 和 Xamarin.iOS

Xamarin 提供内置支持是适用于 iOS，公开使用熟悉的类似于 ADO.NET 的语法的 SQLite 数据库。 使用这些 Api 需要你编写 SQL 语句处理的 SQLite，如`CREATE TABLE`，`INSERT`和`SELECT`语句。

## <a name="assembly-references"></a>程序集引用

若要使用的访问必须添加的 ADO.NET 通过 SQLite`System.Data`和`Mono.Data.Sqlite`引用到 iOS 项目，如下所示 （适用于 Visual Studio for Mac 和 Visual Studio 中的示例）：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Visual Studio for Mac 中的程序集引用")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "在 Visual Studio 中的程序集引用")

-----

右键单击**引用 > 编辑引用...** ，然后单击以选择所需的程序集。

## <a name="about-monodatasqlite"></a>有关 Mono.Data.Sqlite

我们将使用`Mono.Data.Sqlite.SqliteConnection`类，以创建空数据库文件，然后实例化`SqliteCommand`对象，我们可以使用执行 SQL 对数据库的说明。


1. **创建空数据库**-调用`CreateFile`使用有效的方法 (即。 可写) 文件路径。 应检查是否调用此方法之前已存在该文件，否则将旧的基础上创建新的 （空白） 数据库和旧的文件中的数据将会丢失：

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > `dbPath`变量，必须根据本文档前面所述的规则来确定。

2. **创建数据库连接**-SQLite 数据库文件创建后可以创建连接对象，以访问数据。 使用连接字符串，其形式的构造连接`Data Source=file_path`，如下所示：

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    前面曾提到，连接永远不应重复使用跨不同的线程。 如果有疑问，创建所需的连接并将其关闭时完成;但要注意的执行详细通常不太必要。
    
3. **创建和执行数据库命令**-后，我们有一个我们可以执行对其的任意 SQL 命令的连接。 下面的代码显示了正在执行的 CREATE TABLE 语句。

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

直接对数据库执行 SQL 时，应执行正常的预防措施不以使无效请求，例如尝试创建已存在的表。 跟踪的数据库的结构，以便不会造成 SqliteException，如"SQLite 错误表 [项目] 已存在"。

## <a name="basic-data-access"></a>基本数据访问

*DataAccess_Basic*在 iOS 上运行时，本文档的示例代码如下所示：

 ![](using-adonet-images/image9.png "iOS ADO.NET 示例")

下面的代码说明了如何执行简单的 SQLite 操作并为应用程序的主窗口中的文本显示中的结果。

你将需要包含这些命名空间：

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

下面的代码示例显示了整个数据库进行交互：

1.  创建数据库文件
2.  插入一些数据
3.  查询数据

这些操作将通常会出现在多个位置在整个代码，例如可能你的应用程序首次启动时创建的数据库文件和表，并在单个屏幕中执行数据读取和写入应用程序中。 在下面的示例已被分组到单个方法对于此示例：

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}
```

## <a name="more-complex-queries"></a>更复杂的查询

由于 SQLite 允许对数据运行任意 SQL 命令，可以执行任何创建、 插入、 更新、 删除或选择您喜欢的语句。 你可以阅读 SQLite 支持 Sqlite 网站上的 SQL 命令。 使用三种方法之一 SqliteCommand 对象上运行 SQL 语句：

-  **ExecuteNonQuery** – 通常用于表创建或数据期间插入操作。 对于某些操作的返回值是受影响的行数，否则为-1。
-  **ExecuteReader** – 使用时应作为返回的行集合`SqlDataReader`。
-  **ExecuteScalar** – 检索单个值 （例如聚合）。


### <a name="executenonquery"></a>EXECUTENONQUERY

INSERT、 UPDATE 和 DELETE 语句将返回受影响的行数。 所有其他 SQL 语句将返回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下面的方法演示在 SELECT 语句的 WHERE 子句。 因为代码精心编制一个完整的 SQL 语句必须请注意它转义保留的字符，如字符串周围的引号 （'）。

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

ExecuteReader 方法返回 SqliteDataReader 对象。 在示例中所示的读取方法，除了其他有用属性包括：

-  **RowsAffected** – 受查询影响的行的计数。
-  **HasRows** – 是否没有返回任何行。


### <a name="executescalar"></a>EXECUTESCALAR

这用于返回单个值 （例如聚合） 的 SELECT 语句。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar`方法的返回类型是`object`– 应具体取决于数据库查询结果强制转换。 结果可能是 COUNT 查询之间的一个整数或单个列的 SELECT 查询中的字符串。 请注意，这不同于其他 Execute 方法的返回一个读取器对象或受影响的行数。


## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 数据访问](~/xamarin-forms/app-fundamentals/databases.md)
