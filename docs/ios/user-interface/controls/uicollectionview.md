---
title: Xamarin 中的集合视图
description: 集合视图允许使用任意布局显示内容。 它们允许轻松地在框中创建类似于网格的布局, 同时还支持自定义布局。
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 8557a3efca1336f70c0feef2ac4dc9c462eedbf5
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889883"
---
# <a name="collection-views-in-xamarinios"></a>Xamarin 中的集合视图

_集合视图允许使用任意布局显示内容。它们允许轻松地在框中创建类似于网格的布局, 同时还支持自定义布局。_

`UICollectionView`类中提供的集合视图是 iOS 6 中的一种新概念, 使用布局引入屏幕上的多个项。 将数据`UICollectionView`提供给以创建项并与这些项进行交互的模式遵循的是 iOS 开发中通常使用的相同委托和数据源模式。

但是, 集合视图使用独立`UICollectionView`于自身的布局子系统。 因此, 只需提供不同的布局即可轻松更改集合视图的表示形式。

iOS 提供了一个名`UICollectionViewFlowLayout`为的布局类, 该布局类允许在无其他工作的情况中创建基于行的布局, 例如网格。 此外, 还可以创建自定义布局, 以允许任何可想象的演示文稿。

## <a name="uicollectionview-basics"></a>UICollectionView 基础知识

`UICollectionView`类由三个不同的项组成:

- **单元**–每个项的数据驱动视图
- **补充视图**–与部分关联的数据驱动视图。
- **修饰视图**-布局创建的非数据驱动视图

## <a name="cells"></a>单元

