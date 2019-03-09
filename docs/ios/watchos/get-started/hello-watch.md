---
title: 你好，watchOS – 演练
description: 本文档提供了构建使用 Xamarin 的简单 watchOS 应用程序的演练。 它介绍如何适用于 Visual Studio 和 Visual Studio for Mac、 使用情节提要，以及对在代码中的事件做出响应。
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: de04a2d7f42ec36c464c75ced73bf8f8029ec1da
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672607"
---
# <a name="hello-watchos--walkthrough"></a>你好，watchOS – 演练

创建一个解决方案中的步骤后[设置和安装](~/ios/watchos/get-started/installation.md)，将有 3 个项目：

- IOS 父应用程序，用于进行的安装程序或其他设备上的管理任务。 （与其他类型的 iOS 扩展，这通常称为"容器"应用程序。）与监视应用程序，它将是一种用户可以开始运行 Watch 应用而无需**曾经**运行父应用程序;
- 监视扩展，其中包含监视应用程序的程序代码和
- 监视应用程序，保存在手表呈现的情节提要和图像资源。

检查你[引用是正确](~/ios/watchos/get-started/project-references.md)： 父应用程序具有对该扩展的引用和扩展具有对监视应用的引用。

确认捆绑包标识符遵循\*.watchkitextension \*.watchkitapp 约定并扩展的 Info.plist 文件将该属性具有**WKApp 捆绑 ID**值设置为的捆绑标识符Watch 应用。

您应能够监视应用程序现在运行，但在监视应用程序中的情节提要文件为空，因为你将无法告知。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "在解决方案资源管理器")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "在解决方案资源管理器")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
Interface.storyboard Watch 应用启动 Xamarin iOS 设计器中双击 (如果要在 Mac 上还可以右键单击并**打开方式 > Xcode Interface Builder**)


1.  请确保**工具箱**并**属性**面板是可见的
1.  单击此项可选择界面控制器
1.  设置的标识符和界面控制器的标题**interfaceController**并**Hi 监视**，
1.  验证是否**类**设置为**InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "将标识符和界面控制器的标题设置为 interfaceController 和 Hi 监视")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

双击 Interface.storyboard 中监视应用程序以使用 Xamarin iOS 设计器在 Visual Studio 中编辑：

1.  打开属性窗格中;
1.  更改到类**InterfaceController**;
1.  单击界面控制器中;和
1.  设置的标识符和界面控制器的标题**interfaceController**并**Hi 监视**。

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "将标识符和界面控制器的标题设置为 interfaceController 和 Hi 监视")

-----


创建你的 UI:

1. 从**工具箱**pad，
1. 拖放到**按钮**和一个**标签**拖到场景中，并
1. 设置文本和控件所示的属性：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "设置文本和控件所示的属性")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "设置文本和控件所示的属性")

-----

1. 设置**名称**中**属性**的面板来显示每个控件。 在此示例中我们使用了`myButton`和`myLabel`。

1. 选择情节提要上的按钮，转到**属性**填充的**事件**列表，然后

1. 创建一个新**操作**通过键入`OnButtonPress`，然后按**Enter**。
  该操作将显示在列表中，并自动将在其中创建分部方法C#。

![](hello-watch-images/buttonaction.png "OnButtonPress 操作添加到按钮")

保存情节提要后**便会在 InterfaceController.designer.cs**获取更新控件的名称和操作... 如果您打开此文件具有更新后，可以看到如何`RegisterAttribute`对应于控制器和 UI 控件的对应于C#实例变量标记有`OutletAttribute`以及如何将操作映射到分部方法标有`ActionAttribute`:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

现在，打开**InterfaceController.cs** (*不*便会在 InterfaceController.designer.cs) 并添加以下代码：

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

此代码应该是相当透明： 实例变量`clickCount`是每次该函数时递增`OnButtonPress`调用。 文本`myLabel`更改以反映此计数中;`myLabel`，当然，是一个带在 XCode 中创建的容器的名称。 `partial`函数是与你指定的操作名称关联的函数的实现。

如果它尚未启动项目，

1. 右键单击你的监视扩展项目并选择**设为启动项目**，

1. 将部署目标设置为监视工具包兼容模拟器的图像 （如 iPhone 6 iOS 8.2)，

1. 按**调试**按钮以触发生成和模拟器启动。

    [![](hello-watch-images/readytodebug-sml.png "Visual Studio 界面元素")](hello-watch-images/readytodebug.png#lightbox)

当模拟器启动时，按下按钮递增标签。
恭喜，您就可以得到 Watch 应用 ！

![](hello-watch-images/running.png "在模拟器中运行的应用")


## <a name="related-links"></a>相关链接

- [入门 （示例）](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [设置和安装](~/ios/watchos/get-started/installation.md)
- [第一个 Watch 应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
