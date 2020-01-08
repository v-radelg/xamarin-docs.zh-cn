---
title: 控件引用
description: 用于构造 Xamarin 应用程序的所有用户界面元素的说明。 本文列出了构成了 Xamarin.Forms 应用程序的用户界面的控件组。
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: b9436603c17eb8008f470e75a52a93e8e122034f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488097"
---
# <a name="controls-reference"></a>控件引用

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

Xamarin 窗体应用程序的用户界面是构造的对象，这些对象映射到每个目标平台的本机控件。 这使得适用于 iOS、Android 和通用 Windows 平台的特定于平台的应用程序可以使用[.NET Standard 库](~/cross-platform/app-fundamentals/net-standard.md)中包含的 Xamarin 代码。

用于创建 Xamarin 的用户界面的四个主要控件组如下所示：

- [**页**](pages.md)
- [**布局**](layouts.md)
- [**视图**](views.md)
- [**单元格**](cells.md)

Xamarin.Forms 页面通常占据整个屏幕。 页通常包含一种布局，其中包含视图和可能是其他布局。 单元格是使用门户中的专用的组件[ `TableView` ](views.md#tableview)并[ `ListView` ](views.md#listview)。 一个类图，显示通常用于在 Xamarin 中生成用户界面的类型的层次结构。可以在[xamarin. Forms 控件类层次结构](~/xamarin-forms/internals/class-hierarchy.md)中找到窗体。

在上的四个文章[**页面**](pages.md)， [**布局**](layouts.md)， [**视图**](views.md)，并[**单元格**](cells.md)，（如果存在），其中包含指向其 API 文档、 文章描述了其使用 （如果存在） 和一个或多个示例程序介绍了每种类型的控件。 每种类型的控件还附带一个屏幕截图，其中显示了在 iOS 和 Android 设备上运行的[**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)示例页面。 下面的每个屏幕快照是 C# 页上，等效 XAML 页面的源代码的链接和 （在适当的时候） XAML 页的 C# 代码隐藏文件。

> [!NOTE]
> 页面、布局和视图派生自 `VisualElement` 类。 `VisualElement` 类提供各种可用于派生类的属性、方法和事件。 有关详细信息，请参阅[VisualElement 属性、方法和事件](common-properties.md)。

除了使用 Xamarin 提供的控件以外，还提供了第三方控件。 有关详细信息，请参阅[第三方控件](thirdparty.md)。

## <a name="related-links"></a>相关链接

- [Xamarin.Forms FormsGallery 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin. Forms 控件类层次结构](~/xamarin-forms/internals/class-hierarchy.md)
- [API 文档](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
