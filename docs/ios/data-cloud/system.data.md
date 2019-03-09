---
title: 在 Xamarin.iOS System.Data
description: 本文档介绍如何使用 System.Data 和 Mono.Data.Sqlite.dll 访问 SQLite 在 Xamarin.iOS 应用程序中的数据。
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: e6df2d9d45eb2f898bb3c4957ec7960956a184e0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671775"
---
# <a name="systemdata-in-xamarinios"></a>在 Xamarin.iOS System.Data

添加了对支持 Xamarin.iOS 8.10 [System.Data](xref:System.Data)，其中包括`Mono.Data.Sqlite.dll`ADO.NET 提供程序。 支持包括添加以下[程序集](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>示例

以下程序创建的数据库中`Documents/mydb.db3`，和如果数据库以前没有它填充了示例数据。 然后查询该数据库，将输出写入到与`stderr`。

### <a name="add-references"></a>添加引用

首先，右键单击**引用**节点，然后选择**编辑引用...** 然后选择`System.Data`和`Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "添加新引用")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>代码示例

下面的代码演示创建表和使用嵌入的 SQL 命令插入行的简单示例：

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> 上面的代码示例中所述，它是不正确的做法将字符串嵌入 SQL 命令中，因为它使您的代码容易受到[SQL 注入](https://en.wikipedia.org/wiki/SQL_injection)。


### <a name="using-command-parameters"></a>使用命令参数

下面的代码演示如何使用命令参数 （即使的文本中包含特殊 SQL 字符，如单撇号） 安全地插入到数据库的用户输入的文本：

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>缺少的功能

这两**System.Data**并**Mono.Data.Sqlite**缺少某些功能。

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

功能中缺少**System.Data.dll**组成：

-  任何内容需要[System.CodeDom](xref:System.CodeDom) （例如 [System.Data.TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
-  XML 配置文件支持 （例如 [System.Data.Common.DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
-   [System.Data.Common.DbProviderFactories](xref:System.Data.Common.DbProviderFactories) （取决于 XML 配置文件支持）
-   [System.Data.OleDb](xref:System.Data.OleDb)
-   [System.Data.Odbc](xref:System.Data.Odbc)
-  `System.EnterpriseServices.dll`依赖项，*删除*从`System.Data.dll`，将导致删除[SqlConnection.EnlistDistributedTransaction(ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*)方法。


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

同时， **Mono.Data.Sqlite.dll**不遇到的任何源代码更改，但改为可能是大量的宿主*运行时*发出自`Mono.Data.Sqlite.dll`绑定 SQLite 3.5。 iOS 8，同时，附带 SQLite 3.8.5。 简单地说，一些内容已更改两个版本之间。

较旧 iOS 版本附带的 SQLite 的以下版本：

- **iOS 7** -版本 3.7.13。
- **iOS 6** -版本 3.7.13。
- **iOS 5** -版本 3.7.7。
- **iOS 4** -版本 3.6.22。

最常见的问题似乎与数据库架构查询，例如确定在运行时的列存在某一给定的表，如`Mono.Data.Sqlite.SqliteConnection.GetSchema`(重写[DbConnection.GetSchema](xref:System.Data.Common.DbConnection.GetSchema)和`Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable`（重写[DbDataReader.GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable)。 简单地说，它看起来的任何内容使用[DataTable](xref:System.Data.DataTable)可能无法发挥作用。

<a name="Data_Binding" />

## <a name="data-binding"></a>数据绑定

目前不支持数据绑定。

