---
title: 片段
description: Android 3.0 介绍了一些片段，其中展示了如何为在手机和平板电脑上找到的多种不同屏幕大小支持更灵活的设计。 本文介绍如何使用片段来开发 Xamarin Android 应用程序，并介绍如何支持 Android 3.0 （API 级别11）设备上的片段。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f25c587f6a51a6b196f201c1b5060ff401f8cad2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761864"
---
# <a name="fragments"></a>片段

_Android 3.0 介绍了一些片段，其中展示了如何为在手机和平板电脑上找到的多种不同屏幕大小支持更灵活的设计。本文介绍如何使用片段来开发 Xamarin Android 应用程序，并介绍如何支持 Android 3.0 （API 级别11）设备上的片段。_

## <a name="fragments-overview"></a>片段概述

大多数平板电脑上的较大屏幕大小增加了 Android 开发的额外复杂性，为小屏幕设计的布局不一定适用于较大的屏幕，反之亦然。 为了减少此引入的复杂性，Android 3.0 添加了两个新功能：*片段*和*支持包*。

片段可以看作是用户界面模块。 它们允许开发人员将用户界面划分为可在不同活动中运行的、可重复使用的独立部件。 在运行时，这些活动本身将决定要使用哪些片段。

支持包最初称为*兼容库*，允许在运行 android 3.0 之前的 android 版本的设备上使用。

例如，下图演示了单个应用程序如何在各种设备外形上使用片段。

[![如何使用平板电脑和手机的片段图示](images/00.png)](images/00.png#lightbox)

*片段 A*包含列表，而*片段 B*包含在该列表中选定的项的详细信息。 当应用程序在 tablet 上运行时，它可以在同一活动上显示这两个片段。 如果在话筒上运行相同的应用程序（其屏幕大小较小），则会在两个不同的活动中承载片段。 对于这两个窗体因素，片段 A 和片段 B 是相同的，但承载它们的活动不同。

Android 引入了一个名为*FragmentManager*的新类，以帮助活动坐标并管理所有这些片段。 每个活动都有其自己的`FragmentManager`实例，用于添加、删除和查找托管片段。 下图说明了片段和活动之间的关系：

[![阐释活动、片段管理器和片段之间的关系的关系图](images/01.png)](images/01.png#lightbox)

有些情况下，可以将片段视为复合控件或小型活动。 它们将 UI 捆绑到可重复使用的模块中，这些模块随后可由活动中的开发人员单独使用。 片段具有视图层次结构，就像活动一样，但与活动不同，它可以在屏幕之间共享。 视图不同于片断的生命周期。视图不会。

当活动是一个或多个片段的宿主时，它不会直接识别片段本身。 同样，片段不会直接识别宿主活动中的其他片段。 但是，片段和活动`FragmentManager`在其活动中识别。 通过使用`FragmentManager`，活动或片段可以获取对某个片段的特定实例的引用，然后对该实例调用方法。 这样一来，活动或片段可以与其他片段进行通信和交互。

本指南包含有关如何使用片段的全面覆盖，其中包括：

- **创建片段**–如何创建必须实现的基本片段和关键方法。
- **片段管理和事务**–如何在运行时操作片段。
- **Android 支持包**–如何使用可以在较旧版本的 Android 上使用片段的库。

## <a name="requirements"></a>要求

从 API 级别11（Android 3.0）开始 Android SDK 中提供片段，如以下屏幕截图所示：

[![在 Android SDK 管理器中选择 API 级别](images/02.png)](images/02.png#lightbox)

在 Xamarin 4.0 和更高版本中可以使用片段。 Xamarin Android 应用程序必须至少针对 API 级别11（Android 3.0）或更高版本，才能使用片段。 可以在项目属性中设置目标框架，如下所示：

[![在项目选项中设置目标框架 API 级别](images/03-sml.png)](images/03.png#lightbox)

使用 Android 支持包和 Xamarin 4.2 或更高版本时，可以在较旧版本的 Android 中使用片段。 此部分的文档更详细地介绍了如何执行此操作。

## <a name="related-links"></a>相关链接

- [Honeycomb 库（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](https://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV 网络研讨会：片段简介](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
