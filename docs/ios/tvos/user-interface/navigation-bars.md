---
title: 使用 tvOS 在 Xamarin 中的导航栏
description: 本文档介绍如何使用在 tvOS 应用程序中使用 Xamarin 生成的导航栏。 它讨论了设置情节提要中的导航栏和从这些按钮对事件作出响应。
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 81e6cfe1e532bcfa7616e35adb28b314587bafc8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161481"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>使用 tvOS 在 Xamarin 中的导航栏

导航栏可以添加到视图以显示标题和可选的导航栏按钮的顶部。 它们通常用于当用户导航从主页上，如表视图、 集合或显示选定项的详细信息子视图的菜单。

[![](navigation-bars-images/navbar01.png "示例导航栏")](navigation-bars-images/navbar01.png#lightbox)

除了标题 （即显示在中心），在导航栏可以包含一个或多个导航栏按钮 (`UIBarButtonItem`) 上的左侧和右侧的栏。

> [!IMPORTANT]
> 导航栏是默认情况下完全透明的。 应格外小心，确保导航栏的内容对其下面的内容始终可读。 例如，当在表视图或集合中的内容滚动下它时。

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>导航栏和情节提要

若要使用 Xamarin.tvOS 应用中的导航栏的最简单方法是将它们添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**导航栏**从**工具箱**并将其放在屏幕顶部的视图： 

    [![](navigation-bars-images/navbar02.png "导航栏")](navigation-bars-images/navbar02.png#lightbox)
1. 双击**导航栏**以选择此选项将**导航项**。 在中**小组件**选项卡**Properties Pad**，可以设置**标题**: 

    [![](navigation-bars-images/navbar03.png "设置标题")](navigation-bars-images/navbar03.png#lightbox)
1. 接下来，您可以添加一个或多个**栏按钮项**栏的任一端到： 

    [![](navigation-bars-images/navbar04.png "一个条形图按钮项")](navigation-bars-images/navbar04.png#lightbox)
1. 最后，布置**栏按钮项**于中的操作**事件**选项卡**属性资源管理器**: 

    [![](navigation-bars-images/navbar05.png "条形按钮项操作")](navigation-bars-images/navbar05.png#lightbox)
1. 保存更改。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**导航栏**从**工具箱**并将其放在屏幕顶部的视图： 

    [![](navigation-bars-images/navbar02-vs.png "导航栏")](navigation-bars-images/navbar02-vs.png#lightbox)
1. 双击**导航栏**以选择此选项将**导航项**。 在中**小组件**选项卡**属性资源管理器**，可以设置**标题**: 

    [![](navigation-bars-images/navbar03-vs.png "设置标题")](navigation-bars-images/navbar03-vs.png#lightbox)
1. 接下来，您可以添加一个或多个**栏按钮项**栏的任一端到： 

    [![](navigation-bars-images/navbar04-vs.png "条形按钮项")](navigation-bars-images/navbar04-vs.png#lightbox)
1. 最后，布置**栏按钮项**于中的操作**事件**选项卡**属性资源管理器**: 

    [![](navigation-bars-images/navbar05-vs.png "条形按钮项操作")](navigation-bars-images/navbar05-vs.png#lightbox)
1. 保存更改。


-----

> [!IMPORTANT]
> 虽然可以将事件分配如`TouchUpInside`到 UI 元素 （例如用户界面按钮） iOS 设计器中，它将永远不会调用，因为 Apple TV 没有触摸屏输入屏幕上或支持触控事件。 应始终使用`Primary Action`事件时创建用户界面元素的适用于 tvOS 的事件处理程序。

下面的代码在三个不同 BarButtonItems 上提供的事件处理程序示例： `ShowFirstHotel`， `ShowSecondHotel`，和`ShowThirdHotel`。 单击每个项时，背景图像`HotelImage`发生更改。 视图控制器中进行编辑 (示例`ViewController.cs`) 文件：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

只要一个按钮`Enabled`属性是`true`和不受另一个控件或视图，它可以对使用 Siri 远程中焦点项。

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和使用在 Xamarin.tvOS 应用内的导航栏。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
