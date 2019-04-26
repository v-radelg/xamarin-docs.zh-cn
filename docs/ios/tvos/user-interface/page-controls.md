---
title: 使用 tvOS 在 Xamarin 中的页面控件
description: 本文档介绍如何使用 Xamarin 生成的应用程序中的 tvOS 页面控件使用。 它提供页面控件的高级别说明，介绍了如何设置在情节提要，并探讨如何响应页更改事件。
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 173bc7713b5b8c330d4d4c5863bef24be8bdcb52
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61179549"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>使用 tvOS 在 Xamarin 中的页面控件

有时您可能需要 Xamarin.tvOS 应用程序中显示一系列的网页或图像。 页面控件的设计可清晰显示服务的用户是在超出最大页数的页。 页面控件将显示一系列针对深，oval 形状背景的点。 当前页将显示实心圆点，所有其他页显示为空心点。 如果有太多，无法容纳在其背景区域中，页面控件将剪辑的外部的大多数点。

[![](page-controls-images/page01.png "示例页面控件")](page-controls-images/page01.png#lightbox)

旨在为用户提供反馈的非交互式元素中的页控件。 你将需要添加其他控件来更改 （如手势或按钮） 的当前页码。

使用页面控件时，Apple 将具有以下建议：

- **仅完整集合上的使用**-页面控件适合在全屏幕环境以显示单个集合中存在的多个页中。
- **限制页面数**-页面控件适合进行十 （10） 或更少的页和最多为二十 （20） 页。 对于超过 20 种页面，请考虑使用[集合视图](~/ios/tvos/user-interface/collection-views.md)和在网格中显示页。

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>页面控件和情节提要

Xamarin.tvOS 应用中使用的页控件的最简单方法是将它们添加到应用程序的 UI 使用 iOS 设计器。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    
1. 在中**Solution Pad**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**页面控件**从**工具箱**并将其放在视图上： 

    [![](page-controls-images/page02.png "页面控件")](page-controls-images/page02.png#lightbox)
1. 在中**小组件选项卡**的**Properties Pad**，您可以调整页面控件的多个属性，如其**当前页**和 **# 页**: 

    [![](page-controls-images/page03.png "小组件选项卡")](page-controls-images/page03.png#lightbox)
1. 接下来，将控件或笔势添加到要向后移动和向前移动页的集合的视图。
1. 最后，将分配**名称**的控件，以便可以响应在C#代码。 例如： 

    [![](page-controls-images/page04.png "将控件")](page-controls-images/page04.png#lightbox)
1. 保存更改。
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开进行编辑。
1. 拖动**页面控件**从**工具箱**并将其放在视图上： 

    [![](page-controls-images/page02-vs.png "页面控件")](page-controls-images/page02-vs.png#lightbox)
1. 在中**小组件选项卡**的**属性资源管理器**，您可以调整页面控件的多个属性，如其**当前页**和 **# 页**: 

    [![](page-controls-images/page03-vs.png "小组件选项卡")](page-controls-images/page03-vs.png#lightbox)
1. 接下来，将控件或笔势添加到要向后移动和向前移动页的集合的视图。
1. 最后，将分配**名称**的控件，以便可以响应在C#代码。 例如： 

    [![](page-controls-images/page04-vs.png "将控件")](page-controls-images/page04-vs.png#lightbox)
1. 保存更改。
    

-----

> [!IMPORTANT]
> 虽然可以将事件分配如`TouchUpInside`到 UI 元素 （例如用户界面按钮） iOS 设计器中，它将永远不会调用，因为 Apple TV 没有触摸屏输入屏幕上或支持触控事件。 应始终使用`Primary Action`事件时创建用户界面元素的适用于 tvOS 的事件处理程序。

编辑视图控制器 (示例`ViewController.cs`) 文件，并添加代码来处理正在更改的页。 例如：

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

让我们进一步看一下页面控件的两个属性。 首先，若要指定最大页面数，使用以下方法：

```csharp
PageView.Pages = 6;
```

若要更改当前页码，请使用以下代码：

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage`属性为零 (0) 工作，因此第一页将为零和最后一个也是最大页面数减一。

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和使用在 Xamarin.tvOS 应用内的页控件。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
