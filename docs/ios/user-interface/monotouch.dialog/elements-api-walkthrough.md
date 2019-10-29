---
title: 使用元素 API 创建 Xamarin iOS 应用程序
description: 本文基于 Monotouch.dialog 对话框简介一文中提供的信息。 其中提供了演示如何使用 Monotouch.dialog （MT）的演练。D）元素 API 快速开始使用 MT 构建应用程序。2-d.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2258b2e8451f896aff59c89478833976ef7086e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002369"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>使用元素 API 创建 Xamarin iOS 应用程序

_本文基于 Monotouch.dialog 对话框简介一文中提供的信息。其中提供了演示如何使用 Monotouch.dialog （MT）的演练。D）元素 API 快速开始使用 MT 构建应用程序。2-d._

在本演练中，我们将使用 MT。D 元素 API，用于创建显示任务列表的应用程序的主-详细信息样式。 当用户在导航栏中选择 " **+** " 按钮时，会将一个新行添加到该任务的表中。 选择该行将导航到 "详细信息" 屏幕，该屏幕允许更新任务描述和截止日期，如下所示：

[![](elements-api-walkthrough-images/01-task-list-app.png "Selecting the row will navigate to the detail screen that allows us to update the task description and the due date")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>设置 MT。2-d

隶书.D 与 Xamarin 一起分发。 若要使用此方法，请右键单击 Visual Studio 2017 或 Visual Studio for Mac 中的 Xamarin iOS 项目的 "**引用**" 节点，然后添加对**monotouch.dialog**程序集的引用。 然后，根据需要将 `using MonoTouch.Dialog` 语句添加到源代码中。

## <a name="elements-api-walkthrough"></a>元素 API 演练

在 " [monotouch.dialog" 对话框的简介](~/ios/user-interface/monotouch.dialog/index.md)一文中，我们已深入了解了 MT 的不同部分。2-d. 让我们使用元素 API 将它们合并到一个应用程序中。

## <a name="setting-up-the-multi-screen-application"></a>设置多屏幕应用程序

若要启动屏幕创建过程，请 Monotouch.dialog 创建 `DialogViewController`，然后添加 `RootElement`。

若要使用 Monotouch.dialog 创建多屏幕应用程序，我们需要：

1. 创建 `UINavigationController.`
1. 创建 `DialogViewController.`
1. 添加 `DialogViewController` 作为 `UINavigationController.` 的根 
1. 向 `DialogViewController.` 添加 `RootElement`
1. 向 `RootElement.` 添加 `Sections` 和 `Elements` 

### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要创建导航样式应用程序，我们需要创建一个 `UINavigationController`，然后将其添加为 `AppDelegate``FinishedLaunching` 方法中的 `RootViewController`。 为了使 `UINavigationController` 与 Monotouch.dialog 配合使用，我们将 `DialogViewController` 添加到 `UINavigationController`，如下所示：

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    _rootElement = new RootElement ("To Do List"){new Section ()};

    // code to create screens with MT.D will go here …

    _rootVC = new DialogViewController (_rootElement);
    _nav = new UINavigationController (_rootVC);
    _window.RootViewController = _nav;
    _window.MakeKeyAndVisible ();
            
    return true;
}
```

上面的代码创建 `RootElement` 的实例并将其传递到 `DialogViewController`中。 `DialogViewController` 在其层次结构的顶部始终有一个 `RootElement`。 在此示例中，创建了包含字符串 "待办事项列表" 的 `RootElement`，该字符串用作导航控制器导航栏中的标题。 此时，运行该应用程序会显示如下所示的屏幕：

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Running the application will present the screen shown here")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

让我们看看如何使用 Monotouch.dialog 的 `Sections` 和 `Elements` 的层次结构来添加更多屏幕。

### <a name="creating-the-dialog-screens"></a>创建对话框屏幕

`DialogViewController` 是 Monotouch.dialog 用于添加屏幕的 `UITableViewController` 子类。 Monotouch.dialog 通过向 `DialogViewController`中添加 `RootElement` 来创建屏幕，如前文所述。 `RootElement` 可以具有表示表各部分的 `Section` 实例。
部分由元素、其他部分甚至其他 `RootElements`组成。 通过嵌套 `RootElements`，Monotouch.dialog 会自动创建一个导航样式应用程序，如下所示。

### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

作为 `UITableViewController` 子类 `DialogViewController`，其 `UITableView` 为其视图。 在此示例中，每次点击 " **+** " 按钮时，我们都要向表中添加项。 由于 `DialogViewController` 已添加到 `UINavigationController`中，因此可以使用 `NavigationItem`的 `RightBarButton` 属性来添加 **+** 按钮，如下所示：

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

在前面创建 `RootElement` 时，我们向它传递了一个 `Section` 实例，以便可以在用户点击 **+** 按钮时添加元素。 我们可以使用以下代码在按钮的事件处理程序中完成此操作：

```csharp
_addButton.Clicked += (sender, e) => {                
    ++n;
                
    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
    var taskElement = new RootElement (task.Name) {
        new Section () {
            new EntryElement (task.Name, "Enter task description", task.Description)
        },
        new Section () {
            new DateElement ("Due Date", task.DueDate)
        }
    };
    _rootElement [0].Add (taskElement);
};
```

每次点击按钮时，此代码都将创建一个新的 `Task` 对象。 下面演示了 `Task` 类的简单实现：

```csharp
public class Task
{   
    public Task ()
    {
    }
      
    public string Name { get; set; }
        
    public string Description { get; set; }

    public DateTime DueDate { get; set; }
}
```

该任务的 `Name` 属性用于创建 `RootElement`的标题以及一个名为 `n` 的计数器变量，该变量对于每个新任务递增。 添加每个 `taskElement` 时，Monotouch.dialog 会将元素转换为添加到 `TableView` 中的行。

## <a name="presenting-and-managing-dialog-screens"></a>呈现和管理对话框屏幕

我们使用了 `RootElement`，这样 Monotouch.dialog 就会自动为每个任务的详细信息创建一个新屏幕，并在选择行时导航到该屏幕。

"任务详细信息" 屏幕本身由两部分组成：其中每个部分都包含单个元素。 第一个元素是从 `EntryElement` 创建的，可为任务的 `Description` 属性提供可编辑的行。 选择该元素后，将显示一个键盘进行文本编辑，如下所示：

 [![](elements-api-walkthrough-images/03-create-task.png "When the element is selected, a keyboard for text editing is presented as shown")](elements-api-walkthrough-images/03-create-task.png#lightbox)

第二部分包含一个 `DateElement`，它使我们能够管理任务的 `DueDate` 属性。 选择日期会自动加载日期选取器，如下所示：

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecting the date automatically loads a date picker as")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

在 `EntryElement` 和 `DateElement` 情况（或 Monotouch.dialog 中的任何数据输入元素）中，对值所做的任何更改都将自动保留。 我们可以通过编辑日期，然后在根屏幕和各种任务详细信息之间来回导航来对此进行演示，详细信息屏幕中的值将保留在其中。

## <a name="summary"></a>总结

本文介绍了如何使用 Monotouch.dialog 元素 API 的演练。 其中介绍了使用 MT 创建多屏幕应用程序的基本步骤。D. 包括如何使用 `DialogViewController` 以及如何添加元素和节来创建屏幕。 此外，它还演示了如何使用 MT。D 与 `UINavigationController`结合使用。

## <a name="related-links"></a>相关链接

- [MTDWalkthrough （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Monotouch.dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Github 上的 Monotouch.dialog 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
