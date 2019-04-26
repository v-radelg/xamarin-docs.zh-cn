---
title: IOS 11 中的视觉对象设计更新
description: 本文档介绍了在 iOS 11 中引入的更新的可视化设计。 它讨论了到导航栏、 搜索控制器、 边距、 全屏幕内容和表视图的更改。
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c6351f2c25f8e31181c761aea1b471315a8a05e8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400178"
---
# <a name="visual-design-updates-in-ios-11"></a>IOS 11 中的视觉对象设计更新

在 iOS 11 中，Apple 引入了新的可视更改，包括对导航栏、 搜索栏和表视图的更新。 除了已进行改进，允许通过边距和全屏幕内容进行更大的灵活性。 本指南中介绍了这些更改。 

专门与适用于 iPhone X 的设计有关的信息，请观看 Apple[针对 iPhone X 设计](https://developer.apple.com/videos/play/fall2017/801/)视频。

## <a name="uikit"></a>UIKit

在 iOS 11，以使其更易于访问的最终用户适用 UIKit 条。

此类的一个变化是用户 long 类型的值的按下条上时，将显示一个新的 HUD 显示项。 若要启用此功能，设置`largeContentSizeImage`上的属性`UIBarItem`并添加通过可查看大图像[资产目录](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>导航栏
iOS 11 引入了新功能，以使导航栏标题更易于读取。 应用可以通过将分配会显示此较大标题`PrefersLargeTitles`属性设为 true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

使应用程序中的设置更大标题_所有_跨您的应用程序的导航栏标题显得较大，如以下屏幕截图中所示：

![大型导航标题](visual-design-images/image7.png)

若要控制在导航栏上显示大标题时，设置`LargeTitleDisplayMode`上的导航项`Always`， `Never`，或`Automatic`。

### <a name="search-controller"></a>搜索控制器

iOS 11 变得更轻松地搜索控制器将直接添加到导航栏。 创建搜索控制器后，将其添加到导航栏与`SearchController`属性：

```csharp
NavigationItem.SearchController = searchController;
```

[![使用搜索栏中的大型导航标题](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

根据您的应用程序的功能，您可能会或可能不希望搜索栏隐藏在用户滚动浏览列表时。 您可以调整此项是使用`HidesSearchBarWhenScrolling`属性。

## <a name="margins"></a>边距

Apple 已创建一个新属性 – `directionalLayoutMargins` – 可用于设置视图和子视图之间的空间。 使用`directionalLayoutMargins`与`leading`或`trailing`内边距。 无论系统是从左到右还是从右到左的语言，由 iOS 适当地设置应用程序中的间距。

在 iOS 10 和之前，所有视图将都具有它们将对齐的最小边距大小。 iOS 11 引入了重写，使用的选项`ViewRespectsSystemMinimumLayoutMargins`。 例如，此属性设置为 false，可调整你边缘插入量为零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![与零个 ios 11 中嵌入的图像显示边距](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>全屏显示内容

iOS 7[引入](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen)`topLayoutGuide`和`bottomLayoutGuide`作为一种方法，以便它们不通过 UIKit 条隐藏的位于屏幕的可见区域约束你的视图。 这些支持的 iOS 11 中已弃用_安全区域_。

安全区域是考虑您的应用程序和如何将约束添加视图和超级视图之间的可视空间的新方式。 例如，考虑以下映像：

[![安全区域 vs 顶部和底部布局参考线](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

以前，如果你添加了视图，还希望它会显示在上面的绿色区域，您将限制到它_底部_的`TopLayoutGuide`并且_顶部_的`BottomLayoutGuide`。 在 iOS 11 中，你将改为将其约束到_顶部_并_底部_的安全区域。 下面是一个示例：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>表视图

UITableView iOS 11 中有大量小，但很重要，更改。

默认情况下，标头、 页脚和单元格大小将立即自动调整根据其内容。 若要选择退出此自动调整大小行为集`EstimatedRowHeight`， `EstimatedSectionHeaderHeight`，或`EstimatedSectionFooterHeight`为零。

但是，在某些情况下 (例如添加 UITableViewController iOS 设计器中或在 Interface Builder 中使用现有 Storboards 时) 可能需要手动启用自定义大小的单元格。 若要执行此操作，请确保，设置以下属性在表视图单元格、 标头和表尾上分别:

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 已扩展行操作的功能。 `UISwipeActionsConfiguration` 引入了来定义一组在用户轻扫在任一方向表视图中的行时应发生的操作。 此行为是类似于本机 Mail.app。 有关详细信息，请参阅[行操作](~/ios/user-interface/controls/tables/row-action.md)指南。

表视图都支持在 iOS 11 中拖放。 有关详细信息，请参阅[拖放到](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview)指南。


## <a name="related-links"></a>相关链接

- [What's New iOS 11 (Apple) 中](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [设计适用于 iPhone X (Apple) （视频）](https://developer.apple.com/videos/play/fall2017/801/)
- [将应用更新适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)

