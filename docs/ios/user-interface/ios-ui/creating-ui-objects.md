---
title: 在 Xamarin 中创建用户界面对象
description: 本文档概述了如何在 Xamarin 中创建用户界面。 它讨论了 iOS 设计器、Xcode Interface Builder C#、和情节提要。
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 58a1fd68dda2216a62fe6f30cf61d6d2ec7d40d5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003673"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin 中创建用户界面对象

Apple 将相关功能部分分组到与 Xamarin 命名空间相同的 "框架" 中。 `UIKit` 是包含适用于 iOS 的所有用户界面控件的命名空间。

只要代码需要引用用户界面控件（如标签或按钮），请记住包含以下 using 语句：

```csharp
using UIKit;
```

本章中讨论的所有控件都在 UIKit 命名空间中，每个用户控件类名都有 `UI` 前缀。

可以通过以下三种方式编辑 UI 控件和布局：

- **[Xamarin IOS 设计器](~/ios/user-interface/designer/index.md)** –使用 Xamarin 的内置布局设计器设计屏幕。 双击 "情节提要" 或 "XIB 文件" 以利用内置设计器进行编辑。
- **Xcode Interface Builder** -将控件拖动到 Interface Builder 的屏幕布局。 右键单击 " **Solution Pad** " 中的文件，然后选择 "**打开方式" > Xcode Interface Builder**，在 Xcode 中打开情节提要或 XIB 文件。
- **使用C#**  –还可以使用代码以编程方式构造控件，并将其添加到视图层次结构中。

可以通过右键单击 iOS 项目，然后选择 "**添加 > 新文件 ...** " 来添加新的情节提要和 XIB 文件。

无论使用哪种方法，控件属性和事件都可以C#在应用程序逻辑中进行操作。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

若要开始在 iOS 设计器中创建用户界面，请双击情节提要文件。 控件可从**工具箱**拖到设计图面上，如下所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Toolbox Pad")](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Toolbox Pad - Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)

-----

在设计图面上选择控件时， **Properties Pad**将显示该控件的特性。 在下面的屏幕截图中填充的**小组件 > 标识 > Name** "字段，用作*插座*名称。 这是您可以在中C#引用控件的方式：

 [![](creating-ui-objects-images/image3b.png "Properties Widget Pad")](creating-ui-objects-images/image3b.png#lightbox)

有关使用 iOS 设计器的更深入了解，请参阅[Ios 设计器简介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode Interface Builder

如果不熟悉如何使用 Interface Builder，请参阅 Apple 的[Interface Builder](https://developer.apple.com/xcode/interface-builder/)文档。

若要在 Xcode 中打开情节提要，请右键单击以访问情节提要文件的上下文菜单，然后选择使用**Xcode Interface Builder**打开：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控件可以从如下所示的**对象库**拖动到 Design Surface：

 [![](creating-ui-objects-images/image5a.png "Xcode Object Library")](creating-ui-objects-images/image5a.png#lightbox)

使用 Interface Builder 设计 UI 时，必须为要在中 C#引用的每个控件创建一个插座。 这是通过使用 Xcode 工具栏按钮上的 "中心**编辑器**" 按钮打开**助手编辑器**来完成的：

 [![](creating-ui-objects-images/image6a.png "Assistant Editor button")](creating-ui-objects-images/image6a.png#lightbox)

单击用户界面对象;然后，**控制拖动**到 .h 文件中。 若要**控制拖动**，请按住 ctrl 键，然后单击并按住你要为其创建插座的用户界面对象。 在拖动到标头文件中时，请按住 Ctrl 键。 完成拖动 `@interface` 定义。 将显示一条带有标题插入插座或插座的蓝线，如以下屏幕截图中所示。

当你发布单击时，系统将提示你提供插座的名称，此名称将用于创建可在代码中C#引用的属性：

 [![](creating-ui-objects-images/image8a.png "Creating an outlet")](creating-ui-objects-images/image8a.png#lightbox)

有关 Xcode Interface Builder 如何与 Visual Studio for Mac 集成的详细信息，请参阅[Xib 代码生成](~/ios/internals/xib-code-generation.md#generated)文档。

## <a name="using-c"></a>使用 C\#

如果你决定以编程方式使用C#创建用户界面对象（例如，在视图或视图控制器中），请执行以下步骤：

- 为用户界面对象声明类级别的字段。 在 `ViewDidLoad` 示例中，创建一次控件本身。 然后，可以在视图控制器的整个生命周期方法中引用该对象（例如
`ViewWillAppear`) 格式模式中出现的位置匹配。
- 创建一个 `CGRect`，它定义控件的框架（屏幕上的 X 和 Y 坐标以及其宽度和高度）。 你需要确保为此提供 `using CoreGraphics` 指令。
- 调用构造函数来创建和分配控件。
- 设置任何属性或事件处理程序。
- 调用 `Add()` 将控件添加到视图层次结构。

下面是在视图控制器中创建 `UILabel` 的简单示例，使用C#：

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>使用C#和情节提要

将视图控制器添加到 Design Surface 时，将在项目C#中创建两个对应的文件。 在此示例中，已自动创建 `ControlsViewController.cs` 和 `ControlsViewController.designer.cs`：

 [![](creating-ui-objects-images/image9b.png "ViewController partial class")](creating-ui-objects-images/image9b.png#lightbox)

`ControlsViewController.cs` 文件适用于你的*代码*。 这是实现 `View` 生命周期方法（如 `ViewDidLoad` 和 `ViewWillAppear`）的位置，并且可以添加自己的属性、字段和方法。

`ControlsViewController.designer.cs` 生成包含分部类的代码。 在 Visual Studio for Mac 中的设计图面上命名控件，或在 Xcode 中创建一个插座或操作时，会将相应的属性或分部方法添加到设计器（designer.cs）文件中。 下面的代码演示了一个为两个按钮和一个文本视图生成的代码示例，其中其中一个按钮也有一个 `TouchUpInside` 事件。

利用分部类的这些元素，你的代码可以引用控件并响应设计图面上声明的操作：

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

不应手动编辑 `designer.cs` 文件– IDE （Visual Studio for Mac 或 Visual Studio）负责使其与情节提要保持同步。

以编程方式将用户界面对象添加到 `View` 或 `ViewController` 时，你会自行实例化和管理对象引用，因此不需要设计器文件。

## <a name="related-links"></a>相关链接

- [控件（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
