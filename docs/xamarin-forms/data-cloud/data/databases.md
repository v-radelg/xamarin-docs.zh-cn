---
title: Xamarin.Forms 本地数据库
description: Xamarin.Forms 支持使用 SQLite 数据库引擎的数据库驱动型应用程序，如此即可在共享代码中加载和保存对象。 本文介绍 Xamarin.Forms 应用程序如何使用 SQLite.Net 读取和写入数据到本地 SQLite 数据库。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 321448453ebe38bd7d43665a3c8bade4fe0f68c2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645257"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本地数据库

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

Xamarin.Forms 支持使用 SQLite 数据库引擎的数据库驱动型应用程序，如此即可在共享代码中加载和保存对象。本文介绍 Xamarin.Forms 应用程序如何使用 SQLite.Net 读取和写入数据到本地 SQLite 数据库。_

## <a name="overview"></a>概述

Xamarin.Forms 应用程序可以使用 [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) 包通过引用 NuGet 中提供的 `SQLite` 类将数据库操作融入共享代码。 可在 Xamarin.Forms 解决方案的 .NET Standard 库项目中定义数据库操作。

随附的[示例应用程序](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是一个简单的待办事项列表应用程序。 以下屏幕截图显示示例在每个平台上的显示方式：

[![Xamarin.Forms 数据库示例屏幕截图](databases-images/todo-list-sml.png "待办事项列表第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList First Page Screenshots")[![Xamarin.Forms 数据库示例屏幕截图](databases-images/todo-list-sml.png "待办事项第一页屏幕截图")](databases-images/todo-list.png#lightbox "TodoList First Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

若要将 SQLite 支持添加到 Xamarin.Forms.NET Standard 库，请使用 NuGet 的搜索功能查找 sqlite-net-pcl 并安装最新包  ：

![添加 NuGet SQLite.NET PCL 包](databases-images/vs2017-sqlite-pcl-nuget.png "Add NuGet SQLite.NET PCL Package")

有多个 NuGet 包名称类似，正确的包具有以下属性：

- **创建者：** Frank A. Krueger
- **ID：** sqlite net pcl
- **NuGet 链接：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 不管包名称，即便在 .NET Standard 项目中也使用 sqlite-net-pcl NuGet 包  。

添加引用后，将属性添加到 `App` 类，该类返回用于存储数据库的本地文件路径：

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

`TodoItemDatabase` 构造函数，以参数形式采用数据库文件的路径，如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

以单一实例的形式公开数据库的优势是，所创建的单一数据库连接在应用程序运行时保持打开状态，因此避免了每次执行数据库操作时打开和关闭数据库文件所产生的费用。

`TodoItemDatabase` 类的其余部分包含跨平台运行的 SQLite 查询。 示例查询代码如下所示（可在[通过 Xamarin.iOS 使用 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md) 中找到更多详细信息）。

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> 使用异步 SQLite.Net API 的优势是，数据库操作将移到后台线程。 此外，无需编写其他并发处理代码，因为 API 将负责处理它。

## <a name="summary"></a>总结

Xamarin.Forms 支持使用 SQLite 数据库引擎的数据库驱动型应用程序，如此即可在共享代码中加载和保存对象。

本文重点关注如何使用 Xamarin.Forms 访问 SQLite 数据库  。 有关使用 SQLite.Net 本身的详细信息，请参阅 [Android 上的 SQLite.NET](~/android/data-cloud/data-access/using-sqlite-orm.md) 或 [iOS 上的 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md) 文档。

## <a name="related-links"></a>相关链接

- [待办事项示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)

