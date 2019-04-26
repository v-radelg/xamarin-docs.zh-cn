---
title: 常用模式和惯例在 Xamarin.Mac 中
description: 本文档介绍生成 Xamarin.Mac 应用时使用的常见设计模式。 它讨论了模型-视图-控制器模式、 数据源和委托模式和协议。
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 06/17/2016
ms.openlocfilehash: b4266582ce0cec522e207cd06987f2d0eeff8b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61090847"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>常用模式和惯例在 Xamarin.Mac 中

通过 C# 公开 Apple Api，在特定的惯用语言和模式出现反复地。 如果您没有使用与 Xamarin.iOS 编程经验，这些可能很熟悉。 文档将通常引用这些模式和惯例重复，因此具有清楚地了解它们将帮助你了解你找到的文档。

## <a name="mvc---model-view-controller"></a>MVC 的模型视图控制器

模型视图控制器或简称为 MVC 处处 Cocoa 的常用模式。 详细的讨论已超出本文档的范围，但它简单地说是一种方法构建应用程序组件：

- **模型**对象表示基础数据被查看和操作 （如在通讯簿中的地址）
- **视图**对象处理的给定对象在屏幕上绘制和处理用户交互 （文本字段显示在屏幕上的地址）
- **控制器**对象处理模型和视图之间的交互。 它们推送模型更改"启动"更新视图和视图从推送"down"的更改，当用户在 UI 中进行更改时。

如果您熟悉 MVVM （模型视图视图模型） 从其他库，如 WPF，控制器的作用类似于 ViewModel，但通常更紧密地绑定到特定的 UI 元素。

可在此处找到更多详细信息：

- [在 Apple 网站学习 MVC](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Objective C 中的模型视图控制器](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [使用 Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>数据源 / 委托 / 子类化

Cocoa 中另一种非常常见的模式用于处理提供到 UI 元素的数据和响应用户交互。 使用`NSTableView`作为示例，你需要以某种方式为每个行提供数据，某些设置的 UI 元素表示的行，一些组行为响应用户交互和可能是某些自定义项数量。 数据源和委托模式使您能够大多数情况下处理而不必求助于子类化`NSTableView`自己。

- `DataSource`属性分配的自定义子类的实例`NSTableViewDataSource`调用该方法可使用数据填充该表 (通过`GetRowCount`和`GetObjectValue`)。

- `Delegate`属性分配的自定义子类的实例`NSTableViewDelegate`其中提供了某一给定的模型对象的视图 (通过`GetViewForItem`)，并处理用户界面交互 (通过`DidClickTableColumn`， `MouseDownInHeaderOfTableColumn`，等等)。

在某些情况下，你将想要自定义委托或数据源中提供的挂钩超出一种方法中的控件和直接可以子类化视图。 但是请注意，在许多情况下重写默认行为需要您自己处理所有该行为 （自定义选择行为可能需要你自行实现所有所选内容行为）。

在 Xamarin.iOS 中，某些 Api，如`UITableView`已扩展实现委托和数据源的属性 (`UITableViewSource`)。 若要解决常见限制此 it 的一个C#类只能有一个基类，并且我们工具中呈现的协议是通过基类。

有关如何使用 Xamarin.Mac 应用程序中的表视图的详细信息，请参阅我们[表视图](~/mac/user-interface/table-view.md)文档。

## <a name="protocols"></a>协议

Objective C 中的协议可以相比接口在C#，并在许多情况下在类似情况下使用。 例如`NSTableView`上述示例中，委托和数据源是实际协议。 Xamarin.Mac 公开它们作为基类使用可以重写虚拟方法。 之间的主要区别C#的接口和 OBJECTIVE-C 的协议是一种协议中的某些方法可能是可选实现。 您需要查看的文档和/或以确定什么是可选的 API 定义。

详细信息，请参阅我们[委托、 协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)文档。



## <a name="related-links"></a>相关链接

- [表视图](~/mac/user-interface/table-view.md)
- [使用 windows](~/mac/user-interface/window.md)
- [委托、 协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
