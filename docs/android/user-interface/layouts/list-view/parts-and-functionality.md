---
title: ListView 部件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 4566ee5d203b5d098133aebe2c32dbaec712e17a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764214"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Xamarin Android ListView 部件和功能

`ListView`由以下部分组成：

- **行**&ndash;列表中数据的可见表示形式。

- **适配器**&ndash;将数据源绑定到列表视图的非视觉对象。

- **快速滚动**&ndash;允许用户滚动列表长度的句柄。

- **节索引**&ndash;一个用户界面元素，该元素在滚动行上浮动以指示当前行在列表中的位置。

这些屏幕截图使用基本`ListView`控件来显示滚动和节索引的呈现速度：

[![使用纯旧行、快速滚动和节索引的应用屏幕截图](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

下面更详细地介绍了`ListView`组成的元素：

## <a name="rows"></a>行

每行都有自己`View`的。 视图可以是在中`Android.Resources`定义的内置视图之一，也可以是自定义视图。 每一行都可以使用相同的视图布局，也可以是不同的。 此文档中有一些示例，介绍了如何使用内置布局以及如何定义自定义布局。

## <a name="adapter"></a>适配器

控件需要为每行提供格式设置`View`。 `ListView` `Adapter` Android 具有内置的适配器和可使用的视图，或者可以创建自定义类。

## <a name="fast-scrolling"></a>快速滚动

`ListView`当包含多行数据时，可以启用快速滚动，以帮助用户导航到列表的任何部分。 可以选择启用快速滚动的 "滚动条" （在 API 级别11和更高版本中自定义）。

## <a name="section-index"></a>节索引

当滚动到长列表时，可选节索引向用户提供当前正在查看的列表部分的反馈。 它仅适用于长列表，通常与快速滚动一起提供。

## <a name="classes-overview"></a>类概述

用于显示`ListViews`的主要类如下所示：

[![阐释 ListView 和关联类之间的关系的 UML 关系图](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

每个类的用途如下所述：

- **ListView**&ndash;显示可滚动行集合的用户界面元素。 在手机上，它通常使用整个屏幕（在这种情况下`ListActivity` ，可以使用类），或者它可能是手机或平板电脑设备上的更大布局的一部分。

- **视图**Android 中的视图可以是任何用户界面元素，但在的上下文`ListView`中，它需要为每行提供一个。 `View` &ndash;

- **BaseAdapter**&ndash;用于将`ListView`绑定到数据源的适配器实现的基类。

- **ArrayAdapter**将字符串数组绑定`ListView`到的内置适配器类，用于显示。 &ndash; 泛型`ArrayAdapter<T>`对其他类型执行相同的工作。

- **CursorAdapter**&ndash;使用或可`SimpleCursorAdapter`基于 SQLite 查询显示数据。 `CursorAdapter`

本文档包含一些简单的示例， `ArrayAdapter`这些示例使用和更复杂的示例，这些示例`BaseAdapter`需要`CursorAdapter`或的自定义实现。
