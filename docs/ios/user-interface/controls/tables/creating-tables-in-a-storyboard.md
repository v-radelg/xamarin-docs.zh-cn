---
title: 在 iOS 设计器中使用表
description: 前面的部分中我们探讨了如何使用表进行开发。 在中，第五个和最后一个部分，我们将聚合到目前为止我们已了解并创建使用情节提要的一个基本的任务列表应用程序。
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 303c96ae6cdbc9f5b327c971f962d6eac75a6fa1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227363"
---
# <a name="working-with-tables-in-the-ios-designer"></a>在 iOS 设计器中使用表

情节提要是一种创建 iOS 应用程序所见即所得方式和支持在 Mac 和 Windows 上的 Visual Studio。 有关情节提要的详细信息，请参阅[到情节提要简介](~/ios/user-interface/storyboards/index.md)文档。 情节提要还允许您编辑的单元格布局*在*表，这简化了使用表和单元格进行开发

在 iOS 设计器中配置的表视图的属性，有两种类型的单元格内容，可以选择从：**动态**或**静态**原型内容。

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>动态原型内容

一个`UITableView`用原型内容通常用来显示数据的列表，原型单元格 （或单元格，你可以定义多个） 重新用于在列表中每个项。 单元格无需进行实例化，它们在中获得`GetView`方法通过调用`DequeueReusableCell`的方法及其`UITableViewSource`。

 <a name="Static_Content" />


## <a name="static-content"></a>静态内容

`UITableView`具有静态内容的 s 允许直接在设计图面上设计的表。 可以拖动到表并通过更改属性并将控件添加自定义单元格。

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>创建一个情节提要驱动的应用

