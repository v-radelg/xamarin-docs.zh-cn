---
title: RecyclerView
description: RecyclerView 用于显示集合; 是一组视图它旨在更灵活替代较旧的视图组，如 ListView 和 GridView。  本指南介绍如何使用和自 RecyclerView 定义在 Xamarin.Android 应用程序中。
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 1332a7ef5b8e5bb2f1178582bcf058123f1e177c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308750"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 用于显示集合; 是一组视图它旨在更灵活替代较旧的视图组，如 ListView 和 GridView。本指南介绍如何使用和自 RecyclerView 定义在 Xamarin.Android 应用程序中。_

## <a name="recyclerview"></a>RecyclerView

许多应用程序需要显示 （如邮件、 联系人、 图像或歌曲）; 具有相同类型的集合通常，此集合是太大，无法容纳在屏幕上，因此集合也会出现在一个小型窗口，可以顺利地滚动浏览集合中的所有项。
`RecyclerView` 是 Android 小组件显示在列表或网格，使用户可以滚动浏览集合中项的集合。 下面是示例中使用的应用程序的屏幕截图`RecyclerView`可以垂直滚动列表中显示电子邮件收件箱内容：

[![使用列表中的收件箱邮件 RecyclerView 示例应用](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` 提供两个极具吸引力的功能：

-  它具有灵活的体系结构，以便您可以通过插入在您首选的组件中修改其行为。

-  它是高效地使用较大的集合，因为它会重用项视图并需要使用*查看持有者*到缓存的视图引用。

本指南介绍如何使用`RecyclerView`Xamarin.Android 应用程序; 在其中介绍了如何添加`RecyclerView`包到你的 Xamarin.Android 项目，并介绍了如何`RecyclerView`典型的应用程序中的函数。 提供了真实代码示例来演示如何将集成`RecyclerView`到你的应用程序、 如何实现项视图，请单击，以及如何刷新`RecyclerView`在其基础数据发生更改。 本指南假定您熟悉 Xamarin.Android 开发。


### <a name="requirements"></a>要求

尽管`RecyclerView`是通常与 Android 5.0 Lollipop 相关联，它作为提供支持库&ndash;`RecyclerView`作用于应用，该目标 API 级别 (Android 2.1) 7 及更高版本。 使用所需的以下`RecyclerView`基于 Xamarin 的应用程序：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置与 Visual Studio 或 Visual Studio for mac。

-  应用程序项目必须包括**Xamarin.Android.Support.v7.RecyclerView**包。 有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


### <a name="overview"></a>概述

`RecyclerView` 可以视为替换`ListView`和`GridView`在 Android 中的小组件。 与其前身一样，`RecyclerView`设计用于在小型窗口中，显示大型数据集，但`RecyclerView`提供更多布局选项和更好地优化用于显示较大的集合。 如果你熟悉`ListView`，有几个重要区别之间`ListView`和`RecyclerView`:

-   `RecyclerView` 稍微更复杂，无法使用： 您必须编写更多代码以使用`RecyclerView`相比`ListView`。

-   `RecyclerView` 不提供的预定义的适配器;必须实现用于访问你的数据源的适配器代码。 但是，对于 Android 包含使用的几个预定义的适配器`ListView`和`GridView`。

-   `RecyclerView` 不提供项的单击事件时在用户点击项;相反，由帮助器类处理项单击事件。 与此相反，`ListView`提供项的单击事件。

-   `RecyclerView` 通过回收视图和通过强制持有者的视图模式，从而消除了不必要的布局资源查找，可增强性能。 中的持有者的视图模式的使用是可选`ListView`。

-   `RecyclerView` 基于模块化设计，可简化自定义。 例如，您可以在不同的布局策略无需更改大量代码中插入到您的应用程序。
    与此相反，`ListView`是相对比较整体化结构中。

-   `RecyclerView` 包括内置动画项添加和删除。 `ListView` 动画需要应用开发人员来说做一些额外工作。


### <a name="sections"></a>部分

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主题介绍如何`Adapter`， `LayoutManager`，并`ViewHolder`共同用作帮助程序类以支持`RecyclerView`。
它提供的每个帮助程序类的高级概述并介绍了如何在应用中使用它们。

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主题中提供的信息为基础[RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)提供真正的代码示例说明了如何通过各种`RecyclerView`元素实现以生成实际的照片浏览应用。

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主题将额外的代码添加到示例应用程序中显示[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)若要演示如何以处理项的单击事件并更新`RecyclerView`当基础数据源发生更改。


### <a name="summary"></a>总结

本指南介绍了 Android`RecyclerView`小组件; 本文介绍了如何添加`RecyclerView`如何支持到 Xamarin.Android 项目的库`RecyclerView`如何强制实施的持有者的视图模式的效率，以及如何回收视图的各种帮助器类，它们组成`RecyclerView`开展协作，显示集合。 它提供了示例代码演示如何`RecyclerView`集成到应用程序，它介绍了如何定制`RecyclerView`的布局策略通过插入不同的布局管理器，并介绍了如何处理项单击事件并通知`RecyclerView`的数据源更改。

有关详细信息`RecyclerView`，请参阅[RecyclerView 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。


## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
