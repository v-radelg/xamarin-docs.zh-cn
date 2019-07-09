---
title: 使用 Azure Cosmos DB 文档数据库和 Xamarin.Forms 的用户进行身份验证
description: 本文介绍如何将访问控制与 Azure Cosmos DB 分区集合相结合，以便用户只能访问自己的 Xamarin.Forms 应用程序中的文档。
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 52f4e33e051c99b7002120c055c79036828d60dd
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658864"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>使用 Azure Cosmos DB 文档数据库和 Xamarin.Forms 的用户进行身份验证

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)

_Azure Cosmos DB 文档数据库支持已分区的集合，它可以跨多个服务器和分区，同时支持无限的存储和吞吐量。本文介绍如何将访问控制与已分区集合相结合，以便用户只能访问自己的 Xamarin.Forms 应用程序中的文档。_

## <a name="overview"></a>概述

创建已分区的集合时，必须指定分区键，并具有相同的分区键的文档将存储在同一个分区。 因此，用户的标识指定为分区键将导致一个已分区集合，其中只会存储为该用户的文档。 这还可以确保 Azure Cosmos DB 文档数据库会随着用户数量和项增加。

任何集合，必须授予访问权限和 SQL API 的访问控制模型定义了两种类型的访问构造：

- **主密钥**启用完全管理访问权限在 Cosmos DB 帐户中，所有资源并创建 Cosmos DB 帐户时创建。
- **资源令牌**捕获数据库用户和用户具有对某个特定的 Cosmos DB 资源，如集合或文档的权限之间的关系。

公开一个主密钥将打开到可能的恶意或负面使用 Cosmos DB 帐户。 但是，Azure Cosmos DB 资源令牌提供一种允许客户端读取、 写入和删除根据授予的权限的 Azure Cosmos DB 帐户中的特定资源的安全机制。

请求的典型方法，生成，并将资源令牌传递到移动应用程序是使用资源令牌代理。 下图显示了示例应用程序如何使用资源令牌代理来管理对文档数据库数据的访问的高级概述：

![](azure-cosmosdb-auth-images/documentdb-authentication.png "文档数据库身份验证过程")

资源令牌代理是托管在 Azure 应用服务拥有 Cosmos DB 帐户的主密钥的中间层 Web API 服务。 示例应用程序使用资源令牌代理来管理对文档数据库数据的访问，如下所示：

1. 登录名，Xamarin.Forms 应用程序联系 Azure 应用服务，以启动身份验证流。
1. Azure 应用服务执行与 Facebook OAuth 身份验证流。 身份验证流完成后，Xamarin.Forms 应用程序收到的访问令牌。
1. Xamarin.Forms 应用程序使用访问令牌请求从资源令牌代理资源令牌。
1. 资源令牌代理使用的访问令牌请求从 Facebook 用户的标识。 然后使用用户的标识从 Cosmos DB，用于授予对经过身份验证的用户的已分区集合的读/写访问请求的资源令牌。
1. Xamarin.Forms 应用程序使用的资源令牌定义的资源令牌的权限直接访问 Cosmos DB 资源。

> [!NOTE]
> 资源令牌到期后，后续文档数据库的请求将收到 401 未经授权的异常。 此时，Xamarin.Forms 应用程序应重新建立标识，并请求新的资源令牌。

