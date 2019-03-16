---
title: iOS 设计器基础知识
description: 本指南介绍用于 iOS 的 Xamarin 设计器。 它演示了如何使用 iOS 设计器以可视方式布置控件、 如何访问这些控件在代码中，以及如何编辑属性。
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/31/2018
ms.openlocfilehash: 2c6409410ecba7df8559c07c3231f5e493c98897
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071067"
---
# <a name="ios-designer-basics"></a>iOS 设计器基础知识

_本指南介绍用于 iOS 的 Xamarin 设计器。它演示了如何使用 iOS 设计器以可视方式布置控件、 如何访问这些控件在代码中，以及如何编辑属性。_

适用于 iOS 的 Xamarin 设计器是类似于 Xcode 的 Interface Builder 的直观界面的设计器和 Android 设计器。 一些其许多功能包括与 Visual Studio 的 Windows 和 Mac、 拖放编辑、 设置事件处理程序的接口和呈现自定义控件的功能无缝集成。

## <a name="requirements"></a>要求

IOS 设计器是 Visual Studio for Mac 和 Visual Studio 2017 和更高版本在 Windows 中可用。 在 Visual Studio 的 Windows，iOS 设计器必须正确配置的 Mac 生成主机，与的连接，但需要未在运行 Xcode。

本指南假定你熟悉中介绍的内容[入门指导](~/ios/get-started/index.md)。

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>IOS 设计器的工作原理

本部分介绍 iOS 设计器如何便于创建用户界面和将其连接到代码。

IOS 设计器允许开发人员可以直观地设计应用程序的用户界面。 中所述[情节提要简介](~/ios/user-interface/storyboards/index.md)指南中，情节提要介绍屏幕 （视图控制器） 构成一个应用程序、 这些视图控制器和应用程序的整体导航流上放置的界面元素 （视图）. 

视图控制器有两个部分： iOS 设计器中的可视表示形式以及一个关联的 C# 类：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![IOS 设计器中的视图控制器](introduction-images/1-storyboardwithviewcontroller-vsmac.png "iOS 设计器中的视图控制器")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![视图控制器的代码](introduction-images/2-viewcontrollercode-vsmac.png "视图控制器的代码")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IOS 设计器中的视图控制器](introduction-images/1-storyboardwithviewcontroller-vs.png "iOS 设计器中的视图控制器")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![视图控制器的代码](introduction-images/2-viewcontrollercode-vs.png "视图控制器的代码")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

在其默认状态下，视图控制器不提供任何这样的功能。它必须填充控件。 这些控件放置在视图控制器的视图中，包含所有屏幕的内容的矩形区域。 大多数视图控制器包含常用控件按钮、 标签和文本字段，如以下屏幕截图，其中显示了包含一个按钮的视图控制器中所示： 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![包含一个按钮的视图控制器](introduction-images/3-viewcontrollerwithbutton-vsmac.png "包含一个按钮的视图控制器")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![包含一个按钮的视图控制器](introduction-images/3-viewcontrollerwithbutton-vs.png "包含一个按钮的视图控制器")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

可以添加到视图控制器和随它去一些控件，例如包含静态文本标签。 但是，在大多数情况，控件必须自定义以编程方式。 例如，前面添加的按钮应该做一些工作时点击，因此必须在代码中添加一个事件处理程序。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

要访问和操作代码中的按钮，它必须具有唯一标识符。 通过选择按钮，打开提供唯一标识符**Properties Pad**，并设置其**名称**字段改为一个值，例如"提交"按钮：

