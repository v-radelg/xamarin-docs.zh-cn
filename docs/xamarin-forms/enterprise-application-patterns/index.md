---
title: 《使用 Xamarin.Forms 的企业应用程序模式》电子书
description: 此电子书提供用于开发自适应、可维护和可测试的 Xamarin.Forms 企业应用程序的体系结构指南。
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: f972a32f8daf920f2121e5aa56923c0f3a7f808a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299937"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>《使用 Xamarin.Forms 的企业应用程序模式》电子书

_开发自适应、可维护和可测试的 Xamarin.Forms 企业应用程序的体系结构指南_

![](images/cover-sml.png "《使用 Xamarin.Forms 的企业应用程序模式》电子书")

此电子书提供有关如何实现模型-视图-视图模型 (MVVM) 模式、依赖关系注入、导航、验证和配置管理并同时保持松散耦合的指南。 此外，还提供有关利用 IdentityServer 执行身份验证和授权、通过容器化微服务访问数据以及执行单元测试的指南。

## <a name="prefaceprefacemd"></a>[前言](preface.md)

本章介绍本指南的目的和范围及其目标人群。

## <a name="introductionintroductionmd"></a>[介绍](introduction.md)

企业应用的开发人员面临着多种挑战，可能涉及在开发过程中更改应用架构。 因此，需构建出可随时间推移而进行修改或扩展的应用。 这种适应性的设计可能很困难，通常涉及把应用分区可轻松集成到其他应用中的离散、松散耦合的组件当中。

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

模型-视图-视图模型 (MVVM) 模式有助于将应用程序的的业务和演示逻辑与其用户界面 (UI) 隔离开来。 始终清晰隔离应用程序逻辑和 UI 有助于解决诸多开发问题，还可使应用程序更加易于测试、维护和改进。 这样做还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发各自的应用部分时能够更轻松地进行协作。

## <a name="dependency-injectiondependency-injectionmd"></a>[依赖关系注入](dependency-injection.md)

依赖关系注入将启用通过依赖于具体类型的代码来分离这些类型。 它通常使用一个容器，该容器包含一系列注册和接口与抽象类型间的映射以及实现或扩展这些类型的具体类型。

依赖关系注入根据容器的配置提供用于实例化类实例和管理器生命周期的工具，通过此方法减少对象之间的耦合。 在对象创建期间，容器将向对象中注入其所需的任何依赖关系。 如果这些依赖关系尚未创建，容器将首先创建并解析其依赖关系。

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[松散耦合组件之间的通信](communicating-between-loosely-coupled-components.md)

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) 类可实现发布-订阅模式，允许在对象和类型引用不便于链接的组件之间进行基于消息的通信。 此机制允许发布者和订阅者在无需相互引用的情况下进行通信，帮助减少组件之间的依赖性，同时还允许这些组件接受独立开发和测试。

## <a name="navigationnavigationmd"></a>[导航](navigation.md)

Xamarin.Forms 包含对页面导航的支持，通常在逻辑驱动的状态更改时，因用户与 UI 交互或通过应用本身而引起页面导航。 但是，在使用 MVVM 模式的应用中实现导航可能较为复杂。

本章介绍 `NavigationService` 类，该类用于通过视图模型执行视图模型优先导航。 将导航逻辑放置在视图模型类中意味着可通过自动测试来运用该逻辑。 此外，视图模型接下来即可实现用于控制导航的逻辑，以确保强制实施特定业务规则。

## <a name="validationvalidationmd"></a>[验证](validation.md)

任何接受用户输入的应用都应确保输入是有效的。 如果未进行验证，用户提供的数据可能导致应用故障。 验证可强制实施业务规则，并防止攻击者注入恶意数据。

在上下文的模型-视图-视图模型 (MVVM) 模式，视图模型或模型通常需要执行数据验证和信号到视图的任何验证错误，以便用户可以更正它们。

## <a name="configuration-managementconfiguration-managementmd"></a>[配置管理](configuration-management.md)

设置可用于将配置应用行为的数据与代码相互分离，这允许在不重新构建应用的情况下更改行为。 应用设置是应用创建和管理的数据；用户设置是应用的可自定义设置，可影响应用的行为，但不需要频繁进行重新调整。

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[容器化微服务](containerized-microservices.md)

微服务提供一种应用程序开发和部署的方法，可满足现代云应用程序的敏捷性、规模和可靠性要求。 微服务的主要优点之一是，它们可以独立地横向扩展，这意味着可以为了支持需求而扩展需要更多处理能力或网络带宽的特定功能区域，但不必扩展未出现需求增加的应用程序的所在区域。

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[身份验证和授权](authentication-and-authorization.md)

可以通过许多方法将身份验证和授权集成到与 ASP.NET MVC Web 应用程序进行通讯的 Xamarin.Forms 应用中， 在这里，与使用 IdentityServer 4 的容器化的标识微服务执行身份验证和授权。 IdentityServer 是一种适用于 ASP.NET Core 的开源 OpenID Connect 和 OAuth 2.0  框架，它与 ASP.NET Core Identity 集成以执行持有者令牌身份验证。

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[访问远程数据](accessing-remote-data.md)

许多基于 Web 的现代化解决方案使用 Web 服务器托管的 Web 服务来为远程客户端应用程序提供功能。 Web 服务公开的操作可构成 Web API，并且客户端应用应能够利用 Web API，但不需要了解该 API 公开的数据或操作的实现方式。

## <a name="unit-testingunit-testingmd"></a>[单元测试](unit-testing.md)

测试 MVVM 应用程序的模型和视图模型与测试任何其他类的方式相同，可以使用相同的工具和方法。 但是，模型和视图模型类具有一些典型模式，特定的单元测试技术可能对这些模式有益。

## <a name="feedback"></a>反馈

此项目设有社区网站，可在其中发布问题和提供反馈。 社区网站位于 [GitHub](https://github.com/dotnet-architecture/eShopOnContainers)。 或者，还可将有关该电子书的反馈通过电子邮件发送到 [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com)。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
