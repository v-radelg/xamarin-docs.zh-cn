---
title: 实现片段-演练
description: 本文介绍如何使用片段开发 Xamarin Android 应用程序。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027418"
---
# <a name="implementing-fragments---walkthrough"></a>实现片段-演练

_片段是自包含的模块化组件，可帮助解决面向设备的、使用各种屏幕大小的 Android 应用程序的复杂性。本文逐步讲解如何在开发 Xamarin Android 应用程序时创建和使用片段。_

## <a name="overview"></a>概述

本部分介绍如何在 Xamarin Android 应用程序中创建和使用片段。 此应用程序将在列表中显示 William 莎士比亚的多个重头戏的标题。 当用户点击 "播放" 标题时，应用会在单独的活动中显示此重头戏的报价：

[在 Android 手机上以纵向模式运行的![应用](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

当手机旋转到横向模式时，应用的外观将发生变化：播放和引号的列表将出现在同一活动中。 选择播放时，报价将显示在同一活动中：

[在横向模式下，在 Android 手机上运行![应用](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最后，如果应用程序在平板电脑上运行：

[在 Android 平板电脑上运行![应用](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此示例应用程序可以通过使用片段和[替代布局](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)来轻松地适应不同的外形规格和方向，同时实现最少的代码更改。

应用程序的数据将存在于两个字符串数组中，这些数组在应用中C#硬编码为字符串数组。 每个数组都将用作一个片段的数据源。  一个数组将保存某些莎士比亚的名称，另一个数组将保留该重头戏的报价。 当应用程序启动时，它会在 `ListFragment`中显示播放名称。 当用户在 `ListFragment`中单击 "播放" 时，该应用将启动另一个活动，该活动将显示报价。

应用的用户界面将包含两个布局，一个用于纵向模式，另一个用于横向模式。 在运行时，Android 将根据设备的方向确定要加载的布局，并为要呈现的活动提供该布局。 用于响应用户单击和显示数据的所有逻辑都将包含在片段中。 应用中的活动仅作为将承载片段的容器存在。

本演练将分为两个指南。 [第一部分](./walkthrough.md)将重点放在应用程序的核心部分。 将创建一组布局（适用于纵向模式），以及两个片段和两个活动：

1. `MainActivity` &nbsp; 这是应用的启动活动。
1. `TitlesFragment` &nbsp; 此片段将显示由 William 莎士比亚编写的播放标题的列表。 它将由 `MainActivity`来托管。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 将启动 `PlayQuoteActivity`，以响应用户在 `TitlesFragment`中选择一个重头戏。
1. `PlayQuoteFragment` &nbsp; 此片段将显示 William 莎士比亚的播放报价。 它将由 `PlayQuoteActivity`来托管。

[本演练的第二部分](./walkthrough-landscape.md)将讨论如何添加替代布局（针对横向模式进行优化），这会在屏幕上显示这两个片段。 此外，还将对代码进行一些次要代码更改，以便应用将其行为调整为在屏幕上并发显示的碎片数。

## <a name="related-links"></a>相关链接

- [FragmentsWalkthrough （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [设计器概述](~/android/user-interface/android-designer/index.md)
- [实现片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支持包](https://developer.android.com/sdk/compatibility-library.html)
