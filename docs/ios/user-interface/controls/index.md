---
title: 在 Xamarin.iOS 中的用户界面控件
description: 本文档所链接到指南描述对 Xamarin.iOS 开发人员可用的各种 iOS 用户界面控件。 链接的内容介绍警报、 按钮、 集合视图、 图像、 手动相机控件、 映射、 标签、 选取器、 日期选取器和的详细信息。
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: fab653eb24b504fa17e6f2eac32915fd88580817
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827827"
---
# <a name="user-interface-controls-in-xamarinios"></a>在 Xamarin.iOS 中的用户界面控件

本文档介绍了一些最常见的 iOS 用户界面控件以及如何使用它们。

## <a name="alertsalertsmd"></a>[警报](alerts.md)

从 iOS 8 开始，看到具有已完成替换的 UIActionSheet 和 UIAlertView 这两个其中现已弃用。

## <a name="buttonsbuttonsmd"></a>[Button](buttons.md)

UIButton 类用于表示在 iOS 屏幕的按钮的各种不同的样式。 本部分介绍用于在 iOS 中的按钮的不同选项。

## <a name="collection-viewsuicollectionviewmd"></a>[集合视图](uicollectionview.md)

中可用的集合视图`UICollectionView`类中，将介绍使用布局在屏幕上显示多个项中 iOS 6 的新概念。 向提供数据的模式`UICollectionView`创建项并与这些项遵循的相同委派和数据源通常用于 iOS 开发的模式进行交互。

## <a name="imagesimagemd"></a>[图像](image.md)

将映像添加到您的应用程序需要两个步骤： 首先，将图像添加到你的项目;然后，添加控件和代码以在屏幕上显示它们。 请参阅[处理图像](~/ios/app-fundamentals/images-icons/index.md)一文，其中更详细的图像在 Xamarin.iOS 中处理。

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[手动相机控件](intro-to-manual-camera-controls.md)

提供的手动相机控件`AVFoundation Framework`在 iOS 8 中，允许在移动应用程序需要完全控制 iOS 设备的照相机。 此级别细粒度的控制可用于创建专业级别的相机应用程序，并通过创建静止图像或视频时调整相机的参数提供艺术家组合。

## <a name="mapsios-mapsindexmd"></a>[地图](ios-maps/index.md)

映射是在所有新式移动操作系统中的常见功能。 iOS 提供映射支持本机通过 Map kit 包框架。 使用 Map 工具包应用程序可以轻松地添加丰富的交互式地图。 中有许多种情况下，例如添加注释标记在地图上的位置以及覆盖的任意形状的图形，可以自定义这些映射。 Map kit 包甚至具有对显示设备的当前的位置的内置支持。

## <a name="labelslabelsmd"></a>[标签](labels.md)

`UILabel`控件用于显示单个和多行，读取纯文本。

## <a name="pickers-and-date-pickerspickermd"></a>[选取器和日期选取器](picker.md)

选取器控件显示类似的滚轮控件，其中包含具有所选的值被突出显示的值可滚动列表。 用户旋转滚轮以选择所需的选项。

一个特定用户案例的选取器它来设置日期和/或时间。 若要为此 Apple 提供已创建自定义调用 UIDatePicker UIPickerView 类的子类。

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[进度和活动指示器](progress-activity-indicator.md)

iOS 提供了两种主要方法，以指示应用程序中的进度：活动指示器 (包括特定_网络_活动指示器) 和进度栏。

## <a name="search-barssearchbarmd"></a>[搜索栏](searchbar.md)

UISearchBar 用于搜索的值的列表。 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[滑块、开关和分段控件](slider-switch-segmented-controls.md)

滑块控件允许的范围内的数字值的简单选择。 iOS 使用`UISwitch`作为一个布尔值输入的可能由其他平台上的单选按钮。 分段控件是有序的方式，以允许用户交互较少的选项。

## <a name="stack-viewuistackviewmd"></a>[堆栈视图](uistackview.md)

堆栈视图控件 (`UIStackView`) 利用自动布局和大小类水平或垂直，管理的子视图，堆栈的强大，但该功能可动态响应的 iOS 设备的方向和屏幕大小。

## <a name="tables-and-cellstablesindexmd"></a>[表和单元格](tables/index.md)

他部分介绍了用来创建和显示表的类，然后提供了有关如何在 Xamarin.iOS 中使用它们的示例。 它将介绍如何为自定义布局，实现编辑和使用 Xamarin iOS 设计器直观地设计表的表使用的默认外观。 有时显示显然是一系列行 （如音乐应用程序） 和其他情况下很难识别表控件 （如编辑联系人应用或在邮件应用中的会话中）。

## <a name="text-inputtext-inputmd"></a>[文本输入](text-input.md)

接受用户的文本输入可以使用来完成`UITextField`单行输入和多行可编辑文本的 UITextView 的。 可以将任一这些控件拖动到屏幕上，然后双击以设置初始文本。

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[选项卡栏和选项卡栏控制器](creating-tabbed-applications.md)

使用 tab 键导航 UI 的 iOS 应用程序是使用 UITabBarController 类构建的。 在本文中我们将演练如何设置选项卡式应用程序包含多个控制器和视图。 然后，我们将介绍如何加载 UITabBarController 时它不是根控制器，如后登录屏幕。

## <a name="web-viewsuiwebviewmd"></a>[Web 视图](uiwebview.md)

在本文中，我们将探讨每个由 Apple 提供的三个 Web 视图： `UIWebView`， `WKWebview`，和`SFSafariViewController`、 其相似之处和差异，以及如何使用它们。

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/monotouch/Controls/)
