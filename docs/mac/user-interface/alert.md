---
title: 在 Xamarin.Mac 中的警报
description: 本文介绍如何使用 Xamarin.Mac 应用程序中的警报。 它描述如何创建和显示的警报C#代码和响应用户交互。
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6545b1423b809e42293302baf3eba9521848edc1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61237415"
---
# <a name="alerts-in-xamarinmac"></a>在 Xamarin.Mac 中的警报

_本文介绍如何使用 Xamarin.Mac 应用程序中的警报。它描述如何创建和显示的警报C#代码和响应用户交互。_

当使用C#和.NET 中的 Xamarin.Mac 应用程序中，您可以访问相同警报使用的开发人员*Objective C*和*Xcode* does。 

警报是一种特殊的 （如错误） 出现严重问题时，将显示的对话框或作为警告 （如正在准备删除文件）。 由于警报是一个对话框，它还需要用户响应前可以关闭它。

[![](alert-images/alert06.png "示例警报")](alert-images/alert06.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用警报的基础知识。 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>警报简介

警报是一种特殊的 （如错误） 出现严重问题时，将显示的对话框或作为警告 （如正在准备删除文件）。 由于警报会中断用户，因为用户可以继续在其任务之前必须关闭它们，避免显示警报，除非绝对必要。

Apple 建议以下准则：

- 不要使用警报只是为了要为用户提供的信息。
- 不会显示一个警报，以便通用、 可撤消的操作。 即使这种情况下可能会导致数据丢失。
- 如果值得发送警报的情况下，避免使用任何其他 UI 元素或方法来显示它。
- 警告图标应谨慎使用。
- 警报消息中清晰和简洁地描述警报的情况。
- 默认按钮名称应对应于在警报消息中描述的操作。

有关详细信息，请参阅[警报](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1)部分中的 Apple 的[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>警报的剖析

如上面所述，到应用程序的用户时出现严重问题，或者作为警告可能导致丢失数据 （例如，关闭未保存的文件），应显示警报。 在 Xamarin.Mac、 警报创建在C#代码，例如：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

上面的代码与应用程序图标叠加显示在警告图标、 标题、 一条警告消息和一个显示一条警报**确定**按钮：

[![](alert-images/alert01.png "警报的确定按钮")](alert-images/alert01.png#lightbox)

Apple 提供了可用于自定义警报的多个属性：

- **AlertStyle**定义警报的类型为以下值之一：
    - **警告**-用于警告用户并不重要的当前或即将发生的事件。 这是默认样式。
    - **信息性**-用于警告用户当前或即将发生的事件。 目前，没有之间没有明显的差异**警告**和一个**信息**
    - **关键**-用于警告用户即将发生的事件 （如删除文件） 导致严重后果。 应谨慎使用此类警报。
- **MessageText** -这是主消息或警报的标题，应快速向用户定义这种情况。
- **InformativeText** -这是的警报清楚地定义这种情况和向用户提供可操作的选项的位置的正文。
- **图标**-允许向用户显示的自定义图标。
- **HelpAnchor** & **ShowsHelp** -允许要绑定到应用程序 HelpBook 和显示帮助信息警报的警报。
- **按钮**-默认情况下，警报只具有**确定**按钮，但**按钮**集合可用于添加根据需要更多选择。
- **ShowsSuppressionButton** -如果`true`显示一个复选框，用户可用于禁止显示警报。 触发它的事件的后续匹配项。
- **AccessoryView** -可以将另一个子视图附加到该通知以提供额外信息，例如，添加**文本字段**数据条目。 如果将一个新**AccessoryView**或者修改现有，需要调用`Layout()`方法来调整警报的可见的布局。

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>显示警报

有两种不同方法，警报可以显示、 自由浮动或工作表的形式。 下面的代码显示作为自由浮动的警报：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
如果运行此代码，则将显示以下：

[![](alert-images/alert02.png "简单的警报")](alert-images/alert02.png#lightbox)

下面的代码显示为一个工作表的同一个警报：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

如果运行此代码时，下面将显示：

[![](alert-images/alert03.png "工作表的形式显示警报")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>使用警报按钮

默认情况下，显示一条警报仅**确定**按钮。 但是，您不限于，您可以通过追加到创建额外的按钮**按钮**集合。 下面的代码将创建使用自由浮动警报**确定**，**取消**并**也许**按钮：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

添加第一个按钮将处于_默认按钮_如果用户按 Enter 键，则将激活的。 返回的值将是一个整数，表示该按钮按下的用户。 在我们的示例将返回以下值：

- **确定**-1000年。
- **取消**-1001年。
- **也许**-1002年。

如果我们运行此代码，下面将显示：

[![](alert-images/alert04.png "包含三个按钮选项警报")](alert-images/alert04.png#lightbox)

下面是工作表的形式相同的警报的代码：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}", result);
});
```
如果运行此代码时，下面将显示：

[![](alert-images/alert05.png "工作表的形式显示了三个按钮警报")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> 永远不应超过三个按钮添加到警报。

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>显示取消按钮

如果警报`ShowSuppressButton`属性是`true`，警报将显示一个复选框，用户可用于禁止显示警报。 触发它的事件的后续匹配项。 下面的代码显示带有取消按钮的自由浮动警报：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

如果的值`alert.SuppressionButton.State`是`NSCellStateValue.On`，用户已选中禁止复选框，否则它们不具有时。

如果运行代码时，下面将显示：

[![](alert-images/alert06.png "带有取消按钮警报")](alert-images/alert06.png#lightbox)

下面是工作表的形式相同的警报的代码：

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果运行此代码时，下面将显示：

[![](alert-images/alert07.png "工作表的形式显示带有取消按钮警报")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>添加自定义的子视图

警报具有`AccessoryView`属性，可用于自定义警报进一步并添加诸如**文本字段**用户输入。 下面的代码将创建与已添加的文本输入字段的自由浮动警报：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

此处的关键行是`var input = new NSTextField (new CGRect (0, 0, 300, 20));`这将创建一个新**文本字段**，我们将添加警报。 `alert.AccessoryView = input;` 哪些附加**文本字段**警报和调用`Layout()`方法，需要调整大小以适应新的子视图中的警报。

如果我们运行此代码，下面将显示：

[![](alert-images/alert08.png "如果我们运行此代码，下面将显示")](alert-images/alert08.png#lightbox)

下面是作为一个工作表的同一个警报：

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

如果我们运行此代码，下面将显示：

[![](alert-images/alert09.png "具有自定义视图的警报")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>总结

本文已采取了 Xamarin.Mac 应用程序中使用警报的详细的信息。 我们已了解不同的类型和使用警报、 如何创建和自定义警报和如何使用中的警报的C#代码。

## <a name="related-links"></a>相关链接

- [MacWindows （示例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
