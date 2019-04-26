---
title: 在 Xamarin.iOS 中创建用户界面对象
description: 本文档概述了如何在 Xamarin.iOS 中创建用户界面。 此外，介绍了 iOS 设计器中，Xcode Interface Builder C#，和情节提要。
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a4cf63b84d0686bc28b02b18a6266908251bdf6f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153971"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>在 Xamarin.iOS 中创建用户界面对象

Apple 相关的片段组合到"框架"等同于 Xamarin.iOS 的命名空间的功能。 `UIKit` 是包含适用于 iOS 的所有用户界面控件的命名空间。

每当你的代码需要引用用户界面控件，如标签或按钮，请记住要包含以下 using 语句：

```csharp
using UIKit;
```

这一章中所述的所有控件都均在 UIKit 命名空间，并且每个用户控件类名的`UI`前缀。

你可以编辑 UI 控件和布局三种方式：

-  **[Xamarin iOS 设计器](~/ios/user-interface/designer/index.md)** -使用 Xamarin 的内置布局设计器设计屏幕。 双击情节提要或要使用内置设计器编辑的 XIB 文件。
-  **Xcode Interface Builder** – 将控件拖到屏幕的布局与 Interface Builder。 在 Xcode 中打开的情节提要或 XIB 文件，通过右键单击该文件中的**Solution Pad** ，然后选择**打开方式 > Xcode Interface Builder**。
-  **使用C#**  – 控件还可以是以编程方式使用代码构造并添加到的视图层次结构。

可以通过右键单击 iOS 项目，然后选择添加新的情节提要和 XIB 文件**添加 > 新建文件...**.

无论您使用哪种方法控制属性和事件操作与C#应用程序逻辑中。

## <a name="using-xamarin-ios-designer"></a>使用 Xamarin iOS 设计器

若要开始在 iOS 设计器中创建用户界面，双击情节提要文件。 可以将控件拖动到设计图面上，从**工具箱**如下图所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "工具箱面板")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "工具箱面板-Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

当在设计图面上选择控件**Properties Pad**将显示该控件的属性。 **小组件 > 标识 > 名称**字段，下面的屏幕截图中填充，则用作*Outlet*名称。 这是如何引用中的控件C#:

 [![](creating-ui-objects-images/image3b.png "属性小组件面板")](creating-ui-objects-images/image3b.png#lightbox)

有关使用 iOS 设计器的更深入了解，请参阅[iOS 设计器简介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="using-xcode-interface-builder"></a>使用 Xcode Interface Builder

如果您不熟悉使用 Interface Builder，请参阅 Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/)文档。

若要在 Xcode 中打开演示图板，右键单击要访问情节提要文件的上下文菜单并选择要使用打开**Xcode Interface Builder**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "情节提要上下文菜单的 Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "情节提要上下文菜单的 Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

控件可以拖动到设计图面上从上**对象库**如下图所示：

 [![](creating-ui-objects-images/image5a.png "Xcode 对象库")](creating-ui-objects-images/image5a.png#lightbox)

使用 Interface Builder 必须创建 UI 的设计时**输出口**你想要在中引用的每个控件的C#。 这是通过开启**助手编辑器**使用中心**编辑器**Xcode 工具栏按钮上的按钮：

 [![](creating-ui-objects-images/image6a.png "助手编辑器按钮")](creating-ui-objects-images/image6a.png#lightbox)

单击用户界面对象;然后**控件拖动**到.h 文件。 到 * * 控件拖动 * *，按下控件键然后单击并按住通过用户界面对象，您要为创建插座 （或操作）。 在拖动到标头文件时，保留按下控件键。 完成下面拖动`@interface`定义。 下面的屏幕截图中所示，标题插入电源插座或电源插座集合应显示一条蓝线。

当你释放系统会提示提供电源插座，用于创建一个名称单击C#可以在代码中引用的属性：

 [![](creating-ui-objects-images/image8a.png "创建输出口")](creating-ui-objects-images/image8a.png#lightbox)

有关如何 Xcode 的 Interface Builder 集成与 Visual Studio for Mac 的详细信息，请参阅[Xib 代码生成](~/ios/internals/xib-code-generation.md#generated)文档。

##  <a name="using-c"></a>使用C#

如果您决定以编程方式创建一个用户界面对象使用C#（在视图或视图控制器），请执行以下步骤：

-  声明的用户界面对象的类级字段。 创建控件本身中一次，`ViewDidLoad`为例。 然后可以在整个生命周期方法的视图控制器 （例如引用的对象。
`ViewWillAppear`）格式模式中出现的位置生成。
-  创建`CGRect`，它定义控件 (其 X 和 Y 坐标对屏幕上，以及其宽度和高度） 的框架。 将需要确保您具有`using CoreGraphics`此指令。
-  调用构造函数来创建和分配该控件。
-  设置任何属性或事件处理程序。
-  调用`Add()`控件添加到的视图层次结构。

下面是创建的简单示例`UILabel`在视图控制器中使用C#:

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

视图控制器添加到设计图面上，两个相对应的时C#项目中创建文件。 在此示例中，`ControlsViewController.cs`和`ControlsViewController.designer.cs`自动创建：

 [![](creating-ui-objects-images/image9b.png "ViewController 分部类")](creating-ui-objects-images/image9b.png#lightbox)

`MainViewController.cs`文件仅供*代码*。 这就是`View`生命周期方法，如`ViewDidLoad`和`ViewWillAppear`实现可以添加你自己的属性、 字段和方法。

`ControlsViewController.designer.cs`生成的代码包含一个分部类。 名称在设计上的控件出现在 Visual Studio for Mac，或创建时的电源插座或操作的 Xcode、 相应的属性或分部方法中，添加到设计器 (designer.cs) 文件。 下面的代码演示的两个按钮和文本视图中，生成的代码示例，其中的一个按钮还具有`TouchUpInside`事件。

分部类的这些元素可使代码引用的控件和响应在设计图面声明的操作：

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

`designer.cs`不应手动编辑文件 – IDE (Visual Studio for Mac 或 Visual Studio) 负责与情节提要使其保持同步。

当以编程方式为添加用户界面对象`View`或`ViewController`、 实例化和管理的对象引用，并因此没有设计器文件是必需的。



## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
