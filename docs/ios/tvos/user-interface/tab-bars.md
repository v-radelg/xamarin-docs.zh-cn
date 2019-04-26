---
title: 使用 tvOS 在 Xamarin 中的选项卡栏控制器
description: 本文档介绍如何在 tvOS 应用程序中使用 Xamarin 生成的选项卡栏控制器使用。 它提供高级别视图选项卡栏上，并讨论了选项卡栏项、 情节提要集成和选项卡栏项。
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: a0efc30fd9814e4da858c4e3e4e99990eccf102e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61164201"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>使用 tvOS 在 Xamarin 中的选项卡栏控制器

对于许多类型的 tvOS 应用程序，主要导航显示为选项卡栏，屏幕的顶部运行。 在用户轻扫的可能类别和所做的更改下面的内容区域，以反映用户的选择列表中的左侧和右侧。

[![](tab-bars-images/tab01.png "示例选项卡栏")](tab-bars-images/tab01.png#lightbox)

选项卡栏是半透明默认情况下，并始终显示在屏幕的顶部。 具有焦点，选项卡栏将介绍在屏幕的顶部 140 像素，但焦点切换到下面的内容区域时将快速滑消失。

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>选项卡条中 tvOS

`UITabViewController`在类似的方式和工作方式与其在 iOS 中，具有以下主要差异 tvOS 上有相似的目标：

- 与屏幕的底部将显示的 iOS 上的选项卡栏，不同选项卡条中 tvOS 占用屏幕的顶部 140 像素，而且其半透明默认情况下。
- 当焦点离开下面的内容区域在选项卡栏时，选项卡栏将快速滑出屏幕的顶部，并被隐藏。 在用户可以一次点击的菜单按钮或上向上轻扫[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)若要再次显示选项卡栏。
- Siri 远程上向下轻扫，会将焦点移到与第一个选项卡栏下方的内容区域[可获得焦点项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)中显示的内容。 同样，这将隐藏选项卡栏，一旦焦点转移。
- 单击以选择的选项卡栏中显示一个类别将切换到的类别的内容和焦点将切换到该视图中的第一个可获得焦点项。
- 应修复的类别选项卡栏中显示的数量和应随时都可访问所有类别，应永远不会禁用给定的类别。
- 选项卡栏在 tvOS 上不支持自定义项。 此外，它们不显示**详细**类别 （如 iOS) 如果有多个类别比可容纳在选项卡栏中。

Apple 具有以下建议以获得使用选项卡栏：

- **使用选项卡栏，以逻辑方式组织内容**-使用选项卡栏以逻辑方式组织你的 tvOS 应用适用于的内容。 例如，特色、 顶部图表、 购买和搜索。
- **将徽章添加到新内容的通知用户**-您可以选择显示锁屏提醒 (以白色的数字或感叹号的红色 oval) 以通知用户某个类别中的新内容。
- **徽章尽可能少地使用**-不混乱徽章与选项卡栏，并仅显示他们向用户提供关键信息。
- **限制类别数**-若要将降低复杂性和保持可管理应用程序、 不重载具有类别在选项卡栏，并确保所有类别都可见并不很拥挤。 简短的标题效果最佳。
- **不禁用类别**-所有选项卡 （类别） 应始终为可见且已启用在所有时间。 如果给定的选项卡不包含任何内容，原因为用户提供说明。 例如，购买选项卡将为空，如果用户已进行了无购买量。

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>选项卡栏项

在选项卡栏中的每个类别 （选项卡） 由选项卡栏项 (`UITabBarItem`)。 Apple 已对使用的选项卡栏项的以下建议：

- **使用基于文本的选项卡**-Apple 时的选项卡栏项是能够以图标表示，建议使用文本，只是因为简明的标题更容易解释比图标。
- **使用短、 有意义的名词或动词**-选项卡栏项应清楚地中继的内容，它包含和 （如照片、 电影或音乐） 简单名词或动词 （如搜索或播放） 时效果最佳。

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>选项卡栏和情节提要

