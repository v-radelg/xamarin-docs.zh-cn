---
title: 检查实时应用程序
description: 本文档介绍如何使用 Xamarin Inspector 检查应用程序。 它还介绍了 Xamarin 检查器工具的限制。
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2def0a01bdd28af5eefb76afc19a0e49fd1df355
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831555"
---
# <a name="inspecting-live-applications"></a>检查实时应用程序

适用于企业客户提供实时应用检测。

1. 打开任何[支持应用程序项目](~/tools/inspector/install.md#supported-platforms)Visual Studio for Mac 或 Visual Studio 中。
1. 在调试模式下运行你的应用。
1. 单击**检查**IDE 工具栏中的按钮 (在 Visual Studio 中，**检查当前的应用。 ...** 菜单项也会提供**工具**或**调试**菜单)。

[![](inspect-images/mac-heres-the-button.png "单击 IDE 工具栏中的检查按钮")](inspect-images/mac-heres-the-button.png#lightbox)

将打开一个新的 Xamarin Inspector 客户端窗口，使用全新的 REPL 提示符。

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "将打开一个新的 Xamarin Inspector 客户端窗口，使用全新的 REPL 提示符")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

此窗口出现后，您有某个交互式C#可用于执行和评估的提示C#语句和表达式。 这独特之处在于，目标进程的上下文中计算代码。 在这种情况下，我们将演示对显示的 iOS 应用程序运行的代码。

目标进程中，因此，可以使用的实际发生对应用程序的状态进行任何更改C#若要更改应用程序存在，或可以检查实时应用程序的状态。

例如，在 iOS 上，我们可能想要找到我们 UIApplication 委托类，该类是主驱动程序 （我们在其中存储的大量应用程序状态）：

    var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
    del.Database.GetAllCustomers ()
    ...
    del.Database.AddCustomer (...)

（请注意，每次提交发生在多行编辑器中。 `Shift + Enter` 将创建一个新行，并`Cmd + Enter`(`Ctrl + Enter`在 Windows 上) 将提交的代码进行评估。 `Enter` 自动提交时是安全的。）

若要访问你的应用程序的可视元素更方便方法是通过使用"检查"按钮。 后按下这之后，您可以通过单击你的应用程序选择 UI 元素。 该变量`selectedView`将分配来指向在屏幕上的实际元素。 在上面的屏幕截图，可以看到我们如何访问，然后编辑`selectedView.BarTintColor`上`UISearchBar`选择。

实时可视化树也是非常有用。 它表示视图层次结构的当前快照。 可以选择行，以设置`selectedView`REPL 中以及查看视图的属性值。 在 Mac 上，与分层视图的三维分离型可视化效果进行交互。 在 Windows 中，可以直观地编辑视图的属性值。

## <a name="known-limitations"></a>已知的限制

- 视图选择仅支持在主显示器上。
- 属性网格编辑不可用于 Mac 和 Windows 上仅限于几种数据类型。 用于 REPL 更功能强大的编辑。
- 只要检查器外接程序/扩展已安装并启用在 IDE 中，我们将代码注入到您的应用程序每次在调试模式下启动。 如果应用程序中发现任何异常行为，请尝试禁用或卸载检查器外接程序/扩展、 重新启动 IDE，并重新检查。 并请[错误进行归档](~/tools/inspector/install.md#reporting-bugs)，让我们了解 ！
- 如果检查 UI 元素后，即可更改中是否仍要请[告诉我们](~/tools/inspector/install.md#reporting-bugs)，因为这可能表示一个 bug。

