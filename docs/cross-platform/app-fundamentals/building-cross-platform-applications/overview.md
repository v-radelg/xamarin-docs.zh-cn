---
title: 生成跨平台应用程序概述
description: 本文档提供了构建跨平台应用程序的高级概述。 此外，介绍了的值C#，设计模式，如 MVC/MVVM 和本机 Ui。
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 1eb308e0095c29d8ab0d0bdf1f74b807fd2ab97f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275591"
---
# <a name="building-cross-platform-applications-overview"></a>生成跨平台应用程序概述

本指南介绍 Xamarin 平台以及如何构建跨平台应用程序最大化代码重用，并在所有主要移动平台上提供高质量的本机体验： iOS、 Android 和 Windows Phone。

本文档中使用的方法为通常适用于生产力应用程序和游戏应用，但重点是工作效率和实用程序 （非游戏应用程序）。 请参阅[MonoGame 文档简介](~/graphics-games/monogame/introduction/index.md)，或查看[Visual Studio Tools for Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity)的跨平台游戏开发指南。

短语"编写-一次，随处运行"通常用于让的优点的单个基本代码运行多个平台上不被修改。 尽管它具有的代码重用优势，这种方法通常会导致具有最低通用标准功能集的应用程序和泛型查找不符合的用户界面可以很好地到任何目标平台。

Xamarin 不只是"写入-一次，运行无处不在"平台，因为其优势之一是能够实现专用于每个平台的本机用户界面。 但是，使用精心设计，它是仍然可以共享大部分非用户接口代码，并获取系统的这两个优势： 写入数据存储和业务逻辑代码一次，并显示每个平台上的本机 Ui。 本文档介绍了常规的体系结构方法来实现此目标。

下面是用于创建 Xamarin 跨平台应用的关键点的摘要：

-   **使用C#**  -在中编写您的应用程序C#。 编写的现有代码C#可移植到 iOS 和 Android 非常轻松地使用 Xamarin 和显然在 Windows 应用中使用。
-   **使用 MVC 或 MVVM 设计模式**-开发应用程序的用户界面使用模型/视图/控制器模式。 使用模型/视图/控制器方法或模型/视图/视图模型方法将应用程序设计没有明显不同的"模型"和其余部分。 确定你的应用程序的哪些部分将使用的每个平台 (iOS、 Android、 Windows、 Mac) 的本机用户界面元素，并使用此指导原则是拆分为两个组件的应用程序："核心"和"用户界面"。
-   **构建本机 Ui** -每个特定于操作系统的应用程序提供了一个不同的用户界面层 (在中实现C#的本机 UI 帮助设计工具):

1.  在 iOS 上使用 UIKit Api 来创建本机外观的应用程序，可以选择利用 Xamarin 的 iOS 设计器直观地创建您的 UI。
1.  在 Android 上，使用 Android.Views 创建本机外观的应用程序，充分利用 Xamarin 的 UI 设计器。
1.  在 Windows 上你将 XAML 用于表示层，在 Visual Studio 或 Blend 的 UI 设计器中创建。
1.  在 Mac 上，将为表示层，在 Xcode 中创建使用情节提要。

Xamarin.Forms 项目的所有平台，支持，并允许您创建可以在使用 Xamarin.Forms XAML 的平台之间共享的用户界面。 

重复使用代码的量取决于很大程度上多少代码保留在共享的内核，多少代码是用户界面中特定。 核心代码是不直接与用户交互，而是提供服务的应用程序，将收集并显示此信息的各部分的任何内容。

若要增加的重复使用代码量，可以采用跨平台组件，如跨所有这些系统提供常见服务：

1.   [SQLite net](https://www.nuget.org/packages/sqlite-net-pcl/)为本地的 SQL 存储
1.   [Xamarin 插件](https://xamarin.com/plugins)用于访问特定于设备的功能，包括相机、 联系人和地理位置，
1.   [NuGet 包](https://nuget.org)如也是与 Xamarin 项目兼容[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)，
1.  使用.NET framework 功能的网络、 web 服务、 IO 和的详细信息。


其中某些组件中实现*Tasky*案例研究。

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>可重复使用代码分离到核心库

通过分层应用程序体系结构，然后将移动的核心功能，与平台无关，到可重用的核心库遵循的职责分离原则，您可以发挥最大为下图在平台之间共享代码说明：

 ![](overview-images/layers2.png "通过分层应用程序体系结构，然后将移动的核心功能，与平台无关，到可重用的核心库遵循的职责分离原则，您可以最大限度地跨平台共享代码")

 <a name="Case_Studies" />


## <a name="case-studies"></a>案例研究

没有此文档 – 附带一个案例研究*Tasky Pro*。 每个案例研究讨论了本文档在实际示例中所述的概念的实现。 该代码是开源且适用于[github](https://github.com/xamarin/mobile-samples/)。
