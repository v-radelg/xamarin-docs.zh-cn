---
title: ListView 部件和功能
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 248baa5daceff6db01098a155600ea204547e845
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319708"
---
# <a name="listview-parts-and-functionality"></a>ListView 部件和功能


## <a name="overview"></a>概述

一个`ListView`以下几个部分组成：

- **行**&ndash;列表中的数据的可视表示形式。

- **适配器**&ndash;将数据源绑定到列表视图的非可视类。

- **快速滚动**&ndash;使用户可以滚动列表的长度的句柄。

- **部分索引**&ndash;通过滚动浮动的用户界面元素以指示列表中的当前行的位置的行。

这些屏幕截图使用基本`ListView`控件以显示快速滚动和部分索引的呈现方式：

[![使用普通的旧行，应用的屏幕快照快速滚动和部分索引](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

构成元素`ListView`下面更详细地介绍：


## <a name="rows"></a>行

每一行都具有其自己`View`。 该视图可以是内置在中定义的视图之一`Android.Resources`，或自定义视图。 每个行可以使用相同的视图布局或它们可以各不相同。 使用内置的布局和其他解释了如何定义自定义布局的本文档中有示例。


## <a name="adapter"></a>适配器

`ListView`控件需要`Adapter`提供带格式`View`每个行。 Android 具有内置适配器和视图，可以使用，也可以创建自定义类。


## <a name="fast-scrolling"></a>快速滚动

当`ListView`包含许多行的数据快速滚动可以启用，以帮助用户导航到列表的任何部分。 快速滚动滚动条可以根据需要启用 （和自定义在 API 级别 11 和更高版本）。


## <a name="section-index"></a>部分索引

在滚动较长的列表时，可选部分索引提供反馈的用户列表的哪一部分它们当前正在查看。 它只是适用于长列表，通常在与快速滚动一起使用。


## <a name="classes-overview"></a>类概述

用于显示的主要类`ListViews`如下所示：

[![说明 ListView 和关联的类之间的关系的 UML 关系图](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

每个类的用途如下所述：

- **ListView** &ndash;显示可滚动的行集合的用户界面元素。 在手机它通常将占用整个屏幕 (在这种情况下，`ListActivity`类可用于) 也可能是在手机或平板电脑设备上可查看大布局的一部分。

- **视图**&ndash;在 Android 中的视图可以是任何用户界面元素，但上下文中的`ListView`它需要`View`提供每个行。

- **BaseAdapter** &ndash;基类的适配器实现绑定`ListView`到数据源。

- **ArrayAdapter** &ndash;内置适配器类，将绑定到字符串数组`ListView`进行显示。 泛型`ArrayAdapter<T>`对于其他类型执行相同的操作。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`显示基于 SQLite 的查询的数据。

本文档包含使用的简单示例`ArrayAdapter`以及更复杂的示例需要的自定义实现`BaseAdapter`或`CursorAdapter`。

