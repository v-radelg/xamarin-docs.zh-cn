---
title: 演练 - 使用 Apple Instruments 工具
description: 本文介绍了如何使用 Apple 的 Instruments 工具诊断用 Xamarin 生成的 iOS 应用的内存问题。 它演示了如何启动 Instruments、拍摄堆快照、分析内存增长情况等。
ms.prod: xamarin
ms.assetid: 8f21db1d-7107-4158-8058-d47e417689a0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 41254fb6aac176cd796fba851478b31f774553d2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023445"
---
# <a name="walkthrough---using-apples-instruments-tool"></a>演练 - 使用 Apple Instruments 工具

_本文介绍如何使用 Apple 的 Instruments 工具诊断通过 Xamarin 生成的 iOS 应用程序的内存问题。它演示了如何启动 Instruments、拍摄堆快照和分析内存增长情况。此外，还演示了如何使用 Instruments 显示和确定造成内存问题的确切代码行。_

本页演示如何使用 **Xcode 的 Instruments 工具**诊断 iOS 应用程序中的内存问题。
首先，下载 [MemoryDemo 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)，并在 Visual Studio for Mac 中打开先前  解决方案。

## <a name="diagnosing-the-memory-issues"></a>诊断内存问题

1. 在 Visual Studio for Mac 中，通过“工具”>“启动 Instruments”  菜单项启动“Instruments”  。
2. 通过选择“运行”>“上传到设备”  菜单项，将应用程序上传到设备。
3. 选择“分配”  模板（带白色边框的橙色图标）

    ![](walkthrough-apples-instrument-images/00-allocations-tempate.png "Choose the Allocations template")

4. 在窗口顶部的“选择分析模板的对象:”  列表中，选择“Memory Demo”  应用程序。 首先单击 iOS 设备，展开显示已安装应用程序的菜单。

    ![](walkthrough-apples-instrument-images/01-mem-demo.png "Select the Memory Demo application")

5. 按“选择”  按钮（窗口右下方），启动“Instruments”  。 此模板会在顶部窗格中显示两项：分配和 VM 跟踪程序。

6. 按 Instruments 中的“录制”  按钮（左上方的红色圈），启动应用程序。

7. 在顶部窗格中选择“VM 跟踪程序”  行（应用运行后，会包含两个部分：脏大小和驻留大小）。 在“检查器”  窗格中，选择“显示显示设置”  选项（齿轮图标），然后选中“自动快照”  复选框，如右下方屏幕截图所示：

    ![](walkthrough-apples-instrument-images/02-auto-snapshot.png "Choose the Show Display Settings option the gear icon then tick the Automatic Snapshotting checkbox")

8. 在顶部窗格中选择“分配”  行（应用运行后，会显示“所有堆和匿名 VM”  ）
9. 在“检查器”  窗格中，选择“显示显示设置”  选项（齿轮图标），然后单击“标记生成”  按钮生成基线。 窗口顶部的时间线中会出现红色小标记
10. 滚动应用程序，然后再次选择“标记生成”  （重复几次）
11. 单击“停止”  按钮。
12. 展开具有最大“增长”  的“生成”  节点，并按“增长”  排序（降序）。
13. 将“检查器”  窗格更改为“显示扩展详细信息”  (E)，显示“堆栈跟踪”  。

14. 请注意，“&lt;非对象>”  节点显示内存增加过多。 单击此节点旁边的箭头，查看更多详细信息 - 右键单击堆栈跟踪，将“源位置”  添加到窗格中：

    ![](walkthrough-apples-instrument-images/03-mem-growth.png "Add Source Location to the pane")

15. 按“大小”  进行排序，并显示“扩展详细信息”  视图：

    ![](walkthrough-apples-instrument-images/04-extended-detail.png "Sort by Size and display the  Extended Detail view")

16. 单击调用堆栈中的相应条目，查看相关代码：

    ![](walkthrough-apples-instrument-images/05-related-code.png "Viewing the related code")

此情况下，会创建新的映像，并将其存储在每个单元格的集合中，而不是重新使用现有集合视图单元格。

## <a name="resolving-the-memory-issues"></a>解决内存问题

可通过 Instruments 解决这些问题并重新运行应用程序。

通过在类级别声明单个实例，可重复使用映像，并可从现有池中重复使用单元格对象，而无需每次进行创建，如下所示：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Dequeue a cell from the reuse pool
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);

    // Reuse the image declared at the class level
    imageCell.ImageView.Image = image;

    return imageCell;
}
```

现在，运行应用程序时，内存使用率会大幅降低 - 此时生成之间的“增长”  按 Kib（千字节）测量，而在代码修复前则是 MiB（兆字节）：

![](walkthrough-apples-instrument-images/06-reduced-memory.png "Showing the app memory usage")

在 Visual Studio for Mac 的随后  解决方案中，[MemoryDemo 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)中提供了改进的代码。

若要了解如何处理 Xamarin.iOS 方面的内存问题，可参考关于 [Xamarin.iOS 垃圾回收](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)该社区博客。

## <a name="summary"></a>总结

本文演示如何使用 Instruments 诊断内存问题。
其中介绍了如何从 Visual Studio for Mac 内启动 Instruments，加载内存分配模板，并介绍了如何分步使用快照来确定内存问题。
最后，重新检查了应用程序，验证问题是否已纠正。

## <a name="related-links"></a>相关链接

- [MemoryDemo 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/profiling-memorydemo)
- [Xamarin.iOS 垃圾回收（博客文章）](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me/)
