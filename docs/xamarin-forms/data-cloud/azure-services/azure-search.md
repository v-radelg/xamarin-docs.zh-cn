---
title: 使用 Azure 搜索和 Xamarin.Forms 的搜索数据
description: 本文演示如何使用 Microsoft Azure 搜索库可将 Azure 搜索集成到 Xamarin.Forms 应用程序。
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 0ecb546cfb657aa138f05ade64354eb46380e9ab
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658634"
---
# <a name="search-data-with-azure-search-and-xamarinforms"></a>使用 Azure 搜索和 Xamarin.Forms 的搜索数据

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)

_Azure 搜索是云服务，提供索引和查询上传的数据的功能。这会删除基础结构要求和搜索算法复杂性通常与应用程序中实现搜索功能。本文演示如何使用 Microsoft Azure 搜索库可将 Azure 搜索集成到 Xamarin.Forms 应用程序。_

## <a name="overview"></a>概述

数据存储在 Azure 搜索为索引和文档。 *索引*是可由 Azure 搜索服务搜索的数据的存储和从概念上讲类似于数据库表。 一个*文档*是一个可搜索索引中的数据单元，从概念上讲类似于数据库行。 当将文档上载并提交到 Azure 搜索的搜索查询，搜索服务中的特定索引发出请求。

对 Azure 搜索所做的每个请求必须包括服务和 API 密钥的名称。 有两种类型的 API 密钥：

- *管理密钥*授予所有操作的完全控制权限。 这包括管理服务、 创建和删除索引和数据源。
- *查询密钥*授予只读访问权限对索引和文档，并应由发出搜索请求的应用程序。

对 Azure 搜索的最常见请求是执行查询。 有两种类型的可提交的查询：

