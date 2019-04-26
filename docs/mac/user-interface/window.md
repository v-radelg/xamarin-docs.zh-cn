---
title: 在 Xamarin.Mac 中的 Windows
description: 本文介绍如何使用 windows 和 Xamarin.Mac 应用程序中的面板。 其中介绍创建 windows 以及在 Xcode 和 Interface Builder，从情节提要和.xib 文件加载它们并以编程方式使用它们中的面板。
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: ec907e71074a97bd5d1714e79dd504013f5c8a4b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61240350"
---
# <a name="windows-in-xamarinmac"></a>在 Xamarin.Mac 中的 Windows

_本文介绍如何使用 windows 和 Xamarin.Mac 应用程序中的面板。其中介绍创建 windows 以及在 Xcode 和 Interface Builder，从情节提要和.xib 文件加载它们并以编程方式使用它们中的面板。_

当使用C#和.NET 在 Xamarin.Mac 应用程序，将有权访问相同的 Windows 和面板的使用的开发人员*Objective C*并*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护 Windows 和面板 （或选择通过 C# 代码中直接创建）。

根据其用途，Xamarin.Mac 应用程序可以提供一个或多个 Windows 屏幕上管理和协调它显示，并适用于的信息。 窗口的主要功能是：

1. 若要提供的视图和控件中的某个区域可以放置和管理。
2. 若要接受并响应用户与键盘和鼠标交互的响应中的事件。

Windows 可以是无模式状态 （例如可以同时打开多个文档的文本编辑器） 中使用或模式 （如应用程序可以继续操作之前必须关闭导出对话框）。

面板是一种特殊的窗口 (基的子类`NSWindow`类)，通常为提供服务辅助函数在应用程序，例如文本格式的检查器和系统颜色选取器之类的实用程序窗口中。

