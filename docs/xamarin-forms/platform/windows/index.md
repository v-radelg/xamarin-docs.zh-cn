---
title: Windows 平台功能
description: 本文介绍了 Xamarin.Forms 中可用的 Windows 平台支持。
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
---

# <a name="windows-platform-features"></a>Windows 平台功能

开发 Windows 平台的 Xamarin.Forms 应用程序需要 Visual Studio。 [要求页](~/get-started/requirements.md)包含有关系统必备组件的详细信息。

![](images/allhanselman.png "在 Windows 上运行的 Xamarin.Forms 应用程序")

## <a name="platform-specifics"></a>平台特定信息

平台特定信息，可使用的功能仅适用于特定的平台，而无需实现自定义呈现器或效果。

Xamarin.Forms 视图、 页面和布局通用 Windows 平台 (UWP) 上提供以下特定于平台的功能：

- 设置的访问密钥[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[VisualElement 在 Windows 上的访问密钥](visualelement-access-keys.md)。
- 禁用上受支持的旧颜色模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 有关详细信息，请参阅[VisualElement 旧版 Windows 上的颜色模式](legacy-color-mode.md)。

在 UWP 的 Xamarin.Forms 视图提供以下特定于平台的功能：

- 检测文本中的内容从读取顺序[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)实例。 有关详细信息，请参阅[InputView 在 Windows 上的读取顺序](inputview-reading-order.md)。
- 启用中的点击手势支持[ `ListView` ](xref:Xamarin.Forms.ListView)。 有关详细信息，请参阅[在 Windows 上的 ListView SelectionMode](listview-selectionmode.md)。
- 启用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)与拼写检查引擎进行交互。 有关详细信息，请参阅[SearchBar 拼写检查 Windows 上](searchbar-spell-check.md)。
- 启用[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 消息对话框中显示 JavaScript 警报。 有关详细信息，请参阅[在 Windows 上的 web 视图 JavaScript 警报](webview-javascript-alert.md)。

上 UWP 的 Xamarin.Forms 页面提供了以下特定于平台的功能：

- 折叠[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)导航栏。 有关详细信息，请参阅[MasterDetailPage 导航栏上 Windows](masterdetailpage-navigation-bar.md)。
- 设置工具栏的放置选项。 有关详细信息，请参阅[页面上 Windows 工具栏放置](page-toolbar-placement.md)。
- 启用页面图标上显示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具栏。 有关详细信息，请参阅[TabbedPage 图标在 Windows 上](tabbedpage-icons.md)。

## <a name="platform-support"></a>平台支持

可在 Visual Studio 中的 Xamarin.Forms 模板包含一个通用 Windows 平台 (UWP) 项目。

> [!NOTE]
> Xamarin.Forms 1.x 和 2.x 支持_Windows Phone 8 Silverlight_， _Windows Phone 8.1_，并_Windows 8.1_应用程序开发。 但是，这些项目类型已被弃用。

## <a name="getting-started"></a>入门

转到**文件 > 新建 > 项目**Visual Studio 中选择一个**跨平台 > 空白应用 (Xamarin.Forms)** 模板开始。

较旧的 Xamarin.Forms 解决方案，或在 macOS 上，创建不具有上面列出的所有 Windows 项目 （但它们需要手动添加）。 如果你想要面向 Windows 平台的还不是在解决方案中，访问时重新[安装说明进行操作](installation/index.md)添加所需的 Windows 项目类型/秒。

## <a name="samples"></a>示例

[所有样本](https://github.com/xamarin/xamarin-forms-book-preview-2)Charles Petzold 的书籍[*使用 Xamarin.Forms 创建移动应用*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包括 （适用于 Windows 10) 的通用 Windows 平台项目。

["Scott Hanselman"演示应用](https://github.com/jamesmontemagno/Hanselman.Forms)可分开，并且还包括 Apple Watch 和 Android Wear 项目 （分别使用 Xamarin.iOS 和 Xamarin.Android，Xamarin.Forms 不会运行这些平台上）。

## <a name="related-links"></a>相关链接

- [安装 Windows 项目](~/xamarin-forms/platform/windows/installation/index.md)
