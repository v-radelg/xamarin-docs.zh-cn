---
title: 对企业应用进行单元测试
description: 本章介绍了如何在 eShopOnContainers 移动应用中执行单元测试。
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: c631ca73d69ea630592920a32804512f89d5baaf
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529071"
---
# <a name="unit-testing-enterprise-apps"></a>对企业应用进行单元测试

移动应用具有独特的问题, 因为桌面和基于 web 的应用程序无需担心。 移动用户使用的设备、网络连接、服务可用性和其他因素范围会有所不同。 因此, 应测试移动应用, 因为它们将在现实世界中用于提高其质量、可靠性和性能。 应该对应用执行多种类型的测试, 包括单元测试、集成测试和用户界面测试, 并将单元测试作为最常见的测试形式。

单元测试采用一小部分应用, 通常是一种方法, 将其与代码的其余部分隔离, 并验证它是否按预期方式工作。 其目标是检查每个功能单元是否按预期执行, 以便不会在整个应用中传播错误。 检测发生 bug 的 bug 更高效, 可在辅助故障点间接观察 bug 的影响。

当它是软件开发工作流的组成部分时, 单元测试对代码质量具有最大的影响。 一旦编写了方法, 就应该编写单元测试来验证方法的行为, 以响应输入数据的标准、边界和不正确的情况, 并检查代码所做的任何显式或隐式假设。 或者, 在测试驱动开发中, 单元测试在代码之前编写。 在这种情况下, 单元测试将充当设计文档和功能规范。

> [!NOTE]
> 单元测试对于回归是非常有效的, 也就是说, 使用的功能, 但在发生错误的更新时受到干扰。

单元测试通常使用 "排列方式-法-断言" 模式:

- 单元测试方法的 "*排列*" 部分会初始化对象并设置传递给待测试方法的数据的值。
- *Act*节调用所测试的方法, 并提供所需的参数。
- *断言*部分验证待测试方法的操作是否按预期方式运行。

遵循此模式可确保单元测试的可读性和一致性。

## <a name="dependency-injection-and-unit-testing"></a>依赖关系注入和单元测试

采用松耦合体系结构的动机之一是它有利于单元测试。 向 Autofac 注册的类型之一是`OrderService`类。 下面的代码示例演示了此类的大纲:

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

类依赖于容器在实例化`OrderDetailViewModel`对象时解析的类型。`IOrderService` `OrderDetailViewModel` 但是, 不是创建`OrderService`对象对`OrderDetailViewModel`类进行单元测试, 而是将`OrderService`对象替换为模拟, 以用于测试目的。 图10-1 说明了这种关系。

![](unit-testing-images/unittesting.png "实现 IOrderService 接口的类")

**图 10-1:** 实现 IOrderService 接口的类

此方法允许`OrderService`在运行时将对象传入`OrderDetailViewModel`类, 并且在可测试性的兴趣情况下, 它允许`OrderMockService`将类传入`OrderDetailViewModel`类中的测试时间。 此方法的主要优点是, 它可以实现单元测试, 而无需使用难于处理的资源 (如 web 服务或数据库)。

## <a name="testing-mvvm-applications"></a>测试 MVVM 应用程序

从 MVVM 应用程序测试模型和查看模型与测试任何其他类相同, 并且可以使用相同的工具和技术 (如单元测试和模拟)。 但是，模型和视图模型类具有一些典型模式，特定的单元测试技术可能对这些模式有益。

> [!TIP]
> 对每个单元测试进行测试。 不要让单元测试执行单元的行为的多个方面。 这样做会导致难以读取和更新的测试。 它还可能在解释失败时导致混淆。

