---
title: RecyclerView 部件和功能
description: RecyclerView 布局管理器、 适配器和视图持有者的概述。
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 13678d3b1bca102e6f608ad1c11838db1f14cd08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61309156"
---
# <a name="recyclerview-parts-and-functionality"></a>RecyclerView 部件和功能


`RecyclerView` 某些任务在内部 （如滚动和回收的视图），但它的句柄是实质上是协调帮助程序类来显示集合的管理器。 `RecyclerView` 委托到以下帮助器类的任务：

-   **`Adapter`** &ndash; 增大项布局 （实例化布局文件的内容），并将数据绑定到视图中显示`RecyclerView`。 适配器还报告项单击事件。

-   **`LayoutManager`** &ndash; 度量值并将项中的视图`RecyclerView`并管理视图回收的策略。

-   **`ViewHolder`** &ndash; 查找并存储视图的引用。 视图持有者还有助于检测项目视图下鼠标。

-   **`ItemDecoration`** &ndash; 允许应用将特殊的绘制和布局偏移量添加到项目中，突出显示和可视化分组边界之间绘制分隔线的特定视图。

-   **`ItemAnimator`** &ndash; 定义动画的项的操作期间发生或进行更改时向适配器。

之间的关系`RecyclerView`， `LayoutManager`，和`Adapter`类在下图中所示：

![RecyclerView 包含 LayoutManager、 适配器用于访问数据集的关系图](parts-and-functionality-images/01-recyclerview-diagram.png)

此图所示，`LayoutManager`可视为之间的媒介`Adapter`和`RecyclerView`。 `LayoutManager`对进行调用`Adapter`代表方法`RecyclerView`。 例如，`LayoutManager`调用`Adapter`方法时就可以创建新视图中的特定项位置`RecyclerView`。 `Adapter`增大该项的布局，并创建`ViewHolder`实例 （未显示） 来缓存对视图中的该位置的引用。 当`LayoutManager`调用`Adapter`若要将某个特定项绑定到数据集，`Adapter`定位的项的数据，检索从数据集中，并将其复制到关联的项目视图。

当使用`RecyclerView`在您的应用程序，创建以下类的派生的类型是必需的：

-   **`RecyclerView.Adapter`** &ndash; 提供从应用程序的数据集 （这是特定于您的应用程序） 中显示的项视图到的绑定`RecyclerView`。 适配器知道如何将关联中的每个项目视图位置`RecyclerView`到数据源中的特定位置。 此外，该适配器处理其中每个单独的项视图的内容的布局，并创建每个视图的视图持有者。 适配器还报告检测到的项目视图的项的单击事件。

-   **`RecyclerView.ViewHolder`** &ndash; 缓存对项布局文件中的视图的引用，以便不会不必要地重复资源查找。 查看项单击事件，以在用户点击视图持有者关联的项目视图时将它们转发到适配器还排列视图持有者。

-   **`RecyclerView.LayoutManager`** &ndash; 定位中的项`RecyclerView`。 可以使用多个预定义的布局管理器之一，也可以实现自己的自定义布局管理器。
    `RecyclerView` 委托到布局管理器，因此您可以在不同的布局管理器中而无需进行大量插入布局策略更改为您的应用程序。

此外，可以选择性地扩展要更改的外观的以下类`RecyclerView`应用程序中：

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