[![在属性面板中设置按钮的名称](introduction-images/4-settingbuttonname-vsmac.png "在属性面板中设置按钮的名称")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

要访问和操作代码中的按钮，它必须具有唯一标识符。 通过选择按钮，打开提供唯一标识符**属性窗口**，并设置其**名称**字段改为一个值，例如"提交"按钮：

[![在属性窗口中设置按钮的名称](introduction-images/4-settingbuttonname-vs.png "在属性窗口中设置按钮的名称")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

现在，该按钮具有一个名称，可以在代码中访问它。 但是，它的工作原理？

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在中**Solution Pad**，导航到**ViewController.cs**并单击泄露指示器上显示的视图控制器的`ViewController`类定义范围两个文件，其中每个包含[分部类](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定义：

[![两个文件，构成 ViewController 类：ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "的两个文件，构成 ViewController 类：ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在中**解决方案资源管理器**，导航到**ViewController.cs**并单击泄露指示器上显示的视图控制器的`ViewController`类定义涉及两个文件，每个其中包含[分部类](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定义：

[![两个文件，构成 ViewController 类：ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "的两个文件，构成 ViewController 类：ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs**应使用与相关的自定义代码填充`ViewController`类。 在此文件中，`ViewController`类可响应各种 iOS 视图控制器生命周期方法、 自定义 UI，并响应用户输入诸如按钮点击。

- **ViewController.designer.cs**是由 iOS 设计器将映射到代码的直观地构造接口创建的生成的文件。 更改此文件将被覆盖，因为它不应修改。 此文件中的属性声明实现中的代码`ViewController`类以访问，通过**名称**，iOS 设计器中的注册控件集。 打开**ViewController.designer.cs**会显示下面的代码：

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

`SubmitButton`属性声明连接整个`ViewController`类的不只是**ViewController.designer.cs**文件 – 到情节提要中定义的按钮。 由于**ViewController.cs**定义的一部分`ViewController`类，它有权访问`SubmitButton`。

以下屏幕截图展示了 IntelliSense 现在可识别`SubmitButton`中引用**ViewController.cs**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![识别的提交按钮引用的智能感知](introduction-images/6-submitbuttonintellisense-vsmac.png "识别 SubmitButton 引用的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![识别的提交按钮引用的智能感知](introduction-images/6-submitbuttonintellisense-vs.png "识别 SubmitButton 引用的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

本部分演示了如何在 iOS 设计器中创建一个按钮，并访问该按钮在代码中的。

此文档的其余部分提供了 iOS 设计器的详细概述。

## <a name="ios-designer-basics"></a>iOS 设计器基础知识

本部分介绍 iOS 设计器的部分，并提供其功能的教程。

### <a name="launching-the-ios-designer"></a>启动 iOS 设计器

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

使用 Visual Studio for Mac 中创建的 Xamarin.iOS 项目包括一个情节提要。 若要查看情节提要的内容，请双击在.storyboard 文件**Solution Pad**:

[![在 iOS 设计器中打开演示图板](introduction-images/7-storyboardopen-vsmac.png "在 iOS 设计器中打开演示图板")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

使用 Visual Studio 创建的大多数 Xamarin.iOS 项目包括一个情节提要。 若要查看情节提要的内容，请双击在.storyboard 文件**解决方案资源管理器**:

[![在 iOS 设计器中打开演示图板](introduction-images/7-storyboardopen-vs.png "在 iOS 设计器中打开演示图板")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>iOS 设计器功能

IOS 设计器具有六个主要部分：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![IOS 设计器的部分](introduction-images/8-sixpartsofiosdesigner-vsmac.png "iOS 设计器的部分")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **设计图面上**– iOS 设计器的主要工作区。 在文档区域中所示，这样，用户界面可视方式构建。
2. **约束工具栏**– 允许帧编辑模式和约束编辑模式下，两种不同方式来在用户界面中定位元素之间进行切换。
3. **工具箱**– 列出了控制器、 对象、 控件、 数据视图、 手势识别器、 windows 和条，可以拖动到设计图面上并添加到用户界面。
4. **属性面板**– 显示所选的控件，包括标识、 视觉样式、 可访问性、 布局和行为的属性。
5. **文档大纲**– 显示正在编辑的接口的布局与该控件的树。 单击树中的项在 iOS 设计器中选择并显示在其属性**Properties Pad**。 这非常方便的深度嵌套的用户界面中选择特定的控件。
6. **底部工具栏**– 包含用于更改 iOS 设计器如何显示.storyboard 或.xib 文件，包括设备、 方向和缩放选项。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![IOS 设计器的部分](introduction-images/8-sixpartsofiosdesigner-vs.png "iOS 设计器的部分")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **设计图面上**– iOS 设计器的主要工作区。 在文档区域中所示，这样，用户界面可视方式构建。
2. **约束工具栏**– 允许帧编辑模式和约束编辑模式下，两种不同方式来在用户界面中定位元素之间进行切换。
3. **工具箱**– 列出了控制器、 对象、 控件、 数据视图、 手势识别器、 windows 和条，可以拖动到设计图面上并添加到用户界面。
4. **属性窗口**– 显示所选的控件，包括标识、 视觉样式、 可访问性、 布局和行为的属性。
5. **文档大纲**– 显示正在编辑的接口的布局与该控件的树。 单击树中的项在 iOS 设计器中选择并显示在其属性**属性窗口**。 这非常方便的深度嵌套的用户界面中选择特定的控件。
6. **底部工具栏**– 包含用于更改 iOS 设计器如何显示.storyboard 或.xib 文件，包括设备、 方向和缩放选项。

-----

### <a name="design-workflow"></a>设计工作流

#### <a name="adding-a-control-to-the-interface"></a>将控件添加到接口

若要向接口添加一个控件，将其从**工具箱**并将其放置在设计图面上。 当添加或定位控件的控件时，垂直和水平准则突出显示垂直居中、 水平居中和边距等常用布局位置：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
 
![设计图面上突出显示指导原则，通常使用布局位置](introduction-images/9-layoutguides-vsmac.png "设计图面上突出显示指导原则，通常使用布局位置")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![设计图面上突出显示指导原则，通常使用布局位置](introduction-images/9-layoutguides-vs.png "设计图面上突出显示指导原则，通常使用布局位置")

-----

上面的示例中的蓝色虚线提供水平居中对齐方式可视化指导，帮助按钮放置。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="context-menu-commands"></a>上下文菜单命令

上下文菜单可在设计图面上以及在**文档大纲**。 此菜单提供了用于选定的控件以及其父站点，该使用嵌套层次结构中的视图时非常有用的命令：

[![在设计图面上的上下文菜单](introduction-images/10-contextmenudesignsurface-vsmac.png "设计图面上的上下文菜单")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>约束工具栏

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
 
[![约束工具栏](introduction-images/11-constraintstoolbar-vsmac.png "约束工具栏")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![约束工具栏](introduction-images/11-constraintstoolbar-vs.png "约束工具栏")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

约束工具栏已更新，现在包含两个控件： 编辑模式的框架 / 约束编辑模式切换和更新约束 / 更新帧按钮。

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>帧编辑模式 / 约束编辑模式切换

在以前版本的 iOS 设计器中，单击设计图面上的已选择视图帧编辑模式和约束编辑模式之间切换。 现在，约束工具栏中的一个切换控件这些编辑模式之间切换。

- 帧编辑模式：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![帧编辑模式按钮](introduction-images/12a-frameeditingmode-vsmac.png "帧编辑模式按钮")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![帧编辑模式按钮](introduction-images/12a-frameeditingmode-vs.png "帧编辑模式按钮")

-----

- 约束编辑模式：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![约束编辑模式按钮](introduction-images/12b-constrainteditingmode-vsmac.png "约束编辑模式按钮")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![约束编辑模式按钮](introduction-images/12b-constrainteditingmode-vs.png "约束编辑模式按钮")

-----

#### <a name="update-constraints--update-frames-button"></a>更新约束/更新帧按钮

更新约束更新帧按钮位于右侧的编辑模式的框架 / 约束编辑模式切换。

- 在帧编辑模式中，单击此按钮调整任何所选的元素以匹配其约束的帧。
- 约束编辑模式下，单击此按钮调整任何所选的元素以匹配其框架的约束。

### <a name="bottom-toolbar"></a>底部工具栏

在底部工具栏提供了一种方法，以选择设备、 方向和缩放用于在 iOS 设计器中查看的情节提要或.xib 文件：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![底部工具栏中，用于选择设备和方向设计图面的](introduction-images/13-bottomtoolbar-vsmac.png "底部工具栏中，用于选择设备和设计图面上的方向")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![底部工具栏中，用于选择设备和方向设计图面的](introduction-images/13-bottomtoolbar-vs.png "底部工具栏中，用于选择设备和设计图面上的方向")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>设备和方向

展开底部工具栏将显示所有设备、 方向和/或适应适用于当前文档。 单击这些更改设计图面上显示的视图。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![底部工具栏中，展开以显示设备和方向](introduction-images/14-bottomtoolbarexpanded-vsmac.png "底部工具栏中，展开以显示设备和方向")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![底部工具栏中，展开以显示设备和方向](introduction-images/14-bottomtoolbarexpanded-vs.png "底部工具栏中，展开以显示设备和方向")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

请注意，选择设备和方向更改仅如何 iOS 设计器预览设计。 而不考虑当前所选内容，新添加的约束应用于所有设备和方向，除非**编辑特征**使用按钮来指定，否则。

当[大小类](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)都[启用](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes)，则**编辑特征**按钮将展开的底部工具栏中显示。  单击**编辑特征**按钮将显示用于创建基于所选的设备和方向表示大小类接口变体的选项。 请开考虑以下示例：

- 如果**iPhone SE** / **纵向**，是选择，弹出窗口中将提供选项来创建 compact 宽度，正则高度大小类接口变体。 
- 如果**iPad Pro 9.7"** / **横向** / **全屏**是选择，弹出窗口中将提供选项来创建接口变体正则宽度、 正则高度大小类中。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![要用于改变一个接口，按大小类的底部工具栏](introduction-images/15-edittraitsbutton-vsmac.png "底部工具栏正在用于改变一个接口，按大小类")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![要用于改变一个接口，按大小类的底部工具栏](introduction-images/15-edittraitsbutton-vs.png "底部工具栏正在用于改变一个接口，按大小类")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>缩放控件

设计图面上支持多个控件通过缩放：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
 
![在底部工具栏中的缩放控件](introduction-images/16-zoomcontrols-vsmac.png "底部工具栏中的缩放控件")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![在底部工具栏中的缩放控件](introduction-images/16-zoomcontrols-vs.png "底部工具栏中的缩放控件")

-----

控件包括：

1. 缩放到合适大小
2. 缩小
3. 放大
4. 实际大小 （1:1 像素大小）

这些控件调整设计图面上的缩放。 它们不会影响在运行时应用程序的用户界面。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="properties-pad"></a>属性面板

使用**Properties Pad**编辑标识、 可视样式、 可访问性，以及控件的行为。 以下屏幕截图演示了**Properties Pad**按钮的选项：

[![属性面板中的按钮](introduction-images/17-buttonpropertiespad-vsmac.png "Properties Pad 的按钮")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>属性面板节

**Properties Pad**包含三个部分：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>属性窗口

使用**属性窗口**编辑标识、 可视样式、 可访问性，以及控件的行为。 以下屏幕截图演示了**属性窗口**按钮的选项：

[![按钮属性窗口](introduction-images/17-buttonpropertieswindow-vs.png "的按钮属性窗口")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>属性窗口部分

**属性窗口**包含三个部分：

-----

1.  **小组件**– 该控件，例如名称、 类、 样式属性等的主要属性。用于管理控件的内容的属性通常放在此处。
2.  **布局**– 此处列出的位置和大小的控件，包括约束和帧，跟踪的属性。
3.  **事件**– 此处指定的事件和事件处理程序。 适用于处理事件，例如触摸、 点击、 拖动等。此外可以直接在代码中处理事件。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>在属性面板中编辑属性

除了可视化编辑设计图面上，iOS 设计器支持编辑中的属性**Properties Pad**。 可用的属性更改基于所选的控件，如下面的屏幕截图所示：

[![按钮属性](introduction-images/18a-buttonpropertiespad-vsmac.png "按钮属性")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![查看控制器属性](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "查看控制器属性")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>在属性窗口中编辑属性

除了可视化编辑设计图面上，iOS 设计器支持编辑中的属性**属性窗口**。 可用的属性更改基于所选的控件，如下面的屏幕截图所示：

[![按钮属性](introduction-images/18a-buttonpropertieswindow-vs.png "按钮属性")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![查看控制器属性](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "查看控制器属性")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> 属性面板现在显示的标识部分**模块**字段。 有必要与 Swift 类互操作时，仅在本部分中填充它。 使用它来输入 Swift 类，它们是占用命名空间的模块名称。

#### <a name="default-values"></a>默认值

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

中的许多属性**Properties Pad**显示任何值或默认值。 但是，应用程序的代码仍可以修改这些值。 **Properties Pad**不会显示在代码中设置的值。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

中的许多属性**属性窗口**显示任何值或默认值。 但是，应用程序的代码仍可以修改这些值。 **属性窗口**不会显示在代码中设置的值。

-----

#### <a name="event-handlers"></a>事件处理程序

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要指定多个事件的自定义事件处理程序，请使用**事件**选项卡**Properties Pad**。 例如，在下面的屏幕截图`HandleClick`方法处理按钮的**接触内**事件：

[![属性面板中，为按钮设置事件处理程序与](introduction-images/19-buttonpropertiespadevents-vsmac.png "Properties Pad，与为按钮设置事件处理程序")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要指定多个事件的自定义事件处理程序，请使用**事件**选项卡**属性窗口**。 例如，在下面的屏幕截图`HandleClick`方法处理按钮的**接触内**事件：

[![属性窗口中，为按钮设置事件处理程序与](introduction-images/19-buttonpropertieswindowevents-vs.png "属性窗口中，并为按钮设置事件处理程序")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

指定事件处理程序后具有相同名称的方法必须添加到相应的视图控制器类中。 否则为`unrecognized selector`点击按钮时，会发生异常：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![无法识别的选择器异常](introduction-images/20-unrecognizedselector-vsmac.png "无法识别的选择器异常")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

请注意，后一个事件处理程序中指定了**Properties Pad**，iOS 设计器将立即打开相应的代码文件，并提供要插入方法声明。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![无法识别的选择器异常](introduction-images/20-unrecognizedselector-vs.png "无法识别的选择器异常")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

有关使用自定义事件处理程序示例，请参阅[Hello，iOS 入门指南 》](~/ios/get-started/hello-ios/index.md)。

### <a name="outline-view"></a>大纲视图

IOS 设计器还可以以大纲显示控件的接口层次结构。 概要中显示，请选择**文档大纲**选项卡，如下所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![文档大纲](introduction-images/21-buttonoutlineview-vsmac.png "文档大纲")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![文档大纲](introduction-images/21-buttonoutlineview-vs.png "文档大纲")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

大纲视图中的所选的控件保持与设计图面上选定的控件保持同步。  此功能可用于从深度嵌套的接口层次结构中选择项。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="revert-to-xcode"></a>恢复到 Xcode

它是可以互换使用 iOS 设计器和 Xcode Interface Builder。 若要在 Xcode Interface Builder 中打开情节提要或.xib 文件，右键单击文件并选择**打开方式 > Xcode Interface Builder**，如下面的屏幕截图所示：

[![在 Xcode Interface Builder 中打开演示图板](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode Interface Builder 中打开演示图板")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode Interface Builder 中进行编辑后, 保存该文件，并返回到 Visual Studio for mac。 所做的更改将同步到 Xamarin.iOS 项目。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>恢复到 Xcode

它是可以互换使用 iOS 设计器和 Xcode Interface Builder，不过 Xcode Interface Builder 只是可用的 mac 上 若要在 Mac 上的 Xcode Interface Builder 中打开的情节提要或.xib 文件，打开包含在 Xamarin.iOS 项目的解决方案[Visual Studio for Mac](/visualstudio/mac/)，右键单击该文件并选择**打开方式 > Xcode 接口生成器**，如下面的屏幕截图所示：

[![在 Xcode Interface Builder 中打开演示图板](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode Interface Builder 中打开演示图板")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode Interface Builder 中进行编辑后, 保存该文件，并返回到 Visual Studio for mac。 所做的更改将同步到 Xamarin.iOS 项目。

-----

## <a name="xib-support"></a>.xib 支持

IOS 设计器支持创建、 编辑和管理.xib 文件。 这些是该表示单一的自定义视图可添加到应用程序的视图层次结构的 XML 文件。 .Xib 文件通常表示为单个视图或屏幕的应用程序中的接口，而情节提要表示许多屏幕以及它们之间的转换。

有许多观点有关哪些解决方案 –.xib 文件、 情节提要或代码 – 最适合于创建和维护用户界面。 实际上，没有完美的解决方案，并且它始终是值得考虑手头作业的最佳工具。 也就是说，.xib 文件是通常最强大时用于表示在应用程序，如自定义表视图单元格中的多个位置所需的自定义视图。 

有关使用.xib 文件的更多文档可在以下方案：

- [使用视图.xib 模板](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [创建使用.xib 自定义 TableViewCell](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [创建使用.xib 的启动屏幕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

有关使用情节提要的详细信息，请参阅[情节提要简介](~/ios/user-interface/storyboards/index.md)。

这和其他 iOS 设计器相关指南，请参阅以用于构建用户界面，因为大多数 Xamarin.iOS 新项目模板提供情节提要，默认情况下，将情节提要用作标准方法。

## <a name="summary"></a>总结

本指南介绍了 iOS 设计器中，描述其功能和大纲显示它提供了用于设计美观的用户界面的工具。

## <a name="related-links"></a>相关链接

- [情节提要简介](~/ios/user-interface/storyboards/index.md)
- [iOS 可设计的控件演练](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello，iOS](~/ios/get-started/hello-ios/index.md)
- [Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Android 设计器概述](~/android/user-interface/android-designer/index.md)
- [分部类和方法](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [深入了解 Xamarin 设计器适用于 iOS 的发展 2014 （视频）](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [使用 iOS 设计器创建启动屏幕 （视频）](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
