---
title: Xamarin 中的 system.object
description: 本文档介绍了如何使用 system.exception 和 Mono 在 Xamarin iOS 应用程序中访问 SQLite 数据。
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: 4f5f6adf99306754fa7b2aa49855fe228e740d7e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016935"
---
# <a name="systemdata-in-xamarinios"></a>Xamarin 中的 system.object

Xamarin 8.10 添加了对[系统](xref:System.Data)的支持，包括 `Mono.Data.Sqlite.dll` ADO.NET 提供程序。 支持包括添加以下[程序集](~/cross-platform/internals/available-assemblies.md)：

- `System.Data.dll`
- `System.Data.Service.Client.dll`
- `System.Transactions.dll`
- `Mono.Data.Tds.dll`
- `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>示例

以下程序在 `Documents/mydb.db3`中创建一个数据库，如果数据库以前不存在，则使用示例数据填充该数据库。 然后，将查询数据库，并将输出写入到 `stderr`。

### <a name="add-references"></a>添加引用

首先，右键单击 "**引用**" 节点，然后选择 "**编辑引用 ...** "，然后选择 "`System.Data`" 和 "`Mono.Data.Sqlite`"：

[![](system.data-images/edit-references-sml.png "Adding new references")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>代码示例

下面的代码演示了一个简单的示例，说明如何使用嵌入的 SQL 命令来创建表和插入行：

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
> 如上面的代码示例中所述，在 SQL 命令中嵌入字符串是不好的做法，因为这会使你的代码容易受到[sql 注入](https://en.wikipedia.org/wiki/SQL_injection)攻击。

### <a name="using-command-parameters"></a>使用命令参数

下面的代码演示如何使用命令参数将用户输入的文本安全插入到数据库中（即使文本包含特殊的 SQL 字符，如单撇号）：

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

## <a name="missing-functionality"></a>缺少功能

**System.web**和**Mono**都缺少某些功能。

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

**System.web**中缺少的功能包含：

- 需要[system.object 的](xref:System.CodeDom)任何内容（例如 [TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) ）
- XML 配置文件支持（例如 [DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) ）。
- [DbProviderFactories](xref:System.Data.Common.DbProviderFactories) （取决于 XML 配置文件支持）
- [System.object](xref:System.Data.OleDb)
- [System.object](xref:System.Data.Odbc)
- `System.EnterpriseServices.dll` 依赖项已从 `System.Data.dll` 中*删除*，导致删除[EnlistDistributedTransaction （ITransaction）](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*)方法。

<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono. 数据 Sqlite

同时， **Mono** . node.js 不会更改源代码，而是由于 `Mono.Data.Sqlite.dll` 绑定 Sqlite 3.5，因此可能会导致许多*运行时*问题。 同时，iOS 8 附带 SQLite 3.8.5。 说到，这两个版本之间的某些内容已发生了变化。

旧版本的 iOS 附带以下版本的 SQLite：

- **iOS 7** -版本3.7.13。
- **iOS 6** -版本3.7.13。
- **iOS 5** -版本3.7.7。
- **iOS 4** -版本3.6.22。

最常见的问题似乎与数据库架构查询相关，例如，在运行时确定给定表中存在哪些列，如 `Mono.Data.Sqlite.SqliteConnection.GetSchema` （重写[GetSchema](xref:System.Data.Common.DbConnection.GetSchema)和 `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` （重写[DbDataReader. GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable)。 简而言之，使用[DataTable](xref:System.Data.DataTable)的任何内容似乎都不能正常工作。

<a name="Data_Binding" />

## <a name="data-binding"></a>数据绑定

此时不支持数据绑定。
