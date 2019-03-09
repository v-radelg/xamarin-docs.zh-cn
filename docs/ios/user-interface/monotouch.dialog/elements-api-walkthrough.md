---
title: 创建使用元素 API 的 Xamarin.iOS 应用程序
description: 这篇文章基于 MonoTouch 对话框文章简介中提供的信息。 它提供了一个演练，演示如何使用 MonoTouch.Dialog （mt。D） 元素 API 以快速开始构建应用程序与 mt。D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 14711f9cc2c34d72765e28db158379bc2a26849b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670278"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>创建使用元素 API 的 Xamarin.iOS 应用程序

_这篇文章基于 MonoTouch 对话框文章简介中提供的信息。它提供了一个演练，演示如何使用 MonoTouch.Dialog （mt。D） 元素 API 以快速开始构建应用程序与 mt。D._

在本演练中，我们将使用 mt。D 元素 API 创建大纲-细节样式显示任务列表应用程序。 当用户选择<span class="ui"> + </span>按钮在导航栏中，新行将添加到任务表。 选择行将导航到详细信息屏幕，可用于更新在任务说明和到期日期，如下图所示：

 [![](elements-api-walkthrough-images/01-task-list-app.png "选择行将导航到详细信息屏幕，可用于更新在任务说明和截止日期")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>设置 mt。D

MT。通过 Xamarin.iOS 分发 D。 若要使用它，右键单击**引用**节点的 Xamarin.iOS 项目在 Visual Studio 2017 或 Visual Studio for Mac，并添加对引用**MonoTouch.Dialog 1**程序集。 然后，添加`using MonoTouch.Dialog`根据代码源中的语句。

## <a name="elements-api-walkthrough"></a>元素 API 演练

在中[简介 MonoTouch 对话框](~/ios/user-interface/monotouch.dialog/index.md)文章中，我们获得了深入了解的不同部分的 mt。D. 让我们使用元素 API 来将它们组合在一起放入应用程序。

## <a name="setting-up-the-multi-screen-application"></a>设置多屏幕应用程序

若要启动屏幕创建过程，MonoTouch.Dialog 创建`DialogViewController`，然后添加`RootElement`。

若要创建 MonoTouch.Dialog 多屏幕应用程序，我们需要：

1.  创建 `UINavigationController.`
1.  创建 `DialogViewController.`
1.  添加`DialogViewController`作为根的  `UINavigationController.` 
1.  添加`RootElement`到  `DialogViewController.`
1.  添加`Sections`和`Elements`到  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要创建的导航样式应用程序，我们需要创建`UINavigationController`，然后将其作为添加`RootViewController`中`FinishedLaunching`方法的`AppDelegate`。 若要使`UINavigationController`使用 MonoTouch.Dialog，我们将添加`DialogViewController`到`UINavigationController`，如下所示：

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
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

上面的代码创建的实例`RootElement`并将其传递到`DialogViewController`。 `DialogViewController`始终具有`RootElement`在其层次结构的顶部。 在此示例中，`RootElement`创建"待办事项列表，"作为导航控制器的导航栏中的标题的字符串。 在此情况下，运行应用程序将显示如下所示的屏幕如下：

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "运行应用程序将显示如下所示的屏幕")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

让我们了解如何使用 MonoTouch.Dialog 的层次结构`Sections`和`Elements`添加更多屏幕。

### <a name="creating-the-dialog-screens"></a>创建对话框屏幕

一个`DialogViewController`是`UITableViewController`MonoTouch.Dialog 用于添加屏幕的子类。 MonoTouch.Dialog 创建通过添加屏幕`RootElement`到`DialogViewController`，如上面看到的。 `RootElement`可以有`Section`实例表示的表的部分。
部分由元素组成，其他部分，或甚至其他`RootElements`。 通过嵌套`RootElements`，MonoTouch.Dialog 会自动创建导航样式的应用程序，我们将在接下来请参阅。

### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

`DialogViewController`，正在`UITableViewController`子类，拥有`UITableView`作为其视图。 在此示例中，我们想要每次将项添加到表<span class="ui"> + </span>点击按钮。 由于`DialogViewController`已添加到`UINavigationController`，我们可以使用`NavigationItem`的`RightBarButton`属性来添加<span class="ui"> + </span>按钮，如下所示：

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

当我们创建了`RootElement`更早版本，我们为其传递一个`Section`实例，以便我们可以添加与元素<span class="ui"> + </span>用户点击按钮。 我们可以使用以下代码来完成这在事件处理程序的按钮：

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

此代码将创建一个新`Task`对象每次点击按钮。 下面的内容演示的简单实现`Task`类：

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

任务的`Name`使用属性来创建`RootElement`的标题以及一个名为计数器变量`n`为每个新任务时它都递增。 MonoTouch.Dialog 将元素转换为添加到的行`TableView`时每个`taskElement`添加。

## <a name="presenting-and-managing-dialog-screens"></a>显示和管理对话框屏幕

我们使用`RootElement`以便 MonoTouch.Dialog 会自动创建一个新的每个任务的详细信息屏幕和选择行时，导航到它。

任务详细信息屏幕本身由两个部分;以下各节包含单个元素。 从创建第一个元素`EntryElement`提供一个可编辑的行的任务的`Description`属性。 选择元素时，进行文本编辑键盘会看到如下所示：

 [![](elements-api-walkthrough-images/03-create-task.png "所示选择元素时，显示键盘进行文本编辑")](elements-api-walkthrough-images/03-create-task.png#lightbox)

第二部分包含`DateElement`让我们管理的任务可以`DueDate`属性。 选择日期将自动加载日期选取器所示：

 [![](elements-api-walkthrough-images/04-date-picker.png "选择日期将自动加载作为日期选取器")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

在这种`EntryElement`和`DateElement`情况下 （或 MonoTouch.Dialog 中的任何数据输入元素），将自动保留值进行任何更改。 这演示通过编辑日期，然后根屏幕和各种任务详细信息，其中保留的详细信息屏幕中的值之间来回导航。

## <a name="summary"></a>总结

本文提供的演练，说明了如何使用 MonoTouch.Dialog 元素 API。 其中包括如何使用 mt。 创建一个多屏幕应用程序的基本步骤D，包括如何使用`DialogViewController`以及如何添加元素和节创建屏幕。 此外，它介绍了如何使用 mt。结合 D `UINavigationController`。

## <a name="related-links"></a>相关链接

- [MTDWalkthrough （示例）](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [截屏视频-Miguel de Icaza 使用 MonoTouch.Dialog 创建 iOS 登录屏幕](http://youtu.be/3butqB1EG0c)
- [截屏视频-轻松创建 iOS 用户界面与 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 简介](~/ios/user-interface/monotouch.dialog/index.md)
- [反射 API 演练](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [JSON 元素演练](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 应用程序](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
