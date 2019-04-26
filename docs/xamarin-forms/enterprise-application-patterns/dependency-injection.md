---
title: 依赖关系注入
description: 这一章介绍了 eShopOnContainers 移动应用程序如何使用依赖关系注入可以将分离，具体取决于这些类型的代码类型。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fb225349b9ffb1c950486a817897b3c26c6ffbe4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300247"
---
# <a name="dependency-injection"></a>依赖关系注入

通常情况下，实例化对象时调用类构造函数和对象所需的任何值作为参数传递给构造函数。 这是依赖关系注入的示例，特别是被称为*构造函数注入*。 对象所需的依赖关系注入到构造函数中。

通过指定为接口类型的依赖项，依赖关系注入，取决于这些类型的代码中的具体类型的分离状态。 它通常使用保存的注册和接口和抽象类型之间的映射列表的容器和实现或扩展这些类型的具体类型。

还有其他类型的依赖关系注入，如*属性 setter 注入*，并*方法调用注入*，但它们的过程是不太常见。 因此，这一章将讨论仅在执行依赖关系注入容器使用构造函数注入。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>依赖关系注入简介

依赖关系注入是一个专门的控制反转 (IoC) 模式，其中要反转的问题是获取必需的依赖项的过程。 使用依赖关系注入，另一个类负责将依赖项注入到在运行时对象。 下面的代码示例演示如何将`ProfileViewModel`类的结构时使用依赖关系注入：

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

`ProfileViewModel`构造函数接收`IOrderService`作为参数，插入由另一个类的实例。 中的唯一依赖关系`ProfileViewModel`类是接口类型上。 因此，`ProfileViewModel`类不包含负责实例化类的任何知识`IOrderService`对象。 负责实例化的类`IOrderService`对象，并将其插入到`ProfileViewModel`类中被称为*依赖关系注入容器*。

依赖关系注入根据容器的配置提供用于实例化类实例和管理器生命周期的工具，通过此方法减少对象之间的耦合。 在对象创建期间，容器将向对象中注入其所需的任何依赖关系。 如果这些依赖关系尚未创建，容器将首先创建并解析其依赖关系。

> [!NOTE]
> 此外可以手动使用工厂实现依赖关系注入。 但是，使用容器提供了其他功能，例如生存期管理，并通过扫描的程序集注册。

有以下几个使用依赖关系注入容器优点：

-   容器中删除一个类来找到其依赖项和管理其生存期的需要。
-   容器，而不会影响类的实现依赖项映射。
-   容器依赖项，以模拟，从而有利于可测试性。
-   容器通过允许新类，可轻松添加到应用程序可以提高可维护性。

注册和解析视图模型，以及注册服务和将其注入到视图模型，通常将在使用 MVVM 的 Xamarin.Forms 应用程序的上下文中，使用依赖关系注入容器。

