---
title: 访问远程数据
description: 本章介绍了 eShopOnContainers 移动应用如何从容器化微服务访问数据。
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9c793f4d5f0cda5bff2dedef5e4e5e5bdfca69e5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70770806"
---
# <a name="accessing-remote-data"></a>访问远程数据

许多基于 web 的新式解决方案都利用 web 服务器托管的 web 服务，为远程客户端应用程序提供功能。 Web 服务公开的操作构成 web API。

客户端应用程序应能够利用 web API，而无需知道 API 公开的数据或操作是如何实现的。 这要求 API 遵守美国常见标准，使客户端应用程序和 web 服务能够同意要使用的数据格式，以及在客户端应用程序和 web 服务之间交换的数据的结构。

## <a name="introduction-to-representational-state-transfer"></a>具象状态传输简介

具象状态传输（REST）是基于超媒体构建分布式系统的体系结构样式。 REST 模型的主要优势在于它基于开放标准，并且不将模型的实现或访问它的客户端应用绑定到任何特定实现。 因此，REST web 服务可以使用 Microsoft ASP.NET 核心 MVC 来实现，客户端应用可以使用任何可生成 HTTP 请求并分析 HTTP 响应的语言和工具集进行开发。

REST 模型使用导航方案来表示网络上的对象和服务（称为资源）。 实现 REST 的系统通常使用 HTTP 协议来传输请求以访问这些资源。 在此类系统中，客户端应用程序以 URI 的形式提交请求，该 URI 用于标识资源和 HTTP 方法（如 GET、POST、PUT 或 DELETE），指示要对该资源执行的操作。 HTTP 请求的正文包含执行该操作所需的任何数据。

> [!NOTE]
> REST 定义无状态请求模型。 因此，HTTP 请求必须是独立的，并且可能以任意顺序出现。

来自 REST 请求的响应使用标准 HTTP 状态代码。 例如，返回有效数据的请求应包括 HTTP 响应代码200（正常），而无法找到或删除指定资源的请求则应返回包含 HTTP 状态代码404（未找到）的响应。

RESTful web API 公开一组连接资源，并提供核心操作，使应用程序可以操作这些资源并在这些资源之间轻松导航。 出于此原因，构成典型 RESTful web API 的 Uri 面向它所公开的数据，并使用 HTTP 提供的工具来操作此数据。

某个 HTTP 请求中的客户端应用包含的数据以及来自 web 服务器的相应响应消息可以用多种格式（称为媒体类型）提供。 当客户端应用发送在消息正文中返回数据的请求时，它可以在请求的 `Accept` 标头中指定它可以处理的媒体类型。 如果 web 服务器支持此媒体类型，则它可以使用包含指定消息正文中数据格式的 `Content-Type` 标头进行回复。 然后，客户端应用程序负责分析响应消息并相应地解释消息正文中的结果。

