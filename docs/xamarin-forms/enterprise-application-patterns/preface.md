---
title: 《企业应用开发》序言
description: 本章为《使用 Xamarin.Forms 的企业应用程序模式》的序言。
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fd085d2fb12e82233f6d3be2e2773a84539f837b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298858"
---
# <a name="preface-to-enterprise-app-development"></a>《企业应用开发》序言

此电子书提供有关 Xamarin.Forms 构建跨平台企业应用的指南。 Xamarin.Forms 是一个跨平台的 UI 工具包，使开发人员可轻松创建可以跨平台共享的的本机用户界面布局，这些平台包括 iOS、Android 和通用 Windows 平台 (UWP) 。 它提供面向企业对员工 (B2E)、企业对企业 (B2B) 和企业对消费者 (B2C) 应用的全面解决方案，支持跨所有目标平台间共享代码，并且有助于降低总拥有成本 (TCO) 。

本指南提供有关开发自适应、可维护和可测试的 Xamarin.Forms 企业应用程序的体系结构指南。 指南中介绍如何实现 MVVM、依赖关系注入、导航、验证和配置管理，并同时保持松散耦合。 此外还提供有关使用 IdentityServer 执行身份验证和授权、通过容器化微服务访问数据以及执行单元测试的指南。

本指南附带了 [eShopOnContainers 移动应用](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile) 和 [eShopOnContainers 参考应用](https://github.com/dotnet-architecture/eShopOnContainers) 的源代码。 eShopOnContainers 移动应用是一款使用 Xamarin.Forms 开发的跨平台企业应用，它连接一系列名为 eShopOnContainers 参考应用的容器化微服务。 但是，对于希望避免部署容器化微服务的用户，可将 eShopOnContainers 移动应用配置为使用模拟服务中的数据。

## <a name="whats-left-out-of-this-guides-scope"></a>本指南不包含的内容

本指南面向已熟悉 Xamarin.Forms 的读者。 有关 Xamarin.Forms 的详细介绍，请参阅[Xamarin.Forms 文档](~/xamarin-forms/index.yml)，和[使用 Xamarin.Forms 创建移动应用](https://aka.ms/xamebook)。

本指南是补充[.NET 微服务：适用于容器化.NET 应用程序体系结构](https://aka.ms/microservicesebook)，其中重点介绍开发和部署容器化微服务。 其他应该阅读的指南包括[《使用 ASP.NET Core 和 Microsoft Azure 架构和开发现代 Web 应用程序》](http://aka.ms/WebAppEbook)、[《Docker容器化应用生命周期与 Microsoft 平台和工具》](http://aka.ms/dockerlifecycleebook)以及[《用于移动应用开发的 Microsoft 平台和工具》](http://aka.ms/MobAppDev/StndPDF)。

## <a name="who-should-use-this-guide"></a>应使用本指南的用户

本指南的主要受众是希望学习如何利用 Xamarin.Froms 架构和实现跨平台企业应用的开发人员和架构师。

另一部分受众是技术决策人员，他们希望在决定采用哪些方法来使用 Xamarin.Forms 进行跨平台企业应用程序开发之前，获得有关架构和技术方面的概述。

## <a name="how-to-use-this-guide"></a>如何使用本指南

本指南重点介绍使用 Xamarin.Forms 构建跨平台企业应用。 鉴于此，应完整阅读本指南以对此类应用及其技术注意事项获得基本了解。 本指南及其示例应用还可以充当创建新企业应用的起点或参考。 使用相关示例应用作为新应用的模板，或了解如何组织应用的各个组件。 然后，回头参考本指南来获得架构方面的指导。

本指南可随意转发给团队成员，帮助确保他们对使用 Xamarin.Forms 进行跨平台企业应用开发有大致了解。 通过使所有人在使用一组通用术语和基本原则的基础上开展工作，可帮助团队确保构建具有一致的架构模式和做法的应用程序。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
