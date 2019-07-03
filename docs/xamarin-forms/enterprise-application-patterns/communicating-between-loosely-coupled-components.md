---
title: 松散耦合组件之间的通信
description: '这一章介绍了如何在 eShopOnContainers 的移动应用实现发布-订阅模式，允许基于消息的不太方便链接对象和类型引用的组件之间的通信 '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9848d2b832990032bc7eb7f2e3a93c896457134c
ms.sourcegitcommit: e95296f9e516975f5f32d822c323a71fd84007b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538699"
---
# <a name="communicating-between-loosely-coupled-components"></a>松散耦合组件之间的通信

发布-订阅模式是发布服务器将消息发送而无需了解任何接收方，名为订阅服务器的消息传递模式。 同样，订阅服务器侦听的特定消息，而无需任何发布服务器的知识。

在.NET 中的事件实现发布-订阅模式，并且是最简单明了的方法的通信层之间是否不必需的如控件和包含它的页进行松散耦合的组件。 但是，发布服务器和订阅服务器生存期耦合到对方，对象引用，并且订阅服务器类型必须具有对发布服务器类型的引用。 尤其是在有订阅事件的静态或长期存在的对象的短生存期的对象，这可以创建内存管理问题。 如果未移除事件处理程序，订阅服务器上将会保持活动状态，在发布服务器上，对它的引用，这将阻止或延迟的订阅服务器的垃圾回收。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 简介

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) 类可实现发布-订阅模式，允许在对象和类型引用不便于链接的组件之间进行基于消息的通信。 此机制允许发布者和订阅者在无需相互引用的情况下进行通信，帮助减少组件之间的依赖性，同时还允许这些组件接受独立开发和测试。

[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类提供了多播的发布-订阅功能。 这意味着可以有多个发布服务器的发布一条消息，并且可以有多个订阅服务器侦听的同一消息。 图 4 1 说明了此关系：

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "多播发布-订阅功能")

**图 4-1:** 多播发布-订阅功能

发布服务器将使用的消息发送[ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法，而订阅服务器侦听的使用的消息[ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法。 此外，订阅者可以还取消订阅消息订阅，如有必要，使用[ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法。

在内部， [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类使用弱引用。 这意味着它不会使对象保持活动状态，并将允许它们进行垃圾回收。 因此，它应仅有必要类不再希望收到消息时从消息取消订阅。

EShopOnContainers 移动应用使用[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类之间进行通信松散耦合组件。 应用程序定义了三个消息：

-   `AddProduct`发布消息`CatalogViewModel`类项添加到购物篮时。 反过来，`BasketViewModel`类订阅的消息并递增的购物篮中响应中的项数。 此外，`BasketViewModel`类还取消订阅此消息。
-   `Filter`发布消息`CatalogViewModel`类时用户适用于从目录中显示的项的品牌或类型筛选器。 反过来，`CatalogView`类订阅的消息，并更新 UI，以便显示与筛选条件匹配的项。
-   `ChangeTab`发布消息`MainViewModel`类时`CheckoutViewModel`导航到`MainViewModel`以下成功地创建和提交新订单。 反过来，`MainView`类订阅的消息和更新 UI 因此的**我的个人资料**选项卡处于活动状态，以显示用户的订单。

> [!NOTE]
> 虽然[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类允许松散耦合类之间的通信，它不提供此问题仅体系结构的解决方案。 例如，仅由绑定引擎和通过属性更改通知时，也可以实现视图模型和视图之间的通信。 此外，还可以通过导航期间传递的数据实现两个视图模型之间的通信。

在 eShopOnContainers 移动应用中， [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)用于更新到另一个类中发生的操作的响应中的用户界面中。 因此，消息是，在 UI 线程上发布与订阅服务器接收消息的同一线程上。

> [!TIP]
> 将封送到 UI 线程在执行 UI 更新的时间。 如果需要更新 UI 从后台线程发送一条消息，则处理该消息在订阅服务器在 UI 线程上的调用[ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法。

有关详细信息[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)，请参阅[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定义的消息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 消息是用于标识消息的字符串。 以下代码示例演示在 eShopOnContainers 的移动应用中定义的消息：

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

在此示例中，使用常量定义消息。 此方法的优点是，它提供了编译时类型安全和重构支持。

## <a name="publishing-a-message"></a>发布消息

发布者通知之一的消息的订户[ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)重载。 下面的代码示例演示如何发布`AddProduct`消息：

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

在此示例中， [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法指定三个参数：

-   第一个参数指定的发件人类。 必须通过任何订阅者想要接收该消息指定的发件人类。
-   第二个参数指定的消息。
-   第三个参数指定要发送到订阅服务器上的有效负载数据。 在此示例有效负载数据是`CatalogItem`实例。

[ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法将发布的消息和及其有效负载数据，使用即发即弃方法。 因此，即使没有注册以接收消息的订阅服务器发送消息。 这种情况下，将忽略发送的消息。

> [!NOTE]
> [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法可以使用泛型参数来控制如何传递消息。 因此，不同的订阅服务器可以接收共享消息标识但发送不同的有效负载数据类型的多个消息。

## <a name="subscribing-to-a-message"></a>为消息订阅

订户可以注册以使用之一接收消息[ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)重载。 下面的代码示例演示如何在 eShopOnContainers 的移动应用订阅，并处理时，`AddProduct`消息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此示例中， [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法订阅`AddProduct`消息，并接收消息的响应执行的回调委托。 指定为 lambda 表达式，此回调委托执行更新 UI 的代码。

> [!TIP]
> 请考虑使用不可变的有效负载数据。 请勿尝试修改中的有效负载数据中的回调委托，因为多个线程可能会接收到的数据同时访问。 在此方案中，有效负载数据应为不可变，以避免并发错误。

订阅服务器可能不需要处理的已发布消息，每个实例，这可以由指定的泛型类型参数控制[ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法。 在此示例中，订阅服务器上只会收到`AddProduct`从发送的消息`CatalogViewModel`类，其有效负载数据是`CatalogItem`实例。

## <a name="unsubscribing-from-a-message"></a>取消订阅一条消息

订阅服务器可以随时取消订阅他们不再想要接收的消息。 这其中一个来实现[ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)重载，如下面的代码示例中所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

在此示例中， [ `Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法语法反映指定订阅以接收时的类型参数`AddProduct`消息。

## <a name="summary"></a>总结

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) 类可实现发布-订阅模式，允许在对象和类型引用不便于链接的组件之间进行基于消息的通信。 此机制允许发布者和订阅者在无需相互引用的情况下进行通信，帮助减少组件之间的依赖性，同时还允许这些组件接受独立开发和测试。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