若要使用 Xamarin.tvOS 应用中的选项卡栏的最简单方法是将它们添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 启动新的 Xamarin.tvOS 应用程序，然后选择**tvOS** > **应用** > **选项卡式应用**: 

    [![](tab-bars-images/tab02.png "选择选项卡式的应用")](tab-bars-images/tab02.png#lightbox)
1. 按所有提示创建新的 Xamarin.tvOS 解决方案。
1. 在中**Solution Pad**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 若要更改**图标**或**标题**对于给定的类别，选择**选项卡栏项**有关**视图控制器**中**文档大纲**:

    [![](tab-bars-images/tab03a.png "选项卡栏项视图控制器在文档大纲")](tab-bars-images/tab03a.png#lightbox)
1. 然后设置所需的属性**小组件选项卡**的**属性资源管理器**: 

    [![](tab-bars-images/tab03.png "小组件选项卡")](tab-bars-images/tab03.png#lightbox)
1. 若要添加新类别 （选项卡），请删除**视图控制器**拖到设计图面： 

    [![](tab-bars-images/tab04.png "视图控制器")](tab-bars-images/tab04.png#lightbox)
1. 单击并从拖动**选项卡视图控制器**对新**视图控制器**。
1. 从弹出窗口中，选择**查看控制器**作为 （类别） 的选项卡中添加新视图： 

    [![](tab-bars-images/tab05.png "选择选项卡")](tab-bars-images/tab05.png#lightbox)
1. 通过将 UI 元素添加在 iOS 设计器中设计正常工作，每个 Caterogies 内容区域的 UI 的布局。
1. 公开用于 UI 控件中任何所需的事件C#代码。
1. 命名你想要公开中的任何 UI 控件C#代码。
1. 保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 启动新的 Xamarin.tvOS 应用程序，然后选择**tvOS** > **应用** > **选项卡式应用**: 

    [![](tab-bars-images/tab02vs.png "选择选项卡式的应用")](tab-bars-images/tab02vs.png#lightbox)
1. 按所有提示创建新的 Xamarin.tvOS 解决方案。
1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 若要更改**图标**或**标题**对于给定的类别，选择**选项卡栏项**有关**视图控制器**中**文档大纲**:

    [![](tab-bars-images/tab03avs.png "文档大纲中的视图控制器")](tab-bars-images/tab03avs.png#lightbox)
1. 然后设置所需的属性**小组件选项卡**的**属性资源管理器**: 

    [![](tab-bars-images/tab03vs.png "小组件选项卡")](tab-bars-images/tab03vs.png#lightbox)
1. 若要添加新类别 （选项卡），请拖动**视图控制器**从**工具箱**并将其放到设计图面上： 

    [![](tab-bars-images/tab04vs.png "视图控制器")](tab-bars-images/tab04vs.png#lightbox)
1. 单击并从拖动**选项卡视图控制器**对新**视图控制器**。
1. 从弹出窗口中，选择**查看控制器**作为 （类别） 的选项卡中添加新视图： 

    [![](tab-bars-images/tab05vs.png "选择选项卡")](tab-bars-images/tab05vs.png#lightbox)
1. 通过将 UI 元素添加在 iOS 设计器中设计正常工作，每个 Caterogies 内容区域的 UI 的布局。
1. 公开用于 UI 控件中任何所需的事件C#代码。
1. 命名你想要公开中的任何 UI 控件C#代码。
1. 保存更改。
    
-----

> [!IMPORTANT]
> 虽然可以分配事件，如`TouchUpInside`到用户界面元素 (如`UIButton`) 在 iOS 设计器中，它将永远不会调用，因为 Apple TV 没有触摸屏输入屏幕上或支持触控事件。 应始终使用`Primary Action `事件时创建用户界面元素的适用于 tvOS 的事件处理程序。

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>使用选项卡栏

使用`Items`的属性`UITabBar`若要访问的集合`UITabBarItems`它包含零 (0) 索引数组的形式。 `SelectedItem`属性将返回当前所选的选项卡 （类别） 作为`UITabBarItem`。


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>使用选项卡栏项

若要在给定的选项卡上 (oval 红色和白色文本) 显示一个徽章，请使用以下代码：

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

这会生成以下结果运行时：

[![](tab-bars-images/tab06.png "具有徽章的选项卡栏项")](tab-bars-images/tab06.png#lightbox)

使用`Title`的属性`UITabBarItem`来更改标题和`Image`属性来更改的图标。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和在 Xamarin.tvOS 应用内使用的选项卡栏控制器。




## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