有关 REST 的详细信息，请参阅[api 设计](/azure/architecture/best-practices/api-design/)和[api 实现](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 移动应用使用模型-视图-ViewModel （MVVM）模式，该模式的模型元素表示应用中使用的域实体。 EShopOnContainers 引用应用程序中的 controller 和 repository 类接受并返回其中许多模型对象。 因此，它们用作数据传输对象（Dto），用于保存在移动应用和容器化微服务之间传递的所有数据。 使用 Dto 将数据传递给 web 服务并从 web 服务接收数据的主要好处是，通过单个远程调用传输更多的数据，应用程序可以减少需要进行的远程调用的数量。

### <a name="making-web-requests"></a>发出 Web 请求

EShopOnContainers 移动应用使用 `HttpClient` 类通过 HTTP 发出请求，使用 JSON 作为媒体类型。 此类提供了用于异步发送 HTTP 请求以及从 URI 标识的资源接收 HTTP 响应的功能。 @No__t_0 类表示发出 HTTP 请求后从 REST API 接收的 HTTP 响应消息。 它包含有关响应的信息，包括状态代码、标头和任何正文。 @No__t_0 类表示 HTTP 正文和内容标头，如 `Content-Type` 和 `Content-Encoding`。 可以使用任何 `ReadAs` 方法读取内容，例如 `ReadAsStringAsync` 和 `ReadAsByteArrayAsync`，具体取决于数据的格式。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>发出 GET 请求

@No__t_0 类用于管理目录微服务中的数据检索过程。 在 `ViewModelLocator` 类的 `RegisterDependencies` 方法中，`CatalogService` 类将注册为与 Autofac 依赖关系注入容器的 `ICatalogService` 类型的类型映射。 然后，在创建 `CatalogViewModel` 类的实例时，其构造函数将接受 Autofac 解析的 `ICatalogService` 类型，并返回 `CatalogService` 类的实例。 有关依赖关系注入的详细信息，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

图10-1 显示了从目录微服务读取目录数据以便 `CatalogView` 显示的类的交互。

[![](accessing-remote-data-images/catalogdata.png "Retrieving data from the catalog microservice")](accessing-remote-data-images/catalogdata-large.png#lightbox "Retrieving data from the catalog microservice")

**图 10-1**：从目录微服务检索数据

当导航到 `CatalogView` 时，将调用 `CatalogViewModel` 类中的 `OnInitialize` 方法。 此方法从目录微服务中检索目录数据，如下面的代码示例所示：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

此方法调用 Autofac 中注入 `CatalogViewModel` 的 `CatalogService` 实例的 `GetCatalogAsync` 方法。 下面的代码示例说明 `GetCatalogAsync` 方法：

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

此方法生成标识请求将发送到的资源的 URI，并在将结果返回到 `CatalogViewModel` 之前，使用 `RequestProvider` 类对资源调用 GET HTTP 方法。 @No__t_0 类包含的功能以 URI 的形式提交请求、用于标识资源的 HTTP 方法，指示要对该资源执行的操作，以及包含执行操作所需的任何数据的主体。 有关如何将 `RequestProvider` 类注入 `CatalogService class` 的信息，请参阅[依赖项注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下面的代码示例演示了 `RequestProvider` 类中的 `GetAsync` 方法：

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

此方法调用 `CreateHttpClient` 方法，该方法返回设置了相应标头的 `HttpClient` 类的实例。 然后，它将异步 GET 请求提交到由 URI 标识的资源，并将响应存储在 `HttpResponseMessage` 实例中。 然后调用 `HandleResponse` 方法，如果响应未包含成功的 HTTP 状态代码，则会引发异常。 然后，响应将作为字符串读取，从 JSON 转换为 `CatalogRoot` 对象，并返回到 `CatalogService` 中。

下面的代码示例演示了 `CreateHttpClient` 方法：

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

此方法创建 `HttpClient` 类的新实例，并将 `HttpClient` 实例发出的任何请求的 `Accept` 标头设置为 `application/json`，这表示它期望使用 JSON 格式化任何响应的内容。 然后，如果访问令牌作为参数传递给 `CreateHttpClient` 方法，则它将添加到 `HttpClient` 实例发出的任何请求的 `Authorization` 标头中，并以字符串 `Bearer` 作为前缀。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

当 `RequestProvider` 类中的 `GetAsync` 方法调用 `HttpClient.GetAsync` 时，将调用目录中的 `CatalogController` 类中的 `Items` 方法，如以下代码示例所示：

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

此方法使用 EntityFramework 从 SQL 数据库中检索目录数据，并将其作为包含成功 HTTP 状态代码的响应消息返回，并将 JSON 格式 `CatalogItem` 实例的集合返回。

#### <a name="making-a-post-request"></a>发出 POST 请求

@No__t_0 类用于管理购物车微服务的数据检索和更新过程。 在 `ViewModelLocator` 类的 `RegisterDependencies` 方法中，`BasketService` 类将注册为与 Autofac 依赖关系注入容器的 `IBasketService` 类型的类型映射。 然后，在创建 `BasketViewModel` 类的实例时，其构造函数将接受 Autofac 解析的 `IBasketService` 类型，并返回 `BasketService` 类的实例。 有关依赖关系注入的详细信息，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

图10-2 显示了将 `BasketView` 显示的购物篮数据发送到购物篮微服务的类的交互。

[![](accessing-remote-data-images/basketdata.png "Sending data to the basket microservice")](accessing-remote-data-images/basketdata-large.png#lightbox "Sending data to the basket microservice")

**图 10-2**：向购物篮发送数据微服务

将某项添加到购物篮后，将调用 `BasketViewModel` 类中的 `ReCalculateTotalAsync` 方法。 此方法更新购物篮中项的总值，并将购物篮数据发送到购物篮微服务，如下面的代码示例所示：

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

此方法调用 Autofac 中注入 `BasketViewModel` 的 `BasketService` 实例的 `UpdateBasketAsync` 方法。 下面的方法演示 `UpdateBasketAsync` 方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

此方法生成标识请求将发送到的资源的 URI，并在将结果返回到 `BasketViewModel` 之前，使用 `RequestProvider` 类对资源调用 POST HTTP 方法。 请注意，在身份验证过程中从 IdentityServer 获取的访问令牌是向购物篮微服务授权请求所必需的。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下面的代码示例演示了 `RequestProvider` 类中的 `PostAsync` 方法之一：

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

此方法调用 `CreateHttpClient` 方法，该方法返回设置了相应标头的 `HttpClient` 类的实例。 然后，它会将异步 POST 请求提交到由 URI 标识的资源，并以 JSON 格式发送序列化篮数据，并将响应存储在 `HttpResponseMessage` 实例中。 然后调用 `HandleResponse` 方法，如果响应未包含成功的 HTTP 状态代码，则会引发异常。 然后，响应将作为字符串读取，从 JSON 转换为 `CustomerBasket` 对象，并返回到 `BasketService` 中。 有关 `CreateHttpClient` 方法的详细信息，请参阅[发出 GET 请求](#making_a_get_request)。

当 `RequestProvider` 类中的 `PostAsync` 方法调用 `HttpClient.PostAsync` 时，将调用 "购物篮" 中 `BasketController` 类中的 `Post` 方法。将调用 API 项目，如以下代码示例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

此方法使用 `RedisBasketRepository` 类的实例将购物篮数据保存到 Redis 缓存中，并将其作为包含 success HTTP 状态代码和 JSON 格式 `CustomerBasket` 实例的响应消息返回。

#### <a name="making-a-delete-request"></a>发出删除请求

图10-3 显示了从购物篮微服务中删除购物篮数据的类的交互，适用于 `CheckoutView`。

![](accessing-remote-data-images/checkoutdata.png "Deleteing data from the basket microservice")

**图 10-3**：从购物篮中删除数据微服务

当调用签出进程时，将调用 `CheckoutViewModel` 类中的 `CheckoutAsync` 方法。 此方法在清除购物篮之前创建新订单，如以下代码示例所示：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

此方法调用 Autofac 中注入 `CheckoutViewModel` 的 `BasketService` 实例的 `ClearBasketAsync` 方法。 下面的方法演示 `ClearBasketAsync` 方法：

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

此方法生成标识请求将发送到的资源的 URI，并使用 `RequestProvider` 类对资源调用 DELETE HTTP 方法。 请注意，在身份验证过程中从 IdentityServer 获取的访问令牌是向购物篮微服务授权请求所必需的。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下面的代码示例演示了 `RequestProvider` 类中的 `DeleteAsync` 方法：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

此方法调用 `CreateHttpClient` 方法，该方法返回设置了相应标头的 `HttpClient` 类的实例。 然后，它会将异步删除请求提交到由 URI 标识的资源。 有关 `CreateHttpClient` 方法的详细信息，请参阅[发出 GET 请求](#making_a_get_request)。

当 `RequestProvider` 类中的 `DeleteAsync` 方法调用 `HttpClient.DeleteAsync` 时，将调用 "购物篮" 中 `BasketController` 类中的 `Delete` 方法。将调用 API 项目，如以下代码示例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

此方法使用 `RedisBasketRepository` 类的实例从 Redis 缓存中删除购物篮数据。

## <a name="caching-data"></a>缓存数据

可以通过将经常访问的数据缓存到靠近应用程序的快速存储来改进应用程序的性能。 如果快速存储比原始源更靠近应用程序，则缓存可以显著提高检索数据时的响应时间。

缓存最常见的形式是读取缓存，其中应用通过引用缓存来检索数据。 如果数据不在缓存中，则会从数据存储中检索数据并将其添加到缓存中。 应用可通过缓存端模式实现读缓存。 此模式确定项当前是否在缓存中。 如果项不在缓存中，则会从数据存储区中读取该项，并将其添加到缓存中。 有关详细信息，请参阅[缓存端](/azure/architecture/patterns/cache-aside/)模式。

> [!TIP]
> 缓存经常读取且不经常更改的数据。 此数据可在应用程序第一次检索时按需添加到缓存中。 这意味着，应用仅需从数据存储提取一次数据，并且可以通过使用缓存来满足后续访问权限。

分布式应用程序（如 eShopOnContainers reference 应用程序）应提供以下两个或其中一个缓存：

- 共享缓存，可由多个进程或计算机访问。
- 专用缓存，其中的数据保存在运行应用程序的设备上。

EShopOnContainers 移动应用使用专用缓存，其中的数据保存在运行应用实例的设备上。 有关 eShopOnContainers 引用应用程序使用的缓存的信息，请参阅[.Net 微服务：容器化 .Net 应用程序的体系结构](https://aka.ms/microservicesebook)。

> [!TIP]
> 将缓存视为暂时性的数据存储，可随时消失。 确保在原始数据存储以及缓存中维护数据。 如果缓存变得不可用，则会将丢失数据的可能性降至最低。

### <a name="managing-data-expiration"></a>管理数据过期

预计缓存数据将始终与原始数据保持一致。 原始数据存储中的数据可能在缓存后更改，导致缓存的数据过时。 因此，应用程序应该实现一种策略，该策略可帮助确保缓存中的数据尽可能更新，但也可以检测并处理缓存中的数据变得陈旧时出现的情况。 大多数缓存机制都允许将缓存配置为使数据过期，从而减少数据可能过期的时间段。

> [!TIP]
> 设置配置缓存时的默认过期时间。 许多缓存实现过期，这会使数据失效，并在指定的时间段内将数据从缓存中删除。 但是，在选择过期时间时必须小心谨慎。 如果此时间太短，数据将会太快过期，缓存的优点也将减少。 如果时间太长，数据风险就会变得陈旧。 因此，过期时间应该与使用该数据的应用程序的访问模式相匹配。

当缓存的数据过期时，它应从缓存中删除，应用程序必须从原始数据存储中检索数据并将其放回到缓存中。

如果允许数据长时间保留太长时间，则缓存可能会填满。 因此，在将新项添加到缓存中的请求可能需要在称为*逐出*的进程中删除某些项。 缓存服务通常在最近使用的基础上逐出数据。 但是，还有其他逐出策略，包括最近使用过的和先进先出的策略。有关详细信息，请参阅[缓存指南](/azure/architecture/best-practices/caching/)。

<a name="caching_images" />

### <a name="caching-images"></a>缓存图像

EShopOnContainers 移动应用使用从缓存中受益的远程产品映像。 这些图像由[`Image`](xref:Xamarin.Forms.Image)控件以及[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)库提供的 `CachedImage` 控件显示。

Xamarin [`Image`](xref:Xamarin.Forms.Image)控件支持缓存下载的映像。 默认情况下启用缓存，并在本地存储图像24小时。 此外，可以通过[`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)属性配置过期时间。 有关详细信息，请参阅[下载的映像缓存](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)。

FFImageLoading 的 `CachedImage` 控件是 Xamarin [`Image`](xref:Xamarin.Forms.Image)控件的替代，提供其他属性来实现补充功能。 在此功能中，控件提供可配置的缓存，同时支持错误和加载图像占位符。 下面的代码示例演示了 eShopOnContainers 移动应用如何使用 `ProductTemplate` 中的 `CachedImage` 控件，该控件是 `CatalogView` 中的[`ListView`](xref:Xamarin.Forms.ListView)控件使用的数据模板：

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

@No__t_0 控件将 `LoadingPlaceholder` 和 `ErrorPlaceholder` 属性设置为特定于平台的映像。 @No__t_0 属性指定在检索 `Source` 属性指定的图像时要显示的图像，`ErrorPlaceholder` 属性指定当尝试检索指定的图像时出现错误时要显示的图像 `Source`知识产权.

顾名思义，`CachedImage` 控件在设备上缓存远程图像，时间由 `CacheDuration` 属性的值指定。 如果未显式设置此属性值，则将应用默认值30天。

## <a name="increasing-resilience"></a>提高复原能力

与远程服务和资源通信的所有应用程序必须对暂时性故障敏感。 暂时性故障包括服务的网络连接暂时丢失、服务暂时不可用，或者服务繁忙时出现超时。 这些故障通常可自我纠正，如果在适当的延迟后重复操作，则可能会成功。

暂时性故障可能会对应用的感知质量产生巨大影响，即使已在所有可预见的情况下对其进行了全面的测试。 若要确保与远程服务通信的应用可以可靠地运行，它必须能够执行以下所有操作：

- 检测发生的错误，并确定故障是否可能是暂时性的。
- 如果确定故障可能是暂时性的，并跟踪操作重试的次数，请重试该操作。
- 使用适当的重试策略，它指定重试次数、每次尝试之间的延迟以及尝试失败后执行的操作。

可以通过在实现重试模式的代码中包装所有访问远程服务的尝试来实现此暂时性故障处理。

### <a name="retry-pattern"></a>重试模式

如果应用在尝试向远程服务发送请求时检测到故障，它可以通过以下任一方式来处理故障：

- 正在重试操作。 应用可能会立即重试失败的请求。
- 在延迟后重试操作。 在重试请求之前，应用应等待合适的时间。
- 正在取消该操作。 应用程序应取消操作并报告异常。

应根据应用程序的业务要求调整重试策略。 例如，将重试计数和重试间隔优化为尝试的操作非常重要。 如果操作是用户交互的一部分，则重试间隔应较短，且只需重试几次，以避免让用户等待响应。 如果操作是长时间运行的工作流的一部分，在这种情况下，取消或重新启动工作流的成本消耗高昂或耗时，则最好等待两次尝试之间的时间，然后重试更多次。

> [!NOTE]
> 尝试次数最短的重试策略和大量重试会使运行接近或达到容量的远程服务下降。 此外，此类重试策略还可能会影响应用的响应能力，前提是它正尝试执行失败的操作。

如果在重试多次后请求仍失败，则应用程序可以防止进一步请求进入同一资源并报告故障。 然后，在设定的时间段之后，应用程序可以对资源进行一个或多个请求，以查看它们是否成功。 有关详细信息，请参阅断路器[模式](#circuit_breaker_pattern)。

> [!TIP]
> 切勿实现无穷重试机制。 使用有限的重试次数，或实现[断路](/azure/architecture/patterns/circuit-breaker/)器模式以允许服务恢复。

发出 RESTful web 请求时，eShopOnContainers 移动应用当前未实现重试模式。 但是， [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)库提供的 `CachedImage` 控件通过重试映像加载来支持暂时性故障处理。 如果图像加载失败，将进行进一步的尝试。 尝试次数由 `RetryCount` 属性指定，并重试将在 `RetryDelay` 属性指定的延迟后发生。 如果未显式设置这些属性值，则将对 `RetryCount` 属性应用它们的默认值–3，并为 `RetryDelay` 属性应用250毫秒。 有关 `CachedImage` 控件的详细信息，请参阅[缓存图像](#caching_images)。

EShopOnContainers 引用应用程序执行重试模式。 有关详细信息，包括如何结合使用重试模式和 `HttpClient` 类的讨论，请参阅[.Net 微服务：容器化 .Net 应用程序的体系结构](https://aka.ms/microservicesebook)。

有关重试模式的详细信息，请参阅[重试](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>断路器模式

在某些情况下，故障可能是由于需要较长时间才能修复的预期事件引起的。 这些故障可能包括从部分连接断开到服务的完整故障。 在这些情况下，应用无法重试不可能成功的操作，而应接受操作失败并相应地处理此故障。

断路器模式可以防止应用程序重复尝试执行很可能失败的操作，同时使应用能够检测是否已解决错误。

> [!NOTE]
> 断路器模式的目的不同于重试模式。 重试模式使应用能够重试操作，并假定其会成功。 断路器模式阻止应用执行可能会失败的操作。

断路器充当可能失败的操作的代理。 代理应监视最近发生的失败数，并使用此信息来决定是允许操作继续，还是立即返回异常。

EShopOnContainers 移动应用当前未实现断路器模式。 但是，eShopOnContainers 会执行。 有关详细信息，请参阅[.Net 微服务：适用于容器化 .Net 应用程序的体系结构](https://aka.ms/microservicesebook)。

> [!TIP]
> 合并重试和断路器模式。 应用可以通过使用重试模式来通过断路器调用操作来合并重试和断路器模式。 但是，重试逻辑应对断路器返回的任何异常敏感，并在断路器指示故障不是暂时性时放弃重试尝试。

有关断路器模式的详细信息，请[参阅断路器模式。](/azure/architecture/patterns/circuit-breaker/)

## <a name="summary"></a>总结

许多基于 web 的新式解决方案都利用 web 服务器托管的 web 服务，为远程客户端应用程序提供功能。 Web 服务公开的操作构成 web API，客户端应用程序应能够利用 web API，而无需知道 API 公开的数据或操作是如何实现的。

可以通过将经常访问的数据缓存到靠近应用程序的快速存储来改进应用程序的性能。 应用可通过缓存端模式实现读缓存。 此模式确定项当前是否在缓存中。 如果项不在缓存中，则会从数据存储区中读取该项，并将其添加到缓存中。

与 web Api 通信时，应用程序必须对暂时性故障敏感。 暂时性故障包括服务的网络连接暂时丢失、服务暂时不可用，或者服务繁忙时出现超时。 这些故障通常可自我纠正，如果在适当的延迟后重复操作，则很可能会成功。 因此，应用程序应包装用于实现暂时性故障处理机制的代码中访问 web API 的所有尝试。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
