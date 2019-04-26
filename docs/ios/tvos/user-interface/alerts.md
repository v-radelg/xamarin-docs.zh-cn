---
title: 使用 tvOS 在 Xamarin 中的警报
description: 本文档介绍如何使用 Xamarin 中的 tvOS 警报。 它讨论了显示的警报，添加文本字段和一个帮助器类。
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 13c5ae3fac76ec1ec1a0ade135d5919403066226
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409786"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>使用 tvOS 在 Xamarin 中的警报

_本文介绍如何使用看到 Xamarin.tvOS 中向用户显示一条警告消息。_

如果需要获取 tvOS 用户的关注或要求执行破坏性操作 （如删除文件） 的权限，可以显示警报消息使用`UIAlertViewController`:

[![](alerts-images/alert01.png "示例 UIAlertViewController")](alerts-images/alert01.png#lightbox)

如果除了显示一条消息，则可以添加按钮和文本字段到警报，以允许用户响应操作并提供反馈。

<a name="About-Alerts" />

## <a name="about-alerts"></a>有关警报

如上面所述，警报用于获取用户的注意力并通知他们你的应用或请求反馈的状态。 警报必须提供一个标题，它们可根据需要一条消息和一个或多个按钮或文本字段。

[![](alerts-images/alert04.png "示例警报")](alerts-images/alert04.png#lightbox)

Apple 已使用警报的以下建议：

- **警报尽可能少地使用**-警报会中断应用程序的用户的流和中断用户体验并在这种情况下，应仅用于重要的情况下，例如错误通知、 应用内购买和破坏性操作。 
- **提供了有用的选择**-如果警报选项显示给用户，你应确保每个选项提供关键信息，并提供了有用的用户来执行操作。

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>警报的标题和消息

Apple 具有以下建议来显示警报的标题和可选的消息：

- **使用 Multiword 标题**-警报的标题应获取跨清楚地同时仍保持简单的情况的点。 单独的词标题极少能够提供足够的信息。
- **使用不需要一条消息的描述性标题**-只要有可能，请考虑将足够的可选消息文本不是所需的警报的标题描述性。 
- **使消息较短的完整句子**-可选的消息是否需要先获取跨警报的点，使其尽可能简单，使其与正确的大写和标点完整句子。

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>警报按钮

Apple 具有对于将按钮添加到警报的以下建议：

- **限制到两个按钮**-只要有可能，限制为两个按钮的最大值警报。 单个按钮警报提供的信息，但任何操作。 两个按钮警报提供了一个简单的操作选择的是/否。
- **使用 Succinct，逻辑按钮标题**-简单清晰地描述该按钮的操作的一到两个 word 按钮标题效果最佳。 有关详细信息，请参阅我们[使用按钮](~/ios/tvos/user-interface/buttons.md)文档。
- **清楚地标记破坏性按钮**-对于执行破坏性操作 （如删除文件） 的按钮清楚地将它们标记`UIAlertActionStyle.Destructive`样式。

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>显示警报

若要显示警报，你创建的实例`UIAlertViewController`和配置可以添加操作 （按钮） 并选择警报的样式。 例如，以下代码将显示确定 / 取消警报：

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...
        
var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

让我们看看此代码中详细信息。 首先，我们使用给定的标题和消息创建新的警报：

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

接下来，我们想要在警报中显示每个按钮为我们创建了操作定义按钮、 其样式和我们希望按下按钮时要采取的操作的标题：

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

`UIAlertActionStyle`枚举允许您将按钮的样式设置为以下值之一：

- **默认**-按钮为默认按钮处于选中状态时显示警报。
- **取消**-按钮是警报的取消按钮。
- **破坏性**-突出显示该按钮，以破坏性操作，如删除文件的形式。 目前，tvOS 呈现具有红色背景的破坏性按钮。

`AddAction`方法将添加到给定的操作`UIAlertViewController`最后`PresentViewController (alertController, true, null)`方法为用户显示给定的警报。

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>添加文本字段

除了向警报添加操作 （按钮），可以将文本字段添加到警报，以允许用户填写信息，例如用户 Id 和密码：

[![](alerts-images/alert02.png "在警报中的文本字段")](alerts-images/alert02.png#lightbox)

如果用户选择该文本字段，将显示标准 tvOS 键盘，使他们能够为字段输入一个值：

[![](alerts-images/alert03.png "输入文本")](alerts-images/alert03.png#lightbox)

下面的代码显示用于输入一个值，确定/取消警报具有单个文本字段：

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

`AddTextField`方法将新的文本字段添加到可以通过设置属性，如占位符文本 （此字段为空时显示的文本）、 默认文本值和键盘的类型来配置警报。 例如：

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

以便我们可以处理的文本字段更高版本的值，我们也节省了一份使用以下代码：

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

用户在文本字段中输入一个值后，我们可以使用`field`变量访问该值。

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>警报视图控制器帮助程序类

因为显示简单、 常见类型的警报使用`UIAlertViewController`可能会导致大量的重复代码，可以使用一个帮助器类来减少重复代码。 例如：

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

使用此类、 显示和响应的简单警报可以按如下所示：

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了使用`UIAlertController`Xamarin.tvOS 中向用户显示一条警告消息。 首先，它介绍了如何显示的简单警报，然后添加按钮。 接下来，它介绍了如何将文本字段添加到警报。 最后，它介绍了如何使用一个帮助器类以减少所需显示警报的重复代码。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
