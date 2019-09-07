---
title: Xamarin 中的常见模式和惯例
description: 本文档介绍生成 Xamarin Mac 应用时使用的常见设计模式。 它讨论了模型-视图-控制器模式、数据源和委托模式以及协议。
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 06/17/2016
ms.openlocfilehash: b4934fa82d862ad2e8ab53579137873ed9e4bcca
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770167"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Xamarin 中的常见模式和惯例

在通过C#公开的所有 Apple api 中，某些惯例和模式会重新出现。 如果你有经验来使用 Xamarin 进行编程，这些都可能很熟悉。 文档通常会重复引用这些模式和惯例，因此，对其进行深刻了解将有助于你了解所找到的文档。

## <a name="mvc---model-view-controller"></a>MVC-模型视图控制器

模型视图控制器（简称 MVC）是 Cocoa 中的一种非常常见的模式。 此文档的范围之外的详细讨论不在本文档的讨论范围内，但简言之，它是将应用程序构建为组件的一种方法：

- **模型**对象表示正在查看和操作的基础数据（例如通讯簿中的地址）
- **视图**对象用于处理屏幕上给定对象的绘制，并处理用户交互（显示屏幕上的地址的文本字段）
- **控制器**对象处理模型和视图之间的交互。 当用户在 UI 中进行更改时，它们会将模型更改推送到 "向上" 以更新视图并推送 "关闭" 更改。

如果你熟悉来自其他库（如 WPF）的 MVVM （模型视图 ViewModel），则控制器的行为类似于 ViewModel，但通常更紧密绑定到特定的 UI 元素。

可在以下位置找到更多详细信息：

- [在 Apple 网站上学习 MVC](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [目标中的模型视图控制器-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [使用 Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>数据源/委托/子类

Cocoa 中的另一种非常常见的模式是向 UI 元素提供数据并对用户交互做出反应。 `NSTableView`作为示例，您需要以某种方式提供每个行的数据，一组表示该行的 UI 元素，一组用于对用户交互做出反应的行为，还可能有一定数量的自定义。 数据源和委托模式使您能够处理大多数情况，而无需在自己`NSTableView`的子类中进行分类。

- 为属性分配自定义子类的`NSTableViewDataSource`实例，该实例被调用以使用数据填充表（通过`GetRowCount`和`GetObjectValue`）。 `DataSource`

- `MouseDownInHeaderOfTableColumn` `DidClickTableColumn` `GetViewForItem` `NSTableViewDelegate`为属性分配了自定义子类的实例，该实例提供给定模型对象的视图（通过、等）。 `Delegate`

在某些情况下，你将需要以委托或数据源中给定的挂钩之外的方式自定义控件，并且可以直接为视图划分子类。 但请注意，在许多情况下，重写默认行为会要求你自行处理所有该行为（自定义选择行为可能要求你自行实现所有选择行为）。

在 Xamarin 中，某些 api （例如`UITableView` ）已通过实现委托和数据源的属性进行了扩展（`UITableViewSource`）。 这是为了解决一个C#类只能有一个基类的常见限制，而我们通过基类来完成了我们的协议的呈现。

有关在 Xamarin Mac 应用程序中使用表视图的详细信息，请参阅我们的[表视图](~/mac/user-interface/table-view.md)文档。

## <a name="protocols"></a>协议

目标中的协议可与中C#的接口进行比较，在许多情况下，在类似情况下使用。 例如，在`NSTableView`上述示例中，委托和数据源都是实际的协议。 Xamarin 将这些基类公开为可替代的虚方法。 接口和目标- C# C 协议之间的主要区别在于，协议中的某些方法可能是可选的。 需要查看 API 的文档和/或定义，以确定什么是可选的。

有关详细信息，请参阅我们的[委托、协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)文档。

## <a name="related-links"></a>相关链接

- [表视图](~/mac/user-interface/table-view.md)
- [使用 windows](~/mac/user-interface/window.md)
- [委托、协议和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
