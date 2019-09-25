---
title: RecyclerView 部件和功能
description: RecyclerView 布局管理器、适配器和视图持有者的概述。
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 7aa2cae4c8ca1ef9bb0412a4a62dc619af97b57f
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249765"
---
# <a name="recyclerview-parts-and-functionality"></a>RecyclerView 部件和功能

`RecyclerView`在内部处理某些任务（如视图的滚动和回收），但是它实质上是协调 helper 类以显示集合的管理器。 `RecyclerView`将任务委托给以下帮助程序类：

- **`Adapter`** 增加项布局（实例化布局文件的内容），并将数据绑定到`RecyclerView`中显示的视图。 &ndash; 适配器还会报告项目单击事件。

- **`LayoutManager`** 度量和定位中的`RecyclerView`项视图，并管理视图回收策略。 &ndash;

- **`ViewHolder`** &ndash;查找并存储视图引用。 视图刀柄还有助于检测项目-查看单击。

- **`ItemDecoration`** &ndash;允许应用向特定视图添加特殊的绘图和布局偏移量，以便在项、突出显示和视觉分组边界之间绘制分隔线。

- **`ItemAnimator`** &ndash;定义在项操作期间或对适配器进行更改时所发生的动画。

下图描述了`RecyclerView`、 `LayoutManager`和`Adapter`类之间的关系：

![包含 LayoutManager 的 RecyclerView 关系图，使用适配器访问数据集](parts-and-functionality-images/01-recyclerview-diagram.png)

如图所示， `LayoutManager`可以将视为`Adapter`和`RecyclerView`之间的中介。 可代表`Adapter` 调用`RecyclerView`方法。 `LayoutManager` 例如，当需要`LayoutManager`为中`Adapter`的`RecyclerView`特定项位置创建新的视图时，将调用方法。 增加该项目的布局，并`ViewHolder`创建实例（未显示）来缓存对该位置的视图的引用。 `Adapter` `LayoutManager`当`Adapter`调用将特定项绑定到数据集时，将为该项定位数据，从数据集中检索数据，并将其复制到关联的项视图。 `Adapter`

在应用`RecyclerView`中使用时，需要创建以下类的派生类型：

- **`RecyclerView.Adapter`** 提供从应用程序的数据集（特定于您的应用程序）到`RecyclerView`中显示的项视图的绑定。 &ndash; 适配器知道如何将中的`RecyclerView`每个项视图位置与数据源中的特定位置相关联。 此外，适配器还处理每个单独项视图中内容的布局，并为每个视图创建视图占位符。 适配器还会报告项目视图检测到的项目单击事件。

- **`RecyclerView.ViewHolder`** &ndash;缓存对项布局文件中的视图的引用，以避免不必要地重复资源查找。 视图容纳器还会安排在用户点击视图持有者的关联项视图时，将其转发到适配器。

- **`RecyclerView.LayoutManager`** 定位中的`RecyclerView`项。 &ndash; 您可以使用几个预定义的布局管理器之一，也可以实现您自己的自定义布局管理器。
    `RecyclerView`将布局策略委托给布局管理器，这样您就可以插入不同的布局管理器，而无需对应用进行重大更改。

此外，还可以选择扩展以下类，以更改应用`RecyclerView`中的外观：

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

如果未扩展`ItemDecoration`和`ItemAnimator`， `RecyclerView`则使用默认实现。 本指南不介绍如何`ItemDecoration`创建自定义类和`ItemAnimator`类; 有关这些类的详细信息，请参阅[RecyclerView. ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) and [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html)。

<a name="recycling" />

## <a name="how-view-recycling-works"></a>视图回收的工作原理

`RecyclerView`不会为数据源中的每个项分配项视图。 相反，它仅分配适合屏幕的项视图数，并在用户滚动时重用这些项布局。 当视图第一次滚动时，它会经历下图所示的回收过程：

