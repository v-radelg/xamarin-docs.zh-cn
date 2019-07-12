---
title: 在 Xamarin.iOS 中统一的情节提要
description: 本文档介绍了在 Xamarin.iOS 中统一的情节提要。 统一的情节提要允许开发人员支持具有单个接口定义的多个屏幕大小。
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: c9d98d9d3052f52dc7860ba513756e3a33d1dc58
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831920"
---
# <a name="unified-storyboards-in-xamarinios"></a>在 Xamarin.iOS 中统一的情节提要

iOS 8 包括创建用户界面的新的、 更简单易用的机制 — 统一情节提要。 使用单个情节提要以涵盖所有不同的硬件屏幕大小，可以在创建快速且高度可响应视图"设计的一次、 使用多"样式。

因为开发人员不再需要创建适用于 iPhone 和 iPad 设备单独和特定的情节提要，它们可以灵活地使用一个公共接口设计应用程序，然后自定义不同的大小类的接口。 这样一来，应用程序可适用于每个外形规格的优势，可以优化每个用户界面以提供最佳体验。

<a name="size-classes" />

## <a name="size-classes"></a>大小类

在 iOS 8 之前，开发人员使用`UIInterfaceOrientation`和`UIInterfaceIdiom`来区分纵向和横向模式之间和 iPhone 和 iPad 设备之间。 在 iOS8，确定方向和设备是通过使用*大小类*。

设备由大小类定义中垂直和水平轴，有两种类型的大小在 iOS 8 中的类：

-  **正则**– 这是大屏幕大小 （例如 iPad) 或让人觉得很大的一个小工具 (如 `UIScrollView`
-  **Compact** – 这是用于较小的设备 （如 iPhone)。 此大小会考虑设备的方向。


