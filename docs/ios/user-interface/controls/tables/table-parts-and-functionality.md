---
title: 表部件和功能在 Xamarin.iOS
description: 本文档介绍在 iOS UITableView 的各个组成部分。 它讨论了部分标头、 单元格、 部分页脚、 索引和编辑模式。
ms.prod: xamarin
ms.assetid: B4139C8B-28F2-4C0F-297F-BF5432C5A915
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c4d788cce12a9aabdd1170cd1a52915f3b30285f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200490"
---
# <a name="table-parts-and-functionality-in-xamarinios"></a>表部件和功能在 Xamarin.iOS

UITableView 可以使用分组或普通的方式，并包含以下部分：

-  [部分标头](#Section_Header)
-  [单元格](#Cells)（或行，如果您愿意）
-  [部分页脚](#Section_Footer)
-  [Tuple](#Index)
-  [编辑模式](#Edit_Features)(包括轻扫以删除并拖动图柄来更改行的顺序) 

这些屏幕截图显示了部分行、 标头、 页脚、 编辑控件和索引的显示方式。

 [![](table-parts-and-functionality-images/image1a.png "这些屏幕截图显示了部分行、 标头、 页脚、 编辑控件和索引的显示方式")](table-parts-and-functionality-images/image1a.png#lightbox)

下面更详细地说明这些部分：

<a name="Section_Header" />

## <a name="section-header"></a>部分标头

单元格可以根据需要进行划分到各、 带有自定义标头，和/或标记为具有表尾。 具有字符串值设置为标头或者可以提供自定义视图来允许对不同的布局和样式。

<a name="Cells" />

## <a name="cells"></a>单元

单元格是一个表的主用户界面元素。 正确实现，当单元格为重复使用的内存效率。 有四个内置的单元格样式，而且您可以创建您自己自定义单元格-在代码中，或在设计器中使用情节提要时。

<a name="Section_Footer"/>

## <a name="section-footer"></a>部分页脚

可以使用的字符串值，设置可选部分页脚或可以提供自定义视图来允许对不同的布局和样式。 可以单独设置部分页眉和页脚。

<a name="Index" />

## <a name="index"></a>索引

作为带下表的右边缘的字符将出现的索引。
触摸或拖动对索引用于加速滚动到表的该部分。 索引是可选的但建议来帮助导航较长的列表。 索引通常不用于分组样式。

<a name="Edit_Features" />

## <a name="editing-mode"></a>编辑模式

提供了几个不同的编辑功能：

- 轻扫，若要删除单个单元格。
- 进入编辑模式，以显示每个行的删除按钮 
- 正在进入编辑模式，以显示重新排序的句柄。 
- 插入新单元格 （与动画）。

此文档的其余部分演示如何实现使用 Xamarin.iOS 所有这些 UITableView 功能。


## <a name="classes-overview"></a>类概述

用于显示表视图的主要类如下所示：

[![](table-parts-and-functionality-images/classdiagram.png "用于显示表视图的主要类如下所示")](table-parts-and-functionality-images/classdiagram.png#lightbox)

每个类的用途如下所述：

- **UITableView** – 一个包含滚动容器中的单元格的集合视图。 表视图通常在 iPhone 应用中使用整个屏幕，但可能存在在 iPad 上可查看大图的一部分 （或出现在弹出框）。 
- **UITableViewCell** – 一个视图，它表示在表视图中的单个单元格 （或行）。 有四种内置的单元格类型和可以创建自定义中的单元格这两个C#或使用 iOS 设计器。 
- **UITableViewSource** – Xamarin.iOS 排他抽象类，该类提供要显示的表，包括行计数、 返回的每一行的单元格视图、 处理行选择和许多其他可选功能所需的所有方法。 您*必须*子类此类可获得 UITableView 工作。 
- **NSIndexPath** – 包含行和部分属性，用于唯一标识表中单元格的位置。 
- **UITableViewController** – 随时可用 UIViewController 具有 UITableView 硬编码为其视图和可通过 TableView 属性访问。 
- **UIViewController** -如果表未占据整个屏幕可以添加到其帧与任何 UIViewController UITableView 正确设置。 

UITableViewSource 替换以下两个类，这在 Xamarin.iOS 中仍然可用，但不是通常所需：

- **UITableViewDataSource** – 在为抽象类的 Xamarin.iOS 中建模的 Objective C 协议。 必须创建子类来为每个单元格，以及有关标头、 页脚和数量的行和表中的部分信息的视图提供的表。 
- **UITableViewDelegate** – 在为类的 Xamarin.iOS 中建模的 Objective C 协议。 处理所选内容，编辑功能和其他可选的表功能。 

本文档中的示例都使用 UITableViewSource 和忽略这两个类。 它们是在 Apple 的文档中找到任何 Objective C 示例有助于了解其功能 （和可以改为使用 Xamarin.iOS 的 UITableViewSource） 就会引用它们，因为此处提及的。

## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
