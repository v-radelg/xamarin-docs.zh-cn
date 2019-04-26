---
title: Android 穿戴设备简介
description: Google 的 Android Wear 的引入，已不再限制为只是手机和平板电脑谈到开发优秀的 Android 应用程序。 对 Android Wear Xamarin.Android 的支持使您可以运行C#上手腕代码 ！ 本简介提供的 Android Wear 的基本概述，描述其主要功能，并提供了 Android Wear 2.0 中提供的功能的概述。 它列出了一些更受欢迎的 Android Wear 设备，并提供有关其他参考资料的基本 Google Android Wear 文档的链接。
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a35cb82f4f6d20e91f45a782c73d3ef811947c3a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61284163"
---
# <a name="introduction-to-android-wear"></a>Android 穿戴设备简介

_Google 的 Android Wear 的引入，已不再限制为只是手机和平板电脑谈到开发优秀的 Android 应用程序。对 Android Wear Xamarin.Android 的支持使您可以运行C#上手腕代码 ！本简介提供的 Android Wear 的基本概述，描述其主要功能，并提供了 Android Wear 2.0 中提供的功能的概述。它列出了一些更受欢迎的 Android Wear 设备，并提供有关其他参考资料的基本 Google Android Wear 文档的链接。_


## <a name="overview"></a>概述

Android 穿戴设备在各种设备，包括第一代 Motorola 360、 LG 的 G 观看和 Samsung 齿轮 Live 上运行。 此外具有其他功能，包括内置的 GPS 和脱机音乐播放发布了第二个生成，包括 Sony 的 SmartWatch 3。 对于 Android Wear 2.0，Google 已与协作 LG 的两个新监视： LG 监视运动和 LG 监视样式。

![Android Wear 2.0 设备](intro-to-wear-images/hero-image.png "示例 Android Wear 2.0 设备")

Xamarin.Android 5.0 及更高版本支持 Android Wear (API 20) 我们 Android 4.4W 通过支持和 NuGet 包，它将添加其他特定于穿戴设备的 UI 控件。 Xamarin.Android 5.0 及更高版本还包括用于打包穿戴设备应用程序的功能。 也将适用于 Android Wear 2.0 本指南后面所述 NuGet 程序包。


## <a name="android-wear-basics"></a>Android 穿戴设备基础知识

Android 穿戴设备具有不同于手持设备的 Android 应用的用户界面范例。 Wear 应用的第一波次旨在扩展一起提供一些方法，但开头的 Android Wear 2.0 中的手持设备应用，穿戴设备应用，可以在单独使用。 Wear 应用部署，它是与辅助掌上电脑应用程序一起打包。 由于大多数 Wear 应用都依赖于在手持辅助应用程序时，它们需要以某种方式与手持应用进行通信。 以下各节描述这些使用方案，并概述 Android Wear 的基本功能。 



### <a name="usage-scenarios"></a>使用方案

Android Wear 的第一个版本主要关注当前手持通过扩展应用程序增强通知和手持设备的应用程序和可穿戴应用之间同步数据。 因此，这些方案是相对比较简单实现。


#### <a name="wearable-notifications"></a>可穿戴通知

支持 Android Wear 的最简单方法是特性的利用共享掌上电脑和可穿戴设备之间的通知。 通过使用支持 v4 通知 API 并`WearableExtender`类 (提供[Xamarin Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/))，可以利用平台，如收件箱设置卡样式的本机功能或语音输入。 [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)示例提供了代码示例演示如何向 Android Wear 的设备发送的通知的列表。 



#### <a name="companion-applications"></a>配套应用程序