单元是表示数据集中由集合视图显示的单个项的对象。 每个单元格都是`UICollectionViewCell`类的一个实例, 它由三个不同的视图组成, 如下图所示:

 [![](uicollectionview-images/01-uicollectionviewcell.png "每个单元由三个不同的视图组成, 如下所示")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell`类具有以下每个视图的属性:

- `ContentView`–此视图包含单元格显示的内容。 它在屏幕上按最顶层 z 顺序呈现。
- `SelectedBackgroundView`–单元格内置了对选择的支持。 此视图用于以可视方式表示单元格处于选中状态。 如果选择了单元格, `ContentView`它就会呈现在下面。
- `BackgroundView`–单元还可以显示背景, 它由`BackgroundView`提供。 此视图在`SelectedBackgroundView`下呈现。


通过将其`ContentView`设置为`BackgroundView`小于和`SelectedBackgroundView` `BackgroundView` ,`SelectedBackgroundView`可使用来直观显示内容, 而在选择单元时将显示, 如下所示:

 [![](uicollectionview-images/02-cells.png "不同的单元格元素")](uicollectionview-images/02-cells.png#lightbox)

上面的屏幕截图中的单元格是通过从`UICollectionViewCell`继承并分别`ContentView`设置`SelectedBackgroundView` 、 `BackgroundView`和属性创建的, 如以下代码所示:

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />


## <a name="supplementary-views"></a>补充视图

补充视图是显示与的每个部分`UICollectionView`关联的信息的视图。 与单元一样, 补充视图是数据驱动的。 如果单元格显示数据源中的项数据, 则补充视图将显示部分数据, 如书架中的书籍类别或音乐库中的音乐流派。

例如, 可使用补充视图显示特定部分的标题, 如下图所示:

 [![](uicollectionview-images/02a-supplementary-view.png "用于显示特定部分的标题的辅助视图, 如下所示")](uicollectionview-images/02a-supplementary-view.png#lightbox)

若要使用补充视图, 首先需要在`ViewDidLoad`方法中注册:

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

然后, 需要通过使用`GetViewForSupplementaryElement`返回该视图, 并通过使用`DequeueReusableSupplementaryView`创建该视图, 并从`UICollectionReusableView`继承。 以下代码段将生成上面的屏幕截图中所示的 SupplementaryView:

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

补充视图比只是标头和表尾更通用。
它们可以放置在集合视图中的任何位置, 并且可以包含任何视图, 使其外观完全可自定义。

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>修饰视图

修饰视图是纯可视视图, 可以在中`UICollectionView`显示。 与单元和辅助视图不同, 它们不是数据驱动的。 它们始终在布局的子类内创建, 随后可以更改为内容的布局。 例如, 可使用装饰视图呈现一个背景视图, 该背景视图滚动到`UICollectionView`中的内容, 如下所示:

 [![](uicollectionview-images/02c-decoration-view.png "具有红色背景的修饰视图")](uicollectionview-images/02c-decoration-view.png#lightbox)

 下面的代码片段将 samples `CircleLayout`类中的背景更改为红色:

 ```csharp
 public class MyDecorationView : UICollectionReusableView
  {
    [Export ("initWithFrame:")]
    public MyDecorationView (CGRect frame) : base (frame)
    {
      BackgroundColor = UIColor.Red;
    }
  }
 ```


## <a name="data-source"></a>“数据源”

与其他部分的 Io，如`UITableView`并`MKMapView`，`UICollectionView`获取从其数据 *数据源*，通过 Xamarin.iOS 中公开了此 **`UICollectionViewDataSource`** 类。 此类负责向`UICollectionView`提供内容, 例如:

- **Cells** –从`GetCell`方法返回。
- **补充视图**–从`GetViewForSupplementaryElement`方法返回。
- **部分数**–从`NumberOfSections`方法返回。 如果未实现, 默认值为1。
- **每节的项数**–从`GetItemsCount`方法返回。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
为方便起见, `UICollectionViewController`类可用。这会自动配置为委托, 这将在下一节中讨论, 而用于其`UICollectionView`视图的数据源。

与`UITableView`一样`UICollectionView` , 类将仅调用其数据源以获取屏幕上的项的单元格。
滚动到屏幕之外的单元格被置于队列中供重复使用, 如下图所示:

 [![](uicollectionview-images/03-cell-reuse.png "滚动到屏幕之外的单元格被置于队列中供重复使用, 如此处所示")](uicollectionview-images/03-cell-reuse.png#lightbox)

使用`UICollectionView` 和`UITableView`简化了单元重用。 如果重复数据源中没有单元格, 则不再需要直接在数据源中创建单元格, 因为在系统中注册了单元。 如果在调用以从重复数据队列中取消对单元格进行取消排队时单元格不可用, 则 iOS 会根据注册的类型或笔尖自动创建它。
辅助视图也提供相同的技术。

例如, 请考虑以下用于注册`AnimalCell`类的代码:

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

当因为`UICollectionView`某个单元格位于屏幕上而需要某个单元格时`UICollectionView` , 将调用其数据`GetCell`源的方法。 与使用 UITableView 的方式类似, 此方法负责配置支持数据的单元, 在这种情况下, 这是一个`AnimalCell`类。

下面的代码演示了`GetCell` `AnimalCell`返回实例的的实现:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

对`DequeReusableCell`的调用是从重复使用队列取消排队单元的, 或者, 如果在队列中没有可用的单元, 则根据在`CollectionView.RegisterClassForCell`调用中注册的类型创建。

在这种情况下, 通过`AnimalCell`注册类, iOS 将在内部`AnimalCell`创建新的, 并在调用对单元格的取消排队后返回该单元, 之后将使用动物类中包含的图像对其进行配置, 并将其返回给`UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>委托

类使用类型`UICollectionViewDelegate`的委托来支持与中的内容进行`UICollectionView`交互。 `UICollectionView` 这允许控制:

- **单元格选择**–确定是否选择了单元。
- **单元格突出显示**–确定当前是否正在触及某个单元。
- **单元菜单**–为响应长按下手势而显示的单元格的菜单。


对于数据源, `UICollectionViewController`默认情况下, 配置为的委托。 `UICollectionView`

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>单元格突出显示

按下某个单元时, 该单元会转换为突出显示状态, 并且在用户从单元格中移开手指之前, 不会选择该单元格。 这允许在实际选择单元格之前暂时更改单元格的外观。 选择后, 将显示该`SelectedBackgroundView`单元格的。 下图显示了所选内容出现之前的突出显示状态:

 [![](uicollectionview-images/04-cell-highlight.png "此图显示了所选内容出现之前的突出显示状态")](uicollectionview-images/04-cell-highlight.png#lightbox)

若要实现突出显示`ItemHighlighted` , `ItemUnhighlighted` `UICollectionViewDelegate`可以使用的和方法。 例如, 当突出显示单元格时, 以下代码将应用`ContentView`的黄色背景, 而在突出显示时将应用白色背景, 如上图所示:

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />


#### <a name="disabling-selection"></a>禁用所选内容

默认情况下, 中`UICollectionView`启用了选择。 若要禁用选择, `ShouldHighlightItem`请重写并返回 false, 如下所示:

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

禁用突出显示时, 还会禁用选择单元的过程。 此外, 还提供了`ShouldSelectItem`直接控制选择的方法, 但如果`ShouldHighlightItem`实现并返回 false, `ShouldSelectItem`则不会调用。

 `ShouldSelectItem`当未实现时`ShouldHighlightItem` , 允许逐项打开或关闭选择。 如果`ShouldHighlightItem`实现了并返回 true `ShouldSelectItem` , 则它还允许突出显示, 并且返回 false。

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>单元菜单

中的`UICollectionView`每个单元格都能够显示允许剪切、复制和粘贴的菜单 (可选)。 若要创建单元格上的 "编辑" 菜单:

1. 如果`ShouldShowMenu`项应显示菜单, 则重写并返回 true。
1. 对于`CanPerformAction`项可以执行的每个操作, 重写并返回 true, 该操作将是剪切、复制或粘贴。
1. 重`PerformAction`写以执行 "编辑"、"粘贴" 操作的副本。


以下屏幕截图显示了长时间按下某个单元格的菜单:

 [![](uicollectionview-images/04a-menu.png "此屏幕截图显示某个单元格长时间按下的菜单")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>布局

`UICollectionView`支持布局系统, 此布局系统允许定位其所有元素、单元、补充视图和修饰视图, 以独立于`UICollectionView`自身的管理。
使用布局系统, 应用程序可以支持如我们在本文中看到的类似网格的布局, 并提供自定义的布局。

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>布局基础知识

中`UICollectionView`的布局在从`UICollectionViewLayout`继承的类中定义。 布局实现负责为中的`UICollectionView`每个项创建布局特性。 可以通过两种方法创建布局:

- 使用内置`UICollectionViewFlowLayout` 。
- 通过从`UICollectionViewLayout`继承来提供自定义布局。


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>流布局

`UICollectionViewFlowLayout`类提供了一种基于行的布局, 适用于根据我们所见到的单元格网格排列内容。

使用流布局:

- 创建的`UICollectionViewFlowLayout`实例:


```csharp
var layout = new UICollectionViewFlowLayout ();
```

- 将该实例传递到的`UICollectionView`构造函数:


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

这就是在网格中布局内容所需的全部内容。 此外, 当方向发生变化时, `UICollectionViewFlowLayout`句柄会相应地重新排列内容, 如下所示:

 [![](uicollectionview-images/05-layout-orientation.png "方向更改示例")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>节内边距

为了在`UIContentView`周围提供一些空间, 布局`SectionInset`具有类型`UIEdgeInsets`为的属性。 例如, 下面的代码在`UIContentView` `UICollectionViewFlowLayout`由进行布局时, 围绕的每个部分提供50像素缓冲区:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

这会导致部分出现间距, 如下所示:

 [![](uicollectionview-images/06-sectioninset.png "部分周围的间距, 如下所示")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>子类 UICollectionViewFlowLayout

在要直接使用`UICollectionViewFlowLayout`的版本中, 还可以将其划分为子类, 以进一步自定义行内容的布局。 例如, 这可用于创建不将单元格换行到网格中的布局, 而是创建一个具有水平滚动效果的行, 如下所示:

 [![](uicollectionview-images/07-line-layout.png "具有水平滚动效果的单个行")](uicollectionview-images/07-line-layout.png#lightbox)

若要通过子类`UICollectionViewFlowLayout`化实现此操作, 需要:

- 初始化应用于布局本身或构造函数中布局中的所有项的任何布局属性。
- 重`ShouldInvalidateLayoutForBoundsChange`写, 返回 true, 以便在`UICollectionView`更改边界时, 将重新计算单元格的布局。 在这种情况下, 将使用此代码, 以确保在滚动过程中应用应用于 centermost 单元格的转换代码。
- 重`TargetContentOffset`写以使 centermost 单元与的中心`UICollectionView`对齐, 因为滚动将停止。
- 重`LayoutAttributesForElementsInRect`写以返回的`UICollectionViewLayoutAttributes`数组。 每`UICollectionViewLayoutAttribute`个都包含有关如何对特定项进行布局的信息, 包括诸如`Center`其`Size` 、 `ZIndex`和`Transform3D`之类的属性。


下面的代码演示此类实现:

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
  public class LineLayout : UICollectionViewFlowLayout
  {
    public const float ITEM_SIZE = 200.0f;
    public const int ACTIVE_DISTANCE = 200;
    public const float ZOOM_FACTOR = 0.3f;

    public LineLayout ()
    {
      ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
      ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
      MinimumLineSpacing = 50.0f;
    }

    public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
    {
      return true;
    }

    public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
    {
      var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

      foreach (var attributes in array) {
        if (attributes.Frame.IntersectsWith (rect)) {
          float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
          float normalizedDistance = distance / ACTIVE_DISTANCE;
          if (Math.Abs (distance) < ACTIVE_DISTANCE) {
            float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
            attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
            attributes.ZIndex = 1;
          }
        }
      }
      return array;
    }

    public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
    {
      float offSetAdjustment = float.MaxValue;
      float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
      CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
      var array = base.LayoutAttributesForElementsInRect (targetRect);
      foreach (var layoutAttributes in array) {
        float itemHorizontalCenter = (float)layoutAttributes.Center.X;
        if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
          offSetAdjustment = itemHorizontalCenter - horizontalCenter;
        }
      }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
    }

  }
}
```

 <a name="Custom_Layout" />


### <a name="custom-layout"></a>自定义布局

除了使用`UICollectionViewFlowLayout`之外, 还可以通过直接从`UICollectionViewLayout`继承来完全自定义布局。

要重写的关键方法为:

- `PrepareLayout`–用于执行将在整个布局过程中使用的初始几何计算。
- `CollectionViewContentSize`–返回用于显示内容的区域的大小。
- `LayoutAttributesForElementsInRect`–如前面所示的 UICollectionViewFlowLayout 示例所示, 此方法用于提供有关如何布局`UICollectionView`每个项的信息。 不过, 与不同`UICollectionViewFlowLayout`的是, 在创建自定义布局时, 您可以选择定位项。


例如, 可以在循环布局中显示相同的内容, 如下所示:

 [![](uicollectionview-images/08-circle-layout.png "此处所示的循环自定义布局")](uicollectionview-images/08-circle-layout.png#lightbox)

布局的强大之处在于, 从类似网格的布局更改为水平滚动布局, 而在此循环布局中, 只需要更改提供`UICollectionView`的布局类。 中没有任何`UICollectionView`内容, 其委托或数据源代码根本就发生了更改。


## <a name="changes-in-ios-9"></a>IOS 9 中的更改


在 iOS 9 中, 集合视图 (`UICollectionView`) 现在支持通过添加新的默认手势识别器和几个新的支持方法将项拖出框中的项进行重新排序。

使用这些新方法, 您可以轻松实现拖动以在集合视图中重新排序, 并可以选择在重新排序过程的任何阶段自定义项外观。

[![](uicollectionview-images/intro01.png "重新排序过程的示例")](uicollectionview-images/intro01.png#lightbox)

在本文中, 我们将介绍如何实现 Xamarin iOS 应用程序中的重新排序, 以及 iOS 9 对 "集合" 视图控件进行的其他更改:

- [轻松地对项进行重新排序](#Easy-Reordering-of-Items)
  - [简单的重新排序示例](#Simple-Reordering-Example)
  - [使用自定义手势识别器](#Using-a-Custom-Gesture-Recognizer)
  - [自定义布局和重新排序](#Custom-Layouts-and-Reording)
- [集合视图更改](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>项的重新排序

如上所述, 在 iOS 9 中对集合视图的最重大更改之一是添加了简单易用的拖放功能。

在 iOS 9 中, 将重新排序添加到集合视图的最快捷方法是使用`UICollectionViewController`。
集合视图控制器现在具有一个`InstallsStandardGestureForInteractiveMovement`属性, 该属性添加一个支持拖动以重新排列集合中的项的标准*笔势识别器*。
由于默认值为`true`, 因此您只需`MoveItem`实现`UICollectionViewDataSource`类的方法来支持拖放。 例如：

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
  // Reorder our list of items
  ...
}
```

<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>简单的重新排序示例

作为一个快速示例, 请启动新的 Xamarin iOS 项目并编辑**主情节提要**文件。 将拖`UICollectionViewController`到设计图面上:

[![](uicollectionview-images/quick01.png "添加 UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

选择集合视图 (最简单的方法是从文档大纲中执行此操作)。 在 Properties Pad 的 "布局" 选项卡中, 设置以下大小, 如以下屏幕截图中所示:

- **单元大小**:宽度– 60 |高度–60
- **标头大小**:Width – 0 |高度–0
- **页脚大小**:Width – 0 |高度–0
- **最小间距**:单元格– 8 |对于行–8
- **节嵌入**:顶部– 16 |下– 16 |左– 16 |右–16

[![](uicollectionview-images/quick04.png "设置集合视图大小")](uicollectionview-images/quick04.png#lightbox)

接下来, 编辑默认单元:
- 将背景色更改为蓝色
- 添加一个标签作为单元的标题
- 将重复使用标识符设置为**cell**

[![](uicollectionview-images/quick02.png "编辑默认单元")](uicollectionview-images/quick02.png#lightbox)

添加约束, 以在单元格大小更改时保持其位于单元格内:

在_CollectionViewCell_的**属性板**中, 将**类**设置为`TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "将类设置为 TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

将**集合可重复使用**的`Cell`视图设置为:

[![](uicollectionview-images/quick06.png "将集合可重复使用的视图设置为 Cell")](uicollectionview-images/quick06.png#lightbox)

最后, 选择标签, 并将其`TextLabel`命名为:

[![](uicollectionview-images/quick07.png "名称标签 TextLabel")](uicollectionview-images/quick07.png#lightbox)

`TextCollectionViewCell`编辑类并添加以下属性:

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
  public partial class TextCollectionViewCell : UICollectionViewCell
  {
    #region Computed Properties
    public string Title {
      get { return TextLabel.Text; }
      set { TextLabel.Text = value; }
    }
    #endregion

    #region Constructors
    public TextCollectionViewCell (IntPtr handle) : base (handle)
    {
    }
    #endregion
  }
}
```

此标签的属性作为单元的标题公开, 因此可以从代码进行设置。 `Text`

向项目中C#添加一个新类并调用它`WaterfallCollectionSource`。 编辑文件, 使其类似于以下内容:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
  public class WaterfallCollectionSource : UICollectionViewDataSource
  {
    #region Computed Properties
    public WaterfallCollectionView CollectionView { get; set;}
    public List<int> Numbers { get; set; } = new List<int> ();
    #endregion

    #region Constructors
    public WaterfallCollectionSource (WaterfallCollectionView collectionView)
    {
      // Initialize
      CollectionView = collectionView;

      // Init numbers collection
      for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
      }
    }
    #endregion

    #region Override Methods
    public override nint NumberOfSections (UICollectionView collectionView) {
      // We only have one section
      return 1;
    }

    public override nint GetItemsCount (UICollectionView collectionView, nint section) {
      // Return the number of items
      return Numbers.Count;
    }

    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
      // Get a reusable cell and set {~~it's~>its~~} title from the item
      var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
      cell.Title = Numbers [(int)indexPath.Item].ToString();

      return cell;
    }

    public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
      // We can always move items
      return true;
    }

    public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
    {
      // Reorder our list of items
      var item = Numbers [(int)sourceIndexPath.Item];
      Numbers.RemoveAt ((int)sourceIndexPath.Item);
      Numbers.Insert ((int)destinationIndexPath.Item, item);
    }
    #endregion
  }
}
```

此类将成为集合视图的数据源, 并为集合中的每个单元提供信息。
请注意, `MoveItem`实现了方法, 以允许将集合中的项重新排序。

将另一个C#新类添加到项目并调用`WaterfallCollectionDelegate`它。 编辑此文件, 使其类似于以下内容:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
  public class WaterfallCollectionDelegate : UICollectionViewDelegate
  {
    #region Computed Properties
    public WaterfallCollectionView CollectionView { get; set;}
    #endregion

    #region Constructors
    public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
    {

      // Initialize
      CollectionView = collectionView;

    }
    #endregion

    #region Overrides Methods
    public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
      // Always allow for highlighting
      return true;
    }

    public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
    {
      // Get cell and change to green background
      var cell = collectionView.CellForItem(indexPath);
      cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
    }

    public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
    {
      // Get cell and return to blue background
      var cell = collectionView.CellForItem(indexPath);
      cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
    }
    #endregion
  }
}
```

这将充当集合视图的委托。 方法已重写, 以在用户与集合视图中的交互时突出显示某个单元格。

将最后C#一个类添加到项目并调用它`WaterfallCollectionView`。 编辑此文件, 使其类似于以下内容:

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
  [Register("WaterfallCollectionView")]
  public class WaterfallCollectionView : UICollectionView
  {

    #region Constructors
    public WaterfallCollectionView (IntPtr handle) : base (handle)
    {
    }
    #endregion

    #region Override Methods
    public override void AwakeFromNib ()
    {
      base.AwakeFromNib ();

      // Initialize
      DataSource = new WaterfallCollectionSource(this);
      Delegate = new WaterfallCollectionDelegate(this);

    }
    #endregion
  }
}
```

请注意, `Delegate`在从其情节提要 (或**xib**文件) 构造集合视图时, 会设置和。`DataSource`

再次编辑**主情节提要**文件, 选择集合视图并切换到**属性**。 将**类**设置为前面定义`WaterfallCollectionView`的自定义类:

保存对 UI 所做的更改并运行应用。
如果用户从列表中选择某一项并将其拖动到新位置, 则当其他项移出项目的方式时, 它们将自动进行动画处理。
当用户将项放置在新位置时, 它会坚持到该位置。 例如：

[![](uicollectionview-images/intro01.png "将项拖动到新位置的示例")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>使用自定义手势识别器

如果您无法使用`UICollectionViewController`并且必须使用常规`UIViewController`, 或者您想要对拖放手势进行更多控制, 则可以创建自己的自定义手势识别器并在视图加载时将其添加到集合视图。 例如:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  // Create a custom gesture recognizer
  var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

    // Take action based on state
    switch(gesture.State) {
    case UIGestureRecognizerState.Began:
      var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
      if (selectedIndexPath !=null) {
        CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
      }
      break;
    case UIGestureRecognizerState.Changed:
      CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
      break;
    case UIGestureRecognizerState.Ended:
      CollectionView.EndInteractiveMovement();
      break;
    default:
      CollectionView.CancelInteractiveMovement();
      break;
    }

  });

  // Add the custom recognizer to the collection view
  CollectionView.AddGestureRecognizer(longPressGesture);
}
```

这里, 我们将使用几个新方法添加到集合视图来实现和控制拖动操作:

- `BeginInteractiveMovementForItem`-标记移动操作的开始。
- `UpdateInteractiveMovementTargetPosition`-在更新项的位置时发送。
- `EndInteractiveMovement`-标记项移动的结束。
- `CancelInteractiveMovement`-标记用户取消移动操作。

当应用程序运行时, 拖动操作将与集合视图附带的默认拖动手势识别器完全相同。

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>自定义布局和重新排序

在 iOS 9 中, 已添加了几个新方法, 以便在集合视图中使用 "拖动到重新排序" 和 "自定义" 布局。 若要浏览此功能, 请将自定义布局添加到集合。

首先, 将一个名C# `WaterfallCollectionLayout`为的新类添加到项目。 编辑并使其类似于以下内容:

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
  [Register("WaterfallCollectionLayout")]
  public class WaterfallCollectionLayout : UICollectionViewLayout
  {
    #region Private Variables
    private int columnCount = 2;
    private nfloat minimumColumnSpacing = 10;
    private nfloat minimumInterItemSpacing = 10;
    private nfloat headerHeight = 0.0f;
    private nfloat footerHeight = 0.0f;
    private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
    private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
    private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
    private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
    private List<CGRect> unionRects = new List<CGRect>();
    private List<nfloat> columnHeights = new List<nfloat>();
    private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
    private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
    private nfloat unionSize = 20;
    #endregion

    #region Computed Properties
    [Export("ColumnCount")]
    public int ColumnCount {
      get { return columnCount; }
      set {
        WillChangeValue ("ColumnCount");
        columnCount = value;
        DidChangeValue ("ColumnCount");

        InvalidateLayout ();
      }
    }

    [Export("MinimumColumnSpacing")]
    public nfloat MinimumColumnSpacing {
      get { return minimumColumnSpacing; }
      set {
        WillChangeValue ("MinimumColumnSpacing");
        minimumColumnSpacing = value;
        DidChangeValue ("MinimumColumnSpacing");

        InvalidateLayout ();
      }
    }

    [Export("MinimumInterItemSpacing")]
    public nfloat MinimumInterItemSpacing {
      get { return minimumInterItemSpacing; }
      set {
        WillChangeValue ("MinimumInterItemSpacing");
        minimumInterItemSpacing = value;
        DidChangeValue ("MinimumInterItemSpacing");

        InvalidateLayout ();
      }
    }

    [Export("HeaderHeight")]
    public nfloat HeaderHeight {
      get { return headerHeight; }
      set {
        WillChangeValue ("HeaderHeight");
        headerHeight = value;
        DidChangeValue ("HeaderHeight");

        InvalidateLayout ();
      }
    }

    [Export("FooterHeight")]
    public nfloat FooterHeight {
      get { return footerHeight; }
      set {
        WillChangeValue ("FooterHeight");
        footerHeight = value;
        DidChangeValue ("FooterHeight");

        InvalidateLayout ();
      }
    }

    [Export("SectionInset")]
    public UIEdgeInsets SectionInset {
      get { return sectionInset; }
      set {
        WillChangeValue ("SectionInset");
        sectionInset = value;
        DidChangeValue ("SectionInset");

        InvalidateLayout ();
      }
    }

    [Export("ItemRenderDirection")]
    public WaterfallCollectionRenderDirection ItemRenderDirection {
      get { return itemRenderDirection; }
      set {
        WillChangeValue ("ItemRenderDirection");
        itemRenderDirection = value;
        DidChangeValue ("ItemRenderDirection");

        InvalidateLayout ();
      }
    }
    #endregion

    #region Constructors
    public WaterfallCollectionLayout ()
    {
    }

    public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

    }
    #endregion

    #region Public Methods
    public nfloat ItemWidthInSectionAtIndex(int section) {

      var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
      return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
    }
    #endregion

    #region Override Methods
    public override void PrepareLayout ()
    {
      base.PrepareLayout ();

      // Get the number of sections
      var numberofSections = CollectionView.NumberOfSections();
      if (numberofSections == 0)
        return;

      // Reset collections
      headersAttributes.Clear ();
      footersAttributes.Clear ();
      unionRects.Clear ();
      columnHeights.Clear ();
      allItemAttributes.Clear ();
      sectionItemAttributes.Clear ();

      // Initialize column heights
      for (int n = 0; n < ColumnCount; n++) {
        columnHeights.Add ((nfloat)0);
      }

      // Process all sections
      nfloat top = 0.0f;
      var attributes = new UICollectionViewLayoutAttributes ();
      var columnIndex = 0;
      for (nint section = 0; section < numberofSections; ++section) {
        // Calculate section specific metrics
        var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
          MinimumInterItemSpacingForSection (CollectionView, this, section);

        // Calculate widths
        var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
        var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

        // Calculate section header
        var heightHeader = (HeightForHeader == null) ? HeaderHeight :
          HeightForHeader (CollectionView, this, section);

        if (heightHeader > 0) {
          attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
          attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
          headersAttributes.Add (section, attributes);
          allItemAttributes.Add (attributes);

          top = attributes.Frame.GetMaxY ();
        }

        top += SectionInset.Top;
        for (int n = 0; n < ColumnCount; n++) {
          columnHeights [n] = top;
        }

        // Calculate Section Items
        var itemCount = CollectionView.NumberOfItemsInSection(section);
        List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

        for (nint n = 0; n < itemCount; n++) {
          var indexPath = NSIndexPath.FromRowSection (n, section);
          columnIndex = NextColumnIndexForItem (n);
          var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
          var yOffset = columnHeights [columnIndex];
          var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
          nfloat itemHeight = 0.0f;

          if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
            itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
          }

          attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
          attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
          itemAttributes.Add (attributes);
          allItemAttributes.Add (attributes);
          columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
        }
        sectionItemAttributes.Add (itemAttributes);

        // Calculate Section Footer
        nfloat footerHeight = 0.0f;
        columnIndex = LongestColumnIndex();
        top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
        footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

        if (footerHeight > 0) {
          attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
          attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
          footersAttributes.Add (section, attributes);
          allItemAttributes.Add (attributes);
          top = attributes.Frame.GetMaxY ();
        }

        for (int n = 0; n < ColumnCount; n++) {
          columnHeights [n] = top;
        }
      }

      var i =0;
      var attrs = allItemAttributes.Count;
      while(i < attrs) {
        var rect1 = allItemAttributes [i].Frame;
        i = (int)Math.Min (i + unionSize, attrs) - 1;
        var rect2 = allItemAttributes [i].Frame;
        unionRects.Add (CGRect.Union (rect1, rect2));
        i++;
      }

    }

    public override CGSize CollectionViewContentSize {
      get {
        if (CollectionView.NumberOfSections () == 0) {
          return new CGSize (0, 0);
        }

        var contentSize = CollectionView.Bounds.Size;
        contentSize.Height = columnHeights [0];
        return contentSize;
      }
    }

    public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
    {
      if (indexPath.Section >= sectionItemAttributes.Count) {
        return null;
      }

      if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
        return null;
      }

      var list = sectionItemAttributes [indexPath.Section];
      return list [(int)indexPath.Item];
    }

    public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
    {
      var attributes = new UICollectionViewLayoutAttributes ();

      switch (kind) {
      case "header":
        attributes = headersAttributes [indexPath.Section];
        break;
      case "footer":
        attributes = footersAttributes [indexPath.Section];
        break;
      }

      return attributes;
    }

    public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
    {
      var begin = 0;
      var end = unionRects.Count;
      List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();


      for (int i = 0; i < end; i++) {
        if (rect.IntersectsWith(unionRects[i])) {
          begin = i * (int)unionSize;
        }
      }

      for (int i = end - 1; i >= 0; i--) {
        if (rect.IntersectsWith (unionRects [i])) {
          end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
          break;
        }
      }

      for (int i = begin; i < end; i++) {
        var attr = allItemAttributes [i];
        if (rect.IntersectsWith (attr.Frame)) {
          attrs.Add (attr);
        }
      }

      return attrs.ToArray();
    }

    public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
    {
      var oldBounds = CollectionView.Bounds;
      return (newBounds.Width != oldBounds.Width);
    }
    #endregion

    #region Private Methods
    private int ShortestColumnIndex() {
      var index = 0;
      var shortestHeight = nfloat.MaxValue;
      var n = 0;

      // Scan each column for the shortest height
      foreach (nfloat height in columnHeights) {
        if (height < shortestHeight) {
          shortestHeight = height;
          index = n;
        }
        ++n;
      }

      return index;
    }

    private int LongestColumnIndex() {
      var index = 0;
      var longestHeight = nfloat.MinValue;
      var n = 0;

      // Scan each column for the shortest height
      foreach (nfloat height in columnHeights) {
        if (height > longestHeight) {
          longestHeight = height;
          index = n;
        }
        ++n;
      }

      return index;
    }

    private int NextColumnIndexForItem(nint item) {
      var index = 0;

      switch (ItemRenderDirection) {
      case WaterfallCollectionRenderDirection.ShortestFirst:
        index = ShortestColumnIndex ();
        break;
      case WaterfallCollectionRenderDirection.LeftToRight:
        index = ColumnCount;
        break;
      case WaterfallCollectionRenderDirection.RightToLeft:
        index = (ColumnCount - 1) - ((int)item / ColumnCount);
        break;
      }

      return index;
    }
    #endregion

    #region Events
    public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
    public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
    public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

    public event WaterfallCollectionSizeDelegate SizeForItem;
    public event WaterfallCollectionFloatDelegate HeightForHeader;
    public event WaterfallCollectionFloatDelegate HeightForFooter;
    public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
    public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
    #endregion
  }
}
```

此类可用于向集合视图提供自定义的两列瀑布类型布局。
代码使用键值编码 (通过`WillChangeValue`和`DidChangeValue`方法) 为此类中的计算属性提供数据绑定。

接下来, 编辑`WaterfallCollectionSource`并进行以下更改和添加:

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
  // Initialize
  CollectionView = collectionView;

  // Init numbers collection
  for (int n = 0; n < 100; ++n) {
    Numbers.Add (n);
    Heights.Add (rnd.Next (0, 100) + 40.0f);
  }
}
```

