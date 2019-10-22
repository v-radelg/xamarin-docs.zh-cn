---
title: 企业应用程序开发简介
description: 本章介绍了企业应用程序开发，并介绍了 eShopOnContainers 移动应用。
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9bde1140f6590daa4b1d40a8b56edec314bfc66d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70760226"
---
# <a name="introduction-to-enterprise-app-development"></a>企业应用程序开发简介

无论采用何种平台，企业应用程序的开发人员都面临着几个难题：

- 可能随时间变化的应用要求。
- 新的业务机会和挑战。
- 开发期间的持续反馈可能会显著影响应用的范围和要求。

考虑到这一点，生成可在一段时间内轻松修改或扩展的应用很重要。 针对此类适应性进行设计可能会很困难，因为它需要一个允许单独开发和测试应用程序的各个部分的结构，而不会影响应用程序的其余部分。

许多企业应用程序的复杂程度足以要求多个开发人员。 确定如何设计应用程序以使多个开发人员能够在应用程序的不同部分之间有效地工作，同时确保这些部分在集成到应用程序中时无缝集成，这可能是一个很大的挑战。

设计和构建应用程序的传统方法会导致所谓*的应用程序*，其中组件紧密耦合，它们之间没有明确的分隔。 通常情况下，这种单一方法会导致难以维护的应用程序，因为在不中断应用程序的情况下解决 bug 可能会很困难，而且很难添加新功能或替换现有功能。

对这些挑战的有效补救措施是将应用分区为分离、松散耦合的组件，这些组件可以轻松集成到应用中。 这种方法具有以下优点：

- 它允许不同的个人或团队开发、测试、扩展和维护单个功能。
- 它提高了重用性，并完全分离了应用的水平功能（例如身份验证和数据访问）和垂直功能（如特定于应用的业务功能）之间的问题。 这允许更轻松地管理应用程序组件之间的依赖关系和交互。
- 它通过允许不同的个人或团队根据自己的专业知识来重点关注特定的任务或功能，帮助维护角色分离。 特别是，它提供用户界面和应用程序的业务逻辑之间的更清晰分隔。

但是，将应用分区为分离松耦合组件时，必须解决许多问题。 这些方法包括：

