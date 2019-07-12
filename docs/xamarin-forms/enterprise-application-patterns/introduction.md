---
title: 企业应用开发简介
description: 本章介绍企业应用开发，并引入了 eShopOnContainers 移动应用。
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4fbb4047b95fd70f829cd79e4ea26b2958273297
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831179"
---
# <a name="introduction-to-enterprise-app-development"></a>企业应用开发简介

而不考虑平台的企业应用程序的开发人员面临着多种挑战：

-   应用的需求可随时间而变化。
-   新的业务机会和挑战。
-   开发过程中会持续出现反馈，可能会显著影响应用的范围和需求。

考虑到这些挑战，需构建可随时间推移而进行修改或扩展的应用。 要实现这种适应性可能很困难，因为所需架构需满足以下条件：允许独立开发应用的各个部分并单独进行测试，同时不会影响应用的其余部分。

许多企业应用非常复杂，需多名开发人员。 决定应用的设计方式是一项重大挑战，因为要允许多名开发人员独立且有效地开发应用的不同部分，同时确保将各部分集成到应用中时，各部分能无缝结合。

采用传统的设计和构建方法会得到所谓的单一  应用，其中各组件紧密耦合，不存在明确的界限。 通常情况下，使用这种单一方法会增加应用的维护难度，降低维护效率，因为要在不破坏应用中其他组件的情况下解决 bug 十分困难，并且可能难以添加新功能或替换现有功能。

应对这些挑战的一种有效补救措施是将应用划分为离散、松散耦合的组件，这些组件可轻松集成到应用中。 这样的方法有多个优点：

-   允许由不同个人或团队来开发、测试、扩展和维护单个功能。
-   有助于加强应用横向功能（如身份验证和数据访问）与纵向功能（如应用特定的业务功能）间的重用，并明确分离关注点。 这样便可更轻松地管理应用组件间的依赖关系和交互。
-   允许不同个人或团队根据其专业知识关注特定任务或功能，明确角色分工，尤其是它更加清晰地分离了用户界面和应用业务逻辑。 具体而言，它提供了清晰地分隔用户界面和应用程序的业务逻辑。

然而，在把应用划分为离散、松散耦合的组件时，有许多必须解决的问题。 这些问题包括：

