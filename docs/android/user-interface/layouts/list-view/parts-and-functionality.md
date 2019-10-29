---
title: ListView 部件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: b8fd44a70f4c7ecdcf7919dec1c81461200b35bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028869"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Xamarin Android ListView 部件和功能

`ListView` 由以下部分组成：

- **行**&ndash; 列表中数据的可见表示形式。

- **适配器**&ndash; 一个将数据源绑定到列表视图的非视觉对象类。

- **快速滚动**&ndash; 允许用户滚动列表长度的句柄。

- **节索引**&ndash; 一个用户界面元素，该元素将浮动在滚动行上，以指示当前行在列表中的位置。

这些屏幕截图使用基本 `ListView` 控件来显示快速滚动和节索引的呈现方式：

[使用纯旧行、快速滚动和节索引![应用程序的屏幕截图](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

下面更详细地介绍组成 `ListView` 的元素：

## <a name="rows"></a>行

每行都有自己的 `View`。 视图可以是 `Android.Resources`中定义的内置视图之一，也可以是自定义视图。 每一行都可以使用相同的视图布局，也可以是不同的。 此文档中有一些示例，介绍了如何使用内置布局以及如何定义自定义布局。

## <a name="adapter"></a>适配器

`ListView` 控件需要 `Adapter` 为每一行提供格式的 `View`。 Android 具有内置的适配器和可使用的视图，或者可以创建自定义类。

## <a name="fast-scrolling"></a>快速滚动

当 `ListView` 包含多行数据时，可以启用快速滚动，以帮助用户导航到列表的任何部分。 可以选择启用快速滚动的 "滚动条" （在 API 级别11和更高版本中自定义）。

## <a name="section-index"></a>节索引

当滚动到长列表时，可选节索引向用户提供当前正在查看的列表部分的反馈。 它仅适用于长列表，通常与快速滚动一起提供。

## <a name="classes-overview"></a>类概述

用于显示 `ListViews` 的主要类如下所示：

[阐释 ListView 和关联类之间的关系![UML 关系图](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

每个类的用途如下所述：

- **ListView** &ndash; 显示可滚动行集合的用户界面元素。 在手机上，它通常会使用整个屏幕（在这种情况下，可以使用 `ListActivity` 类），也可能是手机或平板电脑设备上的更大布局的一部分。

- **查看**Android 中的视图 &ndash; 可以是任何用户界面元素，但在 `ListView` 环境中，它需要为每一行提供 `View`。

- **BaseAdapter** &ndash; 用于适配器实现的基类，用于将 `ListView` 绑定到数据源。

- 将字符串数组绑定到 `ListView` 以便显示的**ArrayAdapter** &ndash; 内置适配器类。 泛型 `ArrayAdapter<T>` 对其他类型执行相同的工作。

- **CursorAdapter** &ndash; 使用 `CursorAdapter` 或 `SimpleCursorAdapter` 来基于 SQLite 查询显示数据。

本文档包含使用 `ArrayAdapter` 的简单示例以及需要 `BaseAdapter` 或 `CursorAdapter`的自定义实现的更复杂的示例。
