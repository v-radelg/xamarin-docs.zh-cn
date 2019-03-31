---
title: 使用 tvOS 导航和焦点在 Xamarin 中
description: 本文介绍如何焦点和如何它用来显示和处理在 Xamarin.tvOS 应用内导航的概念。
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3cb8d1c1d92146e70056c6cf562f2fa1cb028e7c
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677867"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>使用 tvOS 导航和焦点在 Xamarin 中

_本文介绍如何焦点和如何它用来显示和处理在 Xamarin.tvOS 应用内导航的概念。_


这篇文章介绍的概念[焦点](#Focus-and-Selection)以及如何它用于处理[导航](#Navigation)Xamarin.tvOS 应用的用户界面中。 我们将介绍内置 tvOS 导航控件如何使用焦点、 突出显示和选择内容提供 Xamarin.tvOS 应用的用户界面导航。

[![](navigation-focus-images/intro01.png "tvOS 应用用户界面导航")](navigation-focus-images/intro01.png#lightbox)

接下来，我们来看看如何配合使用焦点[视差](#Focus-and-Parallax)并*分层映像*当前导航状态向最终用户提供可视线索。

最后，我们将介绍使用[焦点](#Working-with-Focus)，[焦点更新](#Working-with-Focus-Updates)，[焦点指南](#Working-with-Focus-Guides)，[集合中的焦点](#Working-with-Focus-in-Collections)和[启用视差](#enabling-parallax)Xamarin.tvOS 应用程序中的图像视图。

<a name="Navigation" />

## <a name="navigation"></a>导航

Xamarin.tvOS 应用程序的用户将不与它的接口直接作为 ios 其中用户点击设备的屏幕上的映像但间接从跨空间使用交互[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)。 需要时设计应用程序的用户界面，以便它流很自然地，但保留沉浸在 Apple TV 体验用户记住这一点。

成功的 tvOS 应用顺利地支持应用程序的用途和它而不会调用注意导航本身提供的数据结构的方式实现导航。 设计您的导航，使它感觉自然且熟悉而无需在支配用户界面或绘制焦点从内容和应用程序的用户体验。

[![](navigation-focus-images/nav01.png "TvOS 设置应用程序")](navigation-focus-images/nav01.png#lightbox)

虽然通常使用 Apple TV 中，用户将转到堆栈集屏幕，每个提供一组给定的内容。 反过来，每个新的屏幕可能会导致一个或多个使用如的标准 UI 控件的内容的子屏幕[按钮](~/ios/tvos/user-interface/buttons.md)，[选项卡栏](~/ios/tvos/user-interface/tab-bars.md)，表[集合视图](~/ios/tvos/user-interface/collection-views.md)或[拆分视图](~/ios/tvos/user-interface/split-views.md)。

使用数据的每个新屏幕，用户导航更深入到此堆栈的屏幕。 通过使用**菜单**按钮远程计算机上使用 Siri，它们可以向后导航到要返回到上一屏幕或主菜单中的堆栈。

请记住以下设计你的 tvOS 应用的导航时，Apple 提供建议：

- **布局到快速进行查找的内容且易于导航**-用户想访问内容的最少数量的分流点，在应用内单击，并尽可能轻扫。 简化您的导航和组织内容的屏幕的最小数字。
- **创建流畅接口使用触摸**-确保用户可以移动之间_可获得焦点项_的同时使用手势可能的最小数目的最小冲突。
- **在考虑到中具有焦点的设计**-用户在同一房间与内容交互，因为它们需要与它使用 Siri 远程交互之前将焦点移动到用户界面项。 如果要求才能实现其目标太多手势，用户将获得您的应用程序感到失望。
- **提供向后导航菜单按钮通过**-若要创建的简单且熟悉的体验，允许用户向后使用 Siri 远程导航**菜单**按钮。 按下**菜单**按钮应始终返回到上一屏幕或返回到应用程序的主菜单。 在应用程序的最高级别，按**菜单**按钮应返回到 Apple 电视主页屏幕。
- **通常避免显示后退按钮**-因为按**菜单**上使用 Siri 远程按钮屏幕堆栈中向后导航，应避免显示复制此行为的额外控件。 此规则的例外是购买屏幕或具有破坏性操作 （如删除内容） 的屏幕位置**取消**应还显示按钮。
- **显示在单个屏幕上，而不是许多大型集合**-Siri 远程旨在使浏览大型集合的内容快速和轻松使用手势。 如果您的应用程序适用于可获得焦点项的大型集合，请考虑将它们保留在单个屏幕而不是它们分解成许多屏幕，这需要在用户的一部分的多个导航。
- **使用标准控件的导航**-同样，若要创建的简单且熟悉的用户体验，只要有可能，使用内置`UIKit`页面控件、 选项卡栏，分段控件、 表视图、 集合视图和拆分等控件你的应用导航的视图。 因为用户已熟悉这些元素，它们将直观地能够导航您的应用程序。
- **倾向于水平内容导航**-Apple TV 的性质，轻扫从左到右 Siri 远程上的是比向上和向下更自然。 设计内容布局为你的应用时，请考虑此选项。

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>焦点和选择内容

在 Apple TV 图像、 按钮或其他 UI 元素被视为_焦点_时的当前导航目标。

[![](navigation-focus-images/focus01.png "焦点和选择内容示例")](navigation-focus-images/focus01.png#lightbox)

与不同，在用户直接交互使用设备的触摸屏上的元素的 iOS 设备使用 Siri 远程在室内与从 tvOS 元素进行交互的用户。 若要显示和处理这种用户交互，Apple TV 使用_焦点_基于的模型。

使用笔势和按钮按下上[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，用户将焦点从一个 UI 元素移到另一个。 一旦焦点已移动到所需的元素，用户单击以选择的内容或激活该元素所表示的操作。

焦点发生变动，作为使用精致的动画效果和副作用 （例如映像上视差效果） 用于明确识别当前具有焦点的用户界面项。

Apple 具有用于处理焦点和选择内容的以下建议：

- **对于动态效果，使用内置的 UI 控件**-通过使用`UIKit`和您的用户界面，焦点模型中的焦点 API 将自动应用的默认动画和视觉效果到 UI 元素。 这使应用感觉本机和 Apple TV 平台的用户熟悉并允许可获得焦点的项之间的流畅且直观的移动。
- **将焦点移在预期的方向上**-在 Apple TV 中，几乎每个元素使用间接操作。 例如，用户时使用 Siri 远程将焦点移和系统，自动滚动以使当前聚焦的项保持可见的接口。 如果您的应用程序实现这种类型的交互，请确保焦点移动在用户的手势的方向。 因此，如果用户往下轻扫权限 Siri 远程焦点应移动到右 （这会导致滚动到左侧屏幕）。 此规则的一个例外是使用直接操作 （其中向上轻扫上移该元素） 的全屏幕项。
- **确保已设定焦点项位于 Obvious** -与你的 UI 元素远程交互，你的用户，因为很重要，当前聚焦的项更醒目。通常这将自动由处理内置`UIKit`元素。 对于自定义控件，使用功能，如项大小或卷影显示焦点。
- **使用到使已设定焦点项响应式视差**-小，循环手势 Siri 远程上的导致简要、 实时的方式移动的已设定焦点的项。 这[视差效果](#Focus-and-Parallax)内置`UIKit`_分层映像_让用户连接到已设定焦点的项的了解。
- **创建具有适当大小的可获得焦点项**-具有充足的间距的大型项是更轻松地选择和导航比较小的项。
- **设计用户界面元素看起来很好或者已设定焦点或 Unfocused** -通常 Apple TV 通过增加其大小表示已设定焦点的项。 请确保你的应用的 UI 元素外观精美在以任何演示文稿大小和，如有必要，提供更大大小的元素的资产。
- **表示焦点流畅地更改**-使用动画项之间顺利淡**已设定焦点**和**Unfocused**状态保留转换被他们疑惑不解。
- **不显示光标**-用户希望导航应用程序的 UI 使用焦点而不是将光标在屏幕上四处移动。 您的用户界面应始终使用焦点模型以提供一致的用户体验。

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>使用焦点

可能想要创建的自定义控件，可能会变得可获得焦点项的时间。 如果因此替代`CanBecomeFocused`属性，并返回`true`，否则返回`false`。 例如：

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

随时可以使用`Focused`属性的`UIKit`控件以查看它是否是当前项。 如果`true`UI 项当前具有焦点，否则不会。 例如：

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

虽然通过代码，您不能直接将焦点移到另一个用户界面元素，可以指定哪些 UI 元素首先获得焦点，通过设置加载屏幕时其`PreferredFocusedView`属性设置为`true`。 例如：

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>使用焦点更新 

当用户将导致焦点从一个 UI 元素转到另一个 （例如，在对 Siri 远程手势响应）_焦点更新事件_将发送到失去焦点的项和获得焦点的项。

继承的自定义元素的`UIView`或`UIViewController`，可以重写几种方法来处理焦点更新事件：

- **DidUpdateFocus** -将视图获得或失去焦点时调用此方法。
- **ShouldUpdateFocus** -此方法用于定义允许其中移动焦点。

若要请求，焦点引擎会移动焦点返回到`PreferredFocusedView`UI 元素，调用`SetNeedsUpdateFocus`的视图控制器的方法。

> [!IMPORTANT]
> 调用`SetNeedsUpdateFocus`才起对正在调用它的视图控制器包含当前具有焦点的视图。




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>使用焦点指南

内置于 tvOS 的焦点引擎是适合处理落在水平和垂直网格的项之间移动。 通常情况下，您需要执行任何操作多个添加到界面设计和焦点引擎的 UI 元素将自动处理而无需开发人员干预这些元素之间移动。

但是，可能有时间，因为您的用户界面设计、 必要条件时 UI 元素不处于上水平和垂直网格，并且可能无法访问，因为它们是相互对角线。 这是因为焦点引擎被设计为处理简单，向下，仅 UI 项之间的左和向右移动。

执行以下 UI 布局有关的示例：

 [![](navigation-focus-images/guide01.png "使用焦点指南示例")](navigation-focus-images/guide01.png#lightbox)
 
因为**详细信息**上的水平和垂直网格与按钮不会回退**购买**按钮会向用户无法访问。 但是，这可以轻松地使用更正_重点指南_提供焦点引擎为移动提示。 

重点指南 (`UIFocusGuide`) 公开为 Focusable 到焦点引擎，从而允许焦点重定向到另一个视图的视图的不可见区域。

因此，若要解决上述示例中，下面的代码无法添加到视图控制器，才能创建焦点指南之间**详细信息**并**购买**按钮：

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

首先，一个新`UIFocusGuide`被创建并添加到视图的布局参考线集合使用`AddLayoutGuide`方法。

接下来，相对于调整重点指南上、 左、 宽度和高度的定位点**详细信息**并**购买**按钮进行定位它们之间。 请参阅：

[![](navigation-focus-images/guide02.png "示例重点指南")](navigation-focus-images/guide02.png#lightbox)

还有一点需要注意正在激活新约束，因为它们创建通过设置其`Active`属性设置为`true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

使用新的焦点指南建立并添加到视图，视图控制器的`DidUpdateFocus`方法可以重写并添加以下代码以切换**更多信息**并**购买**按钮：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

首先，此代码获取`NextFocusedView`从`UIFocusUpdateContext`，没有传递 (`context`)。 如果此视图为`null`，然后需要不会进行处理并已退出该方法。

下一步，`nextFocusableItem`进行计算。 如果它匹配**详细信息**或**购买**按钮，焦点将发送到相反按钮使用焦点指南`PreferredFocusedView`属性。 例如：

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

在的任一按钮是焦点班次的源`PreferredFocusedView`清除属性：

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>使用集合中的焦点

确定是否可以单个项中可获得焦点时`UICollectionView`或`UITableView`，将重写的方法`UICollectionViewDelegate`或`UITableViewDelegate`分别。 例如：

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

`CanFocusItem`方法将返回`true`如果当前项可以在具有焦点，否则它返回`false`。

如果你想`UICollectionView`或`UITableView`若要记住，并将焦点还原到最后一个项丢失和重新获得焦点时，将设置`RemembersLastFocusedIndexPath`属性设置为`true`。

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>焦点和视差

如上面所述，用户界面元素被视为_焦点_时的当前项导航事件期间。 项进入焦点时，通常它的大小略有增加，并以可视方式提升使用卷影。 

如果用户使用 Siri 远程上进行速度较慢且循环手势，已设定焦点的项将 sway 实时响应此移动。 Sway 出现时，照明的光泽应用于其图像进行似乎出类拔萃的图面。 给定的一段非活动状态后, 变暗聚焦不准的任何内容和焦点项将变得更大。 

这些效果协同工作以提供电视屏幕上的内容和躺椅上从 Apple TV 与交互的用户之间的心理连接。

在 Apple TV 中，此视差效果用于在整个系统传递的 3D 深度和动态，从而焦点项的意义。 tvOS 使用一系列透明[分层映像](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images)它移动，可以动态扩展以创建这种效果。

分层的映像所需的 tvOS 应用图标和顶层的动态内容的支持。 尽管不要求这样做，Apple 强烈建议在应用程序的 UI 中的任何其他可获得焦点项中实现分层映像。

### <a name="enabling-parallax"></a>启用视差

`UIImageView`控件 (或任何继承的控件`UIImageView`) 自动支持视差效果。 默认情况下，禁用该支持，以启用它，请使用以下代码：

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

此属性设置为`true`，图像视图会自动获取视差效果按用户和焦点中选择此项时。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了焦点和如何使用它来处理 Xamarin.tvOS 应用的用户界面中的导航的概念。 它检查内置 tvOS 导航控件如何使用焦点、 突出显示和选择内容提供导航。 接下来，它介绍了如何焦点可以使用与视差和分层映像，为当前的导航状态向最终用户提供可视线索。 最后，它会检查工作具有焦点，焦点更新集合和启用视差中的焦点。




## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
