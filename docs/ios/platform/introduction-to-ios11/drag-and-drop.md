---
title: 在 Xamarin.iOS 中拖放
description: 本文档介绍如何使用 iOS 11 中引入的 Api 的 Xamarin.iOS 应用程序中实现拖放。 具体而言，讨论启用拖放入 UITableView。
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/05/2017
ms.openlocfilehash: aa93e015a399e733a2bb52f087a1e482bc23a00a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169618"
---
# <a name="drag-and-drop-in-xamarinios"></a>在 Xamarin.iOS 中拖放

_实现拖放功能适用于 iOS 11_

iOS 11 包括拖放支持在 iPad 上的应用程序之间复制数据。 用户可以选择并将所有类型的内容从定位的应用程序并排地或通过拖动这将触发此应用打开并允许数据要删除的应用图标上：

![拖放的示例从自定义应用程序为 Notes 应用](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 拖放是仅在 iPhone 上的相同应用内可用。

应考虑支持拖放操作任意位置可以创建或编辑内容：

- 文本控件支持拖放功能生成针对 iOS 11 中，而无需任何其他工作的所有应用。
- 表视图和集合视图包括 iOS 11 的简化添加拖放行为中的增强功能。
- 任何其他视图可用于与其他自定义支持拖放。

如果添加拖放功能支持到您的应用程序，可以提供不同级别的内容的保真度;例如，你可能会提供一个带格式的文本和纯文本版本的数据，以便接收应用程序可以选择最适用于为拖动目标。 还有可能要自定义所拖动的可视化效果，还能够在一次拖动多个项。

## <a name="drag-and-drop-with-text-controls"></a>拖放到与文本控件

`UITextView` 和`UITextField`自动支持拖动所选的文本输出，并将放置文本中的内容。

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>拖放功能与 UITableView

`UITableView` 具有内置的处理拖放到与表行，要求只有少量方法，若要启用的默认行为之间的交互。

所涉及了两个接口：

- `IUITableViewDragDelegate` – 当在表视图中启动拖动时包信息。
- `IUITableViewDropDelegate` – 处理信息时放置正在尝试和已完成。

在中[DragAndDropTableView 示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)这两个接口都实现上`UITableViewController`类，以及委托和数据源。 它们分配在`ViewDidLoad`方法：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

下面解释了这两个接口所需的最小代码。

### <a name="table-view-drag-delegate"></a>表视图拖动委托

唯一的方法_必需_若要支持在表视图中拖动行`GetItemsForBeginningDragSession`。 如果用户开始拖动某一行，将调用此方法。

实现如下所示。 它检索与拖动行关联的数据、 进行编码，并配置`NSItemProvider`用于确定应用程序将如何处理该操作的"删除"部分 (例如，无论它们可以处理的数据类型， `PlainText`，在示例中):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

将委托，可实现自定义的拖放行为，例如提供多种数据表示形式，可以在目标应用程序中利用执行哪些上有许多可选方法 (例如为作为纯文本格式或一个向量，带格式文本和位图的版本中绘图）。 此外可以提供相同的应用内拖放时要使用的自定义数据表示形式。

### <a name="table-view-drop-delegate"></a>表视图下拉委托

当拖动操作上表视图中，发生或完成其上方时调用上放置委托方法。 所需的方法确定是否允许数据被删除，并且如果在放置操作完成要采取的操作：

- `CanHandleDropSession` – 虽然拖动过程中，并可能会丢弃该应用程序，此方法确定是否允许正在拖动的数据被删除。
- `DropSessionDidUpdate` – 当拖动正在进行，被调用此方法来确定应采取的操作。 通过拖动到表视图、 拖放会话和可能的索引路径信息所有可用来确定的行为和提供给用户的可视反馈。
- `PerformDrop` – 当用户完成放置后 （通过提升他们的手指），此方法将提取要拖动的数据，并修改表视图，以将数据添加一个新行 （或行） 中。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` 指示表视图是否可以接受要拖动的数据。 在此代码片段，`CanLoadObjects`用于确认此表视图可以接受字符串的数据。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`拖动操作正在进行，若要向用户提供视觉提示时重复调用方法。

在下面的代码，`HasActiveDrag`用于确定当前的表视图中是否源自该操作。 如果是这样，只有单个行允许移动。
如果拖动另一来源，将指示复制操作：

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

删除操作可以是之一`Cancel`， `Move`，或`Copy`。

可以是放置意向，以插入新行，或添加/追加到现有行的数据。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`在用户完成此操作，并修改以反映已放置的数据的表视图和数据源时调用方法。

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

可以添加其他代码来以异步方式加载大型数据对象。

### <a name="testing-drag-and-drop"></a>测试拖放功能

您必须使用 iPad 来测试[示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)。
打开与另一个应用 （如说明） 示例并将它们之间的行和文本：

![拖放操作正在进行中的屏幕截图](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>相关链接

- [拖放到人体学接口指南 (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [拖放到表视图示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [拖放到集合视图示例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [引入了拖动和删除 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/203/)
- [拖放到与集合和表视图 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/223/)