如果不扩展`ItemDecoration`并`ItemAnimator`，`RecyclerView`使用默认实现。 本指南不介绍如何创建自定义`ItemDecoration`并`ItemAnimator`类; 有关这些类的详细信息，请参阅[RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)和[RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>如何查看回收的工作原理

`RecyclerView` 不会为您的数据源中的每个项分配项目视图。 相反，它会分配仅在屏幕上显示的项视图的数量，它重用这些项布局，当用户滚动。 当不可见的第一次滚动视图时，会经历在下图中所示的回收过程：

[![说明的视图回收的六个步骤的关系图](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  当不可见的滚动并不会再显示视图时，它将成为*报废视图*。

2.  片段视图放在池中，并成为*回收视图*。
    此池是缓存的显示相同类型的数据的视图。

3.  当新项时显示时，则视图取自回收池以供重复使用。 因为此视图必须重新绑定适配器在显示之前，调用*脏视图*。

4.  已更新的视图是回收： 适配器查找要显示的下一项的数据并将此数据复制到此项目的视图。 从与回收的视图关联的视图持有者中检索这些视图的引用。

5.  回收的视图添加到中的项列表`RecyclerView`以转到屏幕上。

6.  回收的视图会屏幕上，因为在用户滚动`RecyclerView`到列表中的下一项。 同时，另一个视图滚动视线之外，会根据上述步骤被回收。

除了项目视图重用`RecyclerView`还使用另一个效率优化： 查看持有者。 一个*视图持有者*是缓存查看引用一个简单类。 适配器增大的项布局文件，每次它还会创建相应的视图持有者。 视图持有人使用`FindViewById`夸大的项布局文件中获取对视图的引用。 这些引用用于将新数据加载到视图，每次布局时回收显示新数据。
 


### <a name="the-layout-manager"></a>布局管理器

布局管理器负责定位中的项`RecyclerView`显示; 它确定表示类型 （列表或网格）、 （是否项都显示垂直或水平） 的方向，并应显示哪些方向项（按正常顺序或按相反的顺序）。 程序还负责计算的大小和位置中的每个项的布局管理器**RecycleView**显示。

该布局管理器的其他用途： 它会确定何时回收不再对用户可见的项视图的策略。
因为布局管理器识别哪些视图是可见 （，哪些不是），它是在要确定视图可以回收的最佳位置。 若要回收视图，布局管理器通常会对适配器调用，以回收视图的内容替换为不同的数据，如前面所述[视图回收的工作原理](#recycling)。

您可以扩展`RecyclerView.LayoutManager`创建自己的布局管理器中，也可以使用预定义的布局管理器。 `RecyclerView` 提供了以下预定义的布局管理器：

-   **`LinearLayoutManager`** &ndash; 排列项中可垂直滚动的列或行中，可以水平滚动。

-   **`GridLayoutManager`** &ndash; 在网格中显示项。

-   **`StaggeredGridLayoutManager`** &ndash; 交错的网格，其中某些项具有不同高度和宽度中显示的项。

若要指定布局管理器，实例化所选的布局管理器，并将其传递给`SetLayoutManager`方法。 请注意，您*必须*指定的布局管理器&ndash;`RecyclerView`不会默认情况下选择一个预定义的布局管理器。

有关布局管理器的详细信息，请参阅[RecyclerView.LayoutManager 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html)。


### <a name="the-view-holder"></a>视图持有者

视图持有者是为缓存视图引用定义的类。 适配器使用这些视图引用绑定到其内容的每个视图。 中的每个项`RecyclerView`具有关联的视图持有者实例的缓存项的视图引用。 若要创建视图持有者，使用以下步骤来定义一个类来保存每个项的视图的这组：

1.  子类`RecyclerView.ViewHolder`。
2.  实现一个构造函数，查找并存储视图的引用。
3.  实现该适配器可用于访问这些引用的属性。

详细的示例`ViewHolder`实现所示[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)。
有关详细信息`RecyclerView.ViewHolder`，请参阅[RecyclerView.ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="the-adapter"></a>适配器

大部分"繁琐"`RecyclerView`集成代码会在适配器中发生。 `RecyclerView` 要求提供适配器派生自`RecyclerView.Adapter`访问您的数据源并填充数据源的内容与每个项。
由于数据源是特定于应用程序，必须实现一个知道如何访问你的数据适配器功能。 适配器从数据源中提取信息，并将其加载到每个项`RecyclerView`集合。

下图阐释了适配器如何将数据源视图持有人通过中的内容映射到在每个行项中的各个视图`RecyclerView`:

[![说明数据源连接到 ViewHolders 的适配器的关系图](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

适配器将加载每个`RecyclerView`具有特定行项的数据行。 有关行位置*P*，例如，适配器找到位置处的关联的数据*P*行到此数据在位置中的数据源和副本项*P*中`RecyclerView`集合。
在上面的绘图中，例如，适配器使用视图持有者来查找其引用`ImageView`并`TextView`中的该位置，使其不包含重复调用`FindViewById`这些视图作为用户滚动浏览集合和重用视图。

在实现适配器时，必须重写以下`RecyclerView.Adapter`方法：

-   **`OnCreateViewHolder`** &ndash; 实例化项布局文件和视图持有者。

-   **`OnBindViewHolder`** &ndash; 将指定位置处的数据加载到其引用存储在给定的视图持有者的视图。

-   **`ItemCount`** &ndash; 在数据源中返回的项数。

布局管理器调用这些方法，它将定位中的项而`RecyclerView`。 



### <a name="notifying-recyclerview-of-data-changes"></a>通知数据更改 RecyclerView

`RecyclerView` 不会自动更新其显示时其数据的内容源发生更改;适配器必须通知`RecyclerView`数据集中的更改时。 数据集可能会更改在许多方面;例如，可以更改其中某个项的内容或数据的整体结构可能会更改。
`RecyclerView.Adapter` 提供了多种可以调用的方法，以便`RecyclerView`响应数据更改的最高效的方式：

-  **`NotifyItemChanged`** &ndash; 位于指定位置处的项已更改的信号。

-  **`NotifyItemRangeChanged`** &ndash; 指定范围中的位置的项已更改的信号。

-  **`NotifyItemInserted`** &ndash; 发出信号，表明新插入指定位置中的项。

-  **`NotifyItemRangeInserted`** &ndash; 指定范围中的位置的项已插入新的信号。

-  **`NotifyItemRemoved`** &ndash; 已删除的指定位置中的项的信号。

-  **`NotifyItemRangeRemoved`** &ndash; 指定范围中的位置的项已删除的信号。

-  **`NotifyDataSetChanged`** &ndash; 数据集已更改的信号 （强制执行完整的更新）。

如果您知道确切数据集的更改方式，可以调用适当的方法更高版本，若要刷新`RecyclerView`最高效的方式。 如果不知道确切数据集的更改方式，可以调用`NotifyDataSetChanged`，大大减少效率更高因为`RecyclerView`必须刷新所有对用户可见的视图。 有关这些方法的详细信息，请参阅[RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

在下一主题[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)，示例应用程序实现以演示的部件和上面所述的功能的实际代码示例。


## <a name="related-links"></a>相关链接

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
