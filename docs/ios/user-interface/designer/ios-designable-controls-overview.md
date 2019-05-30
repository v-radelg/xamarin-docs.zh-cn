---
title: IOS 的 Xamarin 设计器中的自定义控件
description: 用于 iOS 的 Xamarin 设计器支持在项目中创建或引用从外部源，如 Xamarin Component Store 呈现自定义控件。
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 267c465fbc10468e70e39831e4f47a4a87f36d00
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66353988"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>IOS 的 Xamarin 设计器中的自定义控件

_用于 iOS 的 Xamarin 设计器支持在项目中创建或引用从外部源，如 Xamarin Component Store 呈现自定义控件。_

用于 iOS 的 Xamarin 设计器是用于可视化应用程序的用户界面的强大工具，并提供 WYSIWYG 编辑对大多数 iOS 视图和视图控制器的支持。 您的应用程序也可能包含扩展的内置到 iOS 的自定义控件。 如果记住的一些指导原则编写这些自定义控件，它们可以也由呈现 iOS 设计器中，提供更丰富的编辑体验。 本文档介绍了这些指导原则。

## <a name="requirements"></a>要求

将在设计图面上呈现控件，可满足以下要求：

1.  它是直接或间接的子类[UIView](xref:UIKit.UIView)或[UIViewController](xref:UIKit.UIViewController)。 其他[NSObject](xref:Foundation.NSObject)子类将显示为设计图面上的图标。
2.  它具有[RegisterAttribute](xref:Foundation.RegisterAttribute)以将其公开到 Objective-c。
3.  它具有[所需的 IntPtr 构造函数](~/ios/internals/api-design/index.md)。
4.  它可以实现[IComponent](xref:System.ComponentModel.IComponent)接口或具有[DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute)设置为 True。

其包含的项目编译为模拟器时，在设计器中将显示在代码中定义的满足上述要求的控件。 默认情况下，所有自定义控件将出现在**自定义组件**一部分**工具箱**。 但是， [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute)可以应用到自定义控件的类，以指定的其他部分。

在设计器不支持加载第三方 OBJECTIVE-C 的库。

## <a name="custom-properties"></a>自定义属性

声明由自定义控件的属性将显示在属性面板中，如果满足以下条件：

1.  该属性具有公共 getter 和 setter。
1.  该属性具有[ExportAttribute](xref:Foundation.ExportAttribute)以及一个[BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute)设置为 True。
1.  属性类型是数值类型、 枚举类型、 字符串、 布尔值， [SizeF](xref:System.Drawing.SizeF)， [UIColor](xref:UIKit.UIColor)，或[UIImage](xref:UIKit.UIImage)。 可能在将来扩展这一系列受支持的类型。


该属性还可以用修饰[DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute)指定为其显示在属性面板的标签。

## <a name="initialization"></a>初始化

有关`UIViewController`子类，则应使用[ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad)取决于在设计器中创建的视图的代码的方法。

有关`UIView`和其他`NSObject`子类， [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib)方法是从布局文件加载后执行自定义控件的初始化的建议的位置。 这是因为当运行时控件的构造函数，但会将它们设置之前，在属性面板中设置任何自定义属性将不会设置`AwakeFromNib`调用：


```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

如果该控件还设计为直接通过代码创建，你可能想要创建具有常见的初始化代码，此类的方法：

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>属性初始化和 AwakeFromNib

上的时间和位置初始化并不会覆盖已在 iOS 设计器内设置的值的自定义组件中的可设计的属性应格外小心。 例如，执行以下代码：

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {
    
    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

`CustomView`组件公开`Counter`可以由 iOS 设计器内开发人员设置的属性。 但是，无论在设计器的值设置哪些值`Counter`属性将始终为零 (0)。 原因是：

-  实例`CustomControl`从情节提要文件进行扩充。
-  在 iOS 设计器中修改任何属性设置 (例如，设置的值`Counter`到两 （2)，例如)。
-  `AwakeFromNib`执行方法以及调用组件的`Initialize`方法。
-  内部`Initialize`的值`Counter`属性将重置为零 (0)。


若要解决上述情况下，初始化`Counter`属性在其他位置 （例如，在组件的构造函数） 或不会覆盖`AwakeFromNib`方法并调用`Initialize`如果组件不需要任何进一步的初始化外部内容当前正在处理通过其构造函数。

## <a name="design-mode"></a>设计模式

在设计图面上，自定义控件必须遵守一些限制：

-  应用捆绑包资源不在设计模式下可用。 通过加载时提供了映像[UIImage 方法](xref:UIKit.UIImage)。
-  不应在设计模式下执行异步操作，例如 web 请求。 设计图面上不支持动画或控件的 UI 的任何其他异步更新。


可以实现自定义控件[IComponent](xref:System.ComponentModel.IComponent)并用[DesignMode](xref:System.ComponentModel.ISite.DesignMode)属性来检查是否为设计图面上。 在此示例中，标签会显示"设计模式"在设计图面上和"运行时"在运行时：

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null && Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

应始终检查`Site`属性`null`然后再尝试访问的任何成员。 如果`Site`是`null`，则可以安全地假定该控件不在设计器中运行时。
在设计模式下`Site`控件的构造函数已运行之后，并在将设置`AwakeFromNib`调用。

## <a name="debugging"></a>调试

满足上述要求的控件将显示在工具箱，并呈现图面上。
如果不呈现控件时，检查该控件或其某个依赖项中的 bug。

设计图面上通常可以捕获由单独的控件继续呈现其他控件时引发的异常。 有故障的控件将被替换为一个红色的占位符，并可以通过单击感叹号图标查看异常跟踪：

 ![](ios-designable-controls-overview-images/exception-box.png "有故障控件作为红色占位符和异常详细信息")

如果调试符号是可用于该控件，跟踪必须文件的名称和行号。 双击行中的堆栈跟踪将跳转到该行中的源代码。

如果在设计器不能确定故障的控制，一条警告消息将显示在设计图面上的顶部：

 ![](ios-designable-controls-overview-images/info-bar.png "在设计图面顶部一条警告消息")

修复或从设计图面中删除发生故障的控件时，将恢复完整呈现。

## <a name="summary"></a>总结

本文介绍了创建和自定义控件在 iOS 设计器中的应用程序。 首先，它描述控件必须满足的设计图面上呈现和公开自定义属性属性面板中的要求。 它然后介绍了代码隐藏的控件和 DesignMode 属性的初始化。 最后介绍会发生什么情况时引发的异常以及如何解决此问题。