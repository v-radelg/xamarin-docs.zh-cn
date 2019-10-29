---
title: Xamarin Designer for iOS 中的自定义控件
description: Xamarin Designer for iOS 支持呈现在你的项目中创建的自定义控件或从 Xamarin 组件存储等外部源进行引用。
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: e8c38ec407d13a99e2990a6d4cf39b5a23728b1d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003978"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Xamarin Designer for iOS 中的自定义控件

_Xamarin Designer for iOS 支持呈现在你的项目中创建的自定义控件或从 Xamarin 组件存储等外部源进行引用。_

Xamarin Designer for iOS 是一种功能强大的工具，可用于可视化应用程序的用户界面，并为大多数 iOS 视图和查看控制器提供 WYSIWYG 编辑支持。 您的应用程序可能还包含扩展内置于 iOS 的自定义控件。 如果使用几个准则编写这些自定义控件，则 iOS 设计器也可以呈现这些控件，从而提供更丰富的编辑体验。 本文档将介绍这些准则。

## <a name="requirements"></a>要求

满足以下所有要求的控件将在设计图面上呈现：

1. 它是[UIView](xref:UIKit.UIView)或[UIViewController](xref:UIKit.UIViewController)的直接或间接子类。 其他[NSObject](xref:Foundation.NSObject)子类将在设计图面上显示为图标。
2. 它有一个[RegisterAttribute](xref:Foundation.RegisterAttribute) ，可将其公开给目标-C。
3. 它具有[所需的 IntPtr 构造函数](~/ios/internals/api-design/index.md)。
4. 它要么实现[IComponent](xref:System.ComponentModel.IComponent)接口，要么将[DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute)设置为 True。

如果代码中定义的控件满足上述要求，则在设计器中，当为模拟器编译其包含项目时，它们将显示在设计器中。 默认情况下，所有自定义控件将出现在 "**工具箱**" 的 "**自定义组件**" 部分中。 但是，可以将[CategoryAttribute](xref:System.ComponentModel.CategoryAttribute)应用到自定义控件的类，以指定不同的部分。

设计器不支持加载第三方目标-C 库。

## <a name="custom-properties"></a>自定义属性

如果满足以下条件，自定义控件所声明的属性将显示在属性面板中：

1. 属性具有公共 getter 和 setter。
1. 属性具有[ExportAttribute](xref:Foundation.ExportAttribute)以及设置为 True 的[BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) 。
1. 属性类型为数值类型、枚举类型、字符串、布尔值、 [SizeF](xref:System.Drawing.SizeF)、 [UIColor](xref:UIKit.UIColor)或[UIImage](xref:UIKit.UIImage)。 将来可能会扩展此支持类型的列表。

还可以使用[DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute)来修饰属性，以指定在属性面板中为其显示的标签。

## <a name="initialization"></a>初始化

对于 `UIViewController` 子类，应为依赖于在设计器中创建的视图的代码使用[ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad)方法。

对于 `UIView` 和其他 `NSObject` 子类，建议使用[AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib)方法在从布局文件中加载自定义控件后执行自定义控件的初始化。 这是因为在运行控件的构造函数时，不会设置属性面板中设置的任何自定义属性，但在调用 `AwakeFromNib` 之前将设置这些属性：

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

如果控件还设计为直接从代码创建，则可能要创建一个具有常见初始化代码的方法，如下所示：

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

应注意在自定义组件中初始化可设计属性的时间和位置，因为不覆盖已在 iOS 设计器中设置的值。 例如，采用以下代码：

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

`CustomView` 组件公开了一个 `Counter` 属性，开发人员可在 iOS 设计器中对其进行设置。 但是，无论在设计器中设置什么值，`Counter` 属性的值将始终为零（0）。 原因是：

- 将从情节提要文件中放大 `CustomControl` 的实例。
- 设置在 iOS 设计器中修改的任何属性（例如，将 `Counter` 的值设置为2（2））。
- 执行 `AwakeFromNib` 方法，并调用该组件的 `Initialize` 方法。
- 在 `Initialize` 将 `Counter` 属性的值重置为零（0）。

若要修复上述情况，请在其他位置（例如，在组件的构造函数中）初始化 `Counter` 属性，或者不重写 `AwakeFromNib` 方法 `Initialize`，如果组件要求在当前由其构造函数处理。

## <a name="design-mode"></a>设计模式

在设计图面上，自定义控件必须遵循几个限制：

- 应用捆绑资源在设计模式下不可用。 在通过[UIImage 方法](xref:UIKit.UIImage)加载时，图像可用。
- 异步操作（如 web 请求）不应在设计模式下执行。 设计图面不支持动画或对控件的 UI 的任何其他异步更新。

自定义控件可以实现[IComponent](xref:System.ComponentModel.IComponent) ，并使用[DesignMode](xref:System.ComponentModel.ISite.DesignMode)属性来检查它是否位于设计图面上。 在此示例中，标签会在运行时显示设计图面上的 "设计模式" 和 "运行时"：

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

应该始终检查 `null` 的 `Site` 属性，然后再尝试访问其任何成员。 如果 `null``Site`，则可以安全地假定该控件未在设计器中运行。
在设计模式下，将在运行控件的构造函数之后、调用 `AwakeFromNib` 之前设置 `Site`。

## <a name="debugging"></a>调试

满足以上要求的控件将显示在工具箱中，并呈现在图面上。
如果控件未呈现，则检查控件中的 bug 或其依赖项之一。

设计图面通常可以捕获各个控件引发的异常，同时继续呈现其他控件。 错误的控件被替换为红色占位符，你可以通过单击感叹号图标来查看异常跟踪：

 ![](ios-designable-controls-overview-images/exception-box.png "A faulty control as red placeholder and the exception details")

如果调试符号可用于控件，则跟踪将包含文件名和行号。
双击堆栈跟踪中的某一行将跳转到源代码中的该行。

如果设计器无法隔离出错的控件，则会在设计图面顶部显示一条警告消息：

 ![](ios-designable-controls-overview-images/info-bar.png "A warning message at the top of the design surface")

当错误的控件已修复或从设计图面中删除时，将恢复完全呈现。

## <a name="summary"></a>总结

本文介绍了如何在 iOS 设计器中创建和应用自定义控件。 首先，它描述了控件必须满足的要求，才能在设计图面上呈现并公开属性面板中的自定义属性。 然后，它会查看控件的初始化代码和 DesignMode 属性。 最后，它描述了引发异常时所发生的情况以及如何解决此问题。
