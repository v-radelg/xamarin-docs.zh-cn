---
title: ViewPager
description: ViewPager 是使您可以实现动作导航的布局管理器。 动作导航允许用户轻扫，左侧和右侧到单步执行的数据页。 本指南介绍如何实现动作导航使用 ViewPager，使用和不使用片段。 它还介绍了如何添加使用 PagerTitleStrip 和 PagerTabStrip 页指示符。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: bb9795eb1e77a48b01556c553ae19613d6ab6de6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267575"
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是使您可以实现动作导航的布局管理器。动作导航允许用户轻扫，左侧和右侧到单步执行的数据页。本指南介绍如何实现动作导航使用 ViewPager，使用和不使用片段。它还介绍了如何添加使用 PagerTitleStrip 和 PagerTabStrip 页指示符。_

 
## <a name="overview"></a>概述

应用程序开发中的常见方案是需要向用户提供动作同级视图之间导航。 在此方法中，在用户轻扫左或向右访问页的内容 （例如，在安装向导或幻灯片放映）。 可以使用来创建这些轻扫视图`ViewPager`小组件中可用[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 `ViewPager`是布局小组件的多个子视图组成，其中每个子视图构成布局中的页： 

[![使用水平轻扫示例的屏幕截图的 TreePager 应用程序](images/01-intro-sml.png)](images/01-intro.png#lightbox)

通常情况下，`ViewPager`结合使用[片段](https://developer.xamarin.com/guides/android/platform_features/fragments/); 但是，某些情况下，可能想要使用`ViewPager`而无需增加的复杂性`Fragment`s。

`ViewPager` 使用适配器模式为其提供要显示的视图。 此处使用的适配器不从概念上讲类似于由[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash;提供的实现`PagerAdapter`生成页面的`ViewPager`向用户显示。 通过显示的页`ViewPager`可以是`View`s 或`Fragment`s。 当`View`显示，适配器子类 Android 的`PagerAdapter`基类。 如果`Fragment`显示，适配器子类 Android 的`FragmentPagerAdapter`。 Android 支持库还包括`FragmentPagerAdapter`(的子类`PagerAdapter`) 来帮助进行连接的详细信息`Fragment`的数据。 

本指南演示了这两种方法： 

-   在[视图的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-views.md)即[TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)开发出应用程序来演示如何使用`ViewPager`显示树目录 （落叶和长期有效树的图像库） 的视图。 
    `PagerTabStrip`  和`PagerTitleStrip`用于显示帮助页面导航的标题。

-   在中[片段的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)，稍微复杂[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)开发出应用程序来演示如何使用`ViewPager`与`Fragment`s 构建的应用程序带来了作为数学问题闪存卡，并响应用户输入。 


## <a name="requirements"></a>要求

若要使用`ViewPager`在应用程序项目中，必须安装[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)包。 有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 

 
## <a name="architecture"></a>体系结构

用于三个组件实现使用动作导航`ViewPager`:

-   ViewPager
-   适配器
-   页导航指示器

下面概述了每个组件。



### <a name="viewpager"></a>ViewPager

`ViewPager` 是显示的集合的布局管理器`View`s 一次。 其工作是检测用户的轻扫手势，并导航到相应的下一步或上一个视图。 例如，下面的屏幕截图演示了`ViewPager`以响应用户手势进行到下一个图像中的转换： 

[![显示视图之间的转换 TreePager 特写应用](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>适配器

`ViewPager` 从其数据中提取*适配器*。 适配器的作业将创建`View`情况下显示的 s `ViewPager`，根据需要提供它们。 下图阐释了这一概念&ndash;适配器创建并填充`View`s，并提供到`ViewPager`。 作为`ViewPager`检测到用户的轻扫手势，它会要求提供相应的适配器`View`显示： 

[![说明如何在适配器连接图像和名称到 ViewPager 的关系图](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在此特定示例中，每个`View`之前将它传递到构造从树图像和树名称`ViewPager`。 



### <a name="pager-indicator"></a>页导航指示器

`ViewPager` 用于显示大型数据集 （例如，图像库可能包含数百个图像）。 若要帮助用户导航大型数据集，`ViewPager`通常伴随*寻呼指示器*显示的字符串。 字符串可能是图像标题、 标题或只是当前视图的数据集内的位置。 

有两个视图可能会产生此导航信息：`PagerTabStrip`和`PagerTitleStrip.`每个顶部显示的字符串`ViewPager`，和每个拉取从其数据`ViewPager`的适配器，使其始终保持与同步当前显示`View`。 它们之间的区别在于`PagerTabStrip`包括"当前"字符串时的可视指示器`PagerTitleStrip`不 （根据这些屏幕截图所示） 执行： 

[![使用 PagerTitleStrip 和 PagerTabStrip TreePager 应用程序的屏幕截图](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南演示如何 immplement `ViewPager`，适配器和指示器应用程序组件并将它们以支持动作导航集成。 



## <a name="related-links"></a>相关链接

- [TreePager （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