- 确定如何在用户界面控件与其逻辑之间提供清晰的问题分离。 创建 Xamarin 时，最重要的决策之一是在代码隐藏文件中放置业务逻辑，还是在用户界面控件与其逻辑之间创建完全分离的问题，以使应用更维护性和可测试性。 有关详细信息，请参阅[ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md)。
- 确定是否要使用依赖关系注入容器。 依赖关系注入容器通过提供一项功能来构造类的实例并注入依赖关系，从而减少对象之间的依赖关系，并基于容器的配置来管理其生存期。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。
- 在平台之间选择提供的事件和松散耦合的组件之间基于消息的通信，不方便地通过对象和类型引用进行链接。 有关详细信息，请参阅[松散耦合组件之间的通信](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)简介。
- 确定如何在页之间导航，包括如何调用导航以及导航逻辑应驻留的位置。 有关详细信息，请参阅[导航](~/xamarin-forms/enterprise-application-patterns/navigation.md)。
- 确定如何验证用户输入的正确性。 决策必须包括验证用户输入的方式，以及如何通知用户有关验证错误的信息。 有关详细信息，请参阅[验证](~/xamarin-forms/enterprise-application-patterns/validation.md)。
- 确定如何执行身份验证，以及如何通过授权保护资源。 有关详细信息，请参阅[身份验证和授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md)。
- 确定如何从 web 服务访问远程数据，包括如何可靠地检索数据以及如何缓存数据。 有关详细信息，请参阅[访问远程数据](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。
- 确定测试应用程序的方法。 有关详细信息，请参阅[单元测试](~/xamarin-forms/enterprise-application-patterns/unit-testing.md)。

本指南提供了有关这些问题的指导，并重点介绍使用 Xamarin 构建跨平台企业应用程序的核心模式和体系结构。 本指南旨在帮助生成可自适应、可维护且可测试的代码，通过解决常见的 Xamarin。 Forms enterprise 应用开发方案，并通过对模型-视图-ViewModel （MVVM）模式。

## <a name="sample-application"></a>示例应用程序

本指南包含一个 eShopOnContainers 的示例应用程序，该应用程序包括以下功能：

- 针对后端服务进行身份验证和授权。
- 浏览衬衫、咖啡杯子和其他营销项的目录。
- 筛选目录。
- 对目录中的项进行排序。
- 查看用户的订单历史记录。
- 设置配置。

### <a name="sample-application-architecture"></a>示例应用程序体系结构

图1-1 提供示例应用程序的体系结构的高级概述。

![](introduction-images/architecture.png "eShopOnContainers high-level architecture")

**图 1-1**： eShopOnContainers 高级体系结构

示例应用程序附带了三个客户端应用：

- 使用 ASP.NET Core 开发的 MVC 应用程序。
- 使用角度2和 Typescript 开发的单页面应用程序（SPA）。 对于 web 应用程序，这种方法可避免跨每个操作执行到服务器的往返过程。
- 用 Xamarin 开发的移动应用，支持 iOS、Android 和通用 Windows 平台（UWP）。

有关 web 应用程序的信息，请参阅[用 ASP.NET Core 和 Microsoft Azure 构建和开发新式 Web 应用程序](https://aka.ms/WebAppEbook)。

该示例应用程序包括以下后端服务：

- 标识微服务，它使用 ASP.NET Core 标识和 IdentityServer。
- 目录微服务，它是使用 EntityFramework 核心使用 SQL Server 数据库的数据驱动的创建、读取、更新、删除（CRUD）服务。
- 排序微服务，它是使用域驱动的设计模式的域驱动的服务。
- 购物篮微服务，它是使用 Redis 缓存的数据驱动 CRUD 服务。

这些后端服务使用 ASP.NET Core MVC 实现为微服务，并在单个 Docker 主机内作为唯一容器部署。 这些后端服务统称为 eShopOnContainers 引用应用程序。 客户端应用通过具象状态传输（REST） web 界面与后端服务进行通信。 有关微服务和 Docker 的详细信息，请参阅[容器化微服务](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md)。

有关后端服务的实现的信息，请参阅[.Net 微服务：适用于容器化 .Net 应用程序的体系结构](https://aka.ms/microservicesebook)。

### <a name="mobile-app"></a>移动应用

本指南重点介绍如何使用 Xamarin 构建跨平台的企业应用，并使用 eShopOnContainers 移动应用作为示例。 图1-2 显示了 eShopOnContainers 移动应用中的页面，其中提供了前面概述的功能。

[![](introduction-images/screenshots.png "The eShopOnContainers mobile app")](introduction-images/screenshots-large.png#lightbox "The eShopOnContainers mobile app")

**图 1-2**： eShopOnContainers 移动应用

移动应用使用 eShopOnContainers 引用应用程序提供的后端服务。 但是，可以将其配置为使用模拟服务中的数据，以避免部署后端服务。

EShopOnContainers 移动应用会演练以下 Xamarin 功能：

- XAML
- 控件
- 绑定
- 转换器
- 样式
- 动画
- 命令
- 行为
- 触发器
- 效果
- 自定义呈现器
- MessagingCenter
- 自定义控件

有关此功能的详细信息，请参阅[xamarin 文档](~/xamarin-forms/index.yml)和[用 Xamarin 创建移动应用](https://aka.ms/xamebook)。

此外，还为 eShopOnContainers 移动应用中的一些类提供单元测试。

#### <a name="mobile-app-solution"></a>移动应用解决方案

EShopOnContainers 移动应用解决方案将源代码和其他资源组织到项目中。 所有项目都使用文件夹将源代码和其他资源组织到类别中。 下表概述了组成 eShopOnContainers 移动应用的项目：

|项目|描述|
|--- |--- |
|eShopOnContainers|此项目是可移植类库（PCL）项目，其中包含共享代码和共享 UI。|
|eShopOnContainers. Droid|此项目包含 Android 特定的代码，是 Android 应用的入口点。|
|eShopOnContainers|此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。|
|eShopOnContainers|此项目包含通用 Windows 平台（UWP）特定代码，是 Windows 应用的入口点。|
|eShopOnContainers. TestRunner. Droid|此项目是 eShopOnContainers Run-unittests 项目的 Android 测试运行程序。|
|eShopOnContainers. TestRunner|此项目是 eShopOnContainers Run-unittests 项目的 iOS 测试运行程序。|
|eShopOnContainers. TestRunner|此项目是 eShopOnContainers Run-unittests 项目的通用 Windows 平台测试运行程序。|
|eShopOnContainers. Run-unittests|此项目包含 eShopOnContainers 项目的单元测试。|

EShopOnContainers 移动应用中的类可在任何 Xamarin 应用程序中重复使用，几乎不需要修改。

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers 项目

EShopOnContainers PCL 项目包含以下文件夹：

|文件夹|描述|
|--- |--- |
|动画|包含使动画可在 XAML 中使用的类。|
|行为|包含向视图类公开的行为。|
|控件|包含应用程序使用的自定义控件。|
|转换器|包含将自定义逻辑应用于绑定的值转换器。|
|效果|包含 `EntryLineColorEffect` 类，该类用于更改特定 `Entry` 控件的边框颜色。|
|异常|包含自定义 `ServiceAuthenticationException`。|
|扩展|包含 `VisualElement` 和 `IEnumerable` 类的扩展方法。|
|帮助程序|包含应用程序的帮助器类。|
|模型|包含应用程序的模型类。|
|属性|包含 .NET 程序集元数据文件 `AssemblyInfo.cs`。|
|Services|包含接口和类，这些接口和类实现提供给应用程序的服务。|
|触发器|包含用于在 XAML 中调用动画的 `BeginAnimation` 触发器。|
|验证|包含用于验证数据输入的类。|
|ViewModels|包含公开给页面的应用程序逻辑。|
|Views|包含应用的页面。|

##### <a name="platform-projects"></a>平台项目

平台项目包含效果实现、自定义呈现器实现以及其他特定于平台的资源。

## <a name="summary"></a>总结

Xamarin 的跨平台移动应用开发工具和平台为 B2E、B2B 和 B2C 移动客户端应用提供全面的解决方案，提供跨所有目标平台（iOS、Android 和 Windows）共享代码的功能，并帮助降低总拥有成本。 应用可以共享其用户界面和应用逻辑代码，同时保留本机平台的外观。

企业应用程序的开发者面临着几个难题，可以在开发过程中更改应用程序的体系结构。 因此，生成应用以便在一段时间内修改或扩展应用程序非常重要。 设计此类适应性很困难，但通常会将应用分区为分离、松散耦合的组件，这些组件可以轻松集成到应用中。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
