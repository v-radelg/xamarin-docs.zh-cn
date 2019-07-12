---
title: 在 Xamarin.iOS 中的集合视图
description: 集合视图允许使用任意布局显示的内容。 这些功能可轻松地创建类似于网格的布局默认情况下，同时还支持自定义布局。
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 890c11908b11b18d6ca626820f1a835d817870da
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829864"
---
# <a name="collection-views-in-xamarinios"></a>在 Xamarin.iOS 中的集合视图

_集合视图允许使用任意布局显示的内容。这些功能可轻松地创建类似于网格的布局默认情况下，同时还支持自定义布局。_

中可用的集合视图`UICollectionView`类中，将介绍使用布局在屏幕上显示多个项中 iOS 6 的新概念。 向提供数据的模式`UICollectionView`创建项并与这些项遵循的相同委派和数据源通常用于 iOS 开发的模式进行交互。

但是，集合视图会使用独立于的布局子系统`UICollectionView`本身。 因此，只需提供不同的布局可以轻松更改表示法的集合视图。

iOS 提供了一个名为的布局类`UICollectionViewFlowLayout`允许基于行的布局如网格来创建任何其他操作。 此外，自定义布局可以也可创建允许你想象的任何演示文稿。

## <a name="uicollectionview-basics"></a>UICollectionView 基础知识

`UICollectionView`类由三个不同项组成：

-  **单元格**– 数据驱动的每个项的视图
-  **补充视图**– 与节关联的数据驱动的视图。
-  **修饰视图**– 非数据驱动的布局由创建视图

## <a name="cells"></a>单元

