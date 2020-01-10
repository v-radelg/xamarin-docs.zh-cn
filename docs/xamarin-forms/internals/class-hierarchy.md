---
title: Xamarin. Forms 控件类层次结构
description: 开发人员应熟悉用于创建 Xamarin 应用程序用户界面的类型的层次结构。
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
ms.openlocfilehash: 7c55ed3082a031352a8eefb8ef579060a9dd6404
ms.sourcegitcommit: 4899cf4aaa73aa56c48f7ee2339c0af8112e1feb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717285"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin. Forms 控件类层次结构

Xamarin 是通过多个命名空间由数百个类型组成的。 开发人员应最熟悉用于创建 Xamarin 窗体应用程序（位于 `Xamarin.Forms` 命名空间中）的用户界面的类型的层次结构。

这些类型可以划分为页、布局、视图和单元格。 Xamarin 页面通常占用整个屏幕，所有页面类型都派生自[`Page`](xref:Xamarin.Forms.Page)类。 页面通常包含布局，所有布局类型都派生自[`Layout`](xref:Xamarin.Forms.Layout)类。 布局通常包含视图和其他布局，并且所有视图类型最终派生自[`View`](xref:Xamarin.Forms.View)类。 最后，单元是在[`TableView`](xref:Xamarin.Forms.TableView)和[`ListView`](xref:Xamarin.Forms.ListView)控件的显示数据中使用的专用控件。 页面、布局、视图和单元均最终派生自[`Element`](xref:Xamarin.Forms.Element)类。

下面的类图显示了通常用于在 Xamarin 中生成用户界面的类型的层次结构：

[![Xamarin. 窗体控件类图](class-hierarchy-images/class-diagram.png "Xamarin. 窗体控件类图")](class-hierarchy-images/class-diagram-large.png#lightbox "Xamarin. 窗体控件类图")

> [!NOTE]
> 可从[此处](class-hierarchy-images/class-diagram-high-resolution.png)下载类图的高分辨率版本。 但请注意，该关系图只显示一种 Shell 类型。

## <a name="related-links"></a>相关链接

- [Xamarin. Forms 控件引用](~/xamarin-forms/user-interface/controls/index.md)
