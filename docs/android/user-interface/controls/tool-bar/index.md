---
title: Toolbar
description: 工具栏是比默认操作栏提供更多灵活性的操作栏组件：可以将其放置在应用中的任何位置，可以更改其大小，还可以使用不同于应用主题的配色方案。 此外，每个应用屏幕都可以有多个工具栏。
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3f4a9393d8ef6ef54ba3dba29f1109080f1e321a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029092"
---
# <a name="toolbar"></a>Toolbar

_工具栏是比默认操作栏提供更多灵活性的操作栏组件：可以将其放置在应用中的任何位置，可以更改其大小，还可以使用不同于应用主题的配色方案。此外，每个应用屏幕都可以有多个工具栏。_

## <a name="overview"></a>概述

任何 Android 活动的关键设计元素都是一个*操作栏*。 操作栏是在 Android 应用中用于导航、搜索、菜单和品牌标记的 UI 组件。 在 android 5.0 棒糖之前的 Android 版本中，建议使用操作栏（也称为*应用栏*）来提供此功能。 

`Toolbar` 小组件（在 Android 5.0 棒糖形中引入）可以被视为操作栏接口的泛化 &ndash; 用于替换操作栏。 可以在应用布局中的任意位置使用 `Toolbar`，它比操作栏更易于自定义。 以下屏幕截图演示了本指南中创建的自定义 `Toolbar` 示例： 

[使用编辑、保存和溢出菜单项的工具栏![示例屏幕截图](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

`Toolbar` 和操作栏之间存在一些重要差异： 

- `Toolbar` 可以放置在用户界面中的任何位置。

- 同一屏幕上可以显示多个工具栏。

- 如果使用片段，则每个片段都可以有自己的 `Toolbar`。 

- 可以将 `Toolbar` 配置为仅跨越屏幕的部分宽度。 

- 由于 `Toolbar` 未绑定到活动的窗口 décor 的配色方案，因此它可以有一个视觉上不同的配色方案。 

- 与操作栏不同，`Toolbar` 不会在左侧包含一个图标。 其菜单上的菜单使用更少的空间。 

- `Toolbar` 高度是可调整的。 

- 可以在 `Toolbar`中包含其他视图。 

`Toolbar` 可以包含一个或多个以下元素： 

- 导航按钮

- 品牌徽标图像

- 标题和副标题

- 自定义视图

- 操作菜单

- 溢出菜单

Google 的[材料设计指南](https://material.google.com/)建议利用这些元素，使应用具有不同的外观（而不只是依赖于应用程序图标和标题）。 

本指南涵盖最常使用的 `Toolbar` 方案：

- 将活动的默认操作栏替换 `Toolbar`。 

- 将第二个 `Toolbar` 添加到活动。

- 使用**Android 支持库 V7 AppCompat**库（在本指南的其余部分中称为*AppCompat* ），在早期版本的 Android 上部署 `Toolbar`。 

## <a name="requirements"></a>要求

`Toolbar` 适用于 Android 5.0 棒糖形（API 21）及更高版本。 如果面向 android 5.0 之前的 Android 版本，请使用[Android 支持库 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，它在 NuGet 包中提供向后兼容 `Toolbar` 支持。 
[工具栏兼容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)介绍了如何使用此库。 

## <a name="related-links"></a>相关链接

- [棒糖形工具栏（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏（示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
