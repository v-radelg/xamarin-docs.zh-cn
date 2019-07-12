---
title: 单元测试的企业应用
description: 这一章介绍了如何在 eShopOnContainers 的移动应用中执行单元测试。
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d83cdce7076eac5a022863b583ecb01346ae440a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831083"
---
# <a name="unit-testing-enterprise-apps"></a>单元测试的企业应用

移动应用有桌面和基于 web 的应用程序无需担心的唯一问题。 移动用户会因它们，通过网络连接，通过服务的可用性，并使用一系列其他因素的设备。 因此，应测试移动应用，因为它们将使用在现实生活中以提高其质量、 可靠性和性能。 有许多类型的测试应包括单元测试、 集成测试，以及用户界面测试使用的单元测试所测试的最常见形式的应用上执行。

单元测试采用应用程序中，通常的方法的一个小单元，将它与代码的其余部分隔离开来，并验证其行为与预期相同。 其目标是功能的，每个单位按预期执行，以便在整个应用不传播错误检查。 检测的 bug 出现的位置是更高效观察间接在辅助点故障的 bug 的影响。

软件开发工作流的组成部分时，单元测试具有最大的影响代码质量。 只要已编写一个方法，则应编写单元测试用于验证响应的标准、 边界和不正确情况的输入数据中的方法和该检查的行为的代码所做的任何显式或隐式假设。 或者，使用测试驱动开发，单元测试的代码之前编写。 在此方案中，单元测试用作设计文档和功能规范。

> [!NOTE]
> 单元测试是非常有效的防范回归-即，用于处理，但具有更新失败的已干扰的功能。

单元测试通常使用排列 act 断言模式：

-   *排列*的单元测试方法的部分初始化对象并设置传递给待测试方法的数据的值。
-   *行动*部分调用具有所需的参数待测试方法。
-   *断言*部分验证待测试方法的操作行为与预期相同。

遵循此模式可确保单元测试是可读且一致。

## <a name="dependency-injection-and-unit-testing"></a>依赖关系注入和单元测试

采用松散耦合的体系结构的动机之一是它方便了单元测试。 使用 Autofac 注册的类型之一是`OrderService`类。 下面的代码示例显示了此类的大纲：

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

`OrderDetailViewModel`类具有的依赖关系`IOrderService`类型可在容器解析时实例化`OrderDetailViewModel`对象。 但是，而不是创建`OrderService`对象进行单元测试`OrderDetailViewModel`类，而是替换`OrderService`出于测试目的模拟对象。 图 10-1 说明了此关系。

![](unit-testing-images/unittesting.png "实现 IOrderService 接口的类")

**图 10-1:** 实现 IOrderService 接口的类

此方法允许`OrderService`对象传递到`OrderDetailViewModel`类在运行时，和感兴趣的可测试性，它允许`OrderMockService`类，以传递到`OrderDetailViewModel`在测试时的类。 这种方法的主要优点是它使单元测试可以无需难以处理资源，例如 web 服务或数据库执行。

## <a name="testing-mvvm-applications"></a>测试 MVVM 应用程序

测试模型和视图模型的 MVVM 应用程序从等同于测试任何其他类，并可以使用相同的工具和技术 – 例如单元测试和模拟。 但是，模型和视图模型类具有一些典型模式，特定的单元测试技术可能对这些模式有益。

> [!TIP]
> 测试与每个单元测试的一件事。 不要冒险行事，以使单元测试的单元的行为的多个方面的练习。 这样做会导致很难读取和更新的测试。 它还会导致混乱，解释失败时。

