---
title: Windows Platform Features
description: This article explains the Windows platform support that's available in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 550ad9b068e71e53a98329663d2ee9b55be1864e
ms.sourcegitcommit: a3b7e016fb25584dbf57bae89b64a9f98031e7c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549974"
---
# <a name="windows-platform-features"></a>Windows Platform Features

Developing Xamarin.Forms applications for Windows platforms requires Visual Studio. The [supported platforms page](~/get-started/supported-platforms.md) contains more information about the pre-requisites.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Platform-specifics

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

The following platform-specific functionality is provided for Xamarin.Forms views, pages, and layouts on the Universal Windows Platform (UWP):

- 设置的访问密钥[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 For more information, see [VisualElement Access Keys on Windows](visualelement-access-keys.md).
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 For more information, see [VisualElement Legacy Color Mode on Windows](legacy-color-mode.md).

The following platform-specific functionality is provided for Xamarin.Forms views on UWP:

- 检测文本中的内容从读取顺序[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)实例。 For more information, see [InputView Reading Order on Windows](inputview-reading-order.md).
- 启用中的点击手势支持[ `ListView` ](xref:Xamarin.Forms.ListView)。 For more information, see [ListView SelectionMode on Windows](listview-selectionmode.md).
- Enabling the pull direction of a `RefreshView` to be changed. For more information, see [RefreshView Pull Direction on Windows](refreshview-pulldirection.md).
- 启用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)与拼写检查引擎进行交互。 For more information, see [SearchBar Spell Check on Windows](searchbar-spell-check.md).
- 启用[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 消息对话框中显示 JavaScript 警报。 For more information, see [WebView JavaScript Alerts on Windows](webview-javascript-alert.md).

The following platform-specific functionality is provided for Xamarin.Forms pages on UWP:

- 折叠[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)导航栏。 For more information, see [MasterDetailPage Navigation Bar on Windows](masterdetailpage-navigation-bar.md).
- 设置工具栏的放置选项。 For more information, see [Page Toolbar Placement on Windows](page-toolbar-placement.md).
- 启用页面图标上显示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具栏。 有关详细信息，请参阅 [Windows 上的 TabbedPage 图标](tabbedpage-icons.md)。

## <a name="platform-support"></a>平台支持

The Xamarin.Forms templates available in Visual Studio contain a Universal Windows Platform (UWP) project.

> [!NOTE]
> Xamarin.Forms 1.x and 2.x support _Windows Phone 8 Silverlight_, _Windows Phone 8.1_, and _Windows 8.1_ application development. However, these project types have been deprecated.

## <a name="getting-started"></a>入门

Go to **File > New > Project** in Visual Studio and choose one of the **Cross-Platform > Blank App (Xamarin.Forms)** templates to get started.

Older Xamarin.Forms solutions, or those created on macOS, will not have all the Windows projects listed above (but they need to be manually added). If the Windows platform you wish to target isn't already in your solution, visit the [setup instructions](installation/index.md) to add the desired Windows project type/s.

## <a name="samples"></a>示例

[All the samples](https://github.com/xamarin/xamarin-forms-book-preview-2) for Charles Petzold's book [*Creating Mobile Apps with Xamarin.Forms*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) include Universal Windows Platform (for Windows 10) projects.

The ["Scott Hanselman" demo app](https://github.com/jamesmontemagno/Hanselman.Forms) is available separately, and also includes Apple Watch and Android Wear projects (using Xamarin.iOS and Xamarin.Android respectively, Xamarin.Forms does not run on those platforms).

## <a name="related-links"></a>相关链接

- [Setup Windows Projects](~/xamarin-forms/platform/windows/installation/index.md)