[![说明查看回收的六个步骤的示意图](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. 当视图在视觉上滚动并不再显示时，它将成为一个*废料视图*。

2. "碎片" 视图放置在一个池中，并成为*回收视图*。
    此池是显示相同数据类型的视图缓存。

3. 当显示新项时，将从回收池中获取视图以供重用。 因为此视图必须在适配器重新绑定后才能显示，所以称为 "*脏视图*"。

4. 已回收脏视图：适配器查找要显示的下一项的数据，并将此数据复制到此项的视图。 这些视图的引用将从与回收视图关联的视图持有者中进行检索。

5. 回收视图将添加到中的项的列表中`RecyclerView` ，这些项将在屏幕上进行。

6. 当用户将滚动`RecyclerView`到列表中的下一项时，回收视图将在屏幕上显示。 同时，另一个视图会滚动显示，并根据以上步骤进行回收。

除项-视图重用外， `RecyclerView`还使用另一个效率优化：查看持有者。 *视图占位符*是缓存视图引用的简单类。 每次适配器增加项布局文件时，它也会创建相应的视图持有者。 视图占位符`FindViewById`用于获取对放大项-布局文件内的视图的引用。 每次回收布局以显示新数据时，这些引用用于将新数据加载到视图中。

## <a name="the-layout-manager"></a>布局管理器

布局管理器负责在`RecyclerView`显示中定位项; 它确定表示类型（列表或网格）、方向（项是垂直显示还是水平显示）以及应显示哪些方向项（按正常顺序或按相反的顺序）。 布局管理器还负责计算**RecycleView**显示中每个项的大小和位置。

布局管理器有一个额外的用途：它确定何时回收不再向用户显示的项视图的策略。
由于布局管理器识别哪些视图是可见的（而不是），因此，它在确定何时可以回收视图的最佳位置。 若要回收视图，布局管理器通常会调用适配器，以将回收视图的内容替换为不同的数据，如前面的[视图回收的工作原理](#recycling)中所述。

您可以扩展`RecyclerView.LayoutManager`以创建自己的布局管理器，也可以使用预定义的布局管理器。 `RecyclerView`提供以下预定义的布局管理器：

- **`LinearLayoutManager`** &ndash;对可垂直滚动的列中的项或在可水平滚动的行中进行排列。

- **`GridLayoutManager`** &ndash;在网格中显示项。

- **`StaggeredGridLayoutManager`** &ndash;显示分散网格中的项，其中某些项具有不同的高度和宽度。

若要指定布局管理器，请实例化所选的布局管理器`SetLayoutManager` ，并将其传递给方法。 请注意，在默认情况下， &ndash;必须指定布局管理器`RecyclerView`未选择预定义的布局管理器。

有关布局管理器的详细信息，请参阅[RecyclerView. LayoutManager 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html)。

## <a name="the-view-holder"></a>视图持有者

视图占位符是您为缓存视图引用定义的类。 适配器使用这些视图引用将每个视图绑定到其内容。 中的`RecyclerView`每个项都有一个关联的视图持有者实例，该实例将缓存该项的视图引用。 若要创建视图持有者，请使用以下步骤来定义一个类，以便保存每个项目的确切视图集：

1. 子类`RecyclerView.ViewHolder`。
2. 实现一个查找并存储视图引用的构造函数。
3. 实现适配器可用于访问这些引用的属性。

[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中提供`ViewHolder`了实现的详细示例。
有关的详细信息`RecyclerView.ViewHolder`，请参阅[RecyclerView. ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

## <a name="the-adapter"></a>适配器

`RecyclerView`集成代码的大部分 "繁重" 操作都在适配器中进行。 `RecyclerView`要求你提供从`RecyclerView.Adapter`派生的适配器以访问数据源，并使用数据源中的内容填充每个项。
由于数据源是特定于应用的，因此你必须实现可了解如何访问数据的适配器功能。 适配器从数据源中提取信息，并将其加载到集合中的`RecyclerView`每个项。

下图说明了适配器如何将数据源中的内容映射到中的每个行项内的`RecyclerView`各个视图：

[![说明将数据源连接到 ViewHolders 的适配器的关系图](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

适配器使用特定行`RecyclerView`项的数据加载每一行。 例如，对于行位置*p*，适配器会在数据源中的位置*p*找到关联的数据，并将此数据复制到`RecyclerView`集合中位于*p*位置的行项。
例如，在上面的绘图中，适配器使用视图持有者在该位置查找`ImageView`和`TextView`的引用，以便在用户滚动到集合时无需重复`FindViewById`调用这些视图。重用视图。

实现适配器时，必须重写以下`RecyclerView.Adapter`方法：

- **`OnCreateViewHolder`** &ndash;实例化项布局文件和视图持有者。

- **`OnBindViewHolder`** &ndash;将位于指定位置的数据加载到其引用存储在给定视图持有者中的视图中。

- **`ItemCount`** &ndash;返回数据源中的项数。

布局管理器在定位中的`RecyclerView`项时调用这些方法。

## <a name="notifying-recyclerview-of-data-changes"></a>向 RecyclerView 通知数据更改

`RecyclerView`不会在其数据源的内容更改时自动更新其显示;数据集发生更改`RecyclerView`时，适配器必须发出通知。 数据集可以通过多种方式进行更改;例如，项目中的内容可能会更改，或者可能会更改数据的整体结构。
`RecyclerView.Adapter`提供了许多方法，您可以调用`RecyclerView`这些方法以最有效的方式响应数据更改：

- **`NotifyItemChanged`** &ndash;指示位于指定位置处的项已更改。

- **`NotifyItemRangeChanged`** &ndash;指示指定位置范围内的项已更改。

- **`NotifyItemInserted`** &ndash;指示指定位置中的项已新插入。

- **`NotifyItemRangeInserted`** &ndash;指示指定位置范围内的项已新插入。

- **`NotifyItemRemoved`** &ndash;指示已删除指定位置中的项。

- **`NotifyItemRangeRemoved`** &ndash;指示已删除指定范围内的项。

- **`NotifyDataSetChanged`** &ndash;通知数据集已更改（强制执行完全更新）。

如果确切知道数据集的更改方式，则可以调用上述相应方法，以最有效的`RecyclerView`方式进行刷新。 如果你不确切地知道数据集的更改方式，则可以调用`NotifyDataSetChanged`，这种方式要低得多，因为`RecyclerView`必须刷新对用户可见的所有视图。 有关这些方法的详细信息，请参阅[RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

下一主题是[一个基本的 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)，它是一个示例应用程序，用于演示上述部分和功能的实际代码示例。

## <a name="related-links"></a>相关链接

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
