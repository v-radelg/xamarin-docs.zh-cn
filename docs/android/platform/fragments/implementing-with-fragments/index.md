---
title: 实现片段的演练
description: 本文介绍如何使用片段开发 Xamarin.Android 应用程序。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 2ff4729e68497391d41521da26917571c146b541
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667257"
---
# <a name="implementing-fragments---walkthrough"></a>实现片段的演练

_片段是自包含的模块化组件，可帮助您解决有多种不同的屏幕大小的目标设备的 Android 应用的复杂性。本文介绍如何创建和开发 Xamarin.Android 应用程序时使用片段。_

## <a name="overview"></a>概述

在本部分中，您将了解如何创建和使用 Xamarin.Android 应用程序中的片段。 此应用程序将通过 William 莎士比亚显示在列表中的多个播放的标题。 当用户点击播放的标题时，应用会在单独的活动显示该播放的引文：

[![在纵向模式下在 Android 手机上运行的应用](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

时电话旋转为横向模式下，将更改应用的外观： 列表播放和引号将出现在同一个活动。 选择播放时，引号将显示在同一活动中：

[![在横向模式下在 Android 手机上运行的应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最后，如果在平板电脑上运行该应用程序：

[![在 Android 平板电脑上运行应用](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此示例应用程序可以轻松地适应不同外观造型和方向的最少的代码更改使用片段并[备用布局](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)。

应用程序的数据将作为应用程序中是硬编码的两个字符串数组中存在C#的字符串数组。 每个数组将充当一个段的数据源。  一个数组将保存的莎士比亚，某些播放的名称和其他数组将包含该播放的引文。 当应用启动时，它将显示中的 play 名称`ListFragment`。 当用户单击中发挥作用`ListFragment`，应用将启动另一个活动将显示引号。

应用程序的用户界面将包含两个布局、 一个用于纵向和横向模式下的一个。 在运行时，Android 将确定要加载哪些布局基于设备的方向，并将提供给要呈现的活动的布局。 片段中，将包含所有响应用户的单击操作和显示数据的逻辑。 仅为将承载片段的容器存在的应用中的活动。

本演练分为两个指南。 [第一部分](./walkthrough.md)将专注于应用程序的核心部分。 将创建一组布局 （已优化为纵向模式），以及两个片段和两个活动：

1. `MainActivity` &nbsp; 这是应用程序的启动活动。
1. `TitlesFragment` &nbsp; 此代码段将显示的编写的 William 莎士比亚剧本的标题的列表。 它将由托管`MainActivity`。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 将启动`PlayQuoteActivity`以响应用户选择在 play `TitlesFragment`。
1. `PlayQuoteFragment` &nbsp; 此代码段将显示通过 William 莎士比亚播放的引文。 它将由托管`PlayQuoteActivity`。

[本演练中的第二个](./walkthrough-landscape.md)将讨论添加备用布局 （已优化的横向模式下） 这将在屏幕上显示两个片段。 此外，一些细微的代码将进行更改的代码，以便该应用将调整其行为与在屏幕同时显示的片段数。

## <a name="related-links"></a>相关链接

- [FragmentsWalkthrough （示例）](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [设计器概述](~/android/user-interface/android-designer/index.md)
- [实现片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](https://developer.android.com/sdk/compatibility-library.html)
