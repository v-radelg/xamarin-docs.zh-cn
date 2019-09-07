---
title: 将 ADO.NET 与 Xamarin 配合使用
description: 本文档介绍如何在 Xamarin iOS 应用程序中使用 ADO.NET 作为方法来访问 SQLite。 它讨论了程序集引用、Mono 和 BasicDataAccess 示例。
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: a9df85a405bc086f86dae73fea615581bf9d28d0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767390"
---
# <a name="using-adonet-with-xamarinios"></a>将 ADO.NET 与 Xamarin 配合使用

Xamarin 内置了对在 iOS 上可用的 SQLite 数据库的支持，并使用熟悉的类似 ADO.NET 的语法公开。 使用这些 api 要求编写由 SQLite 处理的 SQL 语句，如`CREATE TABLE` `INSERT`和`SELECT`语句。

## <a name="assembly-references"></a>程序集引用

若要通过 ADO.NET 使用访问 SQLite，你`System.Data`必须`Mono.Data.Sqlite`添加和引用 iOS 项目，如下所示（有关 Visual Studio for Mac 和 Visual Studio 中的示例）：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Visual Studio for Mac 中的程序集引用")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Visual Studio 中的程序集引用")

-----

右键单击 "**引用" > 编辑引用 ...** "，然后单击以选择所需的程序集。

## <a name="about-monodatasqlite"></a>关于 Mono. Sqlite

我们将使用`Mono.Data.Sqlite.SqliteConnection`类创建一个空数据库文件，然后对对象进行实例`SqliteCommand`化，这些对象可用于针对数据库执行 SQL 指令。

1. **创建空白数据库**-使用有效的`CreateFile` （即可写）文件路径调用方法。 在调用此方法之前，应检查该文件是否已存在，否则将在旧文件的顶部创建新的（空白）数据库，而旧文件中的数据将丢失：

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > 应`dbPath`按照本文档前面所述的规则来确定该变量。

2. **创建数据库连接**-创建 SQLite 数据库文件后，可以创建连接对象来访问数据。 使用的连接字符串`Data Source=file_path`构造连接，如下所示：

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    如前所述，连接永远不能在不同的线程之间重复使用。 如果有疑问，请根据需要创建连接并在完成后关闭它;但请注意，此操作的执行频率要高于所需的频率。

3. **创建和执行数据库命令**-一旦建立了连接，就可以对其执行任意 SQL 命令。 下面的代码显示正在执行的 CREATE TABLE 语句。

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

当直接针对数据库执行 SQL 时，应采取正常的预防措施来避免发出无效请求，例如，尝试创建已存在的表。 跟踪数据库的结构，以便不会导致 SqliteException，例如 "SQLite 错误表 [Items] 已存在"。

## <a name="basic-data-access"></a>基本数据访问

在 iOS 上运行时，此文档的*DataAccess_Basic*示例代码如下所示：

 ![](using-adonet-images/image9.png "iOS ADO.NET 示例")

下面的代码演示了如何执行简单的 SQLite 操作并在应用程序的主窗口中以文本形式显示结果。

需要包含以下命名空间：

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

下面的代码示例演示了整个数据库交互：

1. 创建数据库文件
2. 插入一些数据
3. 查询数据

这些操作通常出现在代码中的多个位置，例如，你可以在应用程序第一次启动时创建数据库文件和表，并在应用中的各个屏幕上执行数据读写操作。 在下面的示例中，已将此示例中的一个方法分组为一个方法：

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

由于 SQLite 允许对数据运行任意 SQL 命令，因此你可以执行所需的任何 CREATE、INSERT、UPDATE、DELETE 或 SELECT 语句。 可以在 Sqlite 网站上阅读 SQLite 支持的 SQL 命令。 在 SqliteCommand 对象上使用以下三种方法之一运行 SQL 语句：

- **ExecuteNonQuery** –通常用于表创建或数据插入。 某些运算的返回值为受影响的行数，否则为-1。
- **ExecuteReader** -在行集合应作为`SqlDataReader`返回时使用。
- **ExecuteScalar** –检索单个值（例如聚合）。

### <a name="executenonquery"></a>EXECUTENONQUERY

INSERT、UPDATE 和 DELETE 语句将返回受影响的行数。 所有其他 SQL 语句将返回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下面的方法显示 SELECT 语句中的 WHERE 子句。 由于代码正在编写完整的 SQL 语句，因此它必须小心地转义保留字符，如引号（"），如字符串。

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

ExecuteReader 方法返回 SqliteDataReader 对象。 除了示例中所示的 Read 方法，其他有用的属性还包括：

- **RowsAffected** –受查询影响的行的计数。
- **HasRows** –是否返回了任何行。

### <a name="executescalar"></a>EXECUTESCALAR

对于返回单个值（例如聚合）的 SELECT 语句，请使用此语句。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

方法的返回类型为`object` –应根据数据库查询强制转换结果。 `ExecuteScalar` 结果可能是来自计数查询的整数，或是单个列 SELECT 查询中的字符串。 请注意，这与返回读取器对象或受影响的行数计数的其他执行方法不同。

## <a name="related-links"></a>相关链接

- [DataAccess Basic （示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级（示例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
