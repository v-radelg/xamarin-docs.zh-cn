---
title: 在 Xamarin.Mac 中创建自定义控件
description: 本文档介绍如何构建在 Xamarin.Mac 中的自定义控件。 它演示如何生成自定义控件、 跟踪其状态、 绘制其界面、 响应用户输入和应用程序中使用控件。
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 015c1e315b6070777542a8f8c5871c00cf336b5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61236042"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>在 Xamarin.Mac 中创建自定义控件

当使用C#和.NET 中的 Xamarin.Mac 应用程序中，您可以访问同一个用户控件的开发人员*Objective C*， *Swift*和*Xcode*does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护用户控件 （或选择通过 C# 代码中直接创建）。

虽然 macOS 提供了丰富的内置用户控件，可能需要创建自定义控件提供功能不提供的框，或以匹配自定义 UI 主题 （如游戏的接口） 的时间。

[![](custom-controls-images/intro01.png "自定义 UI 控件的示例")](custom-controls-images/intro01.png#lightbox)

在本文中，我们将介绍在 Xamarin.Mac 应用程序中创建可重用的自定义用户界面控件的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>自定义控件简介

如上所述，可能是您需要创建一个可重用，自定义用户界面控件提供的独特功能的 Xamarin.Mac 应用程序的 UI 或创建自定义 UI 主题 （如游戏的接口）。

在这些情况下，您可以轻松地从继承`NSControl`并创建一个自定义工具，可以将其添加到你的应用的 UI，通过 C# 代码或通过 Xcode 的 Interface Builder。 通过继承自`NSControl`自定义控件将自动拥有的所有内置用户界面控件具有标准功能 (如`NSButton`)。

如果自定义用户界面控件只是显示信息 （如自定义图表和图形工具），你可能想要从继承`NSView`而不是`NSControl`。

无论使用哪一基类，用于创建自定义控件的基本步骤是相同的。

在此项目将创建自定义的翻转开关组件提供唯一的用户界面主题和构建完全正常运行的自定义用户界面控件的示例。

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>生成自定义控件

由于我们要创建的自定义控件将响应用户输入 （鼠标按钮单击），我们将从继承`NSControl`。 这样一来，我们自定义控件将自动拥有所有内置用户界面控件具有的标准功能和响应方式类似于标准 macOS 控件。

在 Visual Studio for Mac 中，打开你想要创建的自定义用户界面控件 （或创建一个新） 在 Xamarin.Mac 项目。 添加一个新类并调用其`NSFlipSwitch`:

[![](custom-controls-images/custom01.png "添加新类")](custom-controls-images/custom01.png#lightbox)

接下来，编辑`NSFlipSwitch.cs`类，并使其看起来如下所示：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

第一件事要注意我们自定义的类，因为我们继承自`NSControl`并使用**注册**命令来公开此类到 OBJECTIVE-C 和 Xcode 的 Interface Builder:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在以下部分中，我们将查看详细信息中的上述代码的其余部分。

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>跟踪控件的状态

由于我们的自定义控件是一个开关，我们需要一种方法来跟踪开关打开/关闭状态。 我们使用下面的代码来处理的`NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

交换机的状态更改时，我们需要一种方法来更新 UI。 我们会通过强制控件重绘其 UI `NeedsDisplay = true`。

如果我们的控件需要更多的单个开/关状态 （例如具有 3 个位置的多状态交换机），可以使用我们**枚举**来跟踪状态。 对于本示例中，一个简单**bool**将执行操作。

我们还添加了一个帮助器方法来交换打开和关闭之间切换的状态：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

更高版本，我们将扩展此帮助器类，以通知调用方的开关状态已更改。

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>绘制控件的接口

我们将使用核心图形绘制例程来绘制在运行时的自定义控件的用户界面。 我们可以执行此操作之前，我们需要打开我们的控件的图层。 我们使用下面的私有方法执行此操作：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

从每个控件的构造函数，以确保已正确配置该控件调用此方法。 例如：

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

接下来，我们需要重写`DrawRect`方法并添加绘制控件的核心图形例程：

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

我们将会调整控件的可视表示其状态更改时 (例如从**上**到**关闭**)。 只要当状态发生更改，我们可以使用`NeedsDisplay = true`命令来强制控件重绘的新状态。

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>响应用户输入

有两个基本的方法，我们可以添加自定义控件的用户输入：**重写鼠标处理例程**或**笔势识别器**。 我们使用哪种方法将基于我们的控件所需的功能。

> [!IMPORTANT]
> 对于你创建的任何自定义控件，您应使用**重写方法**_或_**手势识别器**，但不是能同时在同一时间，他们可以相互冲突。

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>处理用户输入与重写方法

继承的对象`NSControl`(或`NSView`) 有多个重写方法的处理鼠标或键盘输入。 对于我们示例中的控件，我们想要翻转的状态之间切换**上**并**关闭**当用户单击鼠标左键在控件上。 我们可以添加以下重写方法到`NSFlipSwitch`类来处理这种情况：

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

在上述代码中，我们调用`FlipSwitchState`方法 （上面定义） 来翻转 On/Off 状态中的交换机`MouseDown`方法。 这将强制控件重绘以反映当前状态。

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>处理手势识别器与用户输入

（可选） 可以使用手势识别器来处理与控件交互的用户。 删除前面添加的替代，请编辑`Initialize`方法，并使其如下所示：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

在这里，我们将创建一个新`NSClickGestureRecognizer`并调用我们`FlipSwitchState`方法，以便当用户单击它与鼠标左键时更改开关的状态。 `AddGestureRecognizer (click)`方法将笔势识别器添加到控件。

同样，我们使用哪种方法取决于我们尝试使用我们自定义控件实现。 如果我们需要低级别的访问权限的用户交互，使用重写方法。 如果我们需要预定义的功能，如鼠标单击使用手势识别器。

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>响应状态更改事件

当用户更改自定义控件的状态时，我们需要一种方法，以响应在代码中的状态更改 (如执行这样的操作时的自定义按钮单击)。 

若要提供此功能，编辑`NSFlipSwitch`类，并添加以下代码：

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null) 
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

接下来，编辑`FlipSwitchState`方法，并使其如下所示：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我们提供`ValueChanged`事件，我们可以添加到一个处理程序在 C# 代码，以便我们可以执行的操作，当用户更改的交换机的状态。

第二个，因为我们自定义控件继承自`NSControl`，它自动具有**操作**，可以在 Xcode 的 Interface Builder 中分配。 若要将此称为**操作**时的状态更改，我们使用以下代码：

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，我们检查以查看是否**操作**已分配给控件。 接下来，我们调用**操作**如果已定义。

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>使用自定义控件

与我们完全定义的自定义控件，我们可以或者将其添加到我们的 Xamarin.Mac 应用的 UI 使用 C# 代码或 Xcode 的 Interface Builder 中。

若要添加使用 Interface Builder 的控件，请首先生成已清理的 Xamarin.Mac 项目中，然后双击`Main.storyboard`文件以进行编辑 Interface Builder 中打开它：

[![](custom-controls-images/custom02.png "编辑在 Xcode 中的情节提要")](custom-controls-images/custom02.png#lightbox)

接下来，将`Custom View`到用户界面设计：

[![](custom-controls-images/custom03.png "从库中选择自定义视图")](custom-controls-images/custom03.png#lightbox)

与自定义视图仍处于选中状态，切换到**标识检查器**并更改视图的**类**到`NSFlipSwitch`:

[![](custom-controls-images/custom04.png "设置视图的类")](custom-controls-images/custom04.png#lightbox)

切换到**助手编辑器**并创建**Outlet**自定义控件 (并确保将其在绑定`ViewController.h`文件，而不`.m`文件):

[![](custom-controls-images/custom05.png "配置新输出口")](custom-controls-images/custom05.png#lightbox)

保存所做的更改，返回到 Visual Studio for Mac，并允许同步这些更改。编辑`ViewController.cs`文件，然后进行`ViewDidLoad`方法看起来像以下：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
``` 

在这里，我们响应`ValueChanged`我们在上面定义的事件`NSFlipSwitch`类，并写出当前**值**当用户单击控件上。

（可选），我们可以返回到 Interface Builder，并定义**操作**在控件上：

[![](custom-controls-images/custom06.png "配置新的操作")](custom-controls-images/custom06.png#lightbox)

同样，编辑`ViewController.cs`文件，并添加以下方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您应使用两个**事件**或定义**操作**Interface Builder 中但不是应同时使用这两种方法或它们可以相互冲突。

<a name="Summary" />

## <a name="summary"></a>总结

本文已在 Xamarin.Mac 应用程序中创建可重用的自定义用户界面控件的详细的信息。 我们了解了如何绘制自定义控件 UI 中，两种主要方法来响应用户输入和鼠标以及如何公开到 Xcode 的 Interface Builder 中的操作的新控件。

## <a name="related-links"></a>相关链接

- [MacCustomControl （示例）](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [处理鼠标事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
