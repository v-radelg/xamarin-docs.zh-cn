---
title: 在 Xamarin 中使用 Azure Cosmos DB 文档数据库
description: 此文章介绍了如何使用 Azure Cosmos DB.NET Standard 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: a18572b0f28cb8d6c699d0bbd062bdb067471a34
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770884"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>在 Xamarin 中使用 Azure Cosmos DB 文档数据库

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Azure Cosmos DB 文档数据库是提供低延迟访问产品/服务面向需要无缝缩放和全局复制的应用程序的快速、 高度可用、 可缩放数据库服务的 JSON 文档的 NoSQL 数据库。此文章介绍了如何使用 Azure Cosmos DB.NET Standard 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB 视频**

可以使用 Azure 订阅预配 Azure Cosmos DB 文档数据库帐户。 每个数据库帐户可以有零个或多个数据库。 Azure Cosmos DB 中的文档数据库是适用于文档集合和用户的逻辑容器。

Azure Cosmos DB 文档数据库可能包含零个或多个文档集合。 每个文档集合可以具有不同的性能级别，允许更大的吞吐量为经常访问的集合，指定和更少不常访问的集合的吞吐量。

每个文档集合包含的零个或多个 JSON 文档。 集合中的文档是无架构的因此不需要共享相同的结构或字段。 文档添加到文档集合，Cosmos DB 会自动编制索引以及它们会变得可供查询。

出于开发目的，文档数据库也可以使用通过仿真程序。 使用模拟器，应用程序可以进行开发和测试本地，而无需创建 Azure 订阅，也不会产生任何费用。 有关模拟器的详细信息，请参阅[使用 Azure Cosmos DB 模拟器进行本地开发](/azure/cosmos-db/local-emulator/)。

本文中，以及随附的示例应用程序，演示其中这些任务存储在 Azure Cosmos DB 文档数据库中的待办事项列表应用程序。 有关示例应用程序的详细信息，请参阅[了解示例](~/xamarin-forms/data-cloud/web-services/introduction.md)。

有关 Azure Cosmos DB 的详细信息，请参阅[Azure Cosmos DB 文档](/azure/cosmos-db/)。

## <a name="setup"></a>安装

将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建 Cosmos DB 帐户。 有关详细信息，请参阅[创建 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account)。
1. 添加[Azure Cosmos DB.NET Standard 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)到 Xamarin.Forms 解决方案中的平台项目的 NuGet 包。
1. 添加`using`指令`Microsoft.Azure.Documents`， `Microsoft.Azure.Documents.Client`，和`Microsoft.Azure.Documents.Linq`到将访问 Cosmos DB 帐户的类的命名空间。

执行这些步骤后，可以使用 Azure Cosmos DB.NET Standard 客户端库进行配置和执行针对文档数据库的请求。

> [!NOTE]
> Azure Cosmos DB.NET Standard 客户端库只能安装到平台项目中，并不到可移植类库 (PCL) 项目。 因此，示例应用程序是共享访问项目 (SAP) 以避免代码重复。 但是，`DependencyService`类可用于在 PCL 项目中调用特定于平台的项目中包含的 Azure Cosmos DB.NET Standard 客户端库代码。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帐户

`DocumentClient`类型封装终结点、 凭据和连接策略用来访问 Azure Cosmos DB 帐户，并用于配置和执行针对帐户的请求。 下面的代码示例演示如何创建此类的实例：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

Cosmos DB Uri 和主密钥必须提供给`DocumentClient`构造函数。 可以从 Azure 门户获取这些。 有关详细信息，请参阅[连接到 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect)。

### <a name="creating-a-database"></a>创建数据库

文档数据库是适用于文档集合和用户的逻辑容器，并且可以创建在 Azure 门户中，或以编程方式使用`DocumentClient.CreateDatabaseIfNotExistsAsync`方法：

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