有关 Cosmos DB 分区的详细信息，请参阅[如何进行分区和缩放 Azure Cosmos DB 中的](/azure/cosmos-db/partition-data/)。 有关 Cosmos DB 的访问控制的详细信息，请参阅[保护对 Cosmos DB 数据访问](/azure/cosmos-db/secure-access-to-data/)并[SQL API 中的访问控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。

## <a name="setup"></a>安装

将资源令牌中转站集成到 Xamarin.Forms 应用程序的过程如下所示：

1. 创建一个将使用访问控制的 Cosmos DB 帐户。 有关详细信息，请参阅[Cosmos DB 配置](#cosmosdb_configuration)。
1. 创建 Azure 应用服务，以托管资源令牌中转站。 有关详细信息，请参阅[Azure 应用服务配置](#app_service_configuration)。
1. 创建 Facebook 应用程序来执行身份验证。 有关详细信息，请参阅[Facebook 应用程序配置](#facebook_configuration)。
1. 配置 Azure 应用服务来执行与 Facebook 的简单身份验证。 有关详细信息，请参阅[Azure 应用服务身份验证配置](#app_service_authentication_configuration)。
1. 配置 Xamarin.Forms 示例应用程序与 Azure 应用服务和 Cosmos DB 进行通信。 有关详细信息，请参阅[Xamarin.Forms 应用程序配置](#forms_application_configuration)。

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB 配置

创建一个将使用访问控制的 Cosmos DB 帐户的过程如下所示：

1. 创建 Cosmos DB 帐户。 有关详细信息，请参阅[创建 Azure Cosmos DB 帐户](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 在 Cosmos DB 帐户中，创建一个名为的新集合`UserItems`，指定的分区键`/userid`。

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Azure 应用服务配置

托管资源令牌中转站，在 Azure 应用服务中的过程如下所示：

1. 在 Azure 门户中，创建新的应用服务 web 应用。 有关详细信息，请参阅[在应用服务环境中创建 web 应用](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 Azure 门户中，打开 web 应用的应用设置边栏选项卡并添加以下设置：
    - `accountUrl` -此值应从 Cosmos DB 帐户的密钥边栏选项卡的 Cosmos DB 帐户 URL。
    - `accountKey` -此值应为 Cosmos DB 主要密钥 （主密钥或辅助） 从 Cosmos DB 帐户的密钥边栏选项卡。
    - `databaseId` – 值应为 Cosmos DB 数据库的名称。
    - `collectionId` – 值应为 Cosmos DB 集合的名称 (在这种情况下， `UserItems`)。
    - `hostUrl` – 值应为应用服务帐户的概述边栏选项卡中的 web 应用程序的 URL。

    下面的屏幕截图演示了此配置：

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "应用服务 Web 应用设置")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "应用服务 Web 应用设置")

1. 将资源令牌中转站解决方案发布到 Azure 应用服务 web 应用。

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Facebook 应用程序配置

创建 Facebook 应用程序来执行身份验证的过程如下所示：

1. 创建 Facebook 应用程序。 有关详细信息，请参阅[注册和配置应用](https://developers.facebook.com/docs/apps/register)在 Facebook 开发人员中心。
1. 向应用添加 Facebook 登录产品。 有关详细信息，请参阅[到您的应用程序或网站中添加 Facebook 登录](https://developers.facebook.com/docs/facebook-login)在 Facebook 开发人员中心。
1. 配置 Facebook 登录名，如下所示：
  - 启用客户端 OAuth 登录。
  - 启用 Web OAuth 登录。
  - 设置有效的 OAuth 重定向到的应用服务 web 应用，URI 的 URI 与`/.auth/login/facebook/callback`追加。

  下面的屏幕截图演示了此配置：

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook 登录 OAuth 设置")

有关详细信息，请参阅[向 Facebook 注册应用程序](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Azure 应用服务身份验证配置

配置应用服务轻松身份验证的过程如下所示：

1. 在 Azure 门户中，导航到应用服务 web 应用。
1. 在 Azure 门户中，打开身份验证 / 授权边栏选项卡并执行以下配置：
  - 应启用应用服务身份验证。
  - 当请求未经过身份验证时要执行的操作应设置为**使用 Facebook 登录**。

  下面的屏幕截图演示了此配置：

  [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "应用服务 Web 应用身份验证设置")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "应用服务 Web 应用身份验证设置")

应用服务 web 应用还应配置为与 Facebook 应用启用身份验证流进行通信。 这可以通过选择 Facebook 标识提供程序，并输入**应用程序 ID**和**应用程序密码**Facebook 开发人员中心上的 Facebook 应用程序设置中的值。 有关详细信息，请参阅[到你的应用程序添加 Facebook 信息](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Xamarin.Forms 应用程序配置

配置 Xamarin.Forms 示例应用程序的过程如下所示：

1. 打开 Xamarin.Forms 解决方案。
1. 打开`Constants.cs`和更新的以下常量：
  - `EndpointUri` -此值应从 Cosmos DB 帐户的密钥边栏选项卡的 Cosmos DB 帐户 URL。
  - `DatabaseName` – 值应为文档数据库的名称。
  - `CollectionName` – 值应为文档数据库集合的名称 (在这种情况下， `UserItems`)。
  - `ResourceTokenBrokerUrl` -此值应从应用服务帐户的概述边栏选项卡的资源令牌代理 web 应用的 URL。

## <a name="initiating-login"></a>发起的登录名

示例应用程序将使用 Xamarin.Auth 浏览器重定向到标识提供者 URL，如下面的示例代码所示启动登录过程：

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

这会导致用于在 Azure 应用服务和 Facebook，Facebook 登录页面将显示之间启动一个 OAuth 身份验证流程：

![](azure-cosmosdb-auth-images/login.png "Facebook 登录")

可以通过按取消该登录名**取消**按钮在 iOS 上或按**回**在 Android 上，在这种情况下用户一直处于未经身份验证和标识提供程序用户界面是按钮从屏幕上删除。

有关 Xamarin.Auth 详细信息，请参阅[进行身份验证用户的标识提供者](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="obtaining-a-resource-token"></a>获取资源令牌

以下身份验证成功，`WebRedirectAuthenticator.Completed`事件触发。 下面的代码示例演示如何处理此事件：

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

成功的身份验证的结果是访问令牌，这是可用`AuthenticatorCompletedEventArgs.Account`属性。 提取并在 GET 请求对资源令牌代理中使用访问令牌`resourcetoken`API。

`resourcetoken` API 使用访问令牌从 Facebook，又用于请求从 Cosmos DB 资源令牌请求用户的标识。 如果在文档数据库中用户已存在有效的权限的文档，检索并包含资源令牌的 JSON 文档返回到 Xamarin.Forms 应用程序。 如果用户不存在有效的权限文档，文档数据库中创建用户和权限和资源令牌是从权限文档中提取的返回的 JSON 文档中的 Xamarin.Forms 应用程序。

> [!NOTE]
> 文档数据库用户是文档数据库，与关联的资源，每个数据库可以包含零个或多个用户。 文档数据库的权限是与文档数据库用户时，关联的资源，每个用户可能包含零个或多个权限。 权限资源提供访问权限时尝试访问对文档等资源需要用户的安全令牌。

如果`resourcetoken`API 已成功完成后，它将发送 HTTP 状态代码 200 （正常） 在响应中，以及包含该资源令牌的 JSON 文档。 以下 JSON 数据显示了典型的成功响应消息：

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

`WebRedirectAuthenticator.Completed`事件处理程序读取的响应`resourcetoken`API 并提取资源标记和用户 id。资源令牌作为参数传递`DocumentClient`构造函数，它封装终结点、 凭据和用来访问 Cosmos DB 连接策略，用于配置和执行针对 Cosmos DB 的请求。 资源令牌与直接访问资源，每个请求一起发送，并指示授予对经过身份验证的用户的已分区集合的读/写访问。

## <a name="retrieving-documents"></a>检索文档

可以通过在下面的代码示例创建包含用户的 id 作为分区键，并演示文档查询检索只能属于已经过身份验证的用户的文档：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

查询以异步方式检索所有属于已经过身份验证的用户，从指定集合的文档，并将它们放入`List<TodoItem>`显示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`表示应对于文档，查询的集合的参数和一个`FeedOptions`对象。 `FeedOptions`对象指定无限的数量的项可以由查询和用户的 id 作为分区键。 这可确保只有用户的已分区集合中的文档返回结果。

> [!NOTE]
> 请注意权限文档，这创建的资源令牌中转站，都存储在同一文档集合创建 Xamarin.Forms 应用程序的文档。 因此，文档查询包含`Where`筛选谓词应用于针对文档集合的查询子句。 此子句可确保从文档集合不返回权限的文档。

文档集合中检索文档的详细信息，请参阅[检索文档集合文档](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query)。

## <a name="inserting-documents"></a>插入文档

在将文档插入到文档集合之前,`TodoItem.UserId`应使用正在使用作为分区键的值更新属性，如下面的代码示例中所示：

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

这可确保该文档，将插入到用户的已分区集合。

有关将文档插入到文档集合的详细信息，请参阅[将文档插入到文档集合](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document)。

## <a name="deleting-documents"></a>删除文档

删除分区的集合，从文档中下面的代码示例演示时，必须指定分区键值：

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

这可确保 Cosmos DB，知道其分区集合中的文档中删除。

有关文档集合中删除文档的详细信息，请参阅[文档集合中删除文档](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document)。

## <a name="summary"></a>总结

本文介绍了如何将访问控制与已分区集合相结合，以便用户只能访问自己的 Xamarin.Forms 应用程序中的文档数据库文档。 用户的标识指定为分区键可确保分区的集合可以只存储为该用户的文档。


## <a name="related-links"></a>相关链接

- [Todo Azure Cosmos DB 身份验证 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [使用 Azure Cosmos DB 文档数据库](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [保护对 Azure Cosmos DB 数据访问](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中的访问控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何进行分区和缩放 Azure Cosmos DB 中](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB 客户端库](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
