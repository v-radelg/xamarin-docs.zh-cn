---
title: 使用在 Xamarin.Mac 中的情节提要
description: 本文档介绍如何使用 Xamarin.Mac，检查如何加载从代码、 视图控制器生命周期、 响应方链中的情节提要，segue，窗口控制器、 手势识别器和的详细信息。
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e24b448bedc60a537bfcd4a5bfbdbe9562163818
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865932"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>使用在 Xamarin.Mac 中的情节提要

情节提要定义所有分解为其视图控制器的功能概述给定应用程序的 UI。 在 Xcode 的 Interface Builder 中，每个这些控制器位于其自己的场景中。

[![](indepth-images/intro01.png "在 Xcode 的 Interface Builder 情节提要")](indepth-images/intro01.png#lightbox)

情节提要是一个资源文件 (扩展名为`.storyboard`) 编译和发布时，获取包含在 Xamarin.Mac 应用捆绑包。 若要定义您的应用程序的起始情节提要，对其进行编辑的`Info.plist`文件，然后选择**主界面**从下拉列表框： 

[![](indepth-images/sb01.png "Info.plist 编辑器")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>从代码加载

可能需要从代码加载特定的情节提要，并手动创建视图控制器的时间。 下面的代码可用于执行此操作：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

`FromName`加载情节提要文件具有给定名称的已包含在应用的捆绑包。 `InstantiateControllerWithIdentifier`创建具有给定标识的视图控制器实例。 设计用户界面时，可以在 Xcode 的 Interface Builder 中设置标识：

[![](indepth-images/sb02.png "设置情节提要 ID")](indepth-images/sb02.png#lightbox)

（可选） 可以使用`InstantiateInitialController`方法以加载已分配 Interface Builder 中的初始控制器将视图控制器：

[![](indepth-images/sb03.png "设置初始控制器")](indepth-images/sb03.png#lightbox)

通过标记**情节提要入口点**和更高版本的开放结束箭头。

<a name="View-Controllers" />

## <a name="view-controllers"></a>视图控制器

视图控制器定义给定的视图中的 Mac 应用的信息和提供该信息的数据模型之间的关系。 在情节提要中的每个顶级场景都表示在 Xamarin.Mac 应用的代码中的一个视图控制器。

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>视图控制器生命周期

几个新方法已添加到`NSViewController`类，以支持在 macOS 中的情节提要。 最重要的是将以下方法用于响应视图受控制由给定的视图控制器的生命周期：

- `ViewDidLoad` -从情节提要文件加载视图时，调用此方法。
- `ViewWillAppear` -此方法称为之前在屏幕上显示的视图。
- `ViewDidAppear` -已在屏幕上显示的视图后，将直接调用此方法。
- `ViewWillDisappear` -该视图从屏幕上删除之前，调用此方法。
- `ViewDidDisappear` -从屏幕中删除该视图后，将直接调用此方法。
- `UpdateViewConstraints` -定义视图的约束自动布局位置和大小需要更新时，调用此方法。
- `ViewWillLayout` -此视图的子视图上屏幕的布局之前，调用此方法。
- `ViewDidLayout` -视图的子视图上屏幕的布局后，将直接调用此方法。

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>响应方链

此外，`NSViewControllers`现在是窗口的一部分_响应方链_:

[![](indepth-images/vc01.png "响应方链")](indepth-images/vc01.png#lightbox)

并且这种情况下都是有线向上来接收和响应事件，例如剪切、 复制和粘贴菜单项选择。 允许此自动视图控制器布置仅出现在 macOS Sierra (10.12) 上运行的应用和更高版本。

<a name="Containment" />

### <a name="containment"></a>包含

在情节提要，视图控制器 （如拆分视图控制器和选项卡视图控制器） 现在可以实现_包容_，以便它们可以"包含"其他子视图控制器：

[![](indepth-images/vc02.png "视图控制器包含一个示例")](indepth-images/vc02.png#lightbox)

子视图控制器包含方法和属性以将其绑定到其父视图控制器和用于显示和删除从屏幕的视图。

MacOS 中内置的所有容器视图控制器都有一个特定的布局的 Apple 建议根据是否创建你自己的自定义容器视图控制器：

[![](indepth-images/vc03.png "视图控制器布局")](indepth-images/vc03.png#lightbox)

集合视图控制器包含其中每个包含一个或多个视图控制器包含其自己的视图的集合视图项的数组。

<a name="Segues" />

## <a name="segues"></a>Segue

转入提供所有定义应用程序的 UI 的场景之间的关系。 如果你熟悉在情节提要中在 iOS 中工作，您知道 Segue 的 iOS 通常定义全屏视图之间的转换。 这不同于 macOS、 segues 设计通常定义时"[包容](#Containment)"，其中一个场景是父级场景的子级。

在 macOS，大多数应用程序往往组一起使用 UI 元素，例如拆分视图和选项卡相同时段内的其视图。 与 iOS 不同，其中视图需要打开和关闭屏幕过渡，由于有限物理显示空间。

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>演示文稿 Segue

给定包含向 macOS 的倾向，有些情况下， _Presentation Segue_使用，如模式 Windows、 表视图和 Popovers。 macOS 提供以下内置 segue 类型：

- **显示**-为非模式窗口中显示的 Segue 的目标。 例如，使用这种类型的 Segue 以显示应用程序中文档窗口的另一个实例。
- **模式**-显示一个模式窗口作为 Segue 的目标。 例如，使用这种类型的 Segue 以显示您的应用程序首选项窗口。
- **表**-会产生 Segue 的目标，因为一个工作表附加到父窗口。 例如，使用这种类型的 segue 以显示查找和替换表。
- **弹出框**-如弹出框窗口中显示的 Segue 的目标。 例如，使用此 Segue 类型显示的选项，当用户单击 UI 元素。
- **自定义**-提供使用由开发人员定义的自定义的 Segue 类型 Segue 的目标。 请参阅[创建自定义 Segue](#Creating-Custom-Segues)部分获取更多详细信息。

如果使用演示文稿 Segue，则可以覆盖`PrepareForSegue`的演示文稿来初始化和变量的父视图控制器方法并提供到显示视图控制器的任何数据。

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>触发 Segue

触发 segues 设计，可以指定命名 segues 设计 (通过其**标识符**Interface Builder 中的属性) 并将它们触发事件，例如用户单击按钮或通过调用`PerformSegue`中代码的方法：

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

将布局应用程序的 UI 时，在 Xcode 的 Interface Builder 定义 Segue ID:

[![](indepth-images/sg02.png "输入 Segue 名称")](indepth-images/sg02.png#lightbox)

在充当源的 Segue 视图控制器，应重写`PrepareForSegue`显示方法和执行之前执行 Segue，所需的任何初始化和指定的视图控制器：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

（可选） 可以重写`ShouldPerformSegue`方法，并控制是否通过实际执行 SegueC#代码。 对于手动提供视图控制器，调用其`DismissController`方法以在不再需要时从显示中删除它们。

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>创建自定义 Segue

可能你的应用时需要由定义在 macOS 中内置的 segues 设计不提供的 Segue 类型的时间。 如果是这样，您可以创建可以分配自定义 Segue 在 Xcode 的 Interface Builder 中布局应用程序的 UI 时。

例如，若要创建新的 Segue 类型替换当前的视图控制器 （而不是在新窗口中打开目标场景） 窗口中，我们可以使用以下代码：

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

几个要注意的事项：

- 我们将使用`Register`属性来公开此类目的-C/macOS。
- 我们将重写`Perform`方法来实际执行我们自定义 Segue 的操作。
- 我们要替换的窗口`ContentViewController`Segue 的目标 （目标） 定义的一个控制器。
- 我们将删除原始的视图控制器，以释放内存使用`RemoveFromParentViewController`方法。

若要在 Xcode 的 Interface Builder 中使用这个新的 Segue 类型，我们需要以编译该应用程序第一次，然后切换到 Xcode，通过新之间添加 Segue 两个场景。 设置**样式**到**自定义**并**Segue 类**到`ReplaceViewSegue`（我们自定义的 Segue 类的名称）：

[![](indepth-images/sg01.png "设置 Segue 类")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>窗口控制器

窗口控制器包含和控制可以创建 macOS 应用的不同窗口类型。 情节提要，它们具有以下功能：

1. 他们必须提供内容的视图控制器。 这将是相同的内容视图控制器具有子窗口。
2. `Storyboard`属性将包含其他从中加载窗口控制器的情节提要`null`如果未从情节提要加载。
3. 您可以调用`DismissController`方法来关闭给定的窗口并从视图中删除它。

窗口控制器视图与控制器一样，实现`PerformSegue`，`PrepareForSegue`和`ShouldPerformSegue`方法，可以用作 Segue 操作的源。

窗口控制器负责 macOS 应用的以下功能：

- 他们管理的特定窗口。
- 他们管理窗口的标题栏和工具栏 （如果可用）。
- 他们管理内容的视图控制器，以显示窗口的内容。

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>笔势识别器

适用于 macOS 的手势识别器在 iOS 中的对应到几乎完全相同，并允许开发人员可以轻松地添加手势 （如单击鼠标按钮） 到您的应用 UI 中的元素。

但是，其中在 iOS 中的笔势取决于应用程序的设计 （如点击两根手指与屏幕），最手势 macOS 中的由硬件。

通过使用手势识别器，可以极大地减少在 UI 中的项添加自定义交互所需的代码量。 因为它们可以自动确定 double 和 single 点击之间，请单击并拖动事件，等等。

而不是替代`MouseDown`视图控制器中的事件，则应当使用笔势识别器来处理用户输入的事件时使用情节提要。

MacOS 中提供了以下手势识别器：

- `NSClickGestureRecognizer` -注册鼠标 down 和 up 事件。
- `NSPanGestureRecognizer` 将按下鼠标左键、 拖动和发布事件。
- `NSPressGestureRecognizer` -注册为给定的时间事件内按住鼠标按钮。
- `NSMagnificationGestureRecognizer` -注册触控板硬件中的放大倍数事件。
- `NSRotationGestureRecognizer` -注册触控板硬件中的旋转事件。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用情节提要引用

情节提要引用，可采用庞大而复杂的情节提要设计并将其分割为较小的情节提要获取引用从原始，从而消除复杂性，并将生成单独的情节提要设为更轻松地设计和维护。

另外，还可提供情节提要引用_定位点_到同一个情节提要或上一个不同的特定场景中的另一个场景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>引用外部情节提要

若要添加到外部情节提要的引用，请执行以下操作：

1. 在中**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**  >  **Mac** > **情节提要**。 输入**名称**作为新情节提要，然后单击**新建**按钮： 

    [![](indepth-images/ref01.png "添加新的情节提要")](indepth-images/ref01.png#lightbox)
2. 在中**解决方案资源管理器**，双击要打开在 Xcode 的 Interface Builder 中进行编辑的新情节提要名称。
3. 设计新情节提要的场景的布局，因为通常类似于并保存所做的更改： 

    [![](indepth-images/ref02.png "设计界面")](indepth-images/ref02.png#lightbox)
4. 切换到要在 Interface Builder 中添加对引用的情节提要。
5. 拖动**引用的情节提要**从**对象库**拖到设计图面： 

    [![](indepth-images/ref03.png "在库中选择的情节提要引用")](indepth-images/ref03.png#lightbox)
6. 在中**属性检查器**，选择的名称**情节提要**上面创建的： 

    [![](indepth-images/ref04.png "配置引用")](indepth-images/ref04.png#lightbox)
7. 单击在 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**刚创建的。  从弹出菜单中选择**显示**完成 Segue: 

    [![](indepth-images/ref06.png "设置 Segue 类型")](indepth-images/ref06.png#lightbox) 
8. 将所做的更改保存到情节提要。
9. 返回到 Visual Studio for Mac 进行同步所做的更改。

将显示运行该应用并在用户单击 UI 元素从来自外部的情节提要的情节提要引用中指定的初始窗口控制器创建 Segue 时。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>引用外部情节提要中的特定场景

要添加对特定场景的引用外部情节提要，（而不是初始的窗口控制器），执行以下操作：

1. 在中**解决方案资源管理器**，双击外部情节提要中，打开在 Xcode 的 Interface Builder 中进行编辑。
2. 添加新的场景，并像通常那样设计其布局： 

    [![](indepth-images/ref07.png "设计在 Xcode 中的布局")](indepth-images/ref07.png#lightbox)
3. 在中**标识检查器**，输入**情节提要 ID**新场景的窗口控制器： 

    [![](indepth-images/ref08.png "设置情节提要 ID")](indepth-images/ref08.png#lightbox)
4. 打开要在 Interface Builder 中添加对引用的情节提要。
5. 拖动**引用的情节提要**从**对象库**拖到设计图面： 

    [![](indepth-images/ref03.png "从库中选择的情节提要引用")](indepth-images/ref03.png#lightbox)
6. 在中**标识检查器**，选择的名称**情节提要**并**引用 ID** (情节提要 ID) 的场景的上述步骤中创建的： 

    [![](indepth-images/ref09.png "设置引用 ID")](indepth-images/ref09.png#lightbox)
7. 单击在 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**刚创建的。 从弹出菜单中选择**显示**完成 Segue: 

    [![](indepth-images/ref06.png "设置 Segue 类型")](indepth-images/ref06.png#lightbox) 
8. 将所做的更改保存到情节提要。
9. 返回到 Visual Studio for Mac 进行同步所做的更改。

从场景创建 Segue 的 UI 元素时在应用正在运行且用户单击给定**情节提要 ID**将显示从情节提要引用中指定外部情节提要。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>引用同一个情节提要中的特定场景

若要添加到特定场景中相同的情节提要的引用，请执行以下操作：

1. 在中**解决方案资源管理器**，双击将其打开进行编辑的情节提要。
2. 添加新的场景，并像通常那样设计其布局： 

    [![](indepth-images/ref11.png "编辑在 Xcode 中的情节提要")](indepth-images/ref11.png#lightbox)
3. 在中**标识检查器**，输入**情节提要 ID**新场景的窗口控制器： 

    [![](indepth-images/ref12.png "设置情节提要 ID")](indepth-images/ref12.png#lightbox)
4. 拖动**引用的情节提要**从**工具箱**拖到设计图面： 

    [![](indepth-images/ref03.png "从库中选择的情节提要引用")](indepth-images/ref03.png#lightbox)
5. 在中**属性检查器**，选择**引用 ID** (情节提要 ID) 的场景的上述步骤中创建的： 

    [![](indepth-images/ref13.png "设置引用 ID")](indepth-images/ref13.png#lightbox)
6. 单击在 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**刚创建的。 从弹出菜单中选择**显示**完成 Segue: 

    [![](indepth-images/ref06.png "选择 Segue 类型")](indepth-images/ref06.png#lightbox) 
7. 将所做的更改保存到情节提要。
8. 返回到 Visual Studio for Mac 进行同步所做的更改。

从场景创建 Segue 的 UI 元素时在应用正在运行且用户单击给定**情节提要 ID**将显示在相同的情节提要的情节提要引用中指定。

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>复杂的情节提要示例

有关使用演示图板 Xamarin.Mac 应用中的工作的复杂示例，请参阅[Sourcewriter 示例](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

## <a name="related-links"></a>相关链接

- [MacStoryboard （示例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