单元格是表示单个项的集合视图提供在数据集中的对象。 每个单元格是的一个实例`UICollectionViewCell`类，它由三个不同的视图，如下图中所示：

 [![](uicollectionview-images/01-uicollectionviewcell.png "每个单元格组成的三个不同视图，如下所示")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

`UICollectionViewCell`类为每个视图具有以下属性：

-   `ContentView` – 此视图包含该单元格显示的内容。 它将呈现在屏幕上的最顶层 z 顺序。
-   `SelectedBackgroundView` – 单元格具有内置的对所选内容的支持。 此视图用于以可视方式表示选择了某个单元。 呈现正下方`ContentView`时选择了单元格。
-   `BackgroundView` – 单元还可以显示背景，这主讲人`BackgroundView`。 此视图呈现下`SelectedBackgroundView`。


通过设置`ContentView`以便小于`BackgroundView`并`SelectedBackgroundView`，则`BackgroundView`可用于以可视方式帧的内容，while`SelectedBackgroundView`时，将显示选择了某个单元，如下所示：

 [![](uicollectionview-images/02-cells.png "不同的单元格元素")](uicollectionview-images/02-cells.png#lightbox)

上面的屏幕截图中的单元格创建通过继承自`UICollectionViewCell`并设置`ContentView`，`SelectedBackgroundView`和`BackgroundView`属性，分别如下面的代码中所示：

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

补充视图是显示信息的每个部分与关联的视图`UICollectionView`。 单元格，等补充视图是数据驱动。 单元格显示来自数据源的项数据，其中补充视图将显示部分数据，例如在书架书的类别或音乐库中的音乐流派。

例如，补充视图可用来显示特定部分的标头中如下图所示：

 [![](uicollectionview-images/02a-supplementary-view.png "补充视图用于显示标头的特定部分中，如下所示")](uicollectionview-images/02a-supplementary-view.png#lightbox)

若要使用补充的视图，首先需要在中注册`ViewDidLoad`方法：

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

然后，视图需要通过返回`GetViewForSupplementaryElement`创建使用`DequeueReusableSupplementaryView`，并继承`UICollectionReusableView`。 下面的代码段将生成 SupplementaryView 上面的屏幕截图中所示：

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

补充视图是更通用的标头和表尾之外。
它们可放置在集合视图中的任何位置，并可以包含的任何视图，使其外观完全可自定义。

 <a name="Decoration_Views" />


## <a name="decoration-views"></a>修饰视图

修饰视图是纯 visual 视图，可以显示在`UICollectionView`。 与不同的单元格和补充视图，它们不是数据驱动。 它们始终创建布局的子类中，随后可以更改作为内容的布局。 例如，可以使用修饰视图中的内容呈现背景视图滚动`UICollectionView`，如下所示：

 [![](uicollectionview-images/02c-decoration-view.png "具有红色背景修饰视图")](uicollectionview-images/02c-decoration-view.png#lightbox)

 以下代码段在后台更改为示例中的红色`CircleLayout`类：

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

与其他部分的 Io，如`UITableView`并`MKMapView`，`UICollectionView`获取从其数据 *数据源*，通过 Xamarin.iOS 中公开了此 **`UICollectionViewDataSource`** 类。 此类负责提供内容到`UICollectionView`如：

-  **单元格**– 从返回`GetCell`方法。
-  **补充视图**– 从返回`GetViewForSupplementaryElement`方法。
-  **节数**– 从返回`NumberOfSections`方法。 默认值为 1; 如果未实现。
-  **每个部分中的项数**– 从返回`GetItemsCount`方法。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
为方便起见，`UICollectionViewController`类是可用。这自动配置为这两个委托，这将在下一节中进行讨论，并且数据源的其`UICollectionView`视图。

如同`UITableView`，则`UICollectionView`类只会调用其数据源获取的项在屏幕上的单元格。
滚动到屏幕之外的单元格都放入到的重用，队列，如图所示：

 [![](uicollectionview-images/03-cell-reuse.png "滚动到屏幕之外的单元格都将置于到队列以供重复使用，如下所示")](uicollectionview-images/03-cell-reuse.png#lightbox)

单元格重用已得到简化了`UICollectionView`和`UITableView`。 不再需要直接在数据源中创建一个单元格，如果其中一个不是重复使用队列中出现因为向系统注册的单元格。 如果单元格不可用，进行调用以取消排队中重复使用队列的单元格时，将创建 iOS 自动根据的类型或已注册的 nib。
相同的方法也是可用于补充视图。

例如，考虑下面的代码将其注册`AnimalCell`类：

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

当`UICollectionView`由于其项是在屏幕上，需要一个单元格`UICollectionView`调用其数据源的`GetCell`方法。 与如何使用 UITableView 类似，此方法负责配置从支持数据，将单元格`AnimalCell`类在这种情况下。

下面的代码演示的实现`GetCell`，它返回`AnimalCell`实例：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

在调用`DequeReusableCell`是该单元格将或者取消其排队等候，从重复使用队列，则未在队列中，创建将基于在调用注册的类型提供一个单元格时`CollectionView.RegisterClassForCell`。

在这种情况下，通过注册`AnimalCell`类中，iOS 会创建一个新`AnimalCell`在内部，并返回它时进行调用以取消排队一个单元格之后, 进行配置的映像，包含在 animal 类中并且返回要显示到`UICollectionView`.

 <a name="Delegate" />


### <a name="delegate"></a>委托

`UICollectionView`类使用类型的委托`UICollectionViewDelegate`若要支持与中的内容进行交互`UICollectionView`。 这允许控制：

-  **单元格选择**– 确定选择了单元格。
-  **单元格突出显示**– 确定当前接触单元格。
-  **单元格菜单**– 长按手势的响应中的单元格显示菜单。


与数据源一样`UICollectionViewController`默认情况下为的委托配置`UICollectionView`。

 <a name="Cell_HighLighting" />


#### <a name="cell-highlighting"></a>单元格突出显示

当单元格按下的单元格转换为突出显示的状态，且其未选择直到用户手指从单元格。 这样，临时更改中的单元格的外观实际选择之前。 选择它后该单元格的`SelectedBackgroundView`显示。 下图显示了突出显示的状态，只需在所选内容发生之前：

 [![](uicollectionview-images/04-cell-highlight.png "此图显示了突出显示的状态，只需在所选内容发生之前")](uicollectionview-images/04-cell-highlight.png#lightbox)

若要实现突出显示，请`ItemHighlighted`和`ItemUnhighlighted`方法的`UICollectionViewDelegate`可用。 例如，下面的代码将应用的黄色背景`ContentView`时突出显示该单元格时，和白色背景时未突出显示，如在上图中所示：

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

默认情况下启用选择`UICollectionView`。 若要禁用所选内容，请重写`ShouldHighlightItem`并返回 false，如下所示：

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

突出显示处于禁用状态，选择单元格的过程也是已禁用。 此外，还有`ShouldSelectItem`方法，用于控制所选内容直接，但如果`ShouldHighlightItem`实现，并将返回 false，`ShouldSelectItem`不调用。

 `ShouldSelectItem` 允许选择以逐个项的项，打开或关闭时`ShouldHighlightItem`未实现。 它还允许突出显示而无需选择，如果`ShouldHighlightItem`实现，则返回 true，同时`ShouldSelectItem`返回 false。

 <a name="Cell_Menus" />


#### <a name="cell-menus"></a>单元格菜单

每个单元格中`UICollectionView`能够显示一个菜单，使剪切、 复制和粘贴 （可选） 支持。 若要创建某个单元格上的编辑菜单：

1.  重写`ShouldShowMenu`并返回 true，如果项应显示一个菜单。
1.  重写`CanPerformAction`并返回其中会出现任何剪切、 复制或粘贴的项可以执行，每个操作，则返回 true。
1.  重写`PerformAction`若要执行的编辑，请将复制的粘贴操作。


长按下一个单元格时，以下屏幕截图显示菜单：

 [![](uicollectionview-images/04a-menu.png "长按下一个单元格时，此屏幕截图显示菜单")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />


## <a name="layout"></a>布局

`UICollectionView` 支持允许的所有元素，单元格、 补充视图和修饰视图，管理独立于位置的布局系统`UICollectionView`本身。
使用布局系统，应用程序可以支持类似于网格的一个我们在本文中中, 所见，以及提供自定义布局的布局。

 <a name="Layout_Basics" />


### <a name="layout-basics"></a>布局基础知识

中的布局`UICollectionView`继承的类中定义`UICollectionViewLayout`。 布局实现负责创建中的每个项的布局属性`UICollectionView`。 有两种方法来创建布局：

-  使用内置`UICollectionViewFlowLayout`。
-  通过继承提供自定义布局`UICollectionViewLayout`。


 <a name="Flow_Layout" />


### <a name="flow-layout"></a>流布局

`UICollectionViewFlowLayout`类提供了适用于排列在网格单元格中的内容，正如我们所看到的基于行的布局。

若要使用的流布局：

-  创建的实例`UICollectionViewFlowLayout`:


```csharp
var layout = new UICollectionViewFlowLayout ();
```

-  将该实例传递给构造函数的`UICollectionView`:


```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

这是它只需要一个网格中的内容。 此外，方向发生更改时，`UICollectionViewFlowLayout`处理重新排列内容相应地，如下所示：

 [![](uicollectionview-images/05-layout-orientation.png "方向更改的示例")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />


#### <a name="section-inset"></a>部分嵌入

若要提供围绕一些空间`UIContentView`，布局都`SectionInset`类型的属性`UIEdgeInsets`。 例如，下面的代码提供 50 像素缓冲区的每个部分围绕`UIContentView`时进行布局的`UICollectionViewFlowLayout`:

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

这会导致的区域，如下所示的周围的间距：

 [![](uicollectionview-images/06-sectioninset.png "如下所示在该节周围的间距")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />


#### <a name="subclassing-uicollectionviewflowlayout"></a>子类化 UICollectionViewFlowLayout

在使用的版本中`UICollectionViewFlowLayout`直接，它可以也子类化来进一步自定义内容沿着一条线的布局。 例如，这可以用于创建不会包装到一个网格单元格，但改为使用水平滚动效果，将创建单个行的布局，如下所示：

 [![](uicollectionview-images/07-line-layout.png "具有水平滚动效果的单个行")](uicollectionview-images/07-line-layout.png#lightbox)

若要实现这一点： 子类化`UICollectionViewFlowLayout`要求：

-  正在初始化应用于自身的布局的任何布局属性或构造函数中的布局中的所有项。
-  重写`ShouldInvalidateLayoutForBoundsChange`，使其返回 true，这样，当边界`UICollectionView`将重新计算的更改，单元格的布局。 使用此转换应用于靠近单元格将滚动过程中应用这种情况下确保代码。
-  重写`TargetContentOffset`使靠近单元格的中心的管理单元`UICollectionView`作为滚动停止。
-  重写`LayoutAttributesForElementsInRect`返回的数组`UICollectionViewLayoutAttributes`。 每个`UICollectionViewLayoutAttribute`包含有关如何布局的特定项，如包括属性的信息及其`Center`， `Size` ，`ZIndex`和`Transform3D`。


下面的代码显示了此类实现：

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

除了使用之外`UICollectionViewFlowLayout`，布局也可以完全自定义通过直接从继承`UICollectionViewLayout`。

若要重写的主要方法是：

-   `PrepareLayout` – 用于执行将在整个布局过程中使用的初始几何计算。
-   `CollectionViewContentSize` -返回用于显示内容的区域的大小。
-   `LayoutAttributesForElementsInRect` --如前面所示 UICollectionViewFlowLayout 示例中，使用此方法将信息提供给`UICollectionView`如何布局有关每个项。 但是，与不同`UICollectionViewFlowLayout`，但你选择在创建自定义布局，可以定位项。


例如，相同的内容会以循环的布局，如下所示：

 [![](uicollectionview-images/08-circle-layout.png "循环的自定义布局，如下所示")](uicollectionview-images/08-circle-layout.png#lightbox)

有关布局的强大件事是从类似于网格的布局更改为水平滚动布局，和随后向此循环布局都需要提供给布局类`UICollectionView`进行更改。 在中为 nothing `UICollectionView`，其委托或数据源的代码更改根本。


## <a name="changes-in-ios-9"></a>IOS 9 中的更改


在 iOS 9，集合视图中 (`UICollectionView`) 现在支持将通过添加新的默认手势识别程序和几个新的支持方法在初始状态下的项重新排序。

使用这些新方法，可以轻松地实现拖放以重新排序集合视图中，可以选择自定义项外观重新排序过程任何阶段。

[![](uicollectionview-images/intro01.png "重新排序过程的示例")](uicollectionview-images/intro01.png#lightbox)

在本文中，我们将查看在 Xamarin.iOS 应用程序以及一些其他 iOS 9 具有所做的更改到集合视图控件中实现拖放重新排序：

- [项轻松重新排序](#Easy-Reordering-of-Items)
    - [简单的重新排序示例](#Simple-Reordering-Example)
    - [使用自定义笔势识别器](#Using-a-Custom-Gesture-Recognizer)
    - [自定义布局和重新排序](#Custom-Layouts-and-Reording)
- [集合视图更改](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>项重新排序

如上面所述，对 iOS 9 中的集合视图的最重要更改之一是添加了现成的简单拖放重新排序功能。

在 iOS 9，添加到集合视图重新排序的最快方法是使用`UICollectionViewController`。
集合视图控制器现在具有`InstallsStandardGestureForInteractiveMovement`属性，它将添加一个标准*笔势识别器*支持拖动以重新排列集合中的项。
因为默认值为`true`，只需实现`MoveItem`方法的`UICollectionViewDataSource`类，以支持拖放重新排序。 例如:

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    // Reorder our list of items
    ...
}
```
<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>简单的重新排序示例

作为快速示例中，启动一个新的 Xamarin.iOS 项目和编辑**Main.storyboard**文件。 拖动`UICollectionViewController`拖到设计图面：

[![](uicollectionview-images/quick01.png "添加 UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

选择 （它可能是最简单的方法执行此操作从文档大纲） 的集合视图。 在属性面板的布局选项卡中，设置以下大小，如下面的屏幕截图中所示：

- **单元格大小**:宽度-60 |高度-60
- **标头大小**:宽度-0 |高度-0
- **页脚大小**:宽度-0 |高度-0
- **最小间距**:单元格 – 8 |行 – 8
- **部分内边距**:上-16 |底部 – 16 |左-16 |右 – 16

[![](uicollectionview-images/quick04.png "设置集合视图大小步骤")](uicollectionview-images/quick04.png#lightbox)

接下来，编辑单元格的默认值：
    - 更改其背景颜色设置为蓝色
    - 添加一个标签，使其作为该单元格的标题
    - 将重复使用标识符设置为**单元格**

[![](uicollectionview-images/quick02.png "编辑单元格的默认值")](uicollectionview-images/quick02.png#lightbox)

添加约束，以保留更改大小时的单元格内居中标签：

在**属性面板**有关_CollectionViewCell_并设置**类**到`TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "将类设置为 TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

设置**集合的可重用视图**到`Cell`:

[![](uicollectionview-images/quick06.png "设置为单元格的集合可重用视图")](uicollectionview-images/quick06.png#lightbox)

最后，选择标签并将其命名`TextLabel`:

[![](uicollectionview-images/quick07.png "名称标签 TextLabel")](uicollectionview-images/quick07.png#lightbox)

编辑`TextCollectionViewCell`类，并添加以下属性。:

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

此处`Text`标签的属性公开为标题的单元格，因此可以从代码中设置。

添加一个新C#类到项目，并调用其`WaterfallCollectionSource`。 编辑文件，并使其看起来如下所示：

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

此类将成为我们集合视图的数据源，并提供集合中每个单元格的信息。
请注意，`MoveItem`实现方法是为了允许为集合中的项将重新排序。

添加另一个新C#类到项目，并调用其`WaterfallCollectionDelegate`。 编辑此文件，并使其看起来如下所示：

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

这将充当我们集合视图的委托。 方法已重写以在用户与之交互集合视图中突出显示单元格。

添加最后一个C#类到项目，并调用其`WaterfallCollectionView`。 编辑此文件，并使其看起来如下所示：

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

请注意，`DataSource`并`Delegate`我们在上面创建的时从其情节提要构造集合视图设置 (或 **.xib**文件)。

编辑**Main.storyboard**再次文件并选择集合视图切换到**属性**。 设置**类**到自定义`WaterfallCollectionView`我们上面定义的类：

保存对 UI 所做的更改并运行应用程序。
如果用户从列表中选择项并将其拖动到新位置时，其他项将设置动画效果自动随着开的项。
当用户将项放置在新位置时，它将只讨论该位置。 例如:

[![](uicollectionview-images/intro01.png "举例说明如何将项拖动到新位置")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>使用自定义笔势识别器

您不能使用的情况下`UICollectionViewController`，并且必须使用正则表达式`UIViewController`，或如果你想要使更好地控制拖放手势，您可以创建你自己的自定义笔势识别器并加载视图时将其添加到集合视图。 例如：

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

此处我们将使用几个新方法添加到集合视图来实现，可以控制拖动操作：

- `BeginInteractiveMovementForItem` -将标记移动操作的开始。
- `UpdateInteractiveMovementTargetPosition` -，则发送更新项的位置。
- `EndInteractiveMovement` -将项移动结束标记。
- `CancelInteractiveMovement` -将标记用户取消移动操作。

运行应用程序时，拖放操作将默认拖动手势识别器附带集合视图时一样工作。

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>自定义布局和重新排序

在 iOS 9 中，已添加几个新方法以使用集合视图中的拖放重新排序和自定义布局。 若要浏览此功能，让我们向集合添加自定义布局。

首先，添加一个新C#类调用`WaterfallCollectionLayout`到项目。 对其进行编辑，并使其看起来如下所示：

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

这可以是类用于提供自定义的两个列、 瀑布图类型布局到集合视图。
该代码使用键-值编码 (通过`WillChangeValue`和`DidChangeValue`方法) 来提供此类中我们计算属性的数据绑定。

接下来，编辑`WaterfallCollectionSource`并进行以下更改和添加：

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

这将为每个项将显示在列表中创建随机的高度。

接下来，编辑`WaterfallCollectionView`类，并添加以下帮助程序属性：

```csharp
public WaterfallCollectionSource Source {
    get { return (WaterfallCollectionSource)DataSource; }
}
```

这将使更轻松地在我们的数据源 （和项高度） 获得从自定义布局。

最后，编辑视图控制器，并添加以下代码：

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

这创建我们的自定义布局的实例，设置事件，以便提供每个项的大小并将新的布局附加到集合视图。

我们再次运行 Xamarin.iOS 应用程序，如果集合视图将现在如下所示：

[![](uicollectionview-images/custom01.png "集合视图现在如下所示")](uicollectionview-images/custom01.png#lightbox)

我们可以仍是拖放到重排项之前，但现在更改的项大小以适合其新位置时将其删除。

## <a name="collection-view-changes"></a>集合视图更改

在以下部分中，我们将详细的介绍了 iOS 9 到集合视图中的每个类所做的更改。

### <a name="uicollectionview"></a>UICollectionView

对进行以下更改或添加`UICollectionView`适用于 iOS 9 的类：

- `BeginInteractiveMovementForItem` – 标记的拖动操作开始位置。
- `CancelInteractiveMovement` – 告知集合视图用户已取消拖动操作。
- `EndInteractiveMovement` – 告知集合视图在用户完成拖动操作。
- `GetIndexPathsForVisibleSupplementaryElements` -返回`indexPath`的页眉或页脚中集合视图部分。
- `GetSupplementaryView` -返回给定的页眉或页脚。
- `GetVisibleSupplementaryViews` -返回所有可见的标头和表尾的列表。
- `UpdateInteractiveMovementTargetPosition` – 告知集合视图用户已移动，或在移动，在拖动操作期间的项。

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

对进行以下更改或添加`UICollectionViewController`iOS 9 中的类：

- `InstallsStandardGestureForInteractiveMovement` -如果`true`将使用新笔势识别器的自动支持拖放重新排序。
- `CanMoveItem` – 如果给定的项目可以拖放重新排序，则通知集合视图。
- `GetTargetContentOffset` – 用于获取给定的集合视图项的偏移量。
- `GetTargetIndexPathForMove` – 获取`indexPath`的拖动操作的给定项。
- `MoveItem` – 在列表中移动给定项的顺序。


### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

对进行以下更改或添加`UICollectionViewDataSource`iOS 9 中的类：

- `CanMoveItem` – 如果给定的项目可以拖放重新排序，则通知集合视图。
- `MoveItem` – 在列表中移动给定项的顺序。

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

对进行以下更改或添加`UICollectionViewDelegate`iOS 9 中的类：

- `GetTargetContentOffset` – 用于获取给定的集合视图项的偏移量。
- `GetTargetIndexPathForMove` – 获取`indexPath`的拖动操作的给定项。

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

对进行以下更改或添加`UICollectionViewFlowLayout`iOS 9 中的类：

- `SectionFootersPinToVisibleBounds` – 始终处于可见集合视图边界部分页脚。
- `SectionHeadersPinToVisibleBounds` – 始终处于可见集合视图边界部分标头。

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

对进行以下更改或添加`UICollectionViewLayout`iOS 9 中的类：

- `GetInvalidationContextForEndingInteractiveMovementOfItems` – 当用户完成拖动或取消它，则返回在拖动操作结束时失效上下文。
- `GetInvalidationContextForInteractivelyMovingItems` -返回拖动操作开始处的无效化上下文。
- `GetLayoutAttributesForInteractivelyMovingItem` – 获取布局特性给定项目的同时拖动项。
- `GetTargetIndexPathForInteractivelyMovingItem` -返回`indexPath`拖动项时在给定时间点的项。

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

对进行以下更改或添加`UICollectionViewLayoutAttributes`iOS 9 中的类：

- `CollisionBoundingPath` – 在拖动操作过程中返回两个项目的冲突的路径。
- `CollisionBoundsType` -返回的冲突类型 (作为`UIDynamicItemCollisionBoundsType`) 已在拖动操作过程中发生。

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

对进行以下更改或添加`UICollectionViewLayoutInvalidationContext`iOS 9 中的类：

- `InteractiveMovementTarget` -返回拖动操作的目标的项。
- `PreviousIndexPathsForInteractivelyMovingItems` -返回`indexPaths`的其他项参与拖放进行重新排序操作。
- `TargetIndexPathsForInteractivelyMovingItems` -返回`indexPaths`的项将作为拖放重新排序操作结果重新排序。

### <a name="uicollectionviewsource"></a>UICollectionViewSource

对进行以下更改或添加`UICollectionViewSource`iOS 9 中的类：

- `CanMoveItem` – 如果给定的项目可以拖放重新排序，则通知集合视图。
- `GetTargetContentOffset` -返回通过拖动重新排序操作将被移动的项的偏移量。
- `GetTargetIndexPathForMove` -返回`indexPath`将拖放重新排序操作期间移动的项。
- `MoveItem` – 在列表中移动给定项的顺序。

## <a name="summary"></a>总结

本文已涵盖在 iOS 9 中的集合视图的更改，并介绍了如何在 Xamarin.iOS 中实现这些。
它包括在集合视图; 中实现一个简单的拖放重新排序操作与重新拖动到排序; 使用自定义笔势识别器和拖放重新排序如何影响自定义集合视图布局。

## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [集合视图示例](https://developer.xamarin.com/samples/monotouch/ios9/CollectionView/)
- [SimpleCollectionView （示例）](https://developer.xamarin.com/samples/monotouch/SimpleCollectionView/)
- [事件、协议和委托](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [使用表和单元格](~/ios/user-interface/controls/tables/index.md)
