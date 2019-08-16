---
title: 检查实时应用程序
description: 本文档介绍了如何使用 Xamarin Inspector 来检查应用程序。 它还讨论了 Xamarin Inspector 工具的限制。
ms.prod: xamarin
ms.assetid: 91B3206E-B2A5-4660-A6E5-B924B8FE69A7
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: a22a0f06d6c02bf3600333eb00a26fbd1315440a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528971"
---
# <a name="inspecting-live-applications"></a>检查实时应用程序

Live app 检查适用于企业客户。

1. 在 Visual Studio for Mac 或 Visual Studio 中打开任何[受支持的应用项目](~/tools/inspector/install.md#supported-platforms)。
1. 在调试模式下运行你的应用。
1. 单击**检查**IDE 工具栏中的按钮 (在 Visual Studio 中，**检查当前的应用。 ...** 菜单项也会提供**工具**或**调试**菜单)。

[![](inspect-images/mac-heres-the-button.png "在 IDE 工具栏中, 单击 \"检查\" 按钮")](inspect-images/mac-heres-the-button.png#lightbox)

将打开一个新的 Xamarin Inspector 客户端窗口, 其中包含新的复制提示。

[![](inspect-images/inspector-0.7.0-map-inspect-small.png "将打开一个新的 Xamarin Inspector 客户端窗口, 其中包含新的复制提示")](inspect-images/inspector-0.7.0-map-inspect.png#lightbox)

此窗口出现后, 您将获得一个C#可用于执行和评估C#语句和表达式的交互式提示。 这种情况的独特之处在于, 在目标进程的上下文中计算代码。 在这种情况下, 我们将显示针对显示的 iOS 应用程序运行的代码。

对应用程序状态所做的任何更改实际上都发生在目标进程中, 因此可以使用C#来更改应用程序的实时运行, 也可以检查应用程序的状态。

例如, 在 iOS 上, 我们可能要查找 UIApplication 委托类, 这是我们的主驱动程序 (我们在其中存储了大量应用程序状态):

```csharp
var del = (MyApp.AppDelegate) UIApplication.SharedApplication.Delegate
del.Database.GetAllCustomers ()
...
del.Database.AddCustomer (...)
```

(请注意, 每次提交都会在多行编辑器中进行。 `Shift + Enter`将创建一个新行, 并且`Cmd + Enter` (`Ctrl + Enter`在 Windows 上) 将提交用于计算的代码。 `Enter`当安全时自动提交。)

若要获取应用程序的可视元素, 更方便的方法是使用 "检查" 按钮。 按下后, 可以通过单击应用程序来选择 UI 元素。 变量`selectedView`将被分配为指向屏幕上的实际元素。 在上面的屏幕截图中, 可以看到我们所选的`selectedView.BarTintColor` `UISearchBar`访问和编辑方式。

实时可视化树也非常有用。 它表示视图层次结构的当前快照。 您可以选择要在复制`selectedView`中设置的行, 并查看视图的属性值。 在 Mac 上, 可以与分层视图的3D 分离型可视化对象进行交互。 在 Windows 上, 您可以直观地编辑视图的属性值。

## <a name="known-limitations"></a>已知限制

- 仅在主显示器上支持 "视图选择"。
- 属性网格编辑不适用于 Mac, 在 Windows 上仅限于几种数据类型。 使用复制功能进行更强大的编辑。
- 只要在 IDE 中安装并启用了检查器外接程序, 我们就会在应用程序在调试模式下启动时将代码注入到您的应用程序中。 如果你在应用中发现任何异常行为, 请尝试禁用或卸载检查器外接程序/扩展, 然后重启 IDE 和重新检查。 并请提出[错误](~/tools/inspector/install.md#reporting-bugs), 告诉我们!
- 如果检查 UI 元素使其不会发生更改, 请[告知我们](~/tools/inspector/install.md#reporting-bugs), 因为这可能表示存在 bug。

