---
title: 生成跨平台应用程序
description: 本部分在摘要以及六个部分讨论如何构建使用 Xamarin 开发平台 – 从了解如何在 Xamarin 设计移动应用程序，然后测试和部署到不同的应用商店的应用程序。
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 683400e24844308769f0562552641216d45e7d11
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276327"
---
# <a name="building-cross-platform-applications"></a>生成跨平台应用程序

有两个用于跨平台移动应用程序之间共享代码选项：共享资产项目和可移植类库。 这些选项才[此处所述](~/cross-platform/app-fundamentals/code-sharing.md); 的详细信息[的可移植类库](~/cross-platform/app-fundamentals/pcl.md)并[共享的项目](~/cross-platform/app-fundamentals/shared-projects.md)中也有。

<a name="Sections" />

 [概述](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [第一部分-了解 Xamarin 移动平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [第二部分-体系结构](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [第 3 – 部分设置 Xamarin 跨平台解决方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [第 4 – 部分处理多个平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [第 5 – 实际代码共享策略部分](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [第 6 部分 - 测试和应用商店审批](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>案例研究

本文档中列出的原则被放入中的示例应用程序的做法*Tasky*，以及[预建应用程序](https://xamarin.com/prebuilt)等[Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)。

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky 是 iOS、 Android 和 Windows Phone 的简单的待办事项列表应用程序。
它演示如何使用 Xamarin 创建跨平台应用程序的基础知识，并使用本地 SQLite 数据库。

 [![tasky 列表](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky 列表](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

读取[Tasky 案例研究](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)。

## <a name="summary"></a>总结

本部分介绍 Xamarin 的应用程序开发工具，并讨论如何构建面向多个移动平台的应用程序。

它包括多个平台间的分层式体系结构以便重复使用该结构代码，并介绍可在该体系结构内使用不同的软件模式。

常见的应用程序函数 （如文件和网络操作） 和它们如何可以构建跨平台的方式给出了示例。

最后，它简要讨论了测试，并提供对案例研究，以将这些原则放入操作的引用。

## <a name="related-links"></a>相关链接

- [共享代码选项](~/cross-platform/app-fundamentals/code-sharing.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 示例应用程序 (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Xamarin 移动应用程序开发：跨平台C#和 Xamarin.Forms 基础知识 (Amazon)](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [使用的移动开发C#由 Greg 桎梏 》 (O'Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [中的专业人员跨平台移动开发C#由 Scott Olson、 John Hunter、 Ben Horgen，Kenny Goers (Wrox)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