`CreateDatabaseIfNotExistsAsync`方法指定`Database`对象作为参数，与`Database`对象，指定数据库名称作为其`Id`属性。 `CreateDatabaseIfNotExistsAsync`方法创建数据库; 如果不存在，如果它已存在，则返回的数据库。 但是，示例应用程序将忽略返回任何数据`CreateDatabaseIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法将返回`Task<ResourceResponse<Database>>`可以检查以确定是否创建了数据库，或返回现有数据库对象，并在响应的状态代码。

### <a name="creating-a-document-collection"></a>创建文档集合

文档集合是 JSON 文档的容器，并且可以创建在 Azure 门户中，或以编程方式使用`DocumentClient.CreateDocumentCollectionIfNotExistsAsync`方法：

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

`CreateDocumentCollectionIfNotExistsAsync`方法需要两个强制性参数 – 数据库名称指定为`Uri`，和一个`DocumentCollection`对象。 `DocumentCollection`对象表示与指定其名称的文档集合`Id`属性。 `CreateDocumentCollectionIfNotExistsAsync`方法创建文档集合; 如果不存在，如果它已存在，则返回的文档集合。 但是，示例应用程序将忽略返回任何数据`CreateDocumentCollectionIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法将返回`Task<ResourceResponse<DocumentCollection>>`检查对象，并在响应的状态代码以确定是否已创建一个文档集合，或返回现有的文档集合。

（可选）`CreateDocumentCollectionIfNotExistsAsync`方法还可以指定`RequestOptions`对象，封装到 Cosmos DB 帐户发出的请求可以指定的选项。 `RequestOptions.OfferThroughput`属性用于定义文档集合的性能级别和在示例应用程序中，设置为每秒 400 个请求单位。 此值应增加或减少具体取决于是否将频繁或不常访问集合。

> [!IMPORTANT]
> 请注意，`CreateDocumentCollectionIfNotExistsAsync`方法将创建一个新集合使用保留的吞吐量，它牵涉定价。

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>检索文档集合文档

可以通过创建并执行文档查询检索文档集合中的内容。 使用创建文档查询`DocumentClient.CreateDocumentQuery`方法：

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

此查询以异步方式从指定集合中检索所有文档，并将在文档`List<TodoItem>`显示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`表示应查询的文档的集合的参数。 在此示例中，`collectionLink`变量是类级字段，用于指定`Uri`，表示要检索来自文档的文档集合：

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法创建一个查询，以同步方式，执行并返回`IQueryable<T>`对象。 但是，`AsDocumentQuery`方法将`IQueryable<T>`对象传递给`IDocumentQuery<T>`可以异步执行的对象。 与执行异步查询`IDocumentQuery<T>.ExecuteNextAsync`方法，检索结果，文档数据库中的下一页面进行`IDocumentQuery<T>.HasMoreResults`，该值指示是否有更多结果，以从查询返回的属性。

文档可以是由包括筛选服务器端`Where`中查询，筛选谓词应用于针对文档集合的查询子句：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查询检索所有记录集合从其`Done`属性等于`false`。

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>将文档插入到文档集合

文档是用户定义 JSON 内容和可以插入到文档集合与`DocumentClient.CreateDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法指定`Uri`表示应将文档插入到，该集合的参数和一个`object`参数表示要插入的文档。

### <a name="replacing-a-document-in-a-document-collection"></a>替换文档集合中的文档

可以使用文档集合中替换文档`DocumentClient.ReplaceDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync`方法指定`Uri`表示应替换为集合中的文档的参数和一个`object`表示更新后的文档数据的参数。

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>文档集合中删除文档

可以从使用文档集合中删除一个文档`DocumentClient.DeleteDocumentAsync`方法：

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync`方法指定`Uri`表示应删除的集合中的文档的参数。

### <a name="deleting-a-document-collection"></a>删除文档集合

可以使用数据库中删除文档集合`DocumentClient.DeleteDocumentCollectionAsync`方法：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法指定`Uri`表示要删除的文档集合参数。 请注意，调用此方法也会删除存储在集合中的文档。

### <a name="deleting-a-database"></a>删除数据库

可以使用 Cosmos DB 数据库帐户中删除某个数据库`DocumentClient.DeleteDatabaesAsync`方法：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法指定`Uri`参数表示要删除的数据库。 请注意，调用此方法也会删除存储在数据库中，文档集合和文档集合中存储的文档。

## <a name="summary"></a>总结

本文介绍了如何使用 Azure Cosmos DB.NET Standard 客户端库将 Azure Cosmos DB 文档数据库集成到 Xamarin.Forms 应用程序。 Azure Cosmos DB 文档数据库是提供低延迟访问产品/服务面向需要无缝缩放和全局复制的应用程序的快速、 高度可用、 可缩放数据库服务的 JSON 文档的 NoSQL 数据库。

## <a name="related-links"></a>相关链接

- [Todo Azure Cosmos DB （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Azure Cosmos DB 文档](/azure/cosmos-db/)
- [Azure Cosmos DB.NET Standard 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