存在多个依赖关系注入容器可用，它包含 eShopOnContainers 移动应用中使用 Autofac 来管理的视图模型实例化和服务应用程序中的类。 Autofac 可帮助构建松散耦合应用程序，并提供的所有依赖关系注入容器，包括注册类型映射和对象实例的方法中常见的功能解决对象、 管理对象生存期和注入到解析的对象的构造函数的依赖对象。 有关 Autofac 详细信息，请参阅[Autofac](http://autofac.readthedocs.io/en/latest/index.html) readthedocs.io。

在 Autofac，`IContainer`接口提供了依赖关系注入容器。 图 3-1 显示的依赖项时使用此容器，它会实例化`IOrderService`对象，并将其注入`ProfileViewModel`类。

![](dependency-injection-images/dependencyinjection.png "依赖关系示例时使用依赖关系注入")

**图 3-1:** 依赖项时使用依赖关系注入

在运行时，容器必须知道哪些实现`IOrderService`接口，它应实例化，它可以实例化之前`ProfileViewModel`对象。 这涉及到：

-   在决定如何实例化实现的对象的容器`IOrderService`接口。 这称为*注册*。
-   实例化实现的对象的容器`IOrderService`接口，并`ProfileViewModel`对象。 这称为*解析*。

最终，应用程序将完成使用`ProfileViewModel`对象，该对象将成为可用于垃圾回收。 此时，垃圾回收器应释放的`IOrderService`实例如果其他类不共享同一个实例。

> [!TIP]
> 编写容器无关的代码。 始终尝试写入容器无关的代码来分离特定的依赖关系容器正在使用中的应用程序。

## <a name="registration"></a>注册

依赖项可以注入到对象之前，必须首先向容器注册类型的依赖项。 注册的类型通常涉及将传递容器，一个接口并实现该接口的具体类型。

有两种方法中通过代码容器注册类型和对象的：

-   向容器注册类型或映射。 在需要时，容器将生成指定类型的实例。
-   将现有对象在容器中注册为单一实例。 在需要时，容器将返回对现有对象的引用。

> [!TIP]
> 依赖关系注入容器并不总是适合。 依赖关系注入引入额外的复杂性和要求可能用于小型应用到不在相应或最有用的。 如果一个类不具有任何依赖项，或者不是其他类型的依赖项，它可能毫无意义，将其放在容器中。 此外，如果类具有一个依赖项，它们与该类型的设置，将永远不会更改，它可能毫无意义，将其放在容器中。

应在单个方法在应用中，执行需要依赖关系注入的类型的注册，并应在应用程序的生命周期，以确保应用程序知道它的类之间的依赖关系的早期调用此方法。 在 eShopOnContainers 的移动应用中执行此操作由`ViewModelLocator`类，哪些生成`IContainer`对象以及是否保存对该对象的引用的应用中唯一的类。 以下代码示例演示如何声明 eShopOnContainers 移动应用`IContainer`对象中`ViewModelLocator`类：

```csharp
private static IContainer _container;
```

在中注册类型和实例`RegisterDependencies`中的方法`ViewModelLocator`类。 这通过首先创建来实现`ContainerBuilder`实例，在下面的代码示例所示：

```csharp
var builder = new ContainerBuilder();
```

然后注册类型和实例`ContainerBuilder`对象，将下面的代码示例演示类型注册的最常见形式：

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

`RegisterType`如下所示的方法将接口类型映射到具体类型。 还将告诉您要实例化的容器`RequestProvider`对象时它实例化对象所需的注入`IRequestProvider`通过构造函数。

下面的代码示例中所示，也可以不从接口类型，映射直接注册具体类型：

```csharp
builder.RegisterType<ProfileViewModel>();
```

当`ProfileViewModel`类型解析，容器将注入其必需的依赖项。

Autofac 还允许实例注册的容器是负责维护对一种类型的单一实例的引用。 例如，下面的代码示例显示如何在 eShopOnContainers 的移动应用注册时要使用的具体类型`ProfileViewModel`实例需要`IOrderService`实例：

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

`RegisterType`如下所示的方法将接口类型映射到具体类型。 `SingleInstance`方法配置注册，以便依赖的每个对象都会收到相同的共享的实例。 因此，只有一个`OrderService`实例将共享需要注入对象的容器中存在`IOrderService`通过构造函数。

此外可以与执行实例注册`RegisterInstance`方法，在下面的代码示例所示：

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

`RegisterInstance`如下所示的方法创建一个新`OrderMockService`实例，并将其注册到容器。 因此，只有一个`OrderMockService`实例中的容器，共享需要注入对象的存在`IOrderService`通过构造函数。

以下类型和实例注册`IContainer`必须生成对象，这以下代码示例所示：

```csharp
_container = builder.Build();
```

调用`Build`方法`ContainerBuilder`实例生成新的依赖关系注入容器，其中包含所做的注册。

>💡 **提示**:请考虑`IContainer`作为不可变。 虽然 Autofac 提供了`Update`应尽可能避免使用方法来更新现有的容器，调用此方法中的注册。 有一个容器后修改已生成，特别是当使用该容器的风险。 有关详细信息，请参阅[作为不可变的容器，请考虑](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable)readthedocs.io。

<a name="resolution" />

## <a name="resolution"></a>解决方法

注册某个类型后，它可以是已解决或作为依赖项注入。 如果正在解析类型，需要创建一个新实例的容器，它将任何依赖关系注入到实例。

通常情况下，解析类型时，以下三种情况发生：

1.  如果该类型尚未注册，容器会引发异常。
1.  如果该类型具有已注册为单一实例，容器返回单一实例。 如果这是首次调用类型的该容器创建它必要情况下，并维护对它的引用。
1.  如果该类型没有已注册为单一实例，容器将返回一个新实例，并不会继续对它的引用。

下面的代码示例演示如何将`RequestProvider`使用 Autofac 以前注册的类型可以是已解决：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此示例中，Autofac 需要解决的具体类型`IRequestProvider`类型，以及任何依赖项。 通常情况下，`Resolve`需要特定类型的实例时调用方法。 有关如何控制已解析的对象的生存期信息，请参阅[管理解析的对象生存期](#managing_the_lifetime_of_resolved_objects)。

下面的代码示例显示了如何在 eShopOnContainers 的移动应用实例化视图模型类型和其依赖项：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此示例中，要求 Autofac 来解析请求的视图模型的视图模型类型和该容器还会解析所有依赖项。 解析时`ProfileViewModel`类型，若要解决的依赖关系是`IOrderService`对象。 因此，Autofac 首先构造`OrderService`对象，然后将其传递给构造函数的`ProfileViewModel`类。 详细了解如何在 eShopOnContainers 的移动应用构造视图模型，并将它们关联到视图，请参阅[会自动使用视图模型定位符创建视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

> [!NOTE]
> 注册和解析与容器的类型具有性能成本由于反射的用于创建每种类型的容器的使用，尤其是依赖项要重新构造，以便在应用中每个页面导航。 如果有许多或深入的依赖项，则创建的成本会显著增加。

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解析的对象的生存期

注册后一种类型，Autofac 的默认行为是要创建已注册类型的新实例每次类型得到解决，或当依赖关系机制都注入其他类的实例。 在此方案中，该容器不会保存对已解析的对象的引用。 但是，注册时实例，Autofac 的默认行为是管理作为单一实例对象的生存期。 因此，该实例保持在范围内，当容器处于范围内，并当容器离开作用域并被垃圾回收时释放时，或当代码显式释放该容器。

可以使用 Autofac 实例范围来指定 Autofac 创建从已注册的类型的对象的单一实例行为。 Autofac 实例作用域管理由容器实例化的对象生存期。 默认实例作用域`RegisterType`方法是`InstancePerDependency`作用域。 但是，`SingleInstance`作用域可用于`RegisterType`方法，以便在容器创建或返回类型的单一实例时调用`Resolve`方法。 下面的代码示例演示如何指示 Autofac 若要创建的单一实例`NavigationService`类：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

第一次`INavigationService`接口解析，则容器会创建一个新`NavigationService`对象，并保留对它的引用。 上的任何后续分辨率`INavigationService`接口，容器返回的引用`NavigationService`先前创建的对象。

> [!NOTE]
> SingleInstance 作用域容器被释放后释放创建的对象。

Autofac 包括其他实例作用域。 有关详细信息，请参阅[实例范围](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html)readthedocs.io。

## <a name="summary"></a>总结

依赖关系注入将启用通过依赖于具体类型的代码来分离这些类型。 它通常使用一个容器，该容器包含一系列注册和接口与抽象类型间的映射以及实现或扩展这些类型的具体类型。

Autofac 可帮助构建松散耦合应用程序，并提供的所有依赖关系注入容器，包括注册类型映射和对象实例的方法中常见的功能解决对象、 管理对象生存期和注入到解析的对象的构造函数的依赖对象。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
