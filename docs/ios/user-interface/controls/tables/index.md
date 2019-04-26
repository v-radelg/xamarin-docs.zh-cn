---
title: 使用表和 Xamarin.iOS 中的单元格
description: 本文档所链接到各种指南描述如何在 Xamarin.iOS 应用程序中显示与 UITableView 控件的数据。
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/06/2016
ms.openlocfilehash: 275c7553e465da67ca0780ea6aa9e986ca33b1f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379120"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>使用表和 Xamarin.iOS 中的单元格

本部分介绍了用来创建和显示表的类，然后提供了有关如何在 Xamarin.iOS 中使用它们的示例。 它将介绍如何为自定义布局，实现编辑和使用 Xamarin iOS 设计器直观地设计表的表使用的默认外观。 有时显示显然是一系列行 （如音乐应用程序） 和其他情况下很难识别表控件 （如编辑联系人应用或在邮件应用中的会话中）。

对于那些致力于跨平台应用程序使用 Xamarin.Android，UITableView 控件是类似于在 Android 中的 ListView 类 （和 UITableViewSource 类是类似于 Android 的适配器类）。

这些文章将介绍全面介绍了使用表，其中包括：

-   **表部件**– 介绍和解释的可见元素`UITableView`控件。 
-   **在表中显示数据**– 演示了如何创建和填充表，使用不同的表和单元格样式和通过回收 cell 对象来避免内存问题。 
-   **高级用法**– 生成自定义单元格并使用 UITableView 类的编辑功能。 
-   **直观地创建表**– 适用于 iOS 使用 Xamarin 设计器和情节提要中创建的表驱动的界面。 

## <a name="contents"></a>内容

 [表部件&amp;功能](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [使用数据填充表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [自定义表的外观](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [编辑](~/ios/user-interface/controls/tables/editing.md)
 
 [行操作](~/ios/user-interface/controls/tables/row-action.md)

 [在情节提要中创建表](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [自动调整行高](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [情节提要 （示例） 中的表](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [情节提要 TableView 方案](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [MonoTouch.Dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [Github 上的 TableEditing 示例](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Github 上的 TableParts 示例](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Github 上的 TableAndCellStyles 示例](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [UITableView 类引用](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [UITableViewCell 类引用](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
