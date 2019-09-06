---
title: 使用元素 API 创建 Xamarin iOS 应用程序
description: 本文基于 Monotouch.dialog 对话框简介一文中提供的信息。 其中提供了演示如何使用 Monotouch.dialog （MT）的演练。D）元素 API 快速开始使用 MT 构建应用程序。2-d.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: conceptdev
ms.author: crdun
ms.openlocfilehash: 61f214a7e072725bd7ba6cc4a3d493c8fcb909ff
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289307"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>使用元素 API 创建 Xamarin iOS 应用程序

_本文基于 Monotouch.dialog 对话框简介一文中提供的信息。其中提供了演示如何使用 Monotouch.dialog （MT）的演练。D）元素 API 快速开始使用 MT 构建应用程序。2-d._

在本演练中，我们将使用 MT。D 元素 API，用于创建显示任务列表的应用程序的主-详细信息样式。 当用户在导航栏 **+** 中选择按钮时，会将一个新行添加到该任务的表中。 选择该行将导航到 "详细信息" 屏幕，该屏幕允许更新任务描述和截止日期，如下所示：

[![](elements-api-walkthrough-images/01-task-list-app.png "选择该行将导航到 \"详细信息\" 屏幕，该屏幕允许更新任务描述和截止日期")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>设置 MT。2-d

隶书.D 与 Xamarin 一起分发。 若要使用此方法，请右键单击 Visual Studio 2017 或 Visual Studio for Mac 中的 Xamarin iOS 项目的 "**引用**" 节点，然后添加对**monotouch.dialog**程序集的引用。 然后，根据`using MonoTouch.Dialog`需要在源代码中添加语句。

## <a name="elements-api-walkthrough"></a>元素 API 演练

在 " [monotouch.dialog" 对话框的简介](~/ios/user-interface/monotouch.dialog/index.md)一文中，我们已深入了解了 MT 的不同部分。2-d. 让我们使用元素 API 将它们合并到一个应用程序中。

## <a name="setting-up-the-multi-screen-application"></a>设置多屏幕应用程序

若要启动屏幕创建过程，请 monotouch.dialog 创建一个`DialogViewController`，然后`RootElement`添加。

若要使用 Monotouch.dialog 创建多屏幕应用程序，我们需要：

1. 创建一个`UINavigationController.`
1. 创建一个`DialogViewController.`
1. `DialogViewController`添加作为的根`UINavigationController.` 
1. 将添加`RootElement`到`DialogViewController.`
1. 将`Sections` 和`Elements`添加到`RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要创建导航样式应用程序，我们`UINavigationController`需要创建，然后将其添加为的`FinishedLaunching`方法`AppDelegate`中`RootViewController`的。 若要使用 monotouch.dialog，请`DialogViewController`将添加到`UINavigationController` ，如下所示： `UINavigationController`

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

上面的代码创建一个`RootElement`实例，并将其传递`DialogViewController`到。 始终在其层次结构的顶部有一个`RootElement`。 `DialogViewController` 在此示例中， `RootElement`创建的字符串为 "待办事项列表"，它充当导航控制器导航栏中的标题。 此时，运行该应用程序会显示如下所示的屏幕：

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "运行应用程序将显示此处显示的屏幕")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

让我们看看如何使用 monotouch.dialog 的层次结构`Sections`并`Elements`添加更多屏幕。

### <a name="creating-the-dialog-screens"></a>创建对话框屏幕

是 monotouch.dialog 用于添加屏幕的子类。`UITableViewController` `DialogViewController` Monotouch.dialog 通过将添加`RootElement` `DialogViewController`到来创建屏幕，如前文所述。 `RootElement`可以具有`Section`表示表的各个部分的实例。
部分由元素、其他部分甚至其他`RootElements`部分组成。 嵌套`RootElements`后，monotouch.dialog 会自动创建一个导航样式的应用程序，如下所示。

### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

`DialogViewController` `UITableView`作为子类，具有作为其视图`UITableViewController` 。 在此示例中，我们希望在每次 **+** 点击按钮时向表中添加项。 `NavigationItem` **+** `RightBarButton`由于已将添加`UINavigationController`到中，因此我们可以使用的属性添加按钮，如下所示： `DialogViewController`

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

当我们前面创建`RootElement`了一个实例时，我们向`Section`它传递了单个实例，以便 **+** 可以在用户点击按钮时添加元素。 我们可以使用以下代码在按钮的事件处理程序中完成此操作：

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

每次点击按钮时`Task` ，此代码都将创建一个新的对象。 下面演示了`Task`类的简单实现：

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

任务的`Name`属性用于`RootElement`创建的标题以及名`n`为的计数器变量，每个新任务递增一次。 添加每个`taskElement`元素时，monotouch.dialog 将元素转换为添加到中`TableView`的行。

## <a name="presenting-and-managing-dialog-screens"></a>呈现和管理对话框屏幕

我们使用了`RootElement` ，这样 monotouch.dialog 就会自动为每个任务的详细信息创建一个新屏幕，并在选择行时导航到它。

"任务详细信息" 屏幕本身由两部分组成：其中每个部分都包含单个元素。 从`EntryElement`创建第一个元素，以便为任务的`Description`属性提供可编辑的行。 选择该元素后，将显示一个键盘进行文本编辑，如下所示：

 [![](elements-api-walkthrough-images/03-create-task.png "选择该元素后，将显示文本编辑的键盘，如所示")](elements-api-walkthrough-images/03-create-task.png#lightbox)

第二部分包含一个`DateElement` ，可用于管理任务的`DueDate`属性。 选择日期会自动加载日期选取器，如下所示：

 [![](elements-api-walkthrough-images/04-date-picker.png "选择日期会自动将日期选取器加载到")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

`EntryElement`在和`DateElement`事例（或 monotouch.dialog 中的任何数据输入元素）中，对值所做的任何更改都将自动保留。 我们可以通过编辑日期，然后在根屏幕和各种任务详细信息之间来回导航来对此进行演示，详细信息屏幕中的值将保留在其中。

## <a name="summary"></a>总结

本文介绍了如何使用 Monotouch.dialog 元素 API 的演练。 其中介绍了使用 MT 创建多屏幕应用程序的基本步骤。D. 包括如何使用`DialogViewController`以及如何添加元素和节来创建屏幕。 此外，它还演示了如何使用 MT。D 与一起使用`UINavigationController`。

## <a name="related-links"></a>相关链接

- [MTDWalkthrough （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Monotouch.dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Github 上的 Monotouch.dialog 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
