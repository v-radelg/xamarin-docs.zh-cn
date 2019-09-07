---
title: 依赖关系注入
description: 本章介绍了 eShopOnContainers 移动应用如何使用依赖关系注入将具体类型从依赖于这些类型的代码中分离出来。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 72ffa4508f2c8f050f505313a28ce8278f2570b4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760269"
---
# <a name="dependency-injection"></a>依赖关系注入

通常，在实例化对象时将调用类构造函数，并且该对象需要的任何值都将作为参数传递到构造函数。 这是依赖关系注入的示例，专门称为*构造函数注入*。 将对象所需的依赖项注入构造函数。

通过将依赖项指定为接口类型，依赖关系注入允许从依赖于这些类型的代码分离具体类型。 它通常使用容器来保存接口与抽象类型之间的注册和映射列表，以及实现或扩展这些类型的具体类型。

还有其他类型的依赖项注入，如*属性 setter 注入*和*方法调用注入*，但不太常见。 因此，本章重点介绍如何通过依赖关系注入容器来执行构造函数注入。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>依赖关系注入简介

依赖关系注入是一种专用的控制反转（IoC）模式，在这种情况下，要反转的问题是获取所需依赖关系的过程。 使用依赖关系注入，另一个类负责在运行时将依赖项注入到对象。 下面的代码示例演示如何`ProfileViewModel`在使用依赖关系注入时构造类：

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

构造函数将`IOrderService`实例作为自变量接收，由其他类注入。 `ProfileViewModel` `ProfileViewModel`类中的唯一依赖项在接口类型上。 因此， `ProfileViewModel`类并不知道负责`IOrderService`实例化对象的类。 用于实例化`IOrderService`对象并将其插入`ProfileViewModel`到类中的类称为*依赖关系注入容器*。

依赖关系注入根据容器的配置提供用于实例化类实例和管理器生命周期的工具，通过此方法减少对象之间的耦合。 在对象创建期间，容器将向对象中注入其所需的任何依赖关系。 如果这些依赖关系尚未创建，容器将首先创建并解析其依赖关系。

> [!NOTE]
> 还可以使用工厂手动实现依赖关系注入。 但是，使用容器可提供其他功能（例如生存期管理），并通过程序集扫描进行注册。

使用依赖关系注入容器有多个优点：

- 容器不再需要类来查找依赖项并管理其生存期。
- 容器允许映射已实现的依赖项，而不影响类。
- 通过允许模拟的依赖项，容器有助于实现可测试性。
- 容器通过允许将新类轻松添加到应用中来提高可维护性。

在使用 MVVM 的 Xamarin 窗体应用的上下文中，依赖关系注入容器通常用于注册和解析视图模型，并用于注册服务并将其注入查看模型中。