EShopOnContainers 移动应用使用[xUnit](https://xunit.github.io/)执行单元测试，它支持两种不同类型的单元测试：

-   事实是，始终为 true，测试的测试固定条件。
-   理论是仅适用于特定数据集的测试。

EShopOnContainers 移动应用中包含的单元测试是事实的测试，并因此每个单元测试方法用修饰`[Fact]`属性。

> [!NOTE]
> xUnit 测试执行的测试运行程序。 若要执行测试运行程序，请运行所需平台 eShopOnContainers.TestRunner 项目。

### <a name="testing-asynchronous-functionality"></a>测试异步功能

实现 MVVM 模式时，视图模型通常以异步方式通常调用服务上的操作。 通常调用这些操作的代码的测试的实际服务作为替代使用模拟。 下面的代码示例演示如何通过将模拟服务传递到视图模型测试异步功能：

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

此单元测试检查`Order`的属性`OrderDetailViewModel`实例会将后的值`InitializeAsync`调用方法。 `InitializeAsync`视图模型的相应视图导航到时调用方法。 有关导航的详细信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

当`OrderDetailViewModel`创建实例后，它需要`OrderService`实例指定为参数。 但是，`OrderService`从 web 服务中检索数据。 因此，`OrderMockService`实例，这是 mock 版本的`OrderService`类中，指定为参数`OrderDetailViewModel`构造函数。 然后，当视图模型`InitializeAsync`调用方法时，这样就可调用`IOrderService`操作，模拟数据是检索，而不是与 web 服务通信。

### <a name="testing-inotifypropertychanged-implementations"></a>测试 INotifyPropertyChanged 实现

实现`INotifyPropertyChanged`接口允许视图的视图所源自的变化做出反应模型和模型。 这些更改并不局限于在控件中显示的数据 – 它们还用于控制视图中，例如，会导致动画要启动或禁用的控件的视图模型状态。

可以直接通过单元测试更新的属性可以通过将附加到事件处理程序来测试`PropertyChanged`事件并检查是否设置为该属性的新值后引发该事件。 下面的代码示例显示了此类测试：

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

此单元测试调用`InitializeAsync`方法`OrderViewModel`类，这将导致其`Order`要更新属性。 单元测试将通过，前提是`PropertyChanged`事件引发的`Order`属性。

### <a name="testing-message-based-communication"></a>测试基于消息的通信

视图模型，使用[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类进行通信之间松散耦合的类可进行单元测试通过订阅到待测试的代码要发送的消息，如下面的代码示例中所示：

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

此单元测试检查`CatalogViewModel`发布`AddProduct`消息以响应其`AddCatalogItemCommand`正在执行。 因为[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)类支持多播的消息订阅、 单元测试可以订阅`AddProduct`消息，并执行以接收其响应的回调委托。 此回调委托，指定为 lambda 表达式，设置`boolean`由字段`Assert`语句以验证测试的行为。

### <a name="testing-exception-handling"></a>测试异常处理

单元测试也可以写入该操作或无效输入，均会引发特定异常的检查下面的代码示例中所示：

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

此单元测试将引发异常，因为[ `ListView` ](xref:Xamarin.Forms.ListView)控件不具有名为的事件`OnItemTapped`。 `Assert.Throws<T>`方法是泛型方法其中`T`是预期异常的类型。 将参数传递给`Assert.Throws<T>`方法是一个 lambda 表达式，将引发的异常。 因此，单元测试将传递，提供 lambda 表达式引发`ArgumentException`。

> [!TIP]
> 避免编写检查异常消息字符串的单元测试。 随着时间推移，可能会更改异常消息字符串，因此依赖于它们的存在的单元测试它们被认为是做。

### <a name="testing-validation"></a>测试验证

有两个方面测试验证实施： 正确实现的任何验证规则，测试和测试的`ValidatableObject<T>`类按预期执行。

验证逻辑就通常可以测试，因为它通常是其中的输出取决于输入的独立的进程。 调用的结果应测试`Validate`具有至少一个关联的验证规则，如下面的代码示例中所示的每个属性的方法：

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

此单元测试将检查验证成功时这两个`ValidatableObject<T>`中的属性`MockViewModel`这两个实例具有数据。

检查验证成功，以及验证单元测试还应检查的值`Value`， `IsValid`，并`Errors`每个属性`ValidatableObject<T>`实例，以验证类按预期执行。 下面的代码示例演示如何执行此单元测试：

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

此单元测试将检查验证失败时`Surname`属性`MockViewModel`没有任何数据，并`Value`， `IsValid`，并`Errors`每个属性`ValidatableObject<T>`实例都已正确设置。

## <a name="summary"></a>总结

单元测试采用应用程序中，通常的方法的一个小单元，将它与代码的其余部分隔离开来，并验证其行为与预期相同。 其目标是功能的，每个单位按预期执行，以便在整个应用不传播错误检查。

待测试的对象的行为可以隔离的模拟依赖对象的行为的 mock 对象替换依赖对象。 这使单元测试，而无需难以处理资源，例如 web 服务或数据库执行。

测试 MVVM 应用程序的模型和视图模型与测试任何其他类的方式相同，可以使用相同的工具和方法。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