[![](window-images/intro01.png "编辑在 Xcode 中的窗口")](window-images/intro01.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用 Windows 和面板的基本知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Windows 简介

如上面所述，一个窗口提供了一个区域中的视图和控件可以放置和管理和响应事件时根据用户交互 （无论通过键盘或鼠标）。

根据 Apple，有 macOS 应用中的 Windows 的五种主要类型：

- **文档窗口**-文档窗口包含基于文件的用户数据，例如电子表格或文本文档。
- **应用窗口**-应用程序窗口是不是基于文档的 （如在 Mac 上的日历应用） 的应用程序的主窗口。
- **面板**-其他窗口上方浮动面板，并提供工具或打开文档时，用户可使用的控件。 在某些情况下，一个面板，可以是半透明 (例如在使用大型图形)。
- **对话框**-一个对话框将出现在响应用户操作，通常会提供方法的用户可以完成该操作。 它可以关闭之前，对话框需要来自用户的响应。 (请参阅[使用对话框](~/mac/user-interface/dialog.md))
- **警报**-警报是一种特殊的 （如错误） 出现严重问题时，将显示的对话框或作为警告 （如正在准备删除文件）。 由于警报是一个对话框，它还需要用户响应前可以关闭它。 (请参阅[使用警报](~/mac/user-interface/alert.md))

有关详细信息，请参阅[有关 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)部分中的 Apple 的[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main、 密钥和非活动状态的 Windows

在 Xamarin.Mac 应用程序中的 Windows 可以外观和行为基于用户与它们的当前交互如何以不同方式。 调用的最重要的文档或应用程序窗口为当前用户的关注焦点_主窗口_。 在大多数情况下此窗口也将_密钥窗口_（目前接受用户输入的窗口）。 但这并非总是如此，例如，颜色选取器可以打开并将密钥窗口，在用户交互使用，以更改文档窗口 （它将仍是主窗口） 中的项的状态。

主报表和密钥 Windows （如果它们是单独） 始终处于活动状态，_处于非活动状态的 Windows_是打开的窗口不在前景中。 例如，文本编辑器应用程序可以具有多个文档打开状态时，仅主窗口将处于活动状态，所有其他人将处于非活动状态。 

有关详细信息，请参阅[有关 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)部分中的 Apple 的[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>命名 Windows

窗口可显示标题栏和标题显示时，它通常是应用程序的名称、 正在处理的文档的名称或 （如检查器） 窗口的函数。 某些应用程序不显示标题栏，因为它们是识别建立直通连接，不处理文档。

Apple 建议以下准则：

- 使用一个主、 非文档窗口的标题应用程序名称。 
- 命名新的文档窗口`untitled`。 对于第一个新文档，不要将数字附加到标题 (如`untitled 1`)。 如果用户创建另一个新文档，再进行保存和标题的第一个，则调用该窗口`untitled 2`， `untitled 3`，等等。

有关详细信息，请参阅[命名 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1)部分中的 Apple 的[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>全屏幕 Windows

在 macOS、 应用程序的窗口可以转全屏显示隐藏的所有内容包括应用程序菜单栏 （这可以通过将光标移动到屏幕的顶部显示的） 提供受干扰与它的免费交互是内容。

Apple 建议遵照以下准则：

- 确定是否有意义的窗口转全屏幕。 提供简短交互 （例如，计算器） 的应用程序不应提供全屏幕模式。
- 显示工具栏的全屏任务需要它。 通常在全屏幕模式下隐藏工具栏。
- 全屏窗口应具有完成任务所需的所有功能用户。
- 用户处于全屏窗口时，如果可能，避免查找器交互。
- 而无需转换将重点从主要任务充分利用增加的屏幕空间。

有关详细信息，请参阅[全屏幕 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1)部分中的 Apple 的[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>面板

一个面板是一个辅助窗口，其中包含控件和影响将活动文档或选定内容 （如系统颜色选取器） 的选项：

[![](window-images/panel01.png "颜色面板")](window-images/panel01.png#lightbox)

面板可以是_特定于应用程序_或_系统级_。 特定于应用的面板浮于应用程序的文档窗口的顶部，并在应用程序是在后台时消失。 系统级面板 (如**字体**面板)，无论该应用程序所有打开的窗口之上的浮点数。 

Apple 建议遵照以下准则：

- 一般情况下，使用标准的面板，尽量少和图形密集型任务，应仅使用透明的面板。
- 请考虑使用一个面板，让用户轻松访问重要控件或直接影响其任务的信息。
- 隐藏和显示所需的面板。
- 面板应始终包含标题栏。
- 面板不应包含一个活动的最小化按钮。

#### <a name="inspectors"></a>检查器

大多数新式 macOS 应用程序提供辅助控件和影响的活动文档或作为选择的选项_检查器_属于主窗口 (如**页面**应用如下所示)，而不是使用面板 Windows:

[![](window-images/panel02.png "示例检查器")](window-images/panel02.png#lightbox)

有关详细信息，请参阅[面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)Apple 的部分[OS X 人机接口指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)和我们[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) 的完整实现的示例应用程序**检查器界面**Xamarin.Mac 应用中。

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>创建和维护在 Xcode 中的 Windows

在创建新的 Xamarin.Mac 的 Cocoa 应用程序时，默认情况下获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包含在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](window-images/edit01.png "选择主要情节提要")](window-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开的窗口设计：

[![](window-images/edit02.png "编辑在 Xcode 中的 UI")](window-images/edit02.png#lightbox)

在中**属性检查器**，有几个可用于定义和控制您的窗口的属性：

- **标题**-这是将窗口的标题栏中显示的文本。
- **自动保存**-这是_密钥_将用于 ID 窗口时自动保存它的位置和设置。
- **标题栏**-该窗口是否显示标题栏。
- **统一的标题和工具栏**-如果该窗口包括一个工具栏，它应该是标题栏的一部分。
- **完整的调整大小的内容视图**-允许在窗口标题栏中进行的内容区域。
- **卷影**-窗口是否具有阴影。
- **设置纹理**-纹理的 windows 可以使用效果 （如活力） 和可以移动的任意位置拖动在其主体上。
- **关闭**-窗口是否具有关闭按钮。
- **最大程度减少**-窗口是否具有最小化按钮。
- **重设大小**-窗口是否具有调整大小控制。
- **工具栏按钮**-窗口是否具有隐藏/显示工具栏按钮。
- **可还原的已**-是窗口的位置和设置自动保存和还原。
- **显示在启动**-在窗口自动显示时`.xib`加载文件。
- **隐藏在停用**-隐藏的窗口应用程序进入后台时。
- **发布时关闭**-是窗口关闭时从内存中清除。
- **始终显示工具提示**-是不断显示工具提示。
- **重新计算视图循环**-是重新计算之前绘制窗口的视图顺序。
- **空格**，**凝聚力**并**Cycling** -所有定义窗口在这些 macOS 环境中的行为方式。 
- **全屏**-确定是否此窗口可以进入全屏模式。 
- **动画**-控制动画可用于在窗口的类型。
- **外观**-控制窗口的外观。 现在为只有一个外观，浅绿色。

请参阅 Apple[简介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)并[NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow)文档了解详细信息。

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>设置默认大小和位置

若要设置您的窗口的初始位置并控制其大小，切换到**大小检查器**:

[![](window-images/edit07.png "默认大小和位置")](window-images/edit07.png#lightbox)

从此处可以设置窗口的初始大小，使其最小值和最大大小，在屏幕上设置的初始位置并控制在窗口周围的边框。

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>设置自定义主窗口控制器

为了能够创建输出口和操作公开给 C# 代码的 UI 元素，Xamarin.Mac 应用将需要使用自定义窗口控制器。

请执行以下操作：

1. 在 Xcode 的 Interface Builder 中打开应用的情节提要。
2. 选择`NSWindowController`设计图面中。
3. 切换到**标识检查器**查看，并输入`WindowController`作为**类名**: 

    [![](window-images/windowcontroller01.png "设置类名称")](window-images/windowcontroller01.png#lightbox)
4. 保存所做的更改并返回到 Visual Studio for Mac 进行同步。
5. 一个`WindowController.cs`文件将添加到您的项目**解决方案资源管理器**Visual Studio for Mac 中： 

    [![](window-images/windowcontroller02.png "选择 windows 控制器")](window-images/windowcontroller02.png#lightbox)
6. 重新打开在 Xcode 的 Interface Builder 情节提要。
7. `WindowController.h`文件将可供使用： 

    [![](window-images/windowcontroller03.png "编辑 WindowController.h 文件")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>添加 UI 元素

若要定义窗口的内容，将控件从**库检查器**拖动到**界面编辑器**。 请参阅我们[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)文档了解有关使用 Interface Builder 来创建并启用控件的详细信息。

例如，我们来拖动从工具栏**库检查器**到中的窗口上**界面编辑器**:

[![](window-images/edit03.png "从库中选择一个工具栏")](window-images/edit03.png#lightbox)

接下来，在中拖动**文本视图**并调整其大小以使其填充的工具栏下方的区域：

[![](window-images/edit04.png "添加文本视图")](window-images/edit04.png#lightbox)

由于我们希望**文本视图**若要收缩和发展为窗口的大小的更改，让我们切换到**约束编辑器**并添加以下约束：

[![](window-images/edit05.png "编辑约束")](window-images/edit05.png#lightbox)

通过单击有关**红色 I 型光标**在编辑器的顶部，单击**添加 4 个约束**，我们将指示文本视图停在给定 X、 Y 坐标和扩大或收缩水平和垂直作为调整窗口大小。

最后，让我们公开**文本视图**若要使用代码**Outlet** (并确保选择`ViewController.h`文件):

[![](window-images/edit06.png "配置输出口")](window-images/edit06.png#lightbox)

保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

有关使用详细信息**输出口**并**操作**，请参阅我们[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)文档。

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>标准的窗口的工作流

用于任何窗口的创建和使用 Xamarin.Mac 应用程序中，该过程基本上是与我们做了什么只需上面相同：

1. 对于不自动添加到你的项目是默认值的新窗口，向项目添加新的窗口中定义。 这将在下面详细讨论。
2. 双击`Main.storyboard`文件以打开该窗口设计在 Xcode 的 Interface Builder 中进行编辑。
3. 将一个新的窗口拖动到用户界面设计并挂接到主窗口中使用的窗口_segues 设计_(有关详细信息请参阅[segues 设计](~/mac/platform/storyboards/indepth.md#Segues)一部分我们[使用情节提要](~/mac/platform/storyboards/indepth.md)文档)。
3. 在中设置任何所需的窗口的属性**属性检查器**并**大小检查器**。
4. 在构建您的接口和配置中对其所需的控件中拖动**属性检查器**。
5. 使用**大小检查器**来处理 UI 元素的大小调整。
6. 公开给 C# 代码通过窗口的 UI 元素**输出口**并**操作**。
7. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

现在，我们已创建的基本窗口，我们将介绍典型的进程在 Xamarin.Mac 应用程序执行时使用的 windows。 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>显示默认窗口

默认情况下，新的 Xamarin.Mac 应用程序将自动显示在窗口中定义`MainWindow.xib`文件在启动时：

[![](window-images/display01.png "运行示例窗口")](window-images/display01.png#lightbox)

由于我们已修改该窗口的上方的设计，它现在包括一个默认的工具栏并**文本视图**控件。 以下主题中`Info.plist`文件负责显示此窗口：

[![](window-images/display00.png "编辑 Info.plist")](window-images/display00.png#lightbox)

**主界面**下拉列表中用于选择将用作主要应用程序 UI 的情节提要 (在这种情况下`Main.storyboard`)。

视图控制器会自动添加到项目，以控制 （连同其主视图） 显示该主 Windows。 在中定义`ViewController.cs`文件，并附加到**文件的所有者**下的 Interface Builder 中**标识检查器**:

[![](window-images/display02.png "设置文件的所有者")](window-images/display02.png#lightbox)

对于我们窗口中，我们希望它具有的标题`untitled`它首次打开时我们重写`ViewWillAppear`中的方法`ViewController.cs`以如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> 我们将设置窗口的值`Title`中的属性`ViewWillAppear`方法而不是`ViewDidLoad`方法因为该视图可能会加载到内存，而它不尚未完全实例化。 如果我们尝试访问`Title`中的属性`ViewDidLoad`方法，则会得到`null`由于窗口尚未被构造和有线向上取到的属性尚未的异常。

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>以编程方式关闭窗口

可能想要以编程方式关闭 Xamarin.Mac 应用程序中的窗口的时间，但除了必须用户单击窗口的**关闭**按钮或使用菜单项。 macOS 提供两个不同的方法来关闭`NSWindow`以编程方式：`PerformClose`和`Close`。

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

调用`PerformClose`方法`NSWindow`模拟用户单击的窗口**关闭**按钮立即突出显示按钮，然后关闭窗口。

如果应用程序实现`NSWindow`的`WillClose`事件，将会引发之前关闭的窗口。 如果该事件返回`false`，则将不会关闭窗口。 如果窗口不具有**关闭**按钮，或无法关闭出于任何原因，操作系统将发出警告声。

例如：

```csharp
MyWindow.PerformClose(this);
```

会尝试关闭`MyWindow``NSWindow`实例。 如果成功，窗口将关闭，否则将发出警报的声音，并且将保持打开状态。

<a name="Close" />

### <a name="close"></a>关闭

调用`Close`方法`NSWindow`不模拟用户单击的窗口**关闭**按钮通过立即突出显示按钮，它只需关闭该窗口。

一个窗口没有可见要关闭和`NSWindowWillCloseNotification`通知将发布到默认通知中心将关闭窗口。

`Close`方法在两个重要方面与不同`PerformClose`方法：

1. 它不会引发`WillClose`事件。
2. 它不模拟用户单击**关闭**通过暂时突出显示按钮的按钮。

例如：

```csharp
MyWindow.Close();
```

将关闭`MyWindow``NSWindow`实例。

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>修改的 Windows 内容

在 macOS，Apple 已提供某种方式向用户通知的窗口的内容 (`NSWindow`) 已由用户修改，并且需要保存。 如果该窗口包含修改的内容，小黑点将显示在它的**关闭**小组件：

[![](window-images/close01.png "具有已修改的标记的窗口")](window-images/close01.png#lightbox)

如果用户尝试关闭窗口或退出窗口的内容的 Mac 应用程序虽然有未保存更改，应显示[Dialog Box](~/mac/user-interface/dialog.md)或[模式表](~/mac/user-interface/dialog.md)，并允许用户保存他们的更改第一个：

[![](window-images/close02.png "一种在窗口关闭时显示的表存储")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>将标记为已修改的窗口

若要将标记为具有修改内容的窗口，请使用以下代码：

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

并保存更改后清除修改的标志使用：

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>关闭窗口之前保存更改

若要监视用户关闭窗口，并允许其将事先保存修改的内容，您需要创建一个子类`NSWindowDelegate`并重写其`WindowShouldClose`方法。 例如：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on result
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

使用以下代码将此委托的实例附加到您的窗口：

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>关闭应用之前保存更改

最后，应检查的 Xamarin.Mac 应用以查看其 Windows 的任何包含已修改的内容并允许用户在退出前保存所做的更改。 若要执行此操作，编辑你`AppDelegate.cs`文件中，重写`ApplicationShouldTerminate`方法，并使其如下所示：

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>使用多个 Windows

大多数文档基于 Mac 应用程序可以在同一时间编辑多个文档。 例如，文本编辑器可以打开进行编辑，同时在多个文本文件。 默认情况下，我们新的 Xamarin.Mac 应用程序具有**文件**菜单，其中包含**新建**项自动有线-高达`newDocument:`**操作**。

我们要激活此新项目，并允许用户打开我们在一次编辑多个文档的主窗口的多个副本。

让我们编辑我们`AppDelegate.cs`文件，并添加以下计算属性：

```csharp
public int UntitledWindowCount { get; set;} =1;
```

我们将使用此跟踪的未保存的文件数，以便我们可以向用户 （每个 Apple 相关指南如上文所述） 提供反馈。

接下来，添加以下方法：

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

此代码创建窗口控制器的新版本，加载新的窗口、 使得主报表和密钥窗口中，并设置其标题。 现在，如果我们运行我们的应用程序，并选择**新建**从**文件**菜单将打开一个新的编辑器窗口，并将其显示：

[![](window-images/display04.png "已添加新的无标题的窗口")](window-images/display04.png#lightbox)

如果我们打开**Windows**菜单中，可以看到该应用程序会自动跟踪和处理我们打开的窗口：

[![](window-images/display05.png "Windows 菜单")](window-images/display05.png#lightbox)

使用菜单中的 Xamarin.Mac 应用程序的详细信息，请参阅我们[与菜单一起使用](~/mac/user-interface/menu.md)文档。

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>获取当前活动窗口

在 Xamarin.Mac 应用程序中，可以打开多个窗口 （文档），有些的时候您将需要获取当前的最顶层窗口 （密钥窗口）。 下面的代码将返回密钥窗口中：

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

可以在任何类或需要访问当前的密钥窗口的方法中调用它。 如果没有窗口是当前打开的它将返回`null`。

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>访问所有应用 Windows

有时可能需要访问所有的 Xamarin.Mac 应用当前已经打开的窗口。 例如，若要查看用户想要打开的文件是否为已在现有窗口中打开。

`NSApplication.SharedApplication`维护`Windows`属性，其中包含应用程序中所有打开的窗口的数组。 您可以循环访问所有应用程序的当前 windows 此数组。 例如：

```csharp
// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

示例代码中，我们要强制转换到自定义每个返回的窗口`ViewController`类在我们的应用程序和测试的自定义值`Path`属性与用户想要打开的文件的路径。 如果该文件已打开，我们要将该窗口到前面。

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>调整在代码中的窗口大小

有些应用程序时需要重新调整大小的窗口中的代码的时候。 若要调整大小和重新定位一个窗口，您调整它的`Frame`属性。 调整窗口的大小，通常需要调整它的源，以使窗口保持在同一位置中，由于 macOS 的坐标系统。

与 iOS 不同左上角位置表示 (0，0)，macOS 使用数学的坐标系统在屏幕左下角其中表示 (0，0)。 在 iOS 中，随着将向右侧的向下移动，坐标增加。 在 macOS 中的坐标的向上右侧值而增加。 

下面的示例代码调整窗口的大小：

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> 当您调整窗口大小和位置在代码中的时，需要确保遵守 Interface Builder 中设置的最小值和最大大小。 这不会自动生效，您将能够使窗口变大或低于这些限制。

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>监视窗口大小的更改

有时可能需要监视中的 Xamarin.Mac 应用内的窗口的大小的更改。 例如，若要重绘内容以适合新大小。

若要监视大小更改，请首先确保已在 Xcode 的 Interface Builder 窗口控制器分配的自定义类。 例如，`MasterWindowController`在下面的示例：

[![](window-images/resize01.png "标识检查器")](window-images/resize01.png#lightbox)

接下来，编辑自定义窗口控制器类和监视器`DidResize`控制器的窗口为接到实时大小更改上的事件。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

（可选） 可以使用`DidEndLiveResize`事件以仅后收到通知用户已完成更改窗口的大小。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>设置窗口的标题和表示文件

使用表示文档的 windows 时`NSWindow`已`DocumentEdited`属性，如果设置为`true`关闭按钮来为用户提供指示该文件已被修改，并且应在关闭之前保存在显示一个小的点。

让我们编辑我们`ViewController.cs`文件，然后进行以下更改：

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

我们一直在监控`WillClose`上的窗口和正在检查的状态事件`DocumentEdited`属性。 如果它是`true`我们需要使用户能够将所做的更改保存到文件。 如果我们运行我们的应用程序，并输入一些文本，将显示点：

[![](window-images/file01.png "更改的窗口")](window-images/file01.png#lightbox)

如果我们尝试关闭窗口，我们将收到警报：

[![](window-images/file02.png "显示保存对话框")](window-images/file02.png#lightbox)

如果我们从文件加载文档我们可以将窗口的标题设置为文件的名称使用`window.SetTitleWithRepresentedFilename (Path.GetFileName(path));`方法 (假定`path`是一个字符串，表示所打开的文件)。 此外，我们可以设置文件使用的 URL`window.RepresentedUrl = url;`方法。

如果 URL 指向由操作系统确定的文件类型，将标题栏中显示的图标。 如果用户右键单击该图标，将显示文件的路径。

让我们编辑`AppDelegate.cs`文件，并添加以下方法：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = true;
    dlg.CanChooseDirectories = false;

    if (dlg.RunModal () == 1) {
        // Nab the first file
        var url = dlg.Urls [0];

        if (url != null) {
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

现在如果我们运行我们的应用程序，请选择**打开...** 从**文件**菜单中，选择从文本文件**打开**对话框框，并将其打开：

[![](window-images/file03.png "打开的对话框")](window-images/file03.png#lightbox)

将显示该文件并将使用的文件图标设置标题：

[![](window-images/file04.png "加载的文件的内容")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>向项目添加一个新的窗口

主文档窗口中，除了 Xamarin.Mac 应用程序可能需要向用户，如首选项或检查器面板显示其他类型的窗口。

若要添加新的窗口，请执行以下操作：

1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件以打开在 Xcode 的 Interface Builder 中进行编辑。
2. 将一个新**窗口控制器**从**库**拖放到**设计图面**:

    [![](window-images/new01.png "在库中选择新的窗口控制器")](window-images/new01.png#lightbox)
3. 在中**标识检查器**，输入`PreferencesWindow`有关**情节提要 ID**: 

    [![](window-images/new02.png "设置情节提要 ID")](window-images/new02.png#lightbox)
5. 设计你的接口： 

    [![](window-images/new03.png "设计 UI")](window-images/new03.png#lightbox)
6. 打开应用菜单 (`MacWindows`)，选择**首选项...**，单击并拖动到新的窗口： 

    [![](window-images/new05.png "创建 segue")](window-images/new05.png#lightbox)
7. 选择**显示**弹出菜单中。
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

如果我们运行代码，并选择**首选项...** 从**应用程序菜单**，此时将显示窗口：

[![](window-images/new04.png "示例首选项菜单")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>使用面板

如本文开头所述，一个面板浮动其他窗口之上，并提供工具或当文档打开时，用户可使用的控件。 

就像任何其他类型的创建和使用 Xamarin.Mac 应用程序中，过程基本上是窗口的相同：

1. 向项目添加新的窗口中定义。
2. 双击`.xib`文件以打开该窗口设计在 Xcode 的 Interface Builder 中进行编辑。
2. 在中设置任何所需的窗口的属性**属性检查器**并**大小检查器**。
4. 在构建您的接口和配置中对其所需的控件中拖动**属性检查器**。
5. 使用**大小检查器**来处理 UI 元素的大小调整。
6. 公开给 C# 代码通过窗口的 UI 元素**输出口**并**操作**。
7. 保存所做的更改并切换回 Visual Studio for Mac 与 Xcode 同步。

在中**属性检查器**，可以使用以下特定于面板的选项：

[![](window-images/panel03.png "属性检查器")](window-images/panel03.png#lightbox)

- **样式**-，可以调整从面板的样式：正则面板 （如下所示的标准窗口）、 实用工具面板 （具有较小的标题栏），面板中的 HUD (是半透明和标题栏是在后台的一部分)。
- **非激活**-中确定面板成为关键的窗口。
- **记录模式**-如果文档模式，面板将仅上浮动在应用程序的 windows，否则将浮动最重要的是。


若要添加一个新的面板，请执行以下操作：

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新文件...**.
2. 在新文件对话框中，选择**Xamarin.Mac** > **与控制器的 Cocoa 窗口**:

    [![](window-images/panels00.png "添加新的窗口控制器")](window-images/panels00.png#lightbox)
3. 对“名称”输入 `DocumentPanel`，然后单击“新建”按钮。
4. 双击`DocumentPanel.xib`文件以打开 Interface Builder 中为进行编辑： 

    [![](window-images/new02.png "编辑面板")](window-images/new02.png#lightbox)
5. 删除现有的窗口并将从面板拖**库检查器**中**界面编辑器**: 

    [![](window-images/panels01.png "删除现有的窗口")](window-images/panels01.png#lightbox)
6. 最多挂钩面板**文件的所有者** - **窗口** - **插座**: 

    [![](window-images/panels02.png "将拖到面板的网络")](window-images/panels02.png#lightbox)
7. 切换到**标识检查器**和设置的面板类为`DocumentPanel`: 

    [![](window-images/panels03.png "设置面板的类")](window-images/panels03.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。
7. 编辑`DocumentPanel.cs`文件，并更改为以下类定义： 

    `public partial class DocumentPanel : NSPanel`
8. 保存对文件所做的更改。

编辑`AppDelegate.cs`文件，然后进行`DidFinishLaunching`方法看起来像以下：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

如果我们运行我们的应用程序，将显示在面板：

[![](window-images/panels04.png "正在运行的应用中的面板")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> 面板 Windows apple 已弃用，应使用替换**检查器接口**。 有关创建的完整示例**Inspector**在 Xamarin.Mac 应用中，请参阅我们[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/)示例应用程序。

<a name="Summary" />

## <a name="summary"></a>总结

本文已在 Xamarin.Mac 应用程序中使用 Windows 和面板的详细的信息。 我们看到了不同类型，并使用的 Windows 和面板、 如何创建和维护 Windows 和 Xcode 的 Interface Builder 中的面板和如何使用 C# 代码中的 Windows 和面板。

## <a name="related-links"></a>相关链接

- [MacWindows （示例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector （示例）](https://developer.xamarin.com/samples/mac/MacInspector/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [与菜单一起使用](~/mac/user-interface/menu.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