-   决定如何提供用户界面控件及其逻辑间的明确关注点分离。 创建 Xamarin.Forms 企业应用时，最重要的一点就是确定是否将业务逻辑放到 code-behind 文件中，或是否在用户界面控件及其逻辑之间创建明确的关注点分离，从而提高应用的可维护性和可测试性。 有关详细信息，请参阅[模型-视图-视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
-   决定是否使用依赖关系注入容器。 依赖关系注入容器可提供一个工具来构造类的实例并注入对象的依赖关系，以减少对象间的依赖耦合，并且还可基于容器的配置来管理对象的生命周期。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
-   选择平台提供的事件或松散耦合的基于消息的通信，后者发生在不便通过对象和类型引用链接的组件间。 有关详细信息，请参阅[松散耦合组件间的通信简介](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)。
-   决定如何在页面间导航，包括如何发起导航，以及将导航逻辑保留在哪里。 有关详细信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
-   确定如何验证用户输入正确与否。 制定决策时必须确定如何验证用户输入，以及如何通知用户验证错误。 有关详细信息，请参阅[验证](~/xamarin-forms/enterprise-application-patterns/validation.md)。
-   决定如何执行身份验证，以及如何保护带有授权的资源。 有关详细信息，请参阅[身份验证和授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
-   确定如何从 Web 服务访问远程数据，包括如何可靠地检索数据，以及如何缓存数据。 有关详细信息，请参阅[访问远程数据](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
-   在决定如何测试应用程序。 有关详细信息，请参阅[Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南针对这些问题提供指导，着重关注使用 Xamarin.Forms 构建跨平台企业应用的核心模式和架构。 本指给出了常见 Xamarin.Forms 企业应用开发方案，并借助模型-视图-视图模型 (MVVM) 模式支持呈现、呈现逻辑和实体间的关注点分离，以此帮助生产出适应性良好、可维护和可测试的代码。

## <a name="sample-application"></a>示例应用程序

本指南包含的示例应用 eShopOnContainers，这是一个包含以下功能的在线商店：

-   针对后端服务进行身份验证和授权。
-   浏览衣服、咖啡杯和其他商品目录。
-   筛选目录。
-   对目录中的项进行排序。
-   查看用户的订单历史记录。
-   设置的配置。

### <a name="sample-application-architecture"></a>示例应用程序的架构

图 1-1 提供了示例应用程序架构的高级概述。

![](introduction-images/architecture.png "eShopOnContainers 高级架构")

**图 1-1**: eShopOnContainers 高级架构

示例应用程序附带了三个客户端应用：

-   使用 ASP.NET Core 开发的 MVC 应用程序。
-   使用 Angular 2 和 Typescript 开发的单页应用程序 (SPA)。 采用此方法开发 Web 应用程序时，避免了每执行一次操作都要往返于服务器一次。
-   使用 Xamarin.Forms 开发的移动应用，支持 iOS、Android 和通用 Windows 平台 (UWP)。

有关 web 应用程序的信息，请参阅[《使用 ASP.NET Core 和 Microsoft Azure 构建并开发现代化 Web 应用程序》](https://aka.ms/WebAppEbook)电子书。

示例应用程序包括以下后端服务：

-   使用 ASP.NET Core Identity 和 IdentityServer 的身份识别微服务。
-   目录微服务，这是数据驱动的创建、读取、更新和删除 (CRUD) 服务，基于使用 EntityFramework Core 的 SQL Server 数据库。
-   订单微服务，这是使用域驱动设计模式的域驱动服务。
-   购物车微服务，这是使用 Redis 缓存的数据驱动 CRUD 服务。

这些后端服务作为使用 ASP.NET Core MVC 的微服务实现，并在单个 Docker 主机中部署为唯一容器。 这些后端服务被统称为 eShopOnContainers 参考应用程序。 客户端应用通过具象状态传输 (REST) Web 接口与后端服务进行通信。 有关微服务和 Docker 的详细信息，请参阅[适用于容器化微服务](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

有关后端服务的实现的信息，请参阅[.NET 微服务：适用于容器化 .NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>移动应用

本指南重点介绍使用 Xamarin.Forms 构建跨平台企业应用，并以 eShopOnContainers 移动应用为例。 图 1-2 显示了含上述功能的 eShopOnContainers 移动应用页面。

[![](introduction-images/screenshots.png "eShopOnContainers 移动应用")](introduction-images/screenshots-large.png#lightbox "eShopOnContainers 移动应用程序")

**图 1-2**:EShopOnContainers 移动应用

该移动应用使用了 eShopOnContainers 参考应用程序提供的后端服务。 但若想避免部署后端服务，则可将此移动应用配置为使用模拟服务中的数据。

eShopOnContainers 移动应用程序运用了以下 Xamarin.Forms 功能：

-   XAML
-   Controls
-   绑定
-   Converters
-   样式
-   Animations
-   命令
-   Behaviors
-   Triggers
-   Effects
-   自定义呈现器
-   MessagingCenter
-   自定义控件

有关这些功能的详细信息，请参阅[Xamarin.Forms 文档](~/xamarin-forms/index.yml)，和[使用 Xamarin.Forms 创建移动应用](https://aka.ms/xamebook)。

此外，针对 eShopOnContainers 移动应用程序中的某些类提供了单元测试。

#### <a name="mobile-app-solution"></a>移动应用程序解决方案

eShopOnContainers 移动应用解决方案将源代码和其他资源整合到项目中。 所有项目使用文件夹对源代码和其他资源进行分类。 下表概述了构成 eShopOnContainers 移动应用的项目：

|项目|描述|
|--- |--- |
|eShopOnContainers.Core|此项目是可移植类库 (PCL) 项目，其中包含共享的代码和共享的 UI。|
|eShopOnContainers.Droid|此项目包含特定于 Android 的代码并且是 Android 应用的入口点。|
|eShopOnContainers.iOS|此项目包含特定于 iOS 的代码并且是 iOS 应用的入口点。|
|eShopOnContainers.UWP|此项目包含通用 Windows 平台 (UWP) 的特定代码，并且是 Windows 应用程序的入口点。|
|eShopOnContainers.TestRunner.Droid|此项目是 eShopOnContainers.UnitTests 项目的 Android 测试运行程序。|
|eShopOnContainers.TestRunner.iOS|此项目是 eShopOnContainers.UnitTests 项目的 iOS 测试运行程序。|
|eShopOnContainers.TestRunner.Windows|此项目是 eShopOnContainers.UnitTests 项目的通用 Windows 平台测试运行程序。|
|eShopOnContainers.UnitTests|此项目包含 eShopOnContainers.Core 项目的单元测试。|

eShopOnContainers 移动应用中的类可再度用于所有 Xamarin.Forms 之中，只需少量修改或完全无需修改。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core 项目

eShopOnContainers.Core PCL 项目中包含以下文件夹：

|文件夹|描述|
|--- |--- |
|Animations|包含可在 XAML 中使用动画的类。|
|Behaviors|包含公开给视图类的行为。|
|Controls|包含应用使用的自定义控件。|
|Converters|包含自定义逻辑应用于绑定的值转换器。|
|Effects|包含`EntryLineColorEffect`类，该类用于更改特定`Entry`控件的边框颜色。|
|Exceptions|包含自定义的`ServiceAuthenticationException`类。|
|Extensions|包含用于`VisualElement`和`IEnumerable`类的扩展方法。|
|Helpers|包含应用的帮助程序类。|
|Models|包含应用的模型类。|
|Properties|包含 .NET 程序集元数据文件 `AssemblyInfo.cs`。|
|Services|包含用于实现提供给应用的服务的接口和类。|
|Triggers|包含用于在 XAML 中调用动画的 `BeginAnimation` 触发器。|
|Validations|包含验证数据输入时涉及的类。|
|ViewModels|包含被公开到页面的应用程序逻辑。|
|Views|包含应用的页面。|

##### <a name="platform-projects"></a>平台项目

平台项目包含效果实现、自定义呈现器实现以及其他特定于平台的资源。

## <a name="summary"></a>总结

Xamarin 的跨平台移动应用开发工具和平台为 B2E、B2B 和 B2C 移动客户端应用提供了全面的解决方案，从而能够在所有目标平台 (iOS、Android 和 Windows) 间共享代码，并帮助降低总拥有成本。 应用可共享其用户界面及其应用逻辑代码，同时保留原生平台的外观和感觉。

企业应用开发人员面临着诸多挑战，在开发过程中这些挑战可能会影响应用架构。 因此，需构建出可随时间推移而进行修改或扩展的应用。 要设计出这种适应性十分困难，通常涉及把应用分割为离散、松散耦合的组件，这些组件可轻松地集成到应用中。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
