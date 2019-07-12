---
title: 访问远程数据
description: 本章介绍 eShopOnContainers 移动应用程序如何访问容器化微服务中的数据。
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a4c58139b0ddbaaedf5769eeac6585bac4c013e4
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832112"
---
# <a name="accessing-remote-data"></a>访问远程数据

许多基于 Web 的现代化解决方案使用 Web 服务器托管的 Web 服务来为远程客户端应用程序提供功能。 Web 服务公开的操作构成 web API。

客户端应用程序应该能够使用 web API，而不知道如何实现的数据或 API 公开的操作。 这要求该 API 遵守通用标准，使客户端应用程序和 web 服务来达成一致的客户端应用程序和 web 服务之间交换的数据结构和使用，哪些数据格式。

## <a name="introduction-to-representational-state-transfer"></a>具象状态传输简介

具象状态传输 (REST) 是用于构建基于超媒体的分布式的系统的体系结构样式。 REST 模型的主要优势是它具有基于开放标准，并不绑定该模型或访问任何特定实现到客户端应用程序的实现。 因此，无法使用 Microsoft ASP.NET Core MVC，实现 REST web 服务，并且无法使用任何语言和工具集，可以生成 HTTP 请求并分析 HTTP 响应开发客户端应用程序。

REST 模型使用导航方案来表示网络，称为资源上的对象和服务。 通常实现 REST 的系统使用 HTTP 协议来传输请求，从而访问这些资源。 在此类系统中，客户端应用程序提交中标识的资源的 URI 和 HTTP 方法 （如 GET、 POST、 PUT 或 DELETE），该值指示要对该资源执行的操作的窗体的请求。 HTTP 请求的正文包含执行该操作所需的任何数据。

> [!NOTE]
> REST 定义无状态请求模型。 因此，HTTP 请求必须是独立的并且可能会以任何顺序出现。

从 REST 响应请求可使用的标准 HTTP 状态代码。 例如，返回有效数据的请求应包括 HTTP 响应代码 200 （正常），而无法找到或删除指定的资源的请求应返回包含 HTTP 状态代码 404 （找不到） 的响应。

RESTful web API 公开一组连接的资源，并提供核心操作使应用处理这些资源，并在它们之间轻松导航。 出于此原因，构成典型 RESTful web API 的 Uri 都是面向它公开的数据以及使用设施提供通过 HTTP 对此数据进行操作。

包含通过 HTTP 请求，并从 web 服务器的相应响应消息中的客户端应用的数据会以各种格式，称为媒体类型。 当客户端应用程序发送一条消息的正文中返回数据的请求时，它可以指定它可以处理中的媒体类型`Accept`的请求标头。 如果 web 服务器支持此媒体类型，它可以使用包含的响应来应答`Content-Type`标头指定消息的正文中的数据的格式。 然后是客户端应用可以分析响应消息并相应地解释消息正文中的结果的责任。

