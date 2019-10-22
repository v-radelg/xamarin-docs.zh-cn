---
title: Xamarin 中的 watchOS 菜单控件（Force Touch）
description: 本文档介绍如何在 Xamarin 中使用 watchOS 强制触摸手势。 它讨论了如何响应强制触控、如何添加菜单以及如何更改菜单项。
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: c37d8592b7aadc2c88c31826bc954abfa3c0836d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "70766805"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Xamarin 中的 watchOS 菜单控件（Force Touch）

手表工具包提供了一个在 "监视" 应用屏幕上实现时触发菜单的 Force Touch 手势。

![](menu-images/menu.png "Apple Watch showing a menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>响应 Force Touch

如果已为接口控制器实现 `Menu`，则当用户执行时，将显示该菜单 Force Touch。 如果未实现菜单，屏幕会进行短暂的动画处理，不会发生其他操作。

强制润色与屏幕上的任何特定元素都不关联;只有一个菜单可以附加到接口控制器，它将显示，而不考虑在屏幕上出现 Force Touch 按下的位置。

可以在一个和四个菜单选项之间显示。

## <a name="adding-a-menu"></a>添加菜单

必须在设计时向情节提要上的 `InterfaceController` 中添加 `Menu`。 将菜单控件拖到接口控制器上时，情节提要预览中不会显示任何视觉指示，但会在 "**文档大纲**" 面板中显示该**菜单**：

![](menu-images/menu-action.png "Editing a menu at design time")

最多可以向 menu 控件添加四个菜单项。 它们可在**Properties** pad 中进行配置。 可以设置以下属性：

- Title 和
- 自定义映像，或
- 系统映像： "接受"、"添加"、"阻止"、"拒绝"、"信息"、"更多"、"静音"、"暂停"、"重复"、"继续"、"共享"、"

通过选择 "**属性**" 板的 "**事件**" 部分并键入操作方法的名称来创建 `Action`。 将在代码中创建分部方法，该方法可在接口控制器类中实现，如下所示：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自定义映像

类似于 iOS 中的选项卡图像，菜单项图像需要一个带有 alpha 通道的不透明模式，该模式允许透过显示背景。

为了获得最佳性能，应将用于菜单的图像添加到 "监视应用程序" 项目（而不是 "监视应用扩展项目"）。

## <a name="changing-the-menu-items"></a>更改菜单项

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在运行时添加

尽管*可以*通过编程方式更改 `MenuItem`s 的集合，但不能在运行时将 `Menu` 添加到接口控制器。
使用 `AddMenuItem` 方法，如下所示：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Xamarin Watch 工具包 API 当前要求 `AdMenuItem` 方法的 `selector`，应如下所示：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>在运行时删除

可以调用 `ClearAllMenuItems` 方法，以删除所有*以编程方式添加*的菜单项。

无法清除情节提要中配置的菜单项。

## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的菜单文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
