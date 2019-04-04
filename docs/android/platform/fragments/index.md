---
title: 片段
description: Android 3.0 引入了片段，演示如何在手机和平板电脑上找到的许多不同的屏幕大小支持更灵活的设计。 本文将介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何在预 Android 3.0 (API 级别 11) 设备上支持片段。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 4347ee40b0b72cc2d182ba1ef8f111212dd0ee47
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669721"
---
# <a name="fragments"></a>片段

_Android 3.0 引入了片段，演示如何在手机和平板电脑上找到的许多不同的屏幕大小支持更灵活的设计。本文将介绍如何使用片段开发 Xamarin.Android 应用程序，以及如何在预 Android 3.0 (API 级别 11) 设备上支持片段。_

## <a name="fragments-overview"></a>片段概述

在大多数平板电脑上找到更大的屏幕大小添加到 Android 开发的一层额外的复杂性，此演示适合对于在小屏幕不一定适合也较大屏幕或进行相反转换的布局。 若要减少的复杂情况，这就产生了，Android 3.0 添加了两项新功能，*片段*并*支持包*。

片段可视为用户界面模块。 它们使开发人员分割成独立的、 可重用部分，可以在单独的活动中运行的用户界面。 在运行时，活动本身将决定要使用的片段。

支持包最初称为*兼容性库*和允许的片段以在运行版本的 Android 3.0 (API 级别 11) 之前的 Android 设备上使用。

例如下, 图说明了单个应用程序如何使用跨不同设备外观造型的片段。

[![片段在平板电脑和手机中的使用方式的关系图](images/00.png)](images/00.png#lightbox)

*一个片段*包含列表，而*片段 B*包含该列表中选择的项的详细信息。 平板电脑上运行应用程序时，它可以在同一个活动上显示两个片段。 （使用其较小的屏幕大小） 手机上运行同一应用程序时，这些片段托管在两个单独的活动。 片段 A 和片段 B 将这两个机型上相同，但托管它们的活动不同。

若要帮助协调和管理所有这些片段的活动，Android，引入了一个名为的新类*FragmentManager*。 每个活动具有自己的实例`FragmentManager`用于添加、 删除和查找托管片段。 下图说明了片段和活动之间的关系：

[![说明活动、 片段管理器中，片段之间关系的关系图](images/01.png)](images/01.png#lightbox)

在某些方面，片段可以认为是作为复合控件或微型活动。 它们捆绑到可以然后独立使用由开发人员在活动的可重用模块的部分 UI。 片段具有视图层次结构，就像活动 — 但不同于活动，它可以在共享跨屏幕。 视图不同于片段，片段具有其自己的生命周期;视图不适用。

活动是一个主机到一个或多个片段，而并不直接知道自己的片段。 同样，片段是不直接感知到的其他片段中托管的活动。 但是，片段和活动都认识`FragmentManager`其活动中。 通过使用`FragmentManager`，可以为活动或片段以获取对片段的特定实例的引用，然后对该实例调用方法。 这样一来，可以进行通信的活动或片段并将其与其他片段交互。

本指南包含有关如何使用片段，其中包括全面介绍：

-   **创建片段**– 如何创建基本片段和必须实现的主要方法。
-   **管理和事务片段**– 如何在运行时操作片段。
-   **Android 支持包**-如何使用库，使片段较旧版本的 Android 上使用。


## <a name="requirements"></a>要求

片段是在使用 API 级别 (Android 3.0) 11 启动 Android SDK 中提供，如以下屏幕截图中所示：

[![选择 Android SDK 管理器中的 API 级别](images/02.png)](images/02.png#lightbox)

片段是可在 Xamarin.Android 4.0 和更高版本。 Xamarin.Android 应用程序必须至少为目标 API 级别 11 (Android 3.0) 或更高版本才能使用片段。 可能会在项目属性，如下所示设置目标框架：

[![在项目选项中设置目标框架 API 级别](images/03-sml.png)](images/03.png#lightbox)

它是可以在较旧版本的 Android 使用 Android 支持包和 Xamarin.Android 4.2 或更高版本中使用片段。 在本部分的文档中的更详细地介绍了如何执行此操作。


## <a name="related-links"></a>相关链接

- [Honeycomb 库 （示例）](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](https://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV 网络研讨会：引入了片段](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