有关 REST 的详细信息，请参阅[API 设计](/azure/architecture/best-practices/api-design/)并[API 实现](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 移动应用使用模型-视图-视图模型 (MVVM) 模式，并在应用中使用的域实体模式表示的模型元素。 EShopOnContainers 引用应用程序中的控制器和存储库类接受并返回很多这些模型对象。 因此，它们用作数据传输对象 (Dto) 保存的移动应用和容器化微服务之间传递的所有数据。 使用 Dto 将数据传递到和来自 web 服务接收数据的主要优势是，通过传输更多的数据一次远程调用，该应用程序可以减少不需要进行的远程调用的次数。

### <a name="making-web-requests"></a>发出 Web 请求

EShopOnContainers 移动应用使用`HttpClient`类，以通过 HTTP 使用正在使用的媒体类型的 JSON 发出请求。 此类提供用于以异步方式发送 HTTP 请求的功能和接收 HTTP 响应从 URI 标识的资源。 `HttpResponseMessage`类表示建立 HTTP 请求之后收到从 REST API 的 HTTP 响应消息。 它包含有关响应，包括状态代码、 标头和任何主体的信息。 `HttpContent`类表示的 HTTP 正文和内容标头，如`Content-Type`和`Content-Encoding`。 可以读取内容，使用任一`ReadAs`方法，如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，取决于数据的格式。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>发出 GET 请求

`CatalogService`类用于从目录微服务管理的数据检索过程。 在中`RegisterDependencies`中的方法`ViewModelLocator`类，`CatalogService`类注册针对的类型映射为`ICatalogService`Autofac 依赖关系注入容器使用的类型。 然后的实例时`CatalogViewModel`创建类，其构造函数接受`ICatalogService`类型，其中 Autofac 解析，返回的实例`CatalogService`类。 有关依赖关系注入的详细信息，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

图 10-1 显示了从目录微服务，用于显示通过读取目录数据的类的交互`CatalogView`。

[![](accessing-remote-data-images/catalogdata.png "从目录微服务中检索数据")](accessing-remote-data-images/catalogdata-large.png#lightbox "从目录微服务中检索数据")

**图 10-1**:从目录微服务中检索数据

当`CatalogView`导航到，`OnInitialize`中的方法`CatalogViewModel`调用类。 此方法从目录微服务，检索目录数据，如以下代码示例所示：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

此方法调用`GetCatalogAsync`方法`CatalogService`注入到的实例`CatalogViewModel`Autofac 通过。 下面的代码示例说明 `GetCatalogAsync` 方法：

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

此方法生成用于标识该请求将发送到的资源的 URI，并使用`RequestProvider`类用于对资源的 GET HTTP 方法调用返回的结果之前`CatalogViewModel`。 `RequestProvider`类包含提交的请求 URI 标识的资源，指示要在该资源上执行的操作的 HTTP 方法的窗体中的功能并执行该操作所需的正文，其中包含的任何数据。 有关如何信息`RequestProvider`注入类`CatalogService class`，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下面的代码示例演示`GetAsync`中的方法`RequestProvider`类：

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

此方法调用`CreateHttpClient`方法，返回的实例`HttpClient`设置了适当的标头的类。 然后将提交到与响应存储在由 URI 标识的资源的异步 GET 请求`HttpResponseMessage`实例。 `HandleResponse`然后调用方法，这将引发异常，如果响应不包括成功的 HTTP 状态代码。 然后从 JSON 到转换的字符串中读取响应`CatalogRoot`对象，并返回到`CatalogService`。

`CreateHttpClient`方法在下面的代码示例所示：

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

此方法创建的新实例`HttpClient`类，并设置`Accept`所做的任何请求标头`HttpClient`实例向`application/json`，指示它需要使用 JSON 格式化任何响应的内容。 然后，如果作为参数传递访问令牌`CreateHttpClient`方法，它将添加到`Authorization`所做的任何请求标头`HttpClient`实例，使用字符串作为前缀`Bearer`。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

当`GetAsync`中的方法`RequestProvider`类将调用`HttpClient.GetAsync`，则`Items`中的方法`CatalogController`调用 Catalog.API 项目中的类，下面的代码示例所示：

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

此方法从使用 EntityFramework，SQL 数据库中检索目录数据和将其作为响应消息，其中包含 HTTP 状态代码为成功，则返回，一系列 JSON 格式`CatalogItem`实例。

#### <a name="making-a-post-request"></a>发出 POST 请求

`BasketService`类用于管理数据检索和更新与购物篮微服务的过程。 在中`RegisterDependencies`中的方法`ViewModelLocator`类，`BasketService`类注册针对的类型映射为`IBasketService`Autofac 依赖关系注入容器使用的类型。 然后的实例时`BasketViewModel`创建类，其构造函数接受`IBasketService`类型，其中 Autofac 解析，返回的实例`BasketService`类。 有关依赖关系注入的详细信息，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

图 10-2 显示了发送所显示的购物篮数据的类的交互`BasketView`，向购物车微服务。

[![](accessing-remote-data-images/basketdata.png "将数据发送到购物篮微服务")](accessing-remote-data-images/basketdata-large.png#lightbox "将数据发送到购物篮微服务")

**图 10-2**:将数据发送到购物篮微服务

当的项添加到购物篮`ReCalculateTotalAsync`中的方法`BasketViewModel`调用类。 此方法更新购物篮中的项的总计值，并将篮数据发送到购物篮微服务，如下面的代码示例中所示：

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

此方法调用`UpdateBasketAsync`方法`BasketService`注入到的实例`BasketViewModel`Autofac 通过。 下面的方法演示`UpdateBasketAsync`方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

此方法生成用于标识该请求将发送到的资源的 URI，并使用`RequestProvider`类用于对资源的 POST HTTP 方法调用返回的结果之前`BasketViewModel`。 请注意，在身份验证过程中，从 IdentityServer 获取访问令牌，需要对购物车微服务的请求进行授权。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下面的代码示例显示了之一`PostAsync`中的方法`RequestProvider`类：

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

此方法调用`CreateHttpClient`方法，返回的实例`HttpClient`设置了适当的标头的类。 然后将提交到使用 JSON 格式和存储在响应中发送的序列化的购物篮数据由 URI 标识的资源的异步 POST 请求`HttpResponseMessage`实例。 `HandleResponse`然后调用方法，这将引发异常，如果响应不包括成功的 HTTP 状态代码。 然后，为从 JSON 到转换的字符串中读取的响应`CustomerBasket`对象，并返回到`BasketService`。 有关详细信息`CreateHttpClient`方法，请参阅[发出 GET 请求](#making_a_get_request)。

当`PostAsync`中的方法`RequestProvider`类将调用`HttpClient.PostAsync`，则`Post`中的方法`BasketController`调用 Basket.API 项目中的类，下面的代码示例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

此方法使用的实例`RedisBasketRepository`类到 Redis 缓存中，将篮数据暂留，并将其返回一条包含成功 HTTP 状态代码和 JSON 的响应消息的格式相同`CustomerBasket`实例。

#### <a name="making-a-delete-request"></a>执行删除请求

图 10-3 显示了删除从购物篮微服务的购物篮数据的类的交互`CheckoutView`。

![](accessing-remote-data-images/checkoutdata.png "正在删除数据从购物篮微服务")

**图 10-3**:从购物篮微服务中删除数据

签出过程调用时，`CheckoutAsync`中的方法`CheckoutViewModel`调用类。 此方法创建新订单之前清除购物篮，如下面的代码示例中所示：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

此方法调用`ClearBasketAsync`方法`BasketService`注入到的实例`CheckoutViewModel`Autofac 通过。 下面的方法演示`ClearBasketAsync`方法：

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

此方法生成的 URI 标识的资源的请求将发送到，并使用`RequestProvider`类来调用资源上的 DELETE HTTP 方法。 请注意，在身份验证过程中，从 IdentityServer 获取访问令牌，需要对购物车微服务的请求进行授权。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下面的代码示例演示`DeleteAsync`中的方法`RequestProvider`类：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

此方法调用`CreateHttpClient`方法，返回的实例`HttpClient`设置了适当的标头的类。 然后，它将提交到由 URI 标识的资源的异步删除请求。 有关详细信息`CreateHttpClient`方法，请参阅[发出 GET 请求](#making_a_get_request)。

当`DeleteAsync`中的方法`RequestProvider`类将调用`HttpClient.DeleteAsync`，则`Delete`中的方法`BasketController`调用 Basket.API 项目中的类，下面的代码示例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

此方法使用的实例`RedisBasketRepository`类，以从 Redis 缓存中删除购物篮数据。

## <a name="caching-data"></a>缓存数据

通过缓存到位置靠近的快速存储经常访问的数据可以提高应用性能的应用程序。 如果快速存储是更靠近应用比原始源，则缓存可以显著提高响应时间检索数据时。

缓存的最常见形式是直读缓存，应用程序通过引用缓存检索数据的位置。 如果数据不在缓存中，它从数据存储中检索并添加到缓存。 应用程序可以实现缓存端模式使用直读缓存。 此模式确定该项当前是否已在缓存中。 如果项目不在缓存中，它从数据存储中读取并添加到缓存。 有关详细信息，请参阅[缓存端](/azure/architecture/patterns/cache-aside/)模式。

> [!TIP]
> 它频繁读取且很少变化的缓存数据。 此数据可以添加到按需第一次检索的应用程序缓存。 这意味着应用程序需要从数据存储中提取数据一次，并且可以通过使用缓存来满足后续访问。

分布式应用程序，如 eShopOnContainers 参考应用程序，应提供一个或两个以下的缓存：

-   共享的缓存，可由多个进程或计算机访问。
-   专用缓存，其中数据保存在本地运行该应用程序在设备。

EShopOnContainers 移动应用使用专用缓存，其中数据保存在本地运行应用程序实例中的设备。 有关使用 eShopOnContainers 引用应用程序的缓存的信息，请参阅[.NET 微服务：适用于容器化 .NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

> [!TIP]
> 将缓存视为可能随时消失的暂时性数据存储。 请确保数据都保留在原始数据存储，以及缓存。 如果缓存变得不可用，丢失数据的风险然后最小化。

### <a name="managing-data-expiration"></a>管理数据过期

是不切实际的期望缓存的数据始终保持与原始数据保持一致的。 它已缓存，从而导致缓存会变得陈旧的数据后，可能会更改原始数据存储区中的数据。 因此，应用应实现有助于确保缓存中的数据尽可能保持最新的策略，但可以还检测并处理缓存中的数据变得陈旧时出现的情况。 最缓存机制启用缓存后，若要配置为过期数据，并因此缩短为其数据可能是过期期限。

> [!TIP]
> 设置默认过期时间时配置缓存的时间。 许多缓存实施过期，这使无效数据，并将其删除从缓存中，如果不能指定段内访问。 但是，选择的过期时间时，必须格外小心。 如果它由太短，很快就会过期数据，并且将减少缓存的优点。 如果它由太长，会变得陈旧的数据的风险。 因此，到期时间应与访问的应用程序使用的数据的模式匹配。

时缓存的数据会过期、 应从缓存中删除和检索应用程序必须从原始数据的数据存储，并将其放回缓存。

还有可能缓存可能会填满如果允许数据太长一段时间内保持不变。 因此，将新项添加到缓存请求可能需要在名为的进程中删除一些项*逐出*。 缓存服务通常逐出最早使用的基础上的数据。 但是，有其他逐出策略，包括最近的使用，和的第一个扩展。有关详细信息，请参阅[缓存指南](/azure/architecture/best-practices/caching/)。

<a name="caching_images" />

### <a name="caching-images"></a>缓存的图像

EShopOnContainers 移动应用程序使用远程产品图像的从缓存中受益。 这些映像将由[ `Image` ](xref:Xamarin.Forms.Image)控件，并`CachedImage`由提供控制[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)库。

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image)控件支持缓存下载的图像。 默认情况下，启用缓存，并将该映像存储 24 小时内的本地。 此外，使用配置的过期时间[ `CacheValidity` ](xref:Xamarin.Forms.UriImageSource.CacheValidity)属性。 有关详细信息，请参阅[下载图像缓存](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)。

FFImageLoading 的`CachedImage`控件是适用于 Xamarin.Forms 的替代[ `Image` ](xref:Xamarin.Forms.Image)控件，提供启用补充功能的其他属性。 在此功能，该控件提供了可配置缓存，同时支持错误和加载图像的占位符。 以下代码示例演示如何使用 eShopOnContainers 移动应用`CachedImage`控制`ProductTemplate`，这是使用的数据模板[ `ListView` ](xref:Xamarin.Forms.ListView)控件中`CatalogView`:

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

`CachedImage`控件将设置`LoadingPlaceholder`和`ErrorPlaceholder`属性设置为特定于平台的映像。 `LoadingPlaceholder`属性指定要由指定的图像时显示的图像`Source`检索属性，并`ErrorPlaceholder`属性指定出错时尝试检索图像时显示的图像通过指定`Source`属性。

正如其名字`CachedImage`控件的值指定的时间缓存在设备上的远程图像`CacheDuration`属性。 当此属性的值不显式设置时，应用默认值为 30 天。

## <a name="increasing-resilience"></a>提高恢复能力

与远程服务和资源进行通信的所有应用程序必须对暂时性故障敏感。 暂时性故障包括瞬间断开网络连接到服务、 临时不可用服务，或者当服务繁忙时出现超时。 这些故障通常是可以自己修复，并且如果在适当的延迟后重复操作，则可能会成功。

暂时性故障可以感知质量的应用程序，具有很大的影响，即使已在所有可预见的情况下全面测试。 若要确保与远程服务进行通信的应用程序操作可靠，它必须能够执行所有以下操作：

-   检测到故障的发生，并确定是否可能是暂时性错误。
-   如果确定该错误很可能是暂时性的跟踪的操作重试次数，重试该操作。
-   使用适当的重试策略，用于指定数目的重试，尝试失败后要采取的每次尝试和操作之间的延迟。

可以通过访问代码来实现重试模式中的远程服务的所有尝试都包装实现此暂时性故障处理。

### <a name="retry-pattern"></a>重试模式

如果应用程序检测到故障后尝试向远程服务发送请求时，它可以在任何通过以下方式处理失败：

-   重试该操作。 应用程序无法立即重试失败的请求。
-   重试该操作的延迟后。 应用程序应等待的合适重试请求之前的时间量。
-   正在取消该操作。 应用程序应取消操作并报告异常。

应调整重试策略，以匹配应用程序的业务要求。 例如，务必优化重试计数，然后重试间隔传输到正在尝试的操作。 如果该操作是用户交互的一部分，重试间隔应该较短，且仅几次重试尝试以避免让用户等待响应。 如果操作是长时间运行工作流的一部分，正在取消或重新启动工作流是代价高昂或耗时，则相应两次尝试之间等待更长时间，重试更多次。

> [!NOTE]
> 积极重试策略与之间次数和大量的重试次数，最小的延迟可能会降低接近或最大程度上运行的远程服务。 此外，重试策略还可能会影响应用的响应能力如果不断尝试执行失败的操作。

如果请求仍然失败重试次数后，最好是为应用程序以防止转到同一资源的后续请求，并报告失败。 然后后一段，, 该应用程序可以对一个或多个请求到要查看其是否成功的资源。 有关详细信息，请参阅[断路器模式](#circuit_breaker_pattern)。

> [!TIP]
> 永远不会实现无限的重试机制。 使用有限数量的重试，或实现[断路器](/azure/architecture/patterns/circuit-breaker/)模式，使服务能够恢复。

EShopOnContainers 移动应用程序目前不实现重试模式，发出 rest 样式 web 请求时。 但是，`CachedImage`由提供控制[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)库支持通过映像加载重试的暂时性故障处理。 如果图像加载失败，将进行更多尝试。 指定的尝试次数`RetryCount`属性，然后重试将由指定的延迟之后发生`RetryDelay`属性。 如果这些属性值不显式设置，其默认值应用 – 3`RetryCount`属性，并为 250 毫秒`RetryDelay`属性。 有关详细信息`CachedImage`控件，请参阅[缓存映像](#caching_images)。

EShopOnContainers 引用应用程序实现重试模式。 有关详细信息，其中讨论了如何结合使用的重试模式`HttpClient`类，请参阅[.NET 微服务：适用于容器化 .NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

有关重试模式的详细信息，请参阅[重试](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>断路器模式

在某些情况下，错误可能是因为需要更长时间，若要修复的预期事件。 这些故障的范围可以从部分连接断开到服务完全故障。 在这些情况下，这是毫无意义，因为应用程序以重试不太可能成功，并改为应接受的操作失败，相应地处理此失败的操作。

断路器模式可以防止应用程序重复尝试执行的操作，则可能会失败，同时还能实现应用程序以检测是否已经解决该错误。

> [!NOTE]
> 断路器模式的目的与重试模式不同。 重试模式让应用程序以重试将成功的假定条件下一个操作。 断路器模式阻止应用执行的操作，则可能会失败。

断路器充当可能失败的操作的代理。 该代理应监视最近发生了的故障数并使用此信息来决定是否允许操作继续进行，或立即返回异常。

EShopOnContainers 移动应用程序目前不实现断路器模式。 但是，eShopOnContainers 将执行。 有关详细信息，请参阅[.NET 微服务：适用于容器化 .NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

> [!TIP]
> 组合的重试和断路器模式。 应用程序可以通过使用重试模式来调用通过断路器操作组合的重试和断路器模式。 但是，重试逻辑应能敏锐觉察断路器返回的任何异常并在断路器指示故障不是暂时性的情况下放弃重试尝试。

断路器模式的详细信息，请参阅[断路器](/azure/architecture/patterns/circuit-breaker/)模式。

## <a name="summary"></a>总结

许多基于 Web 的现代化解决方案使用 Web 服务器托管的 Web 服务来为远程客户端应用程序提供功能。 Web 服务公开的操作可构成 Web API，并且客户端应用应能够利用 Web API，但不需要了解该 API 公开的数据或操作的实现方式。

通过缓存到位置靠近的快速存储经常访问的数据可以提高应用性能的应用程序。 应用程序可以实现缓存端模式使用直读缓存。 此模式确定该项当前是否已在缓存中。 如果项目不在缓存中，它从数据存储中读取并添加到缓存。

Web Api 与沟通时，应用必须对暂时性故障敏感。 暂时性故障包括瞬间断开网络连接到服务、 临时不可用服务，或者当服务繁忙时出现超时。 这些故障通常是可以自己修复，并适当的延迟后重复操作，则它可能会成功。 因此，应用应包装所有尝试访问 web API 中实现暂时性故障处理机制的代码。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
