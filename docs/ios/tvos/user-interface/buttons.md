---
title: 使用 tvOS 在 Xamarin 中的按钮
description: 本文档介绍如何使用构建使用 Xamarin 的 tvOS 应用程序中的按钮。 它讨论了如何使用按钮在代码中和在情节提要，和它探讨如何设置按钮样式。
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/07/2017
ms.openlocfilehash: 6d8fc1daaced24dccead78c4f9d0e5d0959b3755
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61198977"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>使用 tvOS 在 Xamarin 中的按钮

使用的实例`UIButton`类，以在 tvOS 窗口中创建一个可获得焦点，可选择按钮。 当用户选择一个按钮时，它将操作消息发送到目标对象允许您对用户进行 Xamarin.tvOS 应用响应的输入。

[![](buttons-images/buttons01.png "示例按钮")](buttons-images/buttons01.png#lightbox)

有关使用焦点和使用 Siri 远程导航的详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)并[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

<a name="About-Buttons" />

## <a name="about-buttons"></a>有关按钮

在 tvOS 按钮用于特定于应用的操作，并可能包含一个标题、 一个图标或两者。 当用户导航应用程序的用户界面使用[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，焦点将移动到给定按钮使其更改文本和背景色。 阴影将也应用于添加 3D 效果，从而使其显示用户界面的其余部分上方上升的按钮。

[![](buttons-images/buttons01.png "示例按钮")](buttons-images/buttons01.png#lightbox)

Apple 具有以下建议以获得使用按钮：

- **使用一个标题或图标**-时这两个图标和标题可以包含一个按钮在名额有限，因此不尝试将组合起来。
- **清楚地标记破坏性按钮**-如果单击按钮执行破坏性操作 （如删除文件），清楚地将其文本和/或图标这种情况下使用标记。 应始终存在破坏性操作[警报](~/ios/tvos/user-interface/alerts.md)询问用户要将操作限制。
- **不使用返回按钮**-Siri 遥控器上的菜单按钮用于返回到上一屏幕。 此规则的一个例外是应用内购买或破坏性操作的位置**取消**应显示按钮。

有关使用焦点和导航的详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)文档。

<a name="Button-Icons" />

### <a name="button-icons"></a>按钮图标

Apple 建议为您按钮的图标使用简单、 辨识度极高的图像。 过于复杂图标很难在沙发上同一房间识别电视屏幕上，因此尝试使用的最简单的表示形式可以已领会了要旨。 只要有可能，使用标准，已知图标 （如搜索放大镜） 的映像。

<a name="Button-Titles" />

### <a name="button-titles"></a>按钮标题

为你的按钮创建标题时，Apple 将具有以下建议：

- **显示说明性文本下面的图标按钮**-如果可能，下面的图标的位置明确的描述性文本仅按钮添加到更多 get 跨按钮的用途。
- **用于标题的谓词或谓词短语**-清楚地状态将采取的操作时将在用户单击按钮。
- **使用标题样式的大小写作**-文章、 连词或介词除外 (四个字母或更少)，该按钮的标题的每个词应采用大写形式。
- **使用较短的点到标题**-使用最短可能措辞描述按钮的操作。

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>按钮和情节提要

使用 Xamarin.tvOS 应用中的按钮的最简单方法是将它们添加到使用 Xamarin 设计器用于 iOS 的应用程序的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**按钮**从**库**并将其放在视图上： 

    [![](buttons-images/storyboard01.png "一个按钮")](buttons-images/storyboard01.png#lightbox)
1. 在中**属性资源管理器**，您可以调整按钮的多个属性，如其**标题**并**文本颜色**: 

    [![](buttons-images/storyboard02.png "按钮属性")](buttons-images/storyboard02.png#lightbox)
1. 接下来，切换到**事件选项卡**和布置**事件**从**按钮**，并调用它`ButtonPressed`: 

    [![](buttons-images/storyboard03.png "事件选项卡")](buttons-images/storyboard03.png#lightbox)
1. 您将自动切换到`ViewController.cs`其中放置新的操作在代码中的视图**向上**并**下**箭头键： 

    [![](buttons-images/storyboard04.png "在代码中放置新的操作")](buttons-images/storyboard04.png#lightbox)
1. 按**Enter**若要选择的位置： 

    [![](buttons-images/storyboard05.png "在代码编辑器")](buttons-images/storyboard05.png#lightbox)
1. 将所做的更改保存到的所有文件。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**按钮**从**库**并将其放在视图上： 

    [![](buttons-images/storyboard01vs.png "一个按钮")](buttons-images/storyboard01vs.png#lightbox)
1. 在中**属性资源管理器**，您可以调整按钮的多个属性，如其**标题**并**文本颜色**: 

    [![](buttons-images/storyboard02vs.png "在属性资源管理器")](buttons-images/storyboard02vs.png#lightbox)
1. 接下来，切换到**事件选项卡**和布置**事件**从**按钮**，并调用它`ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "事件选项卡")](buttons-images/storyboard03vs.png#lightbox)
1. 将所做的更改保存到的所有文件。



编辑视图控制器 (示例`ViewController.cs`) 文件，并添加以下代码以处理按钮被选中：


```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

只要一个按钮`Enabled`属性是`true`和不受另一个控件或视图，它可以对使用 Siri 远程中焦点项。 如果用户选择按钮并单击 Touch 面上，`ButtonPressed`会执行上面定义的操作。

> [!IMPORTANT]
> 虽然可以将分配操作，如`TouchUpInside`到`UIButton`ios 设计器创建时**事件处理程序**，它将永远不会调用，因为 Apple TV 没有触摸屏输入屏幕上或支持触控事件。 应始终使用默认值**操作类型**创建时**操作**tvOS 用户界面元素。




使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>按钮和代码

（可选）`UIButton`可以在中创建C#的代码并添加到 tvOS 应用的视图。 例如：

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

当您创建一个新`UIButton`在代码中，指定其`UIButtonType`为以下值之一：

- **系统**-这是标准的主讲人 tvOS 的按钮类型，是最常使用的类型。
- **DetailDisclosure** -显示"调低"的一种按钮用于隐藏或显示详细的信息。
- **InfoDark** -深灰色的详细信息按钮显示在一个圆周中的"i"。
- **InfoLight** -光的详细信息按钮显示在一个圆周中的"i"。
- **AddContact** -显示作为添加联系人按钮的按钮。
- **自定义**-可用于自定义按钮的多个特征。

接下来，定义屏幕大小和按钮的位置。 示例:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然后，设置按钮的标题。 `UIButtons` 不同于大多数`UIKit`控件，因为它们具有一种状态，以便只是不能更改的标题，则必须将其用于更改给定`UIControlState`。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下来，使用`AllEvents`事件以查看用户单击按钮。 示例:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最后，将按钮添加到视图以显示它：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> 虽然可以将分配操作，如`TouchUpInside`到`UIButton`，它将永远不会调用，因为 Apple TV 没有触摸屏输入屏幕上或支持触控事件。 始终应使用事件如**AllEvents**或**PrimaryActionTriggered**。




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>设置按钮样式

tvOS 提供的多个属性`UIButton`可用来提供其标题并设置样式与背景色和图像等内容。

<a name="Button-Titles" />

### <a name="button-titles"></a>按钮标题

正如我们看到的更高版本，`UIButtons`不同于大多数`UIKit`控件，因为它们具有一种状态，以便只是不能更改的标题，则必须将其用于更改给定`UIControlState`。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

可以设置按钮使用的标题颜色`SetTitleColor`方法。 例如：

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

您可以调整标题的阴影使用`SetTitleShadowColor`。 例如：

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

您可以设置从更改的标题阴影*凹进*到*阳文*当按钮突出显示时使用以下代码：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，您可以将特性化的文本用作按钮的标题。 例如：

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按钮图像

一个`UIButton`可以附加到它的图像并且可以使用作为其背景图像。

若要设置的背景图像的按钮的给定`UIControlState`，使用以下代码：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

设置`AdjustsImageWhenHiglighted`属性设置为`true`较浅图像绘制突出显示该按钮时 （这是默认值）。 设置`AdjustsImageWhenDisabled`属性设置为`true`按钮处于禁用状态时绘制图像变暗 （同样，这是默认值）。

若要设置在按钮上显示的图像，请使用以下代码：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

使用`TintColor`属性来设置应用于的标题和按钮的图像的颜色色调。 按钮`Custom`类型，则此属性不起作用，必须实现`TintColor`行为自己。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和使用 Xamarin.tvOS 应用内的按钮。 它演示了如何使用 iOS 设计器中的按钮以及如何创建中的按钮C#代码。 最后，它介绍了如何修改按钮的标题和更改其样式和外观。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