- 一个*搜索*查询将搜索索引中的所有可搜索字段中的一个或多个项。 搜索查询是使用简化的语法或 Lucene 查询语法生成的。 有关详细信息，请参阅[Azure 搜索中的简单查询语法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)，并[在 Azure 搜索中 Lucene 查询语法](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- 一个*筛选器*查询对索引中的所有可筛选字段计算布尔表达式。 筛选器查询是使用 OData 筛选器语言的子集生成的。 有关详细信息，请参阅[Azure 搜索的 OData 表达式语法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

搜索查询和筛选器查询可以单独或一起使用。 筛选器查询一起使用时，对整个索引中，首先应用，然后搜索查询执行筛选器查询的结果。

Azure 搜索还支持基于搜索输入检索建议。 有关详细信息，请参阅[建议查询](#suggestions)。

## <a name="setup"></a>安装

将 Azure 搜索集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建 Azure 搜索服务。 有关详细信息，请参阅[创建 Azure 搜索服务使用 Azure 门户](/azure/search/search-create-service-portal/)。
1. 删除 Silverlight 为目标框架从 Xamarin.Forms 解决方案可移植类库 (PCL)。 这可以通过更改为支持跨平台开发，但不支持 Silverlight 中，如配置文件 151 或配置文件 92 任何配置文件的 PCL 配置文件。
1. 添加[Microsoft Azure 搜索库](https://www.nuget.org/packages/Microsoft.Azure.Search)到 PCL 项目中的 Xamarin.Forms 解决方案的 NuGet 包。

执行这些步骤后，可以使用 Microsoft 搜索库 API 管理搜索索引和数据源，上载和管理的文档，并执行查询。

## <a name="creating-the-azure-search-index"></a>创建 Azure 搜索索引

索引架构必须定义映射到要在其中搜索的数据的结构。 这可以完成在 Azure 门户中，或以编程方式使用`SearchServiceClient`类。 此类管理连接到 Azure 搜索中，并可用于创建索引。 下面的代码示例演示如何创建此类的实例：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient`构造函数重载采用一个搜索服务名称和一个`SearchCredentials`对象作为参数，与`SearchCredentials`对象包装*管理密钥*Azure 搜索服务。 *管理密钥*创建索引所需。

> [!NOTE]
>  单个`SearchServiceClient`实例应使用应用程序中，以避免打开太多连接到 Azure 搜索。

定义索引`Index`对象，如以下代码示例所示：

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

`Index.Name`属性应设置为索引的名称和`Index.Fields`属性应设置为一个数组`Field`对象。 每个`Field`实例指定一个名称、 类型和任何属性，指定如何使用该字段。 这些属性包括：

- `IsKey` – 指示字段是否为索引的键。 只有一个字段的类型的索引`DataType.String`，必须指定为键字段。
- `IsFacetable` – 指示是否可以在此字段应进行分面导航。 默认值为 `false`。
- `IsFilterable` – 指示是否可以在筛选器查询中使用该字段。 默认值为 `false`。
- `IsRetrievable` – 指示是否可以在搜索结果中检索该字段。 默认值为 `true`。
- `IsSearchable` – 指示字段是否包含在全文搜索。 默认值为 `false`。
- `IsSortable` – 指示是否可以在中使用字段`OrderBy`表达式。 默认值为 `false`。

> [!NOTE]
> 部署后更改索引需要重建和重新加载数据。

`Index`对象可以选择性地指定`Suggesters`属性，该索引要用来支持自动完成或搜索建议查询中定义的字段属性。 `Suggesters`属性应设置为一个数组`Suggester`对象，定义用于生成建议结果的搜索的字段。

在创建后`Index`对象，通过调用创建索引`Indexes.Create`上`SearchServiceClient`实例。

> [!NOTE]
> 当从应用程序创建索引必须响应式保留时，则使用`Indexes.CreateAsync`方法。

有关详细信息，请参阅[创建 Azure 搜索索引使用.NET SDK](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>删除 Azure 搜索索引

可以通过调用来删除索引`Indexes.Delete`上`SearchServiceClient`实例：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>将数据上载到 Azure 搜索索引

定义索引之后, 可将数据上传到它使用两种模型之一：

- **请求模型**– 数据定期引入从 Azure Cosmos DB、 Azure SQL 数据库、 Azure Blob 存储或托管在 Azure 虚拟机中的 SQL Server。
- **推送模型**– 数据以编程方式发送到索引。 这是在本文中采用的模型。

一个`SearchIndexClient`必须创建实例数据导入到索引。 这可以通过调用来实现`SearchServiceClient.Indexes.GetClient`方法，如下面的代码示例中所示：

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

要导入到索引数据打包成`IndexBatch`对象，该封装的集合对象`IndexAction`对象。 每个`IndexAction`实例包含一个文档，以及指示 Azure 搜索对文档执行的操作的属性。 在上面的代码示例`IndexAction.Upload`操作指定，如果是新的插入到索引文档中的结果或替换已存在。 `IndexBatch`对象然后通过调用发送到索引`Documents.Index`方法`SearchIndexClient`对象。 有关其他索引操作的信息，请参阅[决定要使用的索引操作](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)。

> [!NOTE]
> 仅 1000 个文档可以包含在单个索引请求。

请注意，在上面的代码示例`monkeyList`集合创建为集合中的一个匿名对象`Monkey`对象。 这将创建的数据`id`字段，并解析的 Pascal 大小写映射`Monkey`为混合大小写的属性名称搜索索引字段名称。 或者，此映射可以也可以通过添加`[SerializePropertyNamesAsCamelCase]`属性为`Monkey`类。

有关详细信息，请参阅[将数据上传到 Azure 搜索.NET SDK](/azure/search/search-import-data-dotnet/)。

## <a name="querying-the-azure-search-index"></a>查询 Azure 搜索索引

一个`SearchIndexClient`必须创建实例来查询索引。 当应用程序执行查询时，则最好遵循最低特权原则，并创建`SearchIndexClient`直接，传递*查询密钥*作为自变量。 这可确保用户具有对索引和文档的只读访问权限。 下面的代码示例演示了此方法：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient`构造函数重载采用一个搜索服务名称、 索引名称和一个`SearchCredentials`对象作为参数，与`SearchCredentials`对象包装*查询密钥*Azure 搜索服务。

### <a name="search-queries"></a>搜索查询

可以通过调用查询索引`Documents.SearchAsync`方法`SearchIndexClient`实例，如下面的代码示例中所示：

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SearchAsync`方法采用搜索文本参数和可选`SearchParameters`可用于进一步优化查询的对象。 虽然可以通过设置指定一个筛选器查询作为搜索文本参数，指定了一个搜索查询`Filter`属性的`SearchParameters`参数。 下面的代码示例演示了两种查询类型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此筛选器查询应用于整个索引，并从结果中删除文档其中`location`字段不等于中国且不等于越南。 在筛选之后，搜索查询执行筛选器查询的结果。

> [!NOTE]
> 若要筛选而无需搜索，请将传递`*`作为搜索文本参数。

`SearchAsync`方法将返回`DocumentSearchResult`对象，其中包含查询结果。 此对象枚举，与每个`Document`对象创建为`Monkey`对象，并添加到`Monkeys``ObservableCollection`进行显示。 以下屏幕截图显示搜索查询返回的结果从 Azure 搜索：

![](azure-search-images/search.png "搜索结果")

有关搜索和筛选的详细信息，请参阅[查询 Azure 搜索索引使用.NET SDK](/azure/search/search-query-dotnet/)。

<a name="suggestions" />

### <a name="suggestion-queries"></a>建议的查询

Azure 搜索允许请求的建议基于搜索查询，通过调用`Documents.SuggestAsync`方法`SearchIndexClient`实例。 在下面的代码示例说明了这一点：

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SuggestAsync`方法采用一个搜索文本自变量，若要使用的建议器名称 （即是在索引中定义），和一个可选`SuggestParameters`可用于进一步优化查询的对象。 `SuggestParameters`实例设置以下属性：

- `UseFuzzyMatching` – 如果设置为`true`，Azure 搜索将查找建议，即使搜索文本中没有替代或缺少字符。
- `HighlightPreTag` – 将追加到建议命中前面的标记。
- `HighlightPostTag` – 追加到建议命中数的标记。
- `MinimumCoverage` – 表示查询的建议查询必须覆盖的索引百分比报告成功。 默认值为 80。
- `Top` – 要检索的建议数。 它必须是介于 1 和 100，默认值为 5 之间的整数。

总体效果是索引的前 10 个结果将返回具有中，命中项突出显示，并且结果将包括包含同样拼写搜索词的文档。

`SuggestAsync`方法将返回`DocumentSuggestResult`对象，其中包含查询结果。 此对象枚举，与每个`Document`对象创建为`Monkey`对象，并添加到`Monkeys``ObservableCollection`进行显示。 以下屏幕截图显示从 Azure 搜索返回的建议结果：

![](azure-search-images/suggest.png "建议结果")

请注意，在示例应用程序，`SuggestAsync`用户完成输入搜索词时，才会调用方法。 但是，它还可用来通过执行在每个按键上支持自动补全搜索查询。

## <a name="summary"></a>总结

本文演示了如何使用 Microsoft Azure 搜索库可将 Azure 搜索集成到 Xamarin.Forms 应用程序。 Azure 搜索是云服务，提供索引和查询上传的数据的功能。 这会删除基础结构要求和搜索算法复杂性通常与应用程序中实现搜索功能。


## <a name="related-links"></a>相关链接

- [Azure 搜索 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Azure 搜索文档](/azure/search/)
- [Microsoft Azure 搜索库](https://www.nuget.org/packages/Microsoft.Azure.Search/)
