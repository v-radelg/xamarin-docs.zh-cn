---
title: 使用 tvOS 在 Xamarin 中的拆分视图控制器
description: 本文档介绍如何使用 tvOS 拆分使用 Xamarin 生成的应用程序中的视图。 它提供拆分视图控制器的高级概述如何使用它们使用演示图板，访问母版和详细信息视图中，并显示和隐藏母版视图。
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f1bd48378faa9ae6a4853083c93377268c38f01
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374415"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>使用 tvOS 在 Xamarin 中的拆分视图控制器

拆分视图控制器提供和管理 Master 和详细信息视图控制器的并排方案，同时在屏幕上。 拆分视图控制器是用于在母版视图 （在左侧的较小部分） 中呈现持久、 可获得焦点的内容以及相关的详细信息视图 （更大部分位于右侧） 中的详细信息。

[![](split-views-images/intro01.png "示例拆分视图")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>有关拆分视图控制器

如上面所述，拆分视图控制器管理的母版和详细信息视图控制器，会显示在左侧，在右侧较大的详细信息为较小视图 Master 端的端。 

此外，母版视图控制器可以被隐藏或显示所需的方式： 

[![](split-views-images/intro02.png "隐藏主视图控制器")](split-views-images/intro02.png#lightbox)

拆分视图控制器通常用于提供在母版视图的类别和详细信息视图中的筛选的结果的可筛选内容的列表。 这通常会显示为表视图，在左侧，和一个[集合视图](~/ios/tvos/user-interface/collection-views.md)在右侧。

在设计时用户界面要求拆分视图控制器，Apple 建议使用母版和详细信息视图控制器不会更改 （仅的内容更改，不是其结构）。 如果您确实需要换出视图控制器，则最好导航控制器用作需要更改 （Master 或详细信息） 的视图控制器的基础。

Apple 已使用拆分视图控制器的以下建议：

- **使用正确的拆分百分比**-默认情况下的拆分视图控制器使用母版视图控制器的屏幕的三分之一到三分之二的详细信息视图控制器。 （可选） 可以使用对半分割。 选择正确的百分比，以使你内容显示在屏幕上平衡。
- **保持主选择**-时内容上详细信息视图可以更改为响应在母版视图的用户的选择，应修复母版视图内容。 此外，在母版视图应该清楚地显示当前选定的项。
- **使用单一的统一标题**-通常情况下，将想要在详细信息视图，而不是详细信息和母版视图中的标题中使用单个的居中的标题。

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>拆分视图控制器和情节提要

若要使用 Xamarin.tvOS 应用中的拆分视图控制器的最简单方法是将它们添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**拆分视图控制器**从**工具箱**并将其放在视图上： 

    [![](split-views-images/activity01.png "拆分视图控制器")](split-views-images/activity01.png#lightbox)
1. 默认情况下，iOS 设计器将在母版视图安装导航控制器和视图控制器。 如果这不适合应用程序的要求，只需删除它们。
1. 如果你删除默认母版视图中，将新视图控制器拖到设计图面上： 

    [![](split-views-images/activity02.png "视图控制器")](split-views-images/activity02.png#lightbox)
1. 单击并将其从拆分视图控制器拖到新的主视图控制器。 
1. 选择**主**从**弹出菜单**: 

    [![](split-views-images/activity03.png "从弹出菜单中选择 Master")](split-views-images/activity03.png#lightbox)
1. 设计你的母版和详细信息视图的内容： 

    [![](split-views-images/activity04.png "布局示例")](split-views-images/activity04.png#lightbox)
1. 将分配**名称**中**小组件选项卡**的**Properties Pad**用于在 UI 控件C#代码。
1. 保存所做的更改并返回到 Visual Studio for mac。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**拆分视图控制器**从**工具箱**并将其放在视图上： 

    [![](split-views-images/activity01-vs.png "拆分视图控制器")](split-views-images/activity01-vs.png#lightbox)
1. 默认情况下，iOS 设计器将在母版视图中添加导航控制器和视图控制器。 如果这不适合应用程序的要求，只需删除它们。
1. 如果你删除默认母版视图中，将新视图控制器拖到设计图面上： 

    [![](split-views-images/activity02-vs.png "视图控制器")](split-views-images/activity02-vs.png#lightbox)
1. 单击并将其从拆分视图控制器拖到新的主视图控制器。 
1. 选择**主**从**弹出菜单**: 

    [![](split-views-images/activity03-vs.png "从弹出菜单中选择 Master")](split-views-images/activity03-vs.png#lightbox)
1. 设计你的母版和详细信息视图的内容： 

    [![](split-views-images/activity04.png "内容布局")](split-views-images/activity04.png#lightbox)
1. 将分配**名称**中**小组件选项卡**的**属性资源管理器**用于在 UI 控件C#的代码。
1. 保存更改。
    
-----

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>使用拆分视图控制器

如上面所述，拆分视图控制器是常使用的向用户显示经过筛选的内容。 主要类别显示在母版视图中，在左侧和右侧详细信息视图中筛选的结果基于用户的所选内容上。

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>访问母版和详细信息

如果你需要以编程方式访问的母版和详细信息视图控制器，使用`ViewControllers `拆分视图控制器的属性。 例如：

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

它显示为一个数组，其中第一个元素在母版视图控制器 (0) 和第二个元素 (1) 是详细信息。

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>从主服务器访问详细信息

由于通常基于在 Master 中的用户的选择详细信息视图中显示的详细的信息，因此您需要一种方法，若要从 Master 访问详细信息。

若要执行此操作的最简单方法是公开您的主视图控制器类上的属性，例如：

```csharp
public DetailViewController DetailController { get; set;}
```

在拆分视图控制器，重写`ViewDidLoad`方法和关联的两个视图在一起。 例如：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

可以在 Master 可以用来表示所需的新数据的详细信息视图控制器上公开属性和方法。

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>显示和隐藏主

（可选） 可以显示和隐藏主视图控制器使用`PreferredDisplayMode`拆分视图控制器的属性。 例如：

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

`UISplitViewControllerDisplayMode`枚举定义如何母版视图控制器上会显示为以下值之一：

- **自动**-tvOS 将控制表示法的母版和详细信息视图。
- **PrimaryHidden** -这将隐藏主视图控制器。
- **AllVisible** -显示母版和详细信息视图控制器的并排方案。 这是正常的默认的表示形式。
- **PrimaryOverlay** -详细信息视图控制器下，扩展，并介绍了由主机。

若要获取当前的演示文稿状态，请使用`DisplayMode`拆分视图控制器的属性。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和拆分视图控制器在 Xamarin.tvOS 应用内使用。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
