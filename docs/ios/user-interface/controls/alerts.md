---
title: 在 Xamarin.iOS 中显示的警报
description: 本文档介绍如何通过使用看到在 iOS 8 中引入的 Api 在 Xamarin.iOS 中显示警报。
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6071381daa7eedf4fa4b076ea60f2748865cf002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896206"
---
# <a name="displaying-alerts-in-xamarinios"></a>在 Xamarin.iOS 中显示的警报

从 iOS 8 开始，看到具有已完成替换的 UIActionSheet 和 UIAlertView 这两个其中现已弃用。

看到与替换，即子类 UIView 类的 UIViewController 子类。

使用`UIAlertControllerStyle`来指示要显示的警报类型。 这些警报类型包括：

- **UIAlertControllerStyleActionSheet**
    * 预 iOS 8 它将是 UIActionSheet
- **UIAlertControllerStyleAlert**
    * 预 iOS 8 它将是 UIAlertView 

有三个必要步骤，才能创建警报控制器：

- 创建和使用答： 配置警报
    * 标题
    * 消息
    * preferredStyle
    
- （可选）添加文本字段
- 添加所需的操作
- 显示视图控制器

最简单的警报包含一个按钮，在此屏幕截图所示：

 ![警报一个按钮](alerts-images/alert1.png)

若要显示的简单警报的代码如下所示：

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

显示警报的多个选项，以类似的方式完成，但将添加两个操作。 例如，下面的屏幕截图显示了具有两个按钮的警报：

 ![ 警报两个按钮](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

警报还可以显示操作工作表，类似于下面的屏幕截图：

 ![操作工作表警报](alerts-images/alert3.png)

按钮添加到与警报`AddAction`方法：

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/Controls/)
- [警报控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
