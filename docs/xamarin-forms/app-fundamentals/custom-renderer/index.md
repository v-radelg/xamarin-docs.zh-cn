---
title: Xamarin.Forms 自定义呈现器
description: 通过自定义呈现器，开发人员可以自定义 Xamarin.Forms 控件的外观和行为，并以此替代各平台上本机控件的呈现。
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 3a59cbb8385fe201d7eef086092c9b393a21cc71
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666334"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.Forms 自定义呈现器

Xamarin.Forms 使用目标平台的本机控件呈现用户界面，从而让 Xamarin.Forms 应用程序为每个平台保留了相应的界面外观。自定义呈现器允许开发人员重写此过程，自定义每个平台上 Xamarin.Forms 控件的外观和行为。

## <a name="introduction-to-custom-renderersintroductionmd"></a>[自定义呈现器简介](introduction.md)

自定义呈现器为自定义 Xamarin.Forms 控件的外观和行为提供了一种功能强大的方法。 可使用它们进行细微的样式更改，也可进行复杂的特定于平台的布局和行为自定义。 本文介绍了自定义呈现器，并概述了创建自定义呈现器的过程。

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[呈现器基类和本机控件](renderers.md)

每个 Xamarin.Forms 控件都有一个附带的呈现器，适用于创建本机控件实例的各个平台。 本文列出了用于实现每个 Xamarin.Forms 页面、布局、视图和单元的呈现器和本机控件类。

## <a name="customizing-an-entryentrymd"></a>[自定义项](entry.md)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) 控件允许对单行文本进行编辑。 本文演示了如何为 `Entry` 控件创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。

## <a name="customizing-a-contentpagecontentpagemd"></a>[自定义 ContentPage](contentpage.md)

[`ContentPage`](xref:Xamarin.Forms.ContentPage) 是一个可视元素，它显示单个视图并占据大部分屏幕区域。 本文演示了如何为 `ContentPage` 页面创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。

## <a name="customizing-a-mapmapindexmd"></a>[自定义地图](map/index.md)

Xamarin.Forms.Maps 提供跨平台抽象，用于显示在每个平台上使用本机地图 API 的地图，为用户提供快速且熟悉的地图体验。 本主题演示了如何为 `Map` 控件创建自定义呈现器，使开发人员能够使用自己特定于平台的自定义呈现替代默认本机呈现。

## <a name="customizing-a-listviewlistviewmd"></a>[自定义 ListView](listview.md)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 视图以垂直列表的形式显示数据集合。 本文演示如何创建自定义呈现器来封装特定于平台的列表控件和本机单元布局，从而进一步控制本机列表控件的性能。

## <a name="customizing-a-viewcellviewcellmd"></a>[自定义 ViewCell](viewcell.md)

Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) 单元包含开发人员定义的视图，可将该单元添加到 [`ListView`](xref:Xamarin.Forms.ListView) 或者 [`TableView`](xref:Xamarin.Forms.TableView) 中。 本文演示如何为 Xamarin.Forms `ListView` 控件中托管的 `ViewCell` 创建自定义呈现器。 这可防止在 `ListView` 滚动期间重复调用 Xamarin.Forms 布局计算。

## <a name="implementing-a-viewviewmd"></a>[实现视图](view.md)

Xamarin.Forms 自定义用户界面控件应派生自 [`View`](xref:Xamarin.Forms.View) 类，该类用于在屏幕上放置布局和控件。 本文演示如何为 Xamarin.Forms 自定义控件创建自定义呈现器，用于显示设备摄像头的预览视频流。

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[实现 HybridWebView](hybridwebview.md)

本文演示如何为 `HybridWebView` 自定义控件创建自定义呈现器，演示如何增强特定于平台的 Web 控件以允许从 JavaScript 调用 C# 代码。

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[实现视频播放器](video-player/index.md)

本文介绍如何编写呈现器以实现自定义 `VideoPlayer` 控件，该控件可播放 Web 视频、作为应用程序资源嵌入的视频或用户设备的视频库中存储的视频。 本文演示了多种技巧，包括实现方法和只读可绑定属性。


## <a name="related-links"></a>相关链接

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自定义呈现器（Xamarin University 视频）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
