---
title: 在 Xamarin 中使用行操作
description: 本指南演示如何为具有 UISwipeActionsConfiguration 或 UITableViewRowAction 的表行创建自定义轻扫操作
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 542ae6696bae8fccfa6d5ed9842bce126760da37
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021858"
---
# <a name="working-with-row-actions-in-xamarinios"></a>在 Xamarin 中使用行操作

_本指南演示如何为具有 UISwipeActionsConfiguration 或 UITableViewRowAction 的表行创建自定义轻扫操作_

![演示对行的轻扫操作](row-action-images/action02.png)

iOS 提供了两种方法来对表执行操作： `UISwipeActionsConfiguration` 和 `UITableViewRowAction`。

`UISwipeActionsConfiguration` 是在 iOS 11 中引入的，用于定义当用户在表视图中的某_一行上 swipes_时应发生的一组操作。 此行为与本机 Mail 应用程序的行为类似。

`UITableViewRowAction` 类用于定义一个操作，该操作将在用户在表视图中的某一行上水平 swipes 时进行。
例如，编辑表时，默认情况下，按行向左轻扫会显示 "**删除**" 按钮。 通过将 `UITableViewRowAction` 类的多个实例附加到 `UITableView`，可以定义多个自定义操作，每个操作都具有其自己的文本、格式设置和行为。

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

使用 `UISwipeActionsConfiguration`实现轻扫操作需要三个步骤：

1. 重写 `GetLeadingSwipeActionsConfiguration` 和/或 `GetTrailingSwipeActionsConfiguration` 方法。 这些方法返回 `UISwipeActionsConfiguration`。
2. 实例化要返回的 `UISwipeActionsConfiguration`。 此类采用 `UIContextualAction`的数组。
3. 创建 `UIContextualAction`。

以下各节将更详细地介绍这些内容。

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. 实现 SwipeActionsConfigurations 方法

`UITableViewController` （以及 `UITableViewSource` 和 `UITableViewDelegate`）包含两种方法： `GetLeadingSwipeActionsConfiguration` 和 `GetTrailingSwipeActionsConfiguration`，用于对表视图行实现一组轻扫操作。 领先的 "轻扫" 操作是指从屏幕的左侧按从右到左的语言，然后从右到左的语言以从右到左的语言进行的扫。

下面的示例（来自[TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)示例）演示了如何实现领先的刷卡器配置。 从上下文操作创建两个操作，[如下](#create-uicontextualaction)所述。 然后，将这些操作传递到新初始化的[`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations)，该用作返回值。

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

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. 实例化 `UISwipeActionsConfiguration`

使用 `FromActions` 方法来实例化 `UISwipeActionsConfiguration`，以添加新的 `UIContextualAction`数组，如下面的代码段所示：

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

请注意，操作显示的顺序取决于它们如何传递到数组中。 例如，上述用于领先 swipes 的代码显示操作，如下所示：

![在表行上显示的前导轻扫操作](row-action-images/action03.png)

对于尾随 swipes，将显示如下图所示的操作：

![在表行上显示的尾随轻扫操作](row-action-images/action04.png)

此代码段还利用新的 `PerformsFirstActionWithFullSwipe` 属性。 默认情况下，此属性设置为 true，这意味着当用户完全 swipes 一行时，将发生数组中的第一个操作。 如果你有一个非破坏性操作（例如 "删除"），这可能不是理想的行为，因此应将其设置为 `false`。

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>创建 `UIContextualAction`

上下文操作是您实际创建操作的位置，当用户 swipes 表行时，将显示该操作。

若要初始化操作，必须提供 `UIContextualActionStyle`、标题和 `UIContextualActionHandler`。 `UIContextualActionHandler` 采用三个参数：操作、在其中显示操作的视图和完成处理程序：

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

可以编辑各种视觉对象属性，如操作的背景色或图像。 上面的代码段演示了如何将图像添加到操作并将其背景色设置为蓝色。

上下文操作创建完成后，可以使用初始化 `GetLeadingSwipeActionsConfiguration` 方法中的 `UISwipeActionsConfiguration`。

## <a name="uitableviewrowaction"></a>UITableViewRowAction

若要为 `UITableView`定义一个或多个自定义行操作，将需要创建 `UITableViewDelegate` 类的实例并重写 `EditActionsForRow` 方法。 例如:

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

静态 `UITableViewRowAction.Create` 方法用于创建新的 `UITableViewRowAction`，当用户在表中的某一行上水平 swipes 时，它将显示 "**高**" 按钮。 稍后将创建 `TableDelegate` 的新实例，并将其附加到 `UITableView`。 例如:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

运行上述代码，并将用户 swipes 到表行时，将显示 " **Hi** " 按钮，而不是默认显示的 "**删除**" 按钮：

[![](row-action-images/action01.png "The Hi button being displayed instead of the Delete button")](row-action-images/action01.png#lightbox)

如果用户点击 " **Hi** " 按钮，在调试模式下运行应用程序时，`Hello World!` 将写出到 Visual Studio for Mac 或 Visual Studio 中的控制台。

## <a name="related-links"></a>相关链接

- [TableSwipeActions （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
