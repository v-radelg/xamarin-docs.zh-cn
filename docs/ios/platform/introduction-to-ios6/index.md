---
title: iOS 6 简介
description: 本文档所链接到指南描述在 iOS 6 中引入的功能。 集合视图、 PassKit 社交框架，并将所有讨论对 StoreKit 的更改。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 25926d82e060b91b007da9c2295b328cb049e8df
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61302236"
---
# <a name="introduction-to-ios-6"></a>iOS 6 简介

_iOS 6 包含新技术，用于开发应用程序，它为 Xamarin.iOS 6 带来了多种C#开发人员。_

[ ![](images/ios6-large.jpg "IOS 6 徽标")](images/ios6-large.jpg#lightbox)

IOS 6 和 Xamarin.iOS 6，开发人员就具有丰富的功能来创建该目标 iPhone 5 的 iOS 应用程序，包括其可供使用。
本文档列出了一些更令人兴奋的可用的新功能和每个主题的文章的链接。 除了涉及到几个更改的将是非常重要，因为开发人员将移到 iOS 6 和 iPhone 5 的新的分辨率。


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[集合视图简介](~/ios/user-interface/controls/uicollectionview.md)

集合视图允许使用任意布局显示的内容。 这些功能可轻松地创建同时支持自定义布局以及默认情况下，类似于网格的布局。 有关详细信息请参阅，则[集合视图简介](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)指南。


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[PassKit 简介](~/ios/platform/passkit.md)

PassKit 框架允许应用程序与托管 Passbook 应用中的数字传递进行交互。 有关详细信息请参阅，则[传递工具包指南简介](~/ios/platform/passkit.md)。


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[EventKit 简介](~/ios/platform/eventkit.md)

EventKit 框架提供了一种日历、 日历事件和提醒日历数据库存储的数据访问方法。 访问对日历和日历事件以来提供 iOS 4，但 iOS 6 现在公开对提醒数据的访问。 有关详细信息，请参阅[我](~/ios/platform/eventkit.md) [ntroduction 到 EventKit](~/ios/platform/eventkit.md)指南。


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[社交框架简介](~/ios/platform/social-framework.md)

社交框架将为与社交网络在中国用户包括 Twitter 和 Facebook，以及 SinaWeibo 交互提供一个统一的 API。 有关详细信息请参阅，则[社交 Framework 简介](~/ios/platform/social-framework.md)指南。


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[对 StoreKit 的更改](changes-to-storekit.md)

Apple 引入了存储工具包中的两个新功能： 购买和下载 iTunes 或在应用中，从应用商店内容和托管应用内购买内容文件 ！。 有关详细信息请参阅，则[更改为应用商店套件](changes-to-storekit.md)指南。


## <a name="other-changes"></a>其他更改


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload 和 ViewDidUnload 不推荐使用

`ViewWillUnload`并`ViewDidUnload`方法的`UIViewController`不再在 iOS 6 中调用。 在以前版本的 iOS 中，使用这些方法具有已由应用程序分别保存视图卸载之前, 的状态和清理代码。

例如，Visual Studio for Mac 将创建一个名为方法`ReleaseDesignerOutlets`，如下，然后将从调用的所示`ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

但是，在 iOS 6 中，就不再需要调用`ReleaseDesignerOutlets`。   
   
   
   
清理代码，对于 iOS 6 应用程序应使用`DidReceiveMemoryWarning`。 但是，代码将该调用`Dispose`应谨慎使用和只适用于内存密集型对象如下所示如下：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同样，调用`Dispose`如上所示应很少需要。 在常规的大多数应用程序应执行的操作是删除事件处理程序。

对于保存状态的情况下，应用程序可以执行考虑到这`ViewWillDisappear`并`ViewDidDisappear`而不是`ViewWillUnload`。


### <a name="iphone-5-resolution"></a>iPhone 5 解析

iPhone 5 设备具有 640 x 1136 分辨率。 针对以前版本的 iOS 的应用程序将显示 letterboxed 上是 iPhone 5，运行时，如下所示：

 [![](images/01-letterboxed.png "针对以前版本的 iOS 的应用程序会出现 letterboxed 是 iPhone 5 上运行时")](images/01-letterboxed.png#lightbox)

为了使应用程序出现全屏幕上 iPhone 5，只需添加名为的映像`Default-568h@2x.png`具有 640 x 1136 的分辨率。 下面的屏幕截图显示了运行后此映像已包含的应用程序：

 [![](images/02-fullscreen.png "此屏幕截图显示了运行后此映像已包含的应用程序")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子类化 UINavigationBar

在 iOS 6`UINavigationBar`可以子类化。 这样，其他控件的外观和感觉的`UINavigationBar`。 例如，应用程序可以子类化添加子视图，这些视图进行动画处理和修改的边界`UINavigationBar`。

下面的代码演示的子类化示例`UINavigationBar`，它将`UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

若要添加子类`UINavigationBar`到`UINavigationController`，使用`UINavigationController`采用的类型的构造函数`UINavigationBar`和`UIToolbar`，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用此`UINavigationBar`子类导致图像视图显示如以下屏幕截图中所示：

 [![](images/03-navbar.png "在此屏幕截图中所示显示图像视图中使用此 UINavigationBar 子类结果")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>界面方向

在 iOS 之前，6 应用程序可以重写`ShouldAutorotateToInterfaceOrientation`，返回特定控制器支持对于任何方向，返回 true。 例如，下面的代码将用于支持仅纵向：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6`ShouldAutorotateToInterfaceOrientation`已弃用。
而是应用程序可以重写`GetSupportedInterfaceOrientations`在根视图控制器，如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在 iPad 上此值默认为全部四个方向如果`GetSupportedInterfaceOrientation`未实现。 在 iPhone 和 iPod Touch 上，默认值是除之外的所有方向`PortraitUpsideDown`。