如果同时使用了两个概念，结果将是一个 2 x 2 网格，定义可在这两个不同方向的不同可能大小，如以下关系图中所示：

 [![](unified-storyboards-images/sizeclassgrid.png "在常规模式和 Compact 方向定义的不同的可能大小，可以使用一个 2 x 2 网格")](unified-storyboards-images/sizeclassgrid.png#lightbox)

开发人员可以创建一个视图控制器，使用任何可用的四个功能 （如以上各图所示），会导致不同的布局。

### <a name="ipad-size-classes"></a>iPad 大小类

IPad，由于大小，具有**正则**类对于这两个方向的大小。

 [![](unified-storyboards-images/image1.png "iPad 大小类")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>iPhone 大小类

IPhone 也具有不同大小类基于设备的方向：

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone 大小类")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  当设备处于纵向模式下时，屏幕上有**compact**水平类和**正则**垂直
-  当设备处于横向模式下时，将从纵向模式反转屏幕类。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 Plus 大小类

大小是在纵向上使用，但使用不同的变更时更早的 Iphone 相同：

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 Plus 大小类")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

因为 iPhone 6 Plus 有一个足够大的屏幕，它能够在横向模式中有一个常规的宽度大小类。

### <a name="support-for-a-new-screen-scale"></a>对新的屏幕规模的支持

IPhone 6 Plus 使用新的 Retina HD 显示 3.0 （三次原始 iPhone 屏幕分辨率） 屏幕缩放比例。 若要在这些设备上提供尽可能最佳的体验，包括此屏幕缩放设计新的图案。 在 Xcode 6 和更高，资产目录可以包含 1 x、 2x，和 3 x 大小; 图像只需添加新图像资产和 iOS iPhone 6 上运行时，将选择正确的资产加上。

此外加载行为在 iOS 中的图像识别`@3x`上图像文件后缀。 例如，如果开发人员使用以下文件名称的应用程序的捆绑包中包括的图像资产 （不同的分辨率）： `MonkeyIcon.png`， `MonkeyIcon@2x.png`，和`MonkeyIcon@3x.png`。 在 iPhone 6 Plus`MonkeyIcon@3x.png`映像将自动使用，如果开发人员加载图像使用以下代码：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
或如果它们将图像分配给使用 iOS 的 UI 元素与设计器`MonkeyIcon.png`，则`MonkeyIcon@3x.png`将再次自动使用，在 iPhone 6 Plus。

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>动态启动屏幕

当 iOS 应用程序启动的用户的应用程序是实际开始向上向提供反馈，启动屏幕文件将显示为初始屏幕。 在 iOS 8 之前，开发人员将不得不包括多个`Default.png`图像应用程序将运行在每个设备类型、 方向和屏幕分辨率的资产。

新增到 iOS 8、 开发人员可以创建单个原子`.xib`文件中使用自动布局和大小类创建的 Xcode*动态启动屏幕*，将适用于每个设备、 分辨率和方向。 这不仅会减少开发人员可以创建和维护所有所需的图像资产的所需的工作量，但会减少应用程序的已安装捆绑包的大小。

## <a name="traits"></a>Traits

特征是可用于确定如何布局更改为其环境的变化的属性。 它们包含的一组属性 (`HorizontalSizeClass`并`VerticalSizeClass`基于`UIUserInterfaceSizeClass`)，以及接口惯用语法 ( `UIUserInterfaceIdiom`) 显示比例和。

所有上述状态将封装在一个容器，Apple 将称为特征集合 ( `UITraitCollection`)，其中不仅包含包含属性以及它们的值。

## <a name="trait-environment"></a>特征环境

特征环境 iOS 8 中新的接口，并且能够返回特性集合的以下对象：

-  屏幕 ( `UIScreens` )。
-  Windows ( `UIWindows` )。
-  查看控制器 ( `UIViewController` )。
-  视图 ( `UIView` )。
-  演示文稿控制器 ( `UIPresentationController` )。


开发人员使用特征环境所返回的特征集合来确定应如何将用户界面布局方式。

所有特征环境使层次结构，如以下关系图中所示：

 [![](unified-storyboards-images/viewhierarchy.png "特征环境层次结构关系图")](unified-storyboards-images/viewhierarchy.png#lightbox)

特性集合，每个上述特征环境包含将传递，默认情况下，从父级到子级环境。

获取当前的特性集合，除了特征环境还具有`TraitCollectionDidChange`方法，可以在视图或视图控制器子类中重写该方法。 开发人员可以使用此方法修改任何依赖特征，这些特征已更改时的 UI 元素。

## <a name="typical-trait-collections"></a>典型特征集合

本部分将介绍典型的用户体验时使用 iOS 8 的特性集合的类型。

以下是开发人员可能会看到在 iPhone 的典型特征集合：

|Property|值|
|--- |--- |
|`HorizontalSizeClass`|压缩|
|`VerticalSizeClass`|规则|
|`UserInterfaceIdom`|电话|
|`DisplayScale`|2.0|

设置上述表示完全限定特性集合，因为它具有所有其特征属性的值。

还有可能具有缺少它的一些值的特性集合 (它是 Apple 指作为*未指定*):

|Property|值|
|--- |--- |
|`HorizontalSizeClass`|压缩|
|`VerticalSizeClass`|未指定|
|`UserInterfaceIdom`|未指定|
|`DisplayScale`|未指定|

通常情况下，但是，当开发人员要求提供其特征集合特征环境，它将返回的完全限定的集合在上面的示例所示。

如果在当前视图层次结构内不是一个特征的环境 （如视图或视图控制器），开发人员可能会返回未指定的值的一个或多个特征属性。

如果它们使用其中一个提供由 Apple，如创建方法，开发人员还将获得部分限定的特征集合`UITraitCollection.FromHorizontalSizeClass`，以创建新的集合。

可以对多个特征集合执行的一个操作将其进行比较，其中涉及询问一个特性集合是否包含另一个。 指什么*包容*是，对于第二个集合中指定任何特征，值必须匹配与第一个集合中的值完全。

若要测试两个特征，请使用`Contains`方法的`UITraitCollection`传入特征要测试的值。

开发人员可以手动执行比较，在代码中以确定如何布局视图或视图控制器。 但是，`UIKit`在内部使用此方法来提供其部分功能，如下所示外观代理，例如。

## <a name="appearance-proxy"></a>外观代理

若要允许开发人员自定义其视图的属性的 iOS 的早期版本中引入了外观代理。 它已在 iOS 8 支持特征集合扩展。

外观代理现在包括新的方法， `AppearanceForTraitCollection`，给定传递中的特性集合返回一个新的外观代理。 开发人员可以执行的任何自定义外观代理才会生效的视图的符合到指定的特性集合。

通常，开发人员将传入到部分指定的特性集合`AppearanceForTraitCollection`方法，例如指定了水平大小类的压缩，以便他们可以自定义的任意视图中是 compact 水平的应用程序。

## <a name="uiimage"></a>UIImage

Apple 已添加到特性集合的另一个类`UIImage`。 在过去，开发人员必须指定@1X和@2x他们打算在应用程序 （如图标） 中包含任何位图图形资产的版本。

iOS 8 已扩展，使开发人员能够在基于一组特征的图像目录中包含的图像的多个版本。 例如，开发人员可能包括使用 Compact 的特征类和任何其他集合的实际尺寸的图像的较小的图像。

映像之一内的使用时`UIImageView`类，图像视图将自动为其特征集合显示图像的正确版本。 如果特征环境更改 （如用户切换设备从纵向向横向） 时，图像视图将自动选择新的图像大小，以匹配新的特性集合，并更改其大小以匹配的当前版本的映像显示。

## <a name="uiimageasset"></a>UIImageAsset

Apple 已添加名为 iOS 8 的新类`UIImageAsset`为开发人员提供更多控制映像选择。

图像资产封装所有的不同版本的映像，并允许开发人员以寻求匹配已传入的特性集合的特定映像。 图像可以添加或删除从图像资产，实时上。

图像资产的详细信息，请参阅 Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)文档。

## <a name="combining-trait-collections"></a>组合特征集合

开发人员可以执行特征集合的另一个功能是添加两个一起将会导致其中一个集合中的未指定的值将替换为第二个中的指定值的组合集合。 这是使用`FromTraitsFromCollections`方法的`UITraitCollection`类。

如上所述，如果任何特征在其中一个特征集合中未指定，在另一个指定，则值将设置为指定的版本。 但是，如果有指定的给定值的多个版本，从上一个值特征集合将使用的值。

## <a name="adaptive-view-controllers"></a>自适应视图控制器

本部分将介绍 iOS 视图和视图控制器采用特征和大小类会自动为开发人员的应用程序中的适应能力更强的概念的详细信息。

### <a name="split-view-controller"></a>拆分视图控制器

最多在 iOS 8 中已更改的视图控制器类之一是`UISplitViewController`类。 在过去，开发人员通常使用拆分视图控制器在 iPad 版应用程序，然后他们必须提供其视图层次结构的完全不同版本的 iPhone 版应用。

在 iOS 8，`UISplitViewController`它允许开发人员可以创建将用于 iPhone 和 iPad 的功能的一个视图控制器层次结构，类是在 （iPad 和 iPhone），这两个平台上可用。

IPhone 横向时，拆分视图控制器将显示其视图的并排方案，就像在 iPad 上显示时一样。

### <a name="overriding-traits"></a>重写特征

从父容器下的子容器，如下所示下图显示拆分视图控制器上以横向方式 iPad 级联特征环境：

 [![](unified-storyboards-images/cascadingclasses01.png "在横向方向 iPad 上拆分视图控制器")](unified-storyboards-images/cascadingclasses01.png#lightbox)

由于 iPad 常规大小类的水平和垂直方向，拆分视图将显示母版和详细信息视图。

IPhone 上，其中大小类是在这两个方向 compact，拆分视图控制器仅显示详细信息视图中，如下所示：

 [![](unified-storyboards-images/cascadingclasses02.png "拆分视图控制器才会显示详细信息视图")](unified-storyboards-images/cascadingclasses02.png#lightbox)

在应用程序开发人员希望在横向方向显示在 iPhone 上的母版和详细信息视图，开发人员必须为拆分视图控制器插入父容器和重写特性集合。 下图中所示：

 [![](unified-storyboards-images/cascadingclasses03.png "开发人员必须为拆分视图控制器插入父容器和重写特性集合")](unified-storyboards-images/cascadingclasses03.png#lightbox)

一个`UIView`设置为父级的拆分视图控制器和`SetOverrideTraitCollection`视图中新的特性集合传递和确定目标拆分视图控制器上调用方法。 新的特性集合重写`HorizontalSizeClass`，将其设置为`Regular`，以便拆分视图控制器将以横向方式显示在 iPhone 上的母版和详细信息视图。

请注意，`VerticalSizeClass`已设置为`unspecified`，这允许新的特性集合，以添加到特性集合的父级上导致`Compact VerticalSizeClass`拆分视图控制器的子级。

### <a name="trait-changes"></a>特征的更改

本部分将介绍，详细的介绍，特征集合如何转换特征环境发生更改时。 例如，当将设备旋转从纵向向横向。

 [![](unified-storyboards-images/traittransitions01.png "纵向向横向特征的更改概述")](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 执行某些安装程序以准备好进行过渡。 接下来，在系统之间进行动画处理的转换状态。 最后，iOS 8 中清除转换过程中所需的任何临时状态。

iOS 8 提供了开发人员可以使用下表中所示参与特征更改的多个回调：

|Phase|回调|描述|
|--- |--- |--- |
|安装|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>特征集合获取设置为它的新值之前，调用此方法获取的特征更改开头。</li><li>当特性集合的值已更改，但任何动画发生之前，获取调用的方法。</li></ul>|
|动画|`WillTransitionToTraitCollection`|获取传递给此方法转换处理协调器具有`AnimateAlongside`，开发人员可添加将执行以及默认动画的动画的属性。|
|清理|`WillTransitionToTraitCollection`|提供开发人员能够转换发生后包括其自己的清理代码的方法。|

`WillTransitionToTraitCollection`方法非常适用于对视图控制器随着特征集合变化进行动画处理。 `WillTransitionToTraitCollection`方法只是可以在视图控制器上 ( `UIViewController`) 而不是在其他特征环境，如`UIViews`。

`TraitCollectionDidChange`非常适用于使用`UIView`类，开发人员希望更新 UI，如更改特征。

### <a name="collapsing-the-split-view-controllers"></a>折叠拆分视图控制器

现在让我们详细了解在什么后会发生拆分视图控制器折叠为一个列视图从两个列。 作为此更改的一部分，有两个需要执行的进程：

-  默认情况下，拆分视图控制器将使用主视图控制器与视图发生折叠后。 开发人员可以通过重写重写此行为`GetPrimaryViewControllerForCollapsingSplitViewController`方法的`UISplitViewControllerDelegate`并提供他们想要在处于折叠状态显示任何视图控制器。
-  辅助的视图控制器必须获取合并到主视图控制器。 通常，开发人员将不需要执行此步骤; 任何操作拆分视图控制器包含自动处理此阶段根据硬件设备。 但是，可能有一些特殊的情况下，开发人员将需要此更改与之交互。 调用`CollapseSecondViewController`方法的`UISplitViewControllerDelegate`允许折叠发生，而不是详细信息视图时要显示的母版视图控制器。


### <a name="expanding-the-split-view-controller"></a>展开拆分视图控制器

现在让我们详细了解在什么发生拆分视图控制器扩展从折叠状态。 再次重申，有需要执行的两个阶段：

-  首先，定义新的主视图控制器。 默认情况下，拆分视图控制器将自动使用来自折叠视图的主视图控制器。 同样，开发人员可以替代此行为使用`GetPrimaryViewControllerForExpandingSplitViewController`方法的`UISplitViewControllerDelegate`。
-  选择主视图控制器后, 必须重新创建辅助的视图控制器。 同样，拆分视图控制器包含自动处理此阶段根据硬件设备。 开发人员可以通过调用重写此行为`SeparateSecondaryViewController`方法的`UISplitViewControllerDelegate`。


在拆分视图控制器中，主视图控制器所起的作用中同时展开和折叠的视图，通过实现`CollapseSecondViewController`并`SeparateSecondaryViewController`方法的`UISplitViewControllerDelegate`。 `UINavigationController` 实现这些方法来自动推送和弹出辅助视图控制器。

### <a name="showing-view-controllers"></a>显示视图控制器

Apple iOS 8 对所做的另一个更改是在开发人员显示视图控制器的方式。 在过去，如果应用程序必须在叶视图控制器 （如表格视图控制器），并且开发人员介绍了一个不同 （例如，在上一个单元格用户点击响应），该应用程序可能会到达返回到控制器层次结构导航视图控制器，并调用`PushViewController`方法针对以显示新视图。

这将显示导航控制器和它正在其中运行的环境之间非常紧密耦合。 在 iOS 8 中，Apple 已分离这通过提供两种新方法：

-  `ShowViewController` – 调整要显示根据其环境的新视图控制器。 例如，在`UINavigationController`只需将推送到堆栈上的新视图。 在拆分视图控制器中，新的视图控制器将左侧和右侧显示为新的主视图控制器。 如果存在任何容器视图控制器，不则新视图将显示为模式视图控制器。
-  `ShowDetailViewController` – 在以类似的方式工作`ShowViewController`，但在拆分视图控制器将替换为新视图控制器中传递的详细信息视图上实现。 在调用 （如可能在 iPhone 应用程序中所示），拆分视图控制器处于折叠状态，如果将重定向到`ShowViewController`方法和新视图将显示为主视图控制器。 同样，如果存在任何容器视图控制器，不则新视图将显示为模式视图控制器。


这些方法通过从叶视图控制器处开始并遍历的视图层次结构，直到用户找到正确的容器视图控制器来处理新的视图的显示。

开发人员可以实现`ShowViewController`并`ShowDetailViewController`若要获取其自己自定义视图控制器中相同的自动功能的`UINavigationController`和`UISplitViewController`提供。

### <a name="how-it-works"></a>其工作原理

在本部分中我们将看看如何这些方法实际实现在 iOS 8 中。 第一个让我们看看新`GetTargetForAction`方法：

 [![](unified-storyboards-images/gettargetforaction.png "新的 GetTargetForAction 方法")](unified-storyboards-images/gettargetforaction.png#lightbox)

此方法将指导的层次结构链，直到找到正确的容器视图控制器。 例如：

1.  如果`ShowViewController`方法调用时，可实现此方法的链中的第一个视图控制器是导航控制器，以便用作新视图的父级。
1.  如果`ShowDetailViewController`改为调用方法、 拆分视图控制器是第一的视图控制器，来实现它，以便它可作为父级。


`GetTargetForAction`方法的工作原理是定位实现给定的操作的视图控制器，然后询问该视图控制器，如果想要接收该操作。 由于此方法是公共的开发人员可以创建函数就像内置在其自己自定义方法`ShowViewController`和`ShowDetailViewController`方法。

## <a name="adaptive-presentation"></a>自适应的演示文稿

在 iOS 8 中，Apple 已弹出框演示文稿 ( `UIPopoverPresentationController`) 自适应也。 因此弹出框的演示文稿视图控制器将自动出现在正则大小类中，普通的弹出框视图，但会将其显示全屏水平 compact 大小类中 (如 iPhone 上)。

为了适应统一情节提要系统中的更改，新控制器创建对象来管理呈现的视图控制器 — `UIPresentationController`。 此控制器创建从视图控制器显示直到关闭的时间。 由于它是管理类，它可以被视为超级类通过视图控制器响应会影响哪些随后放的回视图控制器的演示文稿控制器控制视图控制器 （如方向） 的设备更改。

当开发人员显示视图控制器使用`PresentViewController`presentation 过程的管理方法，传递给`UIKit`。 UIKit 处理 （除其他事项外） 样式创建正确的控制器、 视图控制器使用唯一的例外是当已将样式设置为`UIModalPresentationCustom`。 在这里，应用程序可以提供它是自己 PresentationController，而不是使用`UIKit`控制器。

### <a name="custom-presentation-styles"></a>自定义演示风格

使用自定义呈现样式，开发人员可以选择使用自定义的演示文稿控制器。 可以使用此自定义控制器，若要修改的外观和行为，它是 allied 到视图。

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>使用大小类

自适应照片 Xamarin 项目，它包含在本文提供在 iOS 8 统一接口应用程序中使用大小类和自适应视图控制器的工作示例。

虽然应用程序创建其 UI 完全在代码中，而不是使用 IOS 设计器，并创建统一的情节提要，同样的技术应用。 稍后在本文中，我们将演示如何使用大小类具有统一的情节提要和 iOS 的 Xamarin 应用程序中的设计器。

现在让我们进一步了解如何在自适应照片项目正在实现的几项大小类功能在 iOS 8，创建自适应应用程序中。

### <a name="adapting-to-trait-environment-changes"></a>需要适应特征环境更改

当用户将设备从纵向向横向旋转时，在 iPhone 上运行自适应的照片应用程序，拆分视图控制器将显示母版和详细信息视图：

 [![](unified-storyboards-images/rotation.png "拆分视图控制器将显示这两个母版和详细信息视图如下所示")](unified-storyboards-images/rotation.png#lightbox)

这通过重写实现`UpdateConstraintsForTraitCollection`上的值基于方法的视图控制器和调整约束`VerticalSizeClass`。 例如：

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>添加过渡动画

如果自适应应用程序将从照片中的拆分视图控制器折叠状态为展开，动画添加到默认动画通过重写`WillTransitionToTraitCollection`的视图控制器的方法。 例如：

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>重写特征环境

如上面所示，自适应的照片应用程序时强制进行拆分视图控制器，以显示详细信息和母版视图 iPhone 设备处于横向视图。

这是通过在视图控制器中使用以下代码来实现：

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>展开和折叠拆分视图控制器

下一步让我们看一下如何在 Xamarin 中实现的扩展和折叠行为的拆分视图控制器。 在`AppDelegate`、 创建拆分视图控制器时，其委托分配来处理这些更改：

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

`SeparateSecondaryViewController`方法测试以查看照片正在显示并采取措施根据该状态。 没有照片是否显示其折叠辅助视图控制器，以便显示母版视图控制器。

`CollapseSecondViewController`方法用于展开拆分视图控制器时如果因此将其折叠回该视图，请参阅在堆栈上，是否存在的所有照片。

### <a name="moving-between-view-controllers"></a>视图控制器之间移动

接下来，让我们看看如何将自适应的照片应用程序移动视图控制器之间。 在中`AAPLConversationViewController`类在用户从表中，选择一个单元格时`ShowDetailViewController`方法调用以显示详细信息视图：

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>显示泄露指示器

自适应的照片应用程序中有几个地方隐藏或显示小时更改到特征环境泄露指示器的位置。 这被处理使用以下代码：

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

这些功能实现使用`GetTargetViewControllerForAction`地上面讨论的方法。

表格视图控制器显示数据时，它使用上面的实现以查看推送将要发生的操作，以及要显示或隐藏相应地泄露指示器的方法：

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>新`ShowDetailTargetDidChangeNotification`类型

Apple 已使用大小类和特征环境与在拆分视图控制器中添加了新的通知类型`ShowDetailTargetDidChangeNotification`。 当目标为拆分视图控制器的详细信息视图发生更改时，如控制器展开或折叠时发送此通知。

自适应的照片应用程序使用此通知详细信息视图控制器发生更改时更新的泄露指示器状态：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

深入研究一下在自适应的照片应用程序以各种方式查看该大小类，特征集合和自适应视图控制器可用于轻松地在 Xamarin.iOS 中创建一个统一的应用程序。

## <a name="unified-storyboards"></a>统一的情节提要

新 ios 8、 统一情节提要使得开发人员可以创建一个，统一情节提要文件，可以通过面向多个大小类显示在 iPhone 和 iPad 设备上。 通过使用统一情节提要，开发人员编写更少 UI 的特定代码，并具有只有一个接口的设计来创建和维护。

统一情节提要的主要优点是：

-  适用于 iPhone 和 iPad 中使用相同的情节提要文件。
-  将向后部署到 iOS 6 和 iOS 7。
-  预览不同的设备、 方向和 Xamarin iOS 设计器中的操作系统版本中的所有内容的布局。

此功能是完全支持在 Visual Studio for Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>启用大小类

默认情况下，任何新的 Xamarin.iOS 项目将使用大小类。 若要使用大小类和自适应转入内的较旧项目从情节提要，它必须首先转换为 iOS 设计器内的 Xcode 6 统一情节提要格式。

若要执行此打开情节提要转换在 iOS 设计器并检查**使用大小类**复选框：

 [![](unified-storyboards-images/sizeclass01.png "使用大小类复选框")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 设计器将确认开发人员想要转换的情节提要，若要使用大小类格式：

 [![](unified-storyboards-images/sizeclass02.png "使用大小类警报")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 自动布局必须也需要检查大小类才能正常工作。

### <a name="generic-device-types"></a>通用设备类型

一旦已转换情节提要，若要使用大小类，它将重新显示在设计图面上和**查看方式**设备将被泛型：

 [![](unified-storyboards-images/sizeclass03.png "查看作为通用设备类型")](unified-storyboards-images/sizeclass03.png#lightbox)

选中的通用设备类型后，所有的视图控制器将调整为 600 x 600 正方形。 此正方形表示任何宽度和任何高度的大小。 当 iOS 设计器在此模式下，任何编辑将应用于所有大小类。

开发人员还可以查看设计图面为 iPhone 的选项：

 [![](unified-storyboards-images/sizeclass04.png "为 iPhone 查看设计图面")](unified-storyboards-images/sizeclass04.png#lightbox)

或查看 iPad 作为：

 [![](unified-storyboards-images/sizeclass05.png "为 iPad 查看设计图面")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>选择一个大小类

大小类选择器按钮是左上角的设计图面 （附近的查看方式下拉列表）。 它允许开发人员可以选择当前正在编辑的大小类：

 [![](unified-storyboards-images/sizeclass06.png "选择一个大小类")](unified-storyboards-images/sizeclass06.png#lightbox)

选择器将提供为 3x3 网格大小类选项。 每个网格中的方块代表一个类，宽度和高度类的组合。 中心方形选择 Any Width/Any 高度大小类 （这是统一的情节提要的默认视图）。 选择此正方形时，开发人员编辑默认布局，继承的所有其他配置。

在网格的左上角正方形表示 Compact Compact 宽度/高度大小类：

 [![](unified-storyboards-images/sizeclass07.png "Compact 宽度/Compact 高度大小类")](unified-storyboards-images/sizeclass07.png#lightbox)

此模式下对应于以横向方式 iPhone。 在网格的右下角正方形表示正则常规宽度/高度大小类，该类表示 iPad:

 [![](unified-storyboards-images/sizeclass08.png "正则宽度/常规高度大小类")](unified-storyboards-images/sizeclass08.png#lightbox)

若要编辑 iPhone 纵向方向的布局，选择在左下角的方块。 这表示 Compact 常规宽度/高度大小类：

 [![](unified-storyboards-images/sizeclass09.png "Compact 宽度/常规高度大小类")](unified-storyboards-images/sizeclass09.png#lightbox)

单击以选中它在方块中并在设计图面将更改视图控制器，以匹配新选择的大小：

 [![](unified-storyboards-images/sizeclass10.png "在设计图面将更改视图控制器，以匹配新选择，如所示的大小")](unified-storyboards-images/sizeclass10.png#lightbox)

大小类以及它们如何影响布局适用于 Iphone 和 Ipad 上，请参阅此文章了解详细信息的大小类部分。

### <a name="adaptive-segue-types"></a>自适应 Segue 类型

如果开发人员已使用了情节提要之前，则它们将与现有的 segue 类型的熟悉**推送**，**模式**并**弹出框**。 统一情节提要文件上启用大小类后，可进行以下自适应 Segue 类型 （对应于新的视图控制器 API 上文所述）：**显示**并**显示详细信息**。

> [!IMPORTANT]
> 启用大小类后，任何现有 segue 将转换为新类型。

需要的 iOS 示例有一个简单的游戏导航菜单，在母版视图拆分视图控制器结合使用统一的情节提要的 8 应用程序。 如果用户单击菜单按钮，在选定的项的视图控制器应显示在拆分视图控制器的详细信息部分中，在 iPad 上运行时。 在 iPhone 上的项视图控制器应推送到导航堆栈上。

若要实现此效果，请在 iOS 设计器中单击的按钮并拖动线条到要显示的视图控制器。 当释放鼠标按钮时，选择`Show Detail`Segue 类型弹出菜单中：

 [![](unified-storyboards-images/segue01.png "Segue 类型弹出菜单中选择显示详细信息")](unified-storyboards-images/segue01.png#lightbox)

将按钮和视图控制器之间创建新的 segue。 现在在 iPhone 模拟器中运行应用程序，将显示主菜单：

 [![](unified-storyboards-images/segue02.png "主菜单")](unified-storyboards-images/segue02.png#lightbox)

单击**选择游戏**按钮和项的视图控制器将推送到导航堆栈上：

 [![](unified-storyboards-images/segue03.png "项视图控制器将推送到导航堆栈上，如所示")](unified-storyboards-images/segue03.png#lightbox)

停止模拟器在 iPhone 和 iPad 模拟器中运行应用程序。 切换到横向方向并在主菜单中再次显示：

 [![](unified-storyboards-images/segue04.png "显示的主菜单")](unified-storyboards-images/segue04.png#lightbox)

同样，单击**选择游戏**拆分视图控制器的详细信息部分中显示按钮和项的视图控制器：

 [![](unified-storyboards-images/segue05.png "项视图控制器在拆分视图控制器的详细信息部分中所示")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>从大小类中排除元素

有些的时候 （如视图、 控件或约束） 的给定的元素不需要特定的大小类内。 若要从大小类中排除某个元素，选择所需的项目中排除**设计图面**。 滚动到底部**属性资源管理器**然后单击**齿轮**下拉列表菜单。 选择的组合**宽度**并**高度**要排除的项：

[![](unified-storyboards-images/exclude-a.png "选择宽度和高度的组合")](unified-storyboards-images/exclude-a.png#lightbox)

一个新*排除用例*将添加到底部中的元素**属性资源管理器**。 接下来，取消选中**已安装**给定大小类对应的复选框：

[![](unified-storyboards-images/exclude-b.png "取消选中已安装复选框")](unified-storyboards-images/exclude-b.png#lightbox)

切换到的宽度和高度的项已从排除的设计图面上，已从给定的大小类，但不是整个的 UI 设计：

 [![](unified-storyboards-images/exclude02.png "切换到的宽度和高度的项已从排除的设计图面")](unified-storyboards-images/exclude02.png#lightbox)

切换回 Any Width/Any 高度大小类和元素仍处于位置：

 [![](unified-storyboards-images/exclude03.png "切换回 Any Width/Any 高度大小类")](unified-storyboards-images/exclude03.png#lightbox)

在 iPad 模拟器中运行应用程序时，该元素显示：

 [![](unified-storyboards-images/exclude04.png "时显示的元素在 iPad 模拟器中的运行应用")](unified-storyboards-images/exclude04.png#lightbox)

并在 iPhone 模拟器上运行应用程序时，缺少的元素：

 [![](unified-storyboards-images/exclude05.png "缺少时的元素在 iPhone 模拟器中的运行应用")](unified-storyboards-images/exclude05.png#lightbox)

若要从元素中删除排除情况下，只需选择中的元素**设计图面**，滚动到底部**属性资源管理器**然后单击 **-** 要删除的用例旁边的按钮。

若要查看统一情节提要的实现，请查看`UnifiedStoryboard`示例 Xamarin iOS 8 应用程序附加到此文档。

## <a name="dynamic-launch-screens"></a>动态启动屏幕

当 iOS 应用程序启动的用户的应用程序是实际开始向上向提供反馈，启动屏幕文件将显示为初始屏幕。 在 iOS 8 之前，开发人员将不得不包括多个`Default.png`图像应用程序将运行在每个设备类型、 方向和屏幕分辨率的资产。 例如， `Default@2x.png`， `Default-Landscape@2x~ipad.png`， `Default-Portrait@2x~ipad.png`，等等。

纳入新 iPhone 6 和 iPhone 6 Plus 设备 （和即将推出的 Apple Watch） 与所有现有的 iPhone 和 iPad 设备，这表示大量不同的大小、 方向和分辨率为`Default.png`必须启动屏幕图像资产创建和维护。 此外，这些文件可能会很大，并将"膨胀"可交付结果的应用程序捆绑包，从而增加的 iTunes App Store （可能使其能够通过移动电话网络传送） 中下载该应用程序所需的时间和增加最终用户的设备上所需的存储量。

新增到 iOS 8、 开发人员可以创建单个原子`.xib`文件中使用自动布局和大小类创建的 Xcode*动态启动屏幕*，将适用于每个设备、 分辨率和方向。 这不仅会减少开发人员可以创建和维护所有所需的图像资产的所需的工作量，但它极大地减少了应用程序的已安装捆绑包的大小。


动态启动屏幕具有以下限制和注意事项：

- 仅使用`UIKit`类。
- 使用的单个根视图`UIView`或`UIViewController`对象。
- 不建立任何连接到应用程序的代码 (请勿添加**操作**或**输出口**)。
- 不要将添加`UIWebView`对象。
- 不要使用任何自定义类。
- 不要使用运行时属性。

记住上述指导原则，让我们看一下将动态启动屏幕添加到现有的 Xamarin iOS 8 项目。

请执行以下操作：

1. 打开**Visual Studio for Mac**并加载**解决方案**添加到动态启动屏幕。
2. 在中**解决方案资源管理器**，右键单击`MainStoryboard.storyboard`文件，然后选择**打开** > **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "使用 Xcode 接口生成器打开")](unified-storyboards-images/dls01.png#lightbox)
3. 在 Xcode 中，选择**文件** > **新建** > **文件...** :

    [![](unified-storyboards-images/dls02.png "选择文件 / 新建")](unified-storyboards-images/dls02.png#lightbox)
4. 选择**iOS** > **用户界面** > **启动屏幕**然后单击**下一步**按钮：

    [![](unified-storyboards-images/dls03.png "选择 iOS / 用户界面 / 启动屏幕")](unified-storyboards-images/dls03.png#lightbox)
5. 将文件命名`LaunchScreen.xib`然后单击**创建**按钮：

    [![](unified-storyboards-images/dls04.png "将文件 LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. 通过添加图形元素，并使用布局约束来定位其产品的给定的设备、 方向和屏幕尺寸编辑启动屏幕的设计：

    [![](unified-storyboards-images/dls05.png "编辑启动屏幕的设计")](unified-storyboards-images/dls05.png#lightbox)
7. 保存对更改`LaunchScreen.xib`。
8. 选择**应用程序目标**并**常规**选项卡：

    [![](unified-storyboards-images/dls06.png "选择应用程序目标和常规选项卡")](unified-storyboards-images/dls06.png#lightbox)
9. 单击**选择 Info.plist**按钮，选择`Info.plist`的 Xamarin 应用程序并单击**选择**按钮：

    [![](unified-storyboards-images/dls07.png "选择 Xamarin 应用的 Info.plist")](unified-storyboards-images/dls07.png#lightbox)
10. 在中**应用图标和启动图像**部分中，打开**启动屏幕文件**下拉列表中选择`LaunchScreen.xib`上面创建：

    [![](unified-storyboards-images/dls08.png "选择 LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. 将所做的更改保存到文件并返回到 Visual Studio for mac。
12. 等待 Visual Studio for Mac 完成同步与 Xcode 的更改。
13. 在中**解决方案资源管理器**，右键单击**资源**文件夹，然后选择**添加** > **添加文件...** :

    [![](unified-storyboards-images/dls09.png "选择添加 / 添加文件...")](unified-storyboards-images/dls09.png#lightbox)
14. 选择`LaunchScreen.xib`上面创建的文件，并单击**打开**按钮：

    [![](unified-storyboards-images/dls10.png "选择 LaunchScreen.xib 文件")](unified-storyboards-images/dls10.png#lightbox)
15. 生成应用程序。

### <a name="testing-the-dynamic-launch-screen"></a>测试动态启动屏幕

在 Visual Studio for Mac 中，选择 iPhone 4 Retina 模拟器并运行应用程序。 动态启动屏幕将显示在正确的格式和方向：

[![](unified-storyboards-images/dls11.png "在垂直方向显示的动态启动屏幕")](unified-storyboards-images/dls11.png#lightbox)

停止 Mac 在 Visual Studio 中的应用程序并选择一个 iPad iOS 8 设备。 运行应用程序和启动屏幕将针对此设备和方向正确地格式化：

[![](unified-storyboards-images/dls12.png "在水平方向显示的动态启动屏幕")](unified-storyboards-images/dls12.png#lightbox)

返回到 Visual Studio for Mac，并停止运行该应用程序。

### <a name="working-with-ios-7"></a>使用 iOS 7

若要保持与 iOS 7 的向后兼容性，包括常用`Default.png`图像作为正常 iOS 8 应用程序中的资产。 iOS 将返回到以前的行为和 iOS 7 设备上运行时将这些文件用作启动屏幕。

若要查看在 Xamarin 中动态启动屏幕的实现，请查看[动态启动屏幕](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)示例 iOS 8 应用程序附加到此文档。

## <a name="summary"></a>总结

本文探讨快速大小类以及它们如何影响在 iPhone 和 iPad 设备中的布局。 它介绍了使用大小类来创建统一的接口特征、 特征环境和特性集合的起作用。 需要花费简要介绍一下自适应视图控制器，而且它们如何使用大小类内统一接口。 它介绍了实现大小类和统一接口完全从C#Xamarin iOS 8 应用程序内的代码。

最后，本文介绍使用 Xamarin iOS 设计器，即可适用于 iOS 设备创建统一情节提要的基础知识，并创建一个单一、 动态启动屏幕将显示为每个 iOS 8 设备上启动屏幕。

## <a name="related-links"></a>相关链接

- [自适应照片 （示例）](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [StoryboardIntro 示例](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [动态启动屏幕 （示例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
- [IOS8-发展 2014 （视频） 中的动态布局](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
