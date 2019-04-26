---
title: watchOS 在 Xamarin 中的菜单控件 (Force Touch)
description: 本文档介绍如何在 Xamarin 中使用 watchOS 强制触摸手势。 它讨论了如何应对强制触摸屏输入，如何添加一个菜单，并更改菜单项。
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7696c820ab6fdf19bdef46db31061fb5914e6cf4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880665"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>watchOS 在 Xamarin 中的菜单控件 (Force Touch)

观看工具包提供了触发一个菜单，当在监视应用程序屏幕上执行强制触摸手势。

![](menu-images/menu.png "在显示菜单的 Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Force Touch 响应

如果`Menu`用户在执行强制接触将显示菜单时已实现的界面控制器。 如果尚未实施无菜单，屏幕简要动画处理任何其他操作。

强制收尾工作了不与任何在屏幕上的特定元素相关联只有一个菜单可以附加到界面控制器，并且它将显示而不考虑 Force Touch 按在屏幕上出现的位置。

1 到 4 个菜单之间可以显示选项。


## <a name="adding-a-menu"></a>将菜单添加

一个`Menu`必须添加到`InterfaceController`在设计时情节提要上。 菜单控件拖动到界面控制器上时无可视指示在情节提要预览，但**菜单**将出现在**文档大纲**板：

![](menu-images/menu-action.png "在设计时编辑菜单")

最多四个菜单项可以添加到菜单控件。 它们可以在中配置**属性**板。 可以设置以下属性：

- 标题、 和
- 自定义映像，或
- 系统映像：接受、 添加块、 拒绝、 信息，也许，更，设为静音、 暂停、 播放和重复，恢复、 共享、 无序播放、 演讲者、 回收站。

创建`Action`通过选择**事件**一部分**属性**板和键入操作方法的名称。 将在代码中，它可以实现在界面控制器类中，此类创建的分部方法：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自定义映像

类似于在 iOS 中的选项卡图像，需要具有 alpha 通道，背景以显示通过的不透明模式菜单项图像。

您应该添加用于为获得最佳性能的监视应用程序项目 （不是监视应用程序扩展项目） 菜单的图像。


## <a name="changing-the-menu-items"></a>更改菜单项

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在运行时添加

不会导致`Menu`若要添加到界面控制器在运行时，尽管的集合`MenuItem`s*可以*以编程方式更改。
使用`AddMenuItem`方法所示：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Xamarin.iOS 监视工具包 API 当前需要`selector`为`AdMenuItem`方法，应声明如下：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>删除在运行时

`ClearAllMenuItems`可以调用方法来删除所有*以编程方式添加*菜单项。

不能清除情节提要中配置的菜单项。



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 菜单文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