有许多依赖关系注入容器可用，eShopOnContainers 移动应用使用 Autofac 管理应用中的视图模型和服务类的实例化。 Autofac 有助于构建松散耦合的应用程序，并提供了依赖关系注入容器中常见的所有功能，包括用于注册类型映射和对象实例、解析对象、管理对象生存期和注入依赖对象到它解析的对象的构造函数。 有关 Autofac 的详细信息，请参阅 readthedocs.io 上的[Autofac](http://autofac.readthedocs.io/en/latest/index.html) 。

在 Autofac 中， `IContainer`接口提供了依赖关系注入容器。 图3-1 显示了使用此容器时的依赖关系，这`IOrderService`将实例化对象并将`ProfileViewModel`其插入到类中。

![](dependency-injection-images/dependencyinjection.png "使用依赖关系注入时的依赖关系示例")

**图3-1：** 使用依赖关系注入时的依赖关系

在运行时，容器必须知道它应实例化`IOrderService`的接口的实现，然后才能`ProfileViewModel`实例化对象。 这涉及：

- 用于决定如何实例化实现`IOrderService`接口的对象的容器。 这称为 "*注册*"。
- 用于实例化实现`IOrderService`接口`ProfileViewModel`和对象的对象的容器。 这称为 "*解析*"。

最终，应用程序将使用`ProfileViewModel`对象完成，并可用于垃圾回收。 此时，如果其他类不共享同一个实例， `IOrderService`垃圾回收器应释放该实例。

> [!TIP]
> 编写与容器无关的代码。 请始终尝试编写与容器无关的代码，以便将应用与所使用的特定依赖关系容器分离。

## <a name="registration"></a>注册

在将依赖项注入到对象之前，必须先使用容器注册依赖项的类型。 注册类型通常涉及传递容器接口和实现接口的具体类型。

通过代码在容器中注册类型和对象有两种方法：

- 使用容器注册类型或映射。 如果需要，容器将生成指定类型的实例。
- 将容器中的现有对象注册为单一实例。 如果需要，容器将返回对现有对象的引用。

> [!TIP]
> 依赖关系注入容器并非始终适合。 依赖关系注入会引入额外的复杂性和要求，这些要求可能不适用于小型应用程序。 如果某个类没有任何依赖项，或者它不是其他类型的依赖项，则将其放在容器中可能并不合理。 此外，如果类具有一组不属于类型的整数依赖项，并且永远不会发生更改，则将其放在容器中可能没有意义。

需要依赖关系注入的类型的注册应在应用的单个方法中执行，此方法应在应用生命周期中及早调用，以确保应用知道其类之间的依赖关系。 在 eShopOnContainers 移动应用中，这是由`ViewModelLocator`类执行的，该类`IContainer`生成对象，是应用中的唯一类，它包含对该对象的引用。 下面的代码示例演示了 eShopOnContainers 移动应用如何声明`IContainer` `ViewModelLocator`类中的对象：

```csharp
private static IContainer _container;
```

在`RegisterDependencies` `ViewModelLocator`类的方法中注册类型和实例。 这是通过首先创建`ContainerBuilder`实例实现的，如以下代码示例所示：

```csharp
var builder = new ContainerBuilder();
```

然后，将向`ContainerBuilder`对象注册类型和实例，下面的代码示例演示类型注册的最常见形式：

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

此处`RegisterType`显示的方法将接口类型映射为具体类型。 它会告知容器在实例化`RequestProvider`需要`IRequestProvider`通过构造函数的注入的对象时对其进行实例化。

也可以直接注册具体类型，无需从接口类型进行映射，如下面的代码示例所示：

```csharp
builder.RegisterType<ProfileViewModel>();
```

当解析`ProfileViewModel`类型时，容器将注入其所需的依赖项。

Autofac 还允许实例注册，其中容器负责维护对某个类型的单独实例的引用。 例如，下面的代码示例演示了 eShopOnContainers 移动应用程序如何在`ProfileViewModel`实例`IOrderService`需要实例时注册要使用的具体类型：

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

此处`RegisterType`显示的方法将接口类型映射为具体类型。 `SingleInstance`方法配置注册，使每个依赖对象收到相同的共享实例。 因此，容器中将`OrderService`只存在一个实例，该容器由需要`IOrderService`通过构造函数注入的对象共享。

还可以通过`RegisterInstance`方法执行实例注册，如以下代码示例所示：

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

此处`RegisterInstance`显示的方法创建一个新`OrderMockService`的实例，并将其注册到容器中。 因此，容器中只`OrderMockService`存在一个实例，该容器由需要`IOrderService`通过构造函数注入的对象共享。

在类型和实例注册后， `IContainer`必须生成对象，如下面的代码示例所示：

```csharp
_container = builder.Build();
```

对实例调用`Build`方法会生成一个新的依赖关系注入容器，其中包含已进行的注册。 `ContainerBuilder`

> [!TIP]
> 将`IContainer`视为不可变。 尽管 Autofac 提供了`Update`一个方法来更新现有容器中的注册，但应尽可能避免调用此方法。 在创建容器后对其进行修改时，尤其是在使用容器的情况下。 有关详细信息，请参阅在 readthedocs.io 上[将容器视为不可变](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable)。

<a name="resolution" />

## <a name="resolution"></a>解决方法

类型注册后，可以将其解析或注入为依赖项。 当正在解析类型并且容器需要创建新的实例时，它会将任何依赖关系注入到该实例中。

通常，解析类型时，会出现以下三种情况之一：

1. 如果该类型尚未注册，则容器会引发异常。
1. 如果该类型已注册为单一实例，则容器返回单一实例。 如果这是第一次调用该类型的，则容器会根据需要创建它，并保持对它的引用。
1. 如果该类型尚未注册为单一实例，则容器返回一个新的实例，并且不保留对该实例的引用。

下面的代码示例演示了以前`RequestProvider`向 Autofac 注册的类型如何进行解析：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此示例中，要求 Autofac 解析`IRequestProvider`类型的具体类型以及任何依赖项。 通常，当`Resolve`需要特定类型的实例时，将调用方法。 有关控制已解析对象的生存期的信息，请参阅[管理已解析对象的生存期](#managing_the_lifetime_of_resolved_objects)。

下面的代码示例演示了 eShopOnContainers 移动应用如何实例化视图模型类型及其依赖项：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此示例中，要求 Autofac 解析请求的视图模型的视图模型类型，并且该容器还将解析任何依赖项。 解析`ProfileViewModel`类型时，要解析的依赖项是一个`IOrderService`对象。 因此，Autofac 首先构造`OrderService`对象，然后将其传递给`ProfileViewModel`类的构造函数。 有关 eShopOnContainers mobile 应用如何构造视图模型并将其与视图相关联的详细信息，请参阅[自动创建具有视图模型定位器的视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

> [!NOTE]
> 使用容器来注册和解析类型会影响性能，因为容器使用反射来创建每个类型，特别是在应用中为每个页面导航重构依赖关系的情况。 如果存在许多或深度依赖关系，则创建成本会显著增加。

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解析对象的生存期

注册类型后，Autofac 的默认行为是在每次解析类型时或者在依赖项机制将实例注入到其他类时，创建已注册类型的新实例。 在这种情况下，容器不会保存对已解析的对象的引用。 但是，在注册实例时，Autofac 的默认行为是将该对象的生存期作为单一实例进行管理。 因此，当容器处于范围内时，实例将保留在范围中，当容器超出范围并进行垃圾回收时，或者当代码显式释放容器时，实例将被释放。

Autofac 实例作用域可用于指定 Autofac 从注册类型创建的对象的单一实例行为。 Autofac 实例作用域管理容器实例化的对象生存期。 该`RegisterType`方法的默认实例作用域`InstancePerDependency`是范围。 但是， `SingleInstance`可以将范围`RegisterType`与方法一起使用，以便容器在调用`Resolve`方法时创建或返回类型的单一实例。 下面的代码示例演示如何指示 Autofac 创建`NavigationService`类的单一实例：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

第一次`INavigationService`解析该接口时，容器会创建一个新`NavigationService`的对象并维护对该对象的引用。 在`INavigationService`接口的任何后续解决方案中，容器将返回对先前创建的`NavigationService`对象的引用。

> [!NOTE]
> SingleInstance 范围在释放容器时释放已创建的对象。

Autofac 包括附加的实例作用域。 有关详细信息，请参阅 readthedocs.io 上的[实例作用域](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html)。

## <a name="summary"></a>总结

依赖关系注入将启用通过依赖于具体类型的代码来分离这些类型。 它通常使用一个容器，该容器包含一系列注册和接口与抽象类型间的映射以及实现或扩展这些类型的具体类型。

Autofac 有助于构建松散耦合的应用程序，并提供了依赖关系注入容器中常见的所有功能，包括用于注册类型映射和对象实例、解析对象、管理对象生存期和注入依赖对象到它解析的对象的构造函数。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