EShopOnContainers 移动应用使用[xUnit](https://xunit.github.io/)来执行单元测试, 支持两种不同类型的单元测试:

- 事实是始终为 true 的测试, 用于测试固定条件。
- 理论是仅适用于一组特定数据的测试。

EShopOnContainers 移动应用随附的单元测试是事实测试, 因此每个单元测试方法都是用`[Fact]`特性修饰的。

> [!NOTE]
> xUnit 测试由测试运行程序执行。 若要执行测试运行程序, 请运行所需平台的 eShopOnContainers. TestRunner 项目。

### <a name="testing-asynchronous-functionality"></a>测试异步功能

实现 MVVM 模式时, 视图模型通常会以异步方式调用服务上的操作。 调用这些操作的代码测试通常使用模拟作为实际服务的替换项。 下面的代码示例演示了如何通过将模拟服务传递到视图模型来测试异步功能:

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

此单元测试检查`Order`在调用`InitializeAsync`方法后`OrderDetailViewModel`实例的属性是否具有值。 当导航到视图模型的相应视图时, 将调用方法。`InitializeAsync` 有关导航的详细信息, 请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

创建实例时, 它`OrderService`需要将实例指定为参数。 `OrderDetailViewModel` 但是, `OrderService`从 web 服务检索数据。 因此, `OrderMockService`实例 ( `OrderService`该类的模拟版本) 被指定`OrderDetailViewModel`为构造函数的参数。 然后, 调用视图模型的`InitializeAsync`方法 (该方法调用`IOrderService`操作) 时, 将检索模拟数据, 而不是与 web 服务进行通信。

### <a name="testing-inotifypropertychanged-implementations"></a>测试 INotifyPropertyChanged 实现

通过实现`INotifyPropertyChanged`接口, 视图可以对源自视图模型和模型的更改做出反应。 这些更改并不限于控件中显示的数据–它们还用于控制视图, 例如, 查看模型状态导致动画启动或禁用控件。

可以通过将事件处理程序附加到`PropertyChanged`事件, 并检查为属性设置新值后是否引发事件, 来测试可通过单元测试直接更新的属性。 下面的代码示例演示了此类测试:

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

此单元测试将调用`InitializeAsync` `OrderViewModel`类的方法, 这将导致其`Order`属性被更新。 如果对`PropertyChanged` `Order`属性引发了事件, 则单元测试将通过。

### <a name="testing-message-based-communication"></a>测试基于消息的通信

如下面的代码示例[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)中所示, 可以通过订阅正在进行的代码所发送的消息, 查看使用类在松耦合类之间进行通信的视图模型。

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

此单元测试检查`CatalogViewModel`是否`AddProduct`发布消息以响应其`AddCatalogItemCommand`正在执行。 由于类支持多路广播消息订阅, 因此单元测试可以订阅该`AddProduct`消息并执行回调委托以响应接收它。 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 指定为 lambda 表达式的此回调委托将设置一个`boolean`字段, 该字段由该`Assert`语句用来验证测试的行为。

### <a name="testing-exception-handling"></a>测试异常处理

还可以编写单元测试来检查是否针对无效操作或输入引发了特定异常, 如下面的代码示例所示:

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

此单元测试将引发异常, 因为该[`ListView`](xref:Xamarin.Forms.ListView)控件没有名为`OnItemTapped`的事件。 方法是泛型方法, 其中`T`是预期异常的类型。 `Assert.Throws<T>` 传递给`Assert.Throws<T>`方法的参数是将引发异常的 lambda 表达式。 因此, 单元测试将会传递, 前提是 lambda 表达式引发`ArgumentException`。

> [!TIP]
> 避免编写检查异常消息字符串的单元测试。 异常消息字符串可能会随时间而变化, 因此依赖于其存在的单元测试被视为脆弱。

### <a name="testing-validation"></a>测试验证

测试验证实现有两个方面: 测试是否正确实现了任何验证规则, 并测试`ValidatableObject<T>`类是否按预期执行。

验证逻辑通常是很简单的测试, 因为它通常是一个自包含进程, 其中的输出取决于输入。 对于至少具有一个关联的验证规则的每`Validate`个属性, 应在其上调用方法的结果, 如以下代码示例所示:

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

如果`ValidatableObject<T>` 实例`MockViewModel`中的两个属性都具有数据, 则此单元测试将检查验证是否成功。

除了检查验证是否成功外, 验证单元测试还应检查`Value`每个`ValidatableObject<T>`实例的、 `IsValid`和`Errors`属性的值, 以验证类是否按预期执行。 下面的代码示例演示了执行此操作的单元测试:

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

此单元测试检查`Surname`在的`MockViewModel`属性没有任何数据, 且每个`ValidatableObject<T>`实例的`Value`、 `IsValid`和`Errors`属性设置正确时, 验证是否失败。

## <a name="summary"></a>总结

单元测试采用一小部分应用, 通常是一种方法, 将其与代码的其余部分隔离, 并验证它是否按预期方式工作。 其目标是检查每个功能单元是否按预期执行, 以便不会在整个应用中传播错误。

可以通过将依赖对象替换为模拟依赖对象的行为的 mock 对象, 来隔离受测对象的行为。 这样, 就可以执行单元测试, 而无需的资源 (如 web 服务或数据库)。

测试 MVVM 应用程序的模型和视图模型与测试任何其他类的方式相同，可以使用相同的工具和方法。


## <a name="related-links"></a>相关链接

- [下载电子书 (2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (示例)](https://github.com/dotnet-architecture/eShopOnContainers)
