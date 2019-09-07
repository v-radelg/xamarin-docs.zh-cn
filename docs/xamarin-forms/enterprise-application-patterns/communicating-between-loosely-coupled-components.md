---
title: 松散耦合组件之间的通信
description: '本章介绍了 eShopOnContainers 移动应用如何实现发布-订阅模式，允许在不方便地通过对象和类型引用进行链接的组件之间进行基于消息的通信 '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d4ed362fdd5587eabc028949b82682922adead0a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760311"
---
# <a name="communicating-between-loosely-coupled-components"></a>松散耦合组件之间的通信

发布-订阅模式是一种消息传递模式，在此模式下，发布者可在无需知道任何接收方（称为订阅方）的情况下发送消息。 同样，订阅方可在不了解任何发布方的情况下侦听特定消息。

.NET 中的事件可实现发布-订阅模式，并且是组件之间的通信层的最简单、直接的方法（如果不需要松耦合，如控件和包含它的页）。 但是，发布服务器和订阅服务器的生存期通过对象引用耦合，而订阅服务器类型必须引用发布服务器类型。 这可能会造成内存管理问题，尤其是当存在订阅静态或长期对象的事件的生存期较短的对象时。 如果不删除事件处理程序，则订阅服务器上的引用将保持活动状态，这将阻止或延迟订阅服务器的垃圾回收。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 简介

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类可实现发布-订阅模式，允许在对象和类型引用不便于链接的组件之间进行基于消息的通信。 此机制允许发布者和订阅者在无需相互引用的情况下进行通信，帮助减少组件之间的依赖性，同时还允许这些组件接受独立开发和测试。

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类提供多播发布-订阅功能。 这意味着可以有多个发布者发布单个消息，并且可以有多个订阅服务器侦听同一消息。 图4-1 说明了此关系：

![](communicating-between-loosely-coupled-components-images/messagingcenter.png " 多播发布-订阅功能")

**图4-1：** 多播发布-订阅功能

发布方使用 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法发送消息，而订阅方使用 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法侦听消息。 此外，订阅方还可以使用 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法取消消息订阅（如果需要）。

在内部，[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类使用弱引用。 这意味着它不会使对象保持活动状态，而是允许对它们进行垃圾回收。 因此，只有当类不再希望接收消息时才需要取消订阅消息。

EShopOnContainers 移动应用使用[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)类在松耦合组件之间进行通信。 该应用程序定义了三个消息：

- 在将项添加到购物`CatalogViewModel`篮时，类会发布消息。`AddProduct` 在返回时， `BasketViewModel`类订阅消息，并递增购物篮中的项目数以做出响应。 此外， `BasketViewModel`类还会从此消息中取消订阅。
- 当用户将品牌或类型`CatalogViewModel`筛选器应用于目录中显示的项目时，将由类发布消息。`Filter` 在返回时， `CatalogView`类订阅消息并更新 UI，以便仅显示符合筛选条件的项。
- 当导航到`MainViewModel`以下成功创建和提交新订单时`ChangeTab` `CheckoutViewModel` ，类会发布消息。 `MainViewModel` 在返回时， `MainView`类订阅消息并更新 UI，以便 "**我的配置文件**" 选项卡处于活动状态，以便显示用户的订单。

> [!NOTE]
> [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)虽然类允许在松耦合类之间进行通信，但它并不提供此问题的唯一体系结构解决方案。 例如，可以通过绑定引擎和属性更改通知来实现视图模型和视图间的通信。 此外，还可以通过在导航过程中传递数据，来实现两个视图模型之间的通信。

在 eShopOnContainers 移动应用中， [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)用于在 UI 中更新以响应另一个类中发生的操作。 因此，消息在 UI 线程上发布，订阅服务器接收同一线程上的消息。

> [!TIP]
> 执行 UI 更新时封送到 UI 线程。 如果需要从后台线程发送的消息更新 UI，请通过调用[`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法来处理订阅服务器上的 UI 线程上的消息。

有关的详细信息[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)，请参阅[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定义消息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)消息是用于标识消息的字符串。 下面的代码示例显示在 eShopOnContainers 移动应用中定义的消息：

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

在此示例中，使用常量定义消息。 此方法的优点是它提供编译时类型安全和重构支持。

## <a name="publishing-a-message"></a>发布消息

发布方通过一种 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 重载通知订阅方查看消息。 下面的代码示例演示如何发布`AddProduct`消息：

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

在此示例中， [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)该方法指定了三个参数：

- 第一个参数指定发送方类。 发送方类必须由想要接收消息的任何订阅服务器指定。
- 第二个参数指定消息。
- 第三个参数指定要发送到订阅服务器的有效负载数据。 在这种情况下，负载数据`CatalogItem`是实例。

[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法将使用一种暂时的方法发布消息及其有效负载数据。 因此，即使没有注册的订阅方接收消息，消息也仍会发送。 在这种情况下，将忽略已发送的消息。

> [!NOTE]
> [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法可以使用泛型参数控制消息的传递方式。 因此，不同的订阅者可以接收共享消息标识但发送不同负载数据类型的多个消息。

## <a name="subscribing-to-a-message"></a>订阅消息

订阅方可以使用一种 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 重载进行注册以接收消息。 下面的代码示例演示 eShopOnContainers 移动应用如何订阅和处理`AddProduct`消息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此示例中， [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法订阅`AddProduct`消息，并执行回调委托以响应接收消息。 此回调委托指定为 lambda 表达式，用于执行更新 UI 的代码。

> [!TIP]
> 请考虑使用不可变的有效负载数据。 不要尝试在回调委托内修改有效负载数据，因为多个线程可以同时访问接收的数据。 在这种情况下，负载数据应是不可变的，以避免并发错误。

订阅方可能不需要处理已发布消息的每个实例，这可以通过 [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法中指定的泛型类型参数来控制。 在此示例中，订阅服务器将只`AddProduct`接收`CatalogViewModel`从类发送的消息， `CatalogItem`其有效负载数据是实例。

## <a name="unsubscribing-from-a-message"></a>取消订阅消息

订阅者可以取消订阅他们不想再收到的消息。 这是通过[`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)重载之一实现的，如下面的代码示例所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

在此示例中， [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法语法反映了在订阅`AddProduct`接收消息时指定的类型参数。

## <a name="summary"></a>总结

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 类可实现发布-订阅模式，允许在对象和类型引用不便于链接的组件之间进行基于消息的通信。 此机制允许发布者和订阅者在无需相互引用的情况下进行通信，帮助减少组件之间的依赖性，同时还允许这些组件接受独立开发和测试。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