另一种策略是创建一个可穿戴设备上本机运行并对与辅助掌上电脑应用程序的完整应用程序。 此方法的一个典型示例是[测验](https://developer.xamarin.com/samples/monodroid/wear/Quiz/)示例应用中，该示例演示了如何创建测验的手持设备上运行并可穿戴设备上询问测验问题。 



### <a name="user-interface"></a>用户界面

穿戴设备的主要导航模式是一系列的垂直排列的卡。 每个卡可以具有关联的分层出同一行的操作。 `GridViewPager`类提供此功能，它遵循相同的适配器概念`ListView`。 您通常将相关联`GridViewPager`与`FragmentGridPagerAdaptor`(或`GridPagerAdaptor`)，用于表示每个行和列的单元格作为`Fragment`: 

[![Wear 导航](intro-to-wear-images/2d-picker-sml.png "Wear 导航")](intro-to-wear-images/2d-picker.png#lightbox)

Wear 还使操作按钮构成的一项非常重大的使用彩色圆圈使用下方的小说明文本 （如上面所示）。  [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)示例演示如何使用`GridViewPager`和`GridPagerAdapter`穿戴设备应用中。

Android Wear 2.0 添加到穿戴设备用户界面导航抽屉、 操作抽屉和内联操作按钮。 有关 Android Wear 2.0 用户界面元素有关的详细信息，请参阅 Android[剖析](https://www.google.com/design/spec-wear/system-overview/anatomy.html)主题。 



### <a name="communications"></a>通信

Android 穿戴设备提供了两个不同的通信 Api，以促进穿戴设备应用和手持的配套应用程序之间的通信： 

**数据 API** &ndash;此 API 是类似于可穿戴设备和手持设备之间同步的数据存储。 Android 负责将可穿戴和手持设备之间的更改传播时将执行此操作最佳选择。 可穿戴设备超出范围时，它将队列更高版本的时间的同步。 此 API 的主入口点是`WearableClass.DataApi`。 有关此 API 的详细信息，请参阅 Android[同步数据项](https://developer.android.com/training/wearables/data-layer/data-items.html)主题。 

**消息 API** &ndash;此 API 使您能够使用较低级别通信路径： 小负载发送单向无需在掌上电脑和可穿戴应用之间的同步。
此 API 的主入口点是`WearableClass.MessageApi`。
有关此 API 的详细信息，请参阅 Android[发送和接收消息](https://developer.android.com/training/wearables/data-layer/messages.html)主题。

可以选择要注册以接收这些消息通过的每个 API 侦听器接口回调或，或者，派生的应用程序中实现的服务`WearableListenerService`。
此服务将自动通过 Android Wear 实例化。
[FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/)示例演示如何实现`WearableListenerService`。



### <a name="deployment"></a>部署

每个可穿戴应用不会随其自己嵌入主应用程序的 APK 的 APK 文件部署。 此打包在 Xamarin.Android 5.0 及更高版本，会自动处理，但必须手动执行的 Xamarin.Android 版本早于版本 5.0。 
[使用打包](~/android/wear/deploy-test/packaging.md)介绍更多详细信息中的部署。 



## <a name="going-further"></a>深入学习 

熟悉 Android Wear 的最佳方式是生成和测试你的第一个应用。 以下列表提供了建议的阅读顺序，以帮助您快速掌握：

1.  [设置和安装](~/android/wear/get-started/installation.md)安装和配置用于构建 Xamarin.Android 穿戴设备应用程序开发环境提供了详细的说明。 

2.  已安装所需的包并配置仿真器或设备后，请参阅[你好，穿戴设备](~/android/wear/get-started/hello-wear.md)的分步说明了如何创建一个小型的 Android Wear 项目该句柄按钮单击，并显示单击在穿戴设备上的计数器。 

3.  [部署和测试](~/android/wear/deploy-test/index.md)提供更详细的有关配置和部署到仿真程序和设备，包括如何将应用部署到在穿戴设备通过蓝牙说明信息。

4.  [使用屏幕大小](~/android/wear/screen-sizes.md)介绍了如何预览和优化您的用户界面穿戴设备的设备上各种可用的屏幕大小。 

5.  [使用打包](~/android/wear/deploy-test/packaging.md)说明手动打包穿戴设备适用于 Google Play 上的分发的应用程序的步骤。

创建第一个 Wear 应用后，你可能想要尝试为 Android Wear 构建自定义手表表盘。 
[创建表盘](~/android/wear/platform/creating-a-watchface.md)开发压下数字监视人脸服务，可以增强其功能到模拟样式表盘具有额外功能的更多代码后跟提供分步说明和示例代码。 



## <a name="android-wear-20"></a>Android Wear 2.0

Android Wear 2.0 引入了各种新特性和功能，如*复杂性*，曲线布局、 导航和操作抽屉和扩展的通知。 此外，Wear 2.0 使您可以构建的独立手持应用于的独立应用程序。 新*手腕手势*功能，可以与您的应用程序的单手交互。 以下部分重点介绍了这些特性，并提供链接，以帮助你开始使用应用程序中使用它们。



### <a name="install-wear-20-packages"></a>安装 Wear 2.0 包

若要生成使用 Xamarin.Android Wear 2.0 应用，必须添加**Xamarin.Android.Wear v2.0**包到你的项目 (单击**浏览选项卡**):

[![Xamarin.Android.Wear v2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "安装 Xamarin.Android.Wear v2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

此 NuGet 包包含绑定的 Android 支持可穿戴和穿戴设备兼容性的库。

除了**Xamarin.Android.Wear**，我们建议你安装**Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "安装 Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Wear 2.0 的主要功能

Android Wear 2.0 是自 2014年中其初始启动后到 Android Wear 史无前例的重大更新。 以下部分重点介绍 Android Wear 2.0 中，主要功能并提供链接以帮助您了解如何在应用中使用这些新功能。 


#### <a name="complications"></a>复杂情况

*复杂情况*是人脸小组件，您可以快速查看而无需往下轻扫手表表盘的小监视。 复杂性是类似于桌面样式仪表板小组件;它们显示信息，例如天气、 电池寿命、 日历事件和 fitness 应用统计信息： 

![复杂情况的示例](intro-to-wear-images/complications.png "复杂情况示例")

有关复杂的详细信息，请参阅 Android[监视人脸复杂性](https://developer.android.com/wear/preview/features/complications.html)主题。 



#### <a name="navigation-and-action-drawers"></a>导航和操作抽屉 

Wear 2.0 中包含两个新抽屉。 *导航抽屉*，其中显示在屏幕的顶部，用户可以应用视图间导航了 （如左下图所示）。 *操作抽屉*，其中显示在屏幕 （如右侧所示） 的底部，用户可以从操作列表中选择。 

![导航和操作抽屉](intro-to-wear-images/drawers.png "导航和操作抽屉")

有关这些两个新的交互式抽屉的详细信息，请参阅 Android [Wear 导航和操作](https://developer.android.com/wear/preview/features/ui-nav-actions.html)主题。 



#### <a name="curved-layouts"></a>曲线的布局 

Wear 2.0 引入了用于显示曲线的布局倒圆角穿戴设备设备上的新功能。 具体而言，新`WearableRecyclerView`类适用于在倒圆角的显示器上显示垂直的项的列表： 

![曲线布局示例](intro-to-wear-images/curved-layout.png "曲线布局示例")

`WearableRecyclerView` 扩展了`RecyclerView`类，以支持曲线的布局和循环滚动手势。 有关详细信息，请参阅 Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) API 文档。 



#### <a name="standalone-apps"></a>独立的应用程序 

Android Wear 2.0 应用程序可独立于掌上电脑应用。 这意味着，例如，智能手表可以继续提供完整的功能即使伴侣手持设备处于关闭还是远从可穿戴设备状态。 有关此功能的详细信息，请参阅 Android[独立的应用程序](https://developer.android.com/wear/preview/features/standalone-apps.html)主题。



#### <a name="wrist-gestures"></a>手腕手势 

手腕手势可使用户与您的应用程序交互而无需使用触摸屏&ndash;用户能够响应应用程序与单个手的形状。 支持两个手腕手势： 

-   笔锋手腕出
-   笔锋手腕中

有关详细信息，请参阅 Android[手腕手势](https://developer.android.com/wear/preview/features/gestures.html)主题。 


有许多详细 Wear 2.0 功能，例如内联操作、 智能回复、 远程输入、 扩展的通知和通知新的桥接模式。 有关 Wear 2.0 的新功能的详细信息，请参阅 Android [API 概述](https://developer.android.com/wear/preview/api-overview.html)。 



## <a name="devices"></a>设备

下面是可以运行 Android Wear 的设备的一些示例：

* [Motorola 360](https://moto360.motorola.com/)
* [LG G 监视](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G 监视 R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung 齿轮实时](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>其他阅读材料

查看 Google 的 Android Wear 文档：

* [有关 Android 穿戴设备](http://www.android.com/wear/)
* [Android 穿戴设备应用程序设计](https://developer.android.com/design/wear/index.html)
* [android.support.wearable 库 ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android Wear 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>总结

本简介提供 Android Wear 的概述。 它所述的 Android Wear 的基本功能，并包含 Android Wear 2.0 中引入的功能的概述。 它提供基本读取以帮助开发人员开始使用 Xamarin.Android 穿戴设备开发的链接，它列出当前市场上的 Android Wear 设备的一些示例。


## <a name="related-links"></a>相关链接

- [安装和设置](~/android/wear/get-started/installation.md)
- [入门](~/android/wear/get-started/index.md)
