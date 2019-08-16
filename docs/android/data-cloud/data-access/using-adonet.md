---
title: 结合使用 ADO.NET 和 Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 76a66b4dbde65a3fc44d3490e0147ff66b088466
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525962"
---
# <a name="using-adonet-with-android"></a>结合使用 ADO.NET 和 Android

Xamarin 内置了对适用于 Android 的 SQLite 数据库的支持, 可以使用熟悉的类似 ADO.NET 的语法公开。 使用这些 api 要求编写由 SQLite 处理的 SQL 语句, 如`CREATE TABLE` `INSERT`和`SELECT`语句。

## <a name="assembly-references"></a>程序集引用

若要通过 ADO.NET 使用访问 SQLite, 你`System.Data`必须`Mono.Data.Sqlite`添加和引用 Android 项目, 如下所示:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows) 

![Visual Studio 中的 Android 引用](using-adonet-images/image7.png "Visual Studio 中的 Android 引用") 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos) 

![Visual Studio for Mac 中的 Android 引用](using-adonet-images/image5.png "Visual Studio for Mac 中的 Android 引用") 

-----


右键单击 "**引用" > 编辑引用 ...** ", 然后单击以选择所需的程序集。

## <a name="about-monodatasqlite"></a>关于 Mono. Sqlite

我们将使用`Mono.Data.Sqlite.SqliteConnection`类创建一个空数据库文件, 然后对对象进行实例`SqliteCommand`化, 这些对象可用于针对数据库执行 SQL 指令。

**创建空白数据库**使用有效的 (即可写) 文件路径调用方法。`CreateFile` &ndash; 在调用此方法之前, 应检查文件是否已存在, 否则将在旧文件的顶部创建新的 (空白) 数据库, 并将丢失旧文件中的数据。
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`应`dbPath`按照本文档前面所述的规则来确定该变量。

**创建数据库连接**&ndash;创建 SQLite 数据库文件后, 可以创建连接对象来访问数据。 使用的连接字符串`Data Source=file_path`构造连接, 如下所示:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

如前所述, 连接永远不能在不同的线程之间重复使用。 如果有疑问, 请根据需要创建连接并在完成后关闭它;但请注意, 此操作的执行频率要高于所需的频率。

**创建和执行数据库命令**&ndash;建立连接后, 可以对其执行任意 SQL 命令。 下面的代码显示正在`CREATE TABLE`执行的语句。

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

当直接针对数据库执行 SQL 时, 应采取正常的预防措施来避免发出无效请求, 例如, 尝试创建已存在的表。 跟踪数据库的结构, 以便不会导致`SqliteException`诸如**SQLite 错误表 [Items] 已经存在**。

## <a name="basic-data-access"></a>基本数据访问

在 Android 上运行时, 此文档的*DataAccess_Basic*示例代码如下所示:

![Android ADO.NET 示例](using-adonet-images/image8.png "Android ADO.NET 示例")

下面的代码演示了如何执行简单的 SQLite 操作并在应用程序的主窗口中以文本形式显示结果。

需要包含以下命名空间:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

下面的代码示例演示了整个数据库交互:

1. 创建数据库文件
2. 插入一些数据
3. 查询数据

这些操作通常出现在代码中的多个位置, 例如, 你可以在应用程序第一次启动时创建数据库文件和表, 并在应用中的各个屏幕上执行数据读写操作。 在下面的示例中, 已将此示例中的一个方法分组为一个方法:

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

由于 SQLite 允许对数据运行任意 SQL 命令, 因此你可以执行所需的`CREATE`任何`INSERT`、 `UPDATE`、 `DELETE`、或`SELECT`语句。 可以在 SQLite 网站上阅读 SQLite 支持的 SQL 命令。 使用`SqliteCommand`对象上三种方法之一运行 SQL 语句:

- **ExecuteNonQuery**&ndash;通常用于表创建或数据插入。 某些运算的返回值为受影响的行数, 否则为-1。

- **ExecuteReader**当行集合应`SqlDataReader`作为返回时使用。 &ndash;

- **ExecuteScalar**&ndash;检索单个值 (例如聚合)。


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`、 `UPDATE`和`DELETE`语句将返回受影响的行数。 所有其他 SQL 语句将返回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下面的方法显示`WHERE` `SELECT`语句中的子句。
由于代码正在编写完整的 SQL 语句, 因此它必须小心地转义保留字符, 如引号 ("), 如字符串。

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

`ExecuteReader` 方法返回 `SqliteDataReader` 对象。 除了示例中所`Read`示的方法, 其他有用的属性还包括:

- **RowsAffected**&ndash;受查询影响的行的计数。

- **HasRows**&ndash;是否返回了任何行。


### <a name="executescalar"></a>EXECUTESCALAR

对于`SELECT`返回单个值 (例如聚合) 的语句, 请使用此语句。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

方法的返回类型是`object` &ndash; , 应根据数据库查询强制转换结果。 `ExecuteScalar` 结果可能是来自`COUNT`查询的整数, 或是单个列`SELECT`查询中的字符串。 请注意, 这不同于返回`Execute`读取器对象或受影响的行数计数的其他方法。



## <a name="related-links"></a>相关链接

- [DataAccess Basic (示例)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 高级 (示例)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 数据食谱](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 窗体数据访问](~/xamarin-forms/data-cloud/data/databases.md)
