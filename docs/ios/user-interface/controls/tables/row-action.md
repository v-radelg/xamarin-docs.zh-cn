---
title: 使用 Xamarin.iOS 中的行操作
description: 本指南演示如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 创建的表行的自定义轻扫操作
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408216"
---
# <a name="working-with-row-actions-in-xamarinios"></a>使用 Xamarin.iOS 中的行操作

_本指南演示如何使用 UISwipeActionsConfiguration 或 UITableViewRowAction 创建的表行的自定义轻扫操作_

![演示在行上的轻扫操作](row-action-images/action02.png)

iOS 提供两种方法对表执行操作：`UISwipeActionsConfiguration`和`UITableViewRowAction`。

`UISwipeActionsConfiguration` 在 iOS 11 中引入，用于定义一组应执行的操作的发生用户扫 _，在任一方向_表视图中的行。 此行为是类似于本机 Mail.app 

`UITableViewRowAction`类用于定义用户扫左水平表视图中的行时将执行的操作。
例如，当编辑表中，轻扫的行的左侧显示**删除**默认情况下的按钮。 通过将附加的多个实例`UITableViewRowAction`类来`UITableView`，可以定义多个自定义操作，每个都有其自己的文本、 格式设置和行为。


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

有三个步骤实现轻扫操作所需`UISwipeActionsConfiguration`:

1. 重写`GetLeadingSwipeActionsConfiguration`和/或`GetTrailingSwipeActionsConfiguration`方法。 这些方法返回`UISwipeActionsConfiguration`。 
2. 实例化`UISwipeActionsConfiguration`要返回。 此类采用数组的`UIContextualAction`。
3. 创建 `UIContextualAction`。

以下各节中更详细地介绍了这些。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1.实现 SwipeActionsConfigurations 方法

`UITableViewController` (还`UITableViewSource`并`UITableViewDelegate`) 包含两个方法：`GetLeadingSwipeActionsConfiguration`和`GetTrailingSwipeActionsConfiguration`，，用于实现轻扫操作的一组在表视图行。 前导轻扫操作是指轻扫从左到右语言中的屏幕的左侧和从右到左语言中的屏幕的右侧。 

下面的示例 (从[TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)示例) 演示如何实现前导轻扫配置。 两个操作创建的上下文的操作，将进行介绍[如下](#create-uicontextualaction)。 这些操作将再传递到新初始化[ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations)，它作为返回值使用。


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2.实例化 `UISwipeActionsConfiguration`

实例化`UISwipeActionsConfiguration`通过使用`FromActions`方法将添加一个新数组`UIContextualAction`s，如下面的代码段中所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

请务必注意，在其中显示你的操作的顺序是取决于如何传递到你的阵列。 例如，前导扫上面的代码，因此显示的操作：

![前导轻扫操作显示在表行](row-action-images/action03.png)

为尾部扫，操作将显示在下图中所示：

![尾随轻扫操作显示在表行](row-action-images/action04.png)

此代码片段还使用新的`PerformsFirstActionWithFullSwipe`属性。 默认情况下，此属性设置为 true，这表示当用户在行上完全轻扫数组中的第一个操作将会发生。 如果不是破坏性操作 (例如"删除"，这可能不是理想的行为，因此应将其设置为`false`。

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>创建 `UIContextualAction`

上下文的操作是实际创建将在用户轻扫表行时显示的操作。

若要初始化的操作必须提供`UIContextualActionStyle`、 一个标题的以及`UIContextualActionHandler`。 `UIContextualActionHandler`采用三个参数： 操作、 操作中，显示的视图和完成处理程序：

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

可以编辑各种可视属性，例如背景色或图像的操作。 上面的代码段演示如何向操作添加图像并设置其背景颜色设置为蓝色。

创建上下文的操作后，它们可用于初始化`UISwipeActionsConfiguration`在`GetLeadingSwipeActionsConfiguration`方法。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

可以定义一个或多个自定义行操作`UITableView`，将需要创建的实例`UITableViewDelegate`类并重写`EditActionsForRow`方法。 例如：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

静态`UITableViewRowAction.Create`方法用于创建一个新`UITableViewRowAction`将显示**Hi**按钮时用户扫水平左表中的行。 更高版本的新实例`TableDelegate`创建并附加到`UITableView`。 例如：

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

当运行上面的代码和上一个表行，剩余用户扫**Hi**按钮将显示而不是**删除**默认情况下显示的按钮：

[![](row-action-images/action01.png "而不是删除按钮显示 Hi 按钮")](row-action-images/action01.png#lightbox)

如果在用户点击**Hi**按钮，`Hello World!`将写出到控制台在 Visual Studio for Mac 或 Visual Studio 在调试模式下运行应用程序时。



## <a name="related-links"></a>相关链接

- [TableSwipeActions （示例）](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