这会为将在列表中显示的每个项创建随机高度。

接下来, 编辑`WaterfallCollectionView`类并添加以下帮助器属性:

```csharp
public WaterfallCollectionSource Source {
  get { return (WaterfallCollectionSource)DataSource; }
}
```

这将使从自定义布局获取数据源 (和项高度) 变得更加容易。

最后, 编辑视图控制器并添加以下代码:

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  var waterfallLayout = new WaterfallCollectionLayout ();

  // Wireup events
  waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
    var collection = collectionView as WaterfallCollectionView;
    return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
  };

  // Attach the custom layout to the collection
  CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

这会创建自定义布局的实例, 设置事件以提供每个项的大小并将新布局附加到集合视图。

如果再次运行 Xamarin iOS 应用, "集合" 视图现在将如下所示:

[![](uicollectionview-images/custom01.png "集合视图现在将如下所示")](uicollectionview-images/custom01.png#lightbox)

我们仍可像以前一样拖动项目, 但在删除项目时, 它们现在会更改大小以适应新位置。

## <a name="collection-view-changes"></a>集合视图更改

在以下部分中, 我们将详细介绍 iOS 9 对集合视图中每个类所做的更改。

### <a name="uicollectionview"></a>UICollectionView

为 iOS 9 的`UICollectionView`类进行了以下更改或添加操作:

- `BeginInteractiveMovementForItem`–标记拖动操作的开始。
- `CancelInteractiveMovement`–通知集合视图用户已取消拖动操作。
- `EndInteractiveMovement`–通知集合视图用户已完成拖动操作。
- `GetIndexPathsForVisibleSupplementaryElements`–返回`indexPath`集合视图部分中的页眉或页脚的。
- `GetSupplementaryView`–返回给定的页眉或页脚。
- `GetVisibleSupplementaryViews`–返回所有可见的页眉和页脚的列表。
- `UpdateInteractiveMovementTargetPosition`–通知集合视图, 用户在拖动操作过程中已移动或正在移动该项。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

在 iOS 9 中对`UICollectionViewController`类进行了以下更改或添加:

- `InstallsStandardGestureForInteractiveMovement`–如果`true`将使用自动支持重新排序的新笔势识别器, 则为。
- `CanMoveItem`–当给定的项可进行拖动重新排序时, 通知集合视图。
- `GetTargetContentOffset`–用于获取给定集合视图项的偏移量。
- `GetTargetIndexPathForMove`–为拖动`indexPath`操作获取给定项的。
- `MoveItem`–移动列表中给定项的顺序。


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

在 iOS 9 中对`UICollectionViewDataSource`类进行了以下更改或添加:

- `CanMoveItem`–当给定的项可进行拖动重新排序时, 通知集合视图。
- `MoveItem`–移动列表中给定项的顺序。

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

在 iOS 9 中对`UICollectionViewDelegate`类进行了以下更改或添加:

- `GetTargetContentOffset`–用于获取给定集合视图项的偏移量。
- `GetTargetIndexPathForMove`–为拖动`indexPath`操作获取给定项的。

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

在 iOS 9 中对`UICollectionViewFlowLayout`类进行了以下更改或添加:

- `SectionFootersPinToVisibleBounds`–将节页脚显示在可见的集合视图边界内。
- `SectionHeadersPinToVisibleBounds`–将节标头显示为可见集合视图边界。

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

在 iOS 9 中对`UICollectionViewLayout`类进行了以下更改或添加:

- `GetInvalidationContextForEndingInteractiveMovementOfItems`–当用户完成拖动或取消操作时, 将在拖动操作结束时返回无效的上下文。
- `GetInvalidationContextForInteractivelyMovingItems`–在拖动操作开始时返回无效的上下文。
- `GetLayoutAttributesForInteractivelyMovingItem`–在拖动项时获取给定项的布局特性。
- `GetTargetIndexPathForInteractivelyMovingItem`–在拖动`indexPath`项时, 返回位于给定点的项的。

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

在 iOS 9 中对`UICollectionViewLayoutAttributes`类进行了以下更改或添加:

- `CollisionBoundingPath`–在拖动操作过程中返回两个项的冲突路径。
- `CollisionBoundsType`–返回在拖动操作期间发生的冲突`UIDynamicItemCollisionBoundsType`的类型 (作为)。

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

在 iOS 9 中对`UICollectionViewLayoutInvalidationContext`类进行了以下更改或添加:

- `InteractiveMovementTarget`–返回拖动操作的目标项。
- `PreviousIndexPathsForInteractivelyMovingItems`–返回`indexPaths`拖放操作中涉及的其他项的。
- `TargetIndexPathsForInteractivelyMovingItems`–返回`indexPaths`将因拖动到重新排序操作而重新排序的项的。

### <a name="uicollectionviewsource"></a>UICollectionViewSource

在 iOS 9 中对`UICollectionViewSource`类进行了以下更改或添加:

- `CanMoveItem`–当给定的项可进行拖动重新排序时, 通知集合视图。
- `GetTargetContentOffset`–返回将通过拖放操作移动的项的偏移量。
- `GetTargetIndexPathForMove`–返回`indexPath`将在拖动到重新排序操作期间移动的项的。
- `MoveItem`–移动列表中给定项的顺序。

## <a name="summary"></a>总结

本文介绍了 iOS 9 中的集合视图更改, 并介绍了如何在 Xamarin 中实现它们。
它介绍了如何实现集合视图中的简单的 "拖到重新排序" 操作;使用自定义手势识别器进行重新排序;如何对自定义集合视图布局产生影响。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [集合视图示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-collectionview)
- [SimpleCollectionView (示例)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplecollectionview)
- [事件、协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [使用表和单元格](~/ios/user-interface/controls/tables/index.md)