StoryboardTable 示例包含在情节提要中使用 UITableView 这两种类型的简单母版-详细信息应用程序。 本部分的剩余部分将介绍如何构建一个小型待办事项列表示例，完成后将如下所示：

 [![示例屏幕](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

将使用情节提要，生成用户界面，并且这两个屏幕将 UITableView。 在主屏幕中使用*原型内容*为布局行和详细信息屏幕将使用*静态内容*创建使用自定义单元格布局的数据输入窗体。

## <a name="walkthrough"></a>演练

Visual Studio 中使用创建新的解决方案 **（创建） 新项目...> 单一视图应用 (C#)**，并调用它_StoryboardTables_。

 [![创建新项目对话框](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

该解决方案将打开，其中一些C#文件和一个`Main.storyboard`已创建的文件。 双击`Main.storyboard`文件以在 iOS 设计器中打开它。

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>修改情节提要

在三个步骤中，将编辑情节提要：

-  所需的第一个，布局查看控制器和设置其属性。
-  其次，通过拖放到您的视图上的对象来创建 UI
-  最后，将所需的 UIKit 类添加到每个视图并为各种控件提供一个名称，以便它们可以在代码中引用。


情节提要完成后，可以添加代码以使一切正常工作。

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>布局视图控制器

到情节提要的第一个更改是删除现有的详细信息视图，并将它替换为 UITableViewController。 请执行以下步骤：

1.  选择视图控制器底部栏并将其删除。
2.  拖动**导航控制器**和一个**表格视图控制器**到从工具箱情节提要上。 
3.  从根视图控制器创建 segue，到刚添加的第二个表视图控制器。 若要创建 segue，控制 + 拖动*从详细信息单元格*到新添加的 UITableViewController。 选择的选项**显示**下**Segue 选择**。 
4.  选择新的 segue 您创建并将其提供给此 segue 在代码中引用的标识符。 单击 segue 并输入`TaskSegue`有关**标识符**中**Properties Pad**，如下所示：    
  [![属性面板中命名 segue](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. 接下来，通过选择它们并使用属性面板中配置两个表视图。 请务必选择视图，而不是视图控制器 – 你可以使用文档大纲以帮助进行选择。

6.  要将根视图控制器更改**内容：动态原型**(在设计图面上的视图将标有**原型内容**):

    [![将内容属性设置为动态原型](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  更改新**UITableViewController**为**内容：静态单元格**。 


8. 新 UITableViewController 必须具有其类名称和标识符设置。 选择视图控制器，然后键入_TaskDetailViewController_有关**类**中**Properties Pad** – 这将创建一个新`TaskDetailViewController.cs`解决方案中的文件板。 输入**StoryboardID**作为_详细信息_，在下面的示例所示。 这将在稍后用于加载此视图中的C#代码：  

    [![设置情节提要 ID](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. 情节提要设计图面现在应如下所示 （根视图控制器的导航项标题已更改为"的任务板"）：

    [![设计图面](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>创建 UI

现在，视图和转入是配置，用户界面元素需要添加。

#### <a name="root-view-controller"></a>根视图控制器

首先，在母版视图控制器选择原型单元格并设置**标识符**作为_taskcell_，如下所示。 这将用于以后在代码中检索此 UITableViewCell 的实例：

 [![设置单元标识符](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

接下来，你将需要创建一个按钮，将添加新任务，如下图所示：

[![条形图的导航栏中按钮项](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

请执行以下操作： 

-  拖动**栏按钮项**从工具箱拖到_导航栏的右侧_。
-  在中**Properties Pad**下**栏按钮项**选择**标识符：添加**(以使其*+* 加号按钮)。 
-  请为其提供一个名称，以便它可以标识代码中在后面的阶段。 请注意，你将需要提供根视图控制器类名称 (例如**ItemViewController**) 以允许您设置栏按钮项的名称。


#### <a name="taskdetail-view-controller"></a>TaskDetail 视图控制器

详细信息视图需要更多工作。 表视图单元格需要拖到视图，然后填充标签、 文本视图和按钮。 下面的屏幕截图显示了具有两个部分的已完成的 UI。 一个部分包含三个单元格、 三个标签、 两个文本字段和一个开关，而第二部分有一个具有两个按钮的单元格：

 [![详细信息视图布局](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

若要生成完整布局的步骤如下：

选择表视图，然后打开**属性板**。 更新以下属性：

-  **部分**:_2_ 
-  **样式**:_分组_
-  **分隔符**:_无_
-  **选择**:_没有选定内容_

选择的上半部分和下**属性 > 表视图部分**更改**行**到_3_，如下所示：


 [![将顶部部分设置为三个行](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

打开每个单元格**Properties Pad**和设置：

-  **样式**:_自定义_
-  **标识符**:选择 （例如每个单元的唯一标识符。 "_标题_"，"_说明_"、"_完成_")。
-  将所需的控件，以生成屏幕截图所示的布局 (放置**UILabel**， **UITextField**并**UISwitch**上正确的单元格，并设置标签相应地，ie。标题、 说明和完成)。


在第二个部分中，将**行**到_1_并抓取要增加其高度的单元格的底部重设大小句柄。

-  **将标识符设置**： 为唯一值 （例如。 "保存"）。 
-  **设置背景**:_清除颜色_。
-  将两个按钮拖动到单元格上并适当地设置其标题 (即_保存_并_删除_)，如下所示：

   [![在下半部分中设置两个按钮](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

此时您还可能想要设置单元格和控制措施来确保自适应布局上的约束。

### <a name="adding-uikit-class-and-naming-controls"></a>添加 UIKit 类和命名控件

创建自己的情节提要有几个最后一个步骤。 首先我们必须为每个控件提供一个名称下的**标识 > 名称**以便它们可以更高版本上使用在代码中。 这些名称，如下所示：

-  **标题 UITextField** :_TitleText_
-  **说明 UITextField** :_NotesText_
-  **UISwitch** :_DoneSwitch_
-  **删除 UIButton** :_DeleteButton_
-  **保存 UIButton** :_SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>添加代码

将在 Mac 或与 Windows 上的 Visual Studio 中完成剩余工作C#。 请注意，在代码中使用的属性名称反映上述演练中设置。

我们想要创建的第一次`Chores`类，该类将提供一种方法来获取和设置的 ID、 名称、 说明和完成布尔值、 值，以便我们可以使用这些值在整个应用程序。

在你`Chores`类添加以下代码：

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

接下来，创建`RootTableSource`继承的类`UITableViewSource`。 

这与非演示图板表视图之间的区别在于`GetView`方法不需要实例化任何单元格 –`theDequeueReusableCell`方法将始终返回实例 （具有匹配标识符） 的原型单元格。

下面的代码摘自`RootTableSource.cs`文件：

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

若要使用`RootTableSource`类中，创建一个新集合中的`ItemViewController`的构造函数：

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

在`ViewWillAppear`将集合传递到源并将分配给表视图：

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

如果你运行该应用现在，在主屏幕现在加载，并显示两个任务的列表。 触摸任务时定义的情节提要 segue 将导致详细信息屏幕显示，但目前不显示任何数据。

将参数中发送 segue，重写`PrepareForSegue`方法和设置的属性`DestinationViewController`(`TaskDetailViewController`在此示例中)。 目标视图控制器类将已经实例化，但不是尚未显示给用户-这意味着您可以在类上设置属性，但不能修改任何 UI 控件：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

在中`TaskDetailViewController``SetTask`方法将其参数分配给属性以便可以 ViewWillAppear 中引用它们。 不能修改控件属性`SetTask`因为可能不存在时`PrepareForSegue`调用：

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

Segue 现在将打开详细信息屏幕，并显示所选的任务信息。 遗憾的是，没有用于实现**保存**并**删除**按钮。 在实现按钮之前, 添加到这些方法**ItemViewController.cs** ，更新基础数据并关闭详细信息屏幕：

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

接下来，你将需要添加的按钮`TouchUpInside`事件处理程序`ViewDidLoad`方法**TaskDetailViewController.cs**。 `Delegate`属性引用`ItemViewController`创建专门因此我们可以调用`SaveTask`和`DeleteTask`，这关闭此视图作为其操作的一部分：

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

功能生成的最后一个剩余部分是的新任务的创建。 在中**ItemViewController.cs**添加任务创建新的方法，并打开详细信息视图。 若要实例化从情节提要使用视图`InstantiateViewController`方法替换`Identifier`该视图-在此示例中，将为详细信息的：

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

最后，关联中的导航栏中按钮**ItemViewController.cs**的`ViewDidLoad`方法来调用它：

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

完成情节提要示例-已完成的应用如下所示：

[![已完成的应用](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

此示例演示：

-  使用原型内容以便重复使用以显示数据的列表都定义了单元创建表。 
-  使用静态内容来生成输入窗体中创建表。 这包括更改的表样式并添加节、 单元格和 UI 控件。 
-  如何创建 segue，并重写`PrepareForSegue`方法以通知它需要任何参数的目标视图。 
-  正在加载情节提要视图直接与`Storyboard.InstantiateViewController`方法。



## <a name="related-links"></a>相关链接

- [StoryboardTable （示例）](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [情节提要简介](~/ios/user-interface/storyboards/index.md)
