---
title: 你好，watchOS –演练
description: 本文档提供使用 Xamarin 构建简单的 watchOS 应用程序的演练。 它介绍了如何在 Visual Studio 和 Visual Studio for Mac 中工作，如何使用情节提要并在代码中响应事件。
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 12/14/2016
ms.openlocfilehash: c5527db543a0b0d5218c37f0d75e22afcd59297a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293148"
---
# <a name="hello-watchos--walkthrough"></a>你好，watchOS –演练

按照[设置和安装](~/ios/watchos/get-started/installation.md)中的步骤创建解决方案后，将有3个项目：

- 用于安装程序或其他设备上管理任务的 iOS 父应用程序。 （对于其他类型的 iOS 扩展，这通常称为 "容器" 应用。）借助 Watch 应用，用户可以**开始运行 Watch 应用，而无**需运行父应用;
- 监视扩展，其中包含 watch 应用程序的程序代码;与
- Watch 应用，用于保存在手表上呈现的情节提要和图像资源。

检查[引用是否正确](~/ios/watchos/get-started/project-references.md)：父应用程序具有对扩展的引用，并且该扩展包含对该监视应用的引用。

确认你的\*捆绑标识符是否遵循. watchkitextension \*. watchkitapp 约定，以及你的扩展的 info.plist 文件是否将其**WKApp 捆绑 ID**值设置为你的监视应用的捆绑标识符。

你现在应该能够运行你的监视应用程序，但由于你的 Watch 应用中的情节提要文件为空白，因此你无法判断。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "解决方案资源管理器")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "解决方案资源管理器")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

双击 Watch 应用中的 "情节提要"，以启动 Xamarin iOS 设计器（如果在 Mac 上，还可以右键单击并**打开 > Xcode Interface Builder**）


1. 确保 "**工具箱**" 和 "**属性**" 板可见，
1. 单击以选择接口控制器，
1. 将接口控制器的标识符和标题设置为**interfaceController**和**Hi 手表**，
1. 验证**类**是否设置为**InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "将接口控制器的标识符和标题设置为 interfaceController 和高手表")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

双击 Watch 应用中的 "情节提要"，在 Visual Studio 中通过 Xamarin iOS 设计器进行编辑：

1. 打开 "属性" 窗格;
1. 将类更改为**InterfaceController**;
1. 单击接口控制器;与
1. 将接口控制器的标识符和标题设置为**interfaceController**和**Hi**。

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "将接口控制器的标识符和标题设置为 interfaceController 和高手表")

-----


创建用户界面：

1. 从**工具箱**面板中，
1. 将**按钮**和**标签**拖放到场景上，并
1. 设置控件的文本和属性，如下所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "设置控件的文本和属性，如下所示")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "设置控件的文本和属性，如下所示")

-----

1. 在 "**属性**" 面板中设置每个控件的**名称**。 在此示例中，我们`myButton`使用`myLabel`了和。

1. 选择情节提要上的按钮，并依次单击 "**属性**" 面板的 "**事件**" 列表、

1. 通过键入`OnButtonPress`并按**enter**创建新**操作**。
  该操作将显示在列表中，并会在中C#自动创建分部方法。

![](hello-watch-images/buttonaction.png "添加到按钮的 OnButtonPress 操作")

保存情节提要后，将用控件名称和操作更新**InterfaceController.designer.cs** 。 如果在更新文件后打开此文件，则可以看到如何`RegisterAttribute`对应于控制器，以及 UI 控件如何对应于标记有的C# `OutletAttribute`实例变量，以及如何将操作映射到用`ActionAttribute`来标记的分部方法:

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

现在，打开**InterfaceController.cs** （*而不*是 InterfaceController.designer.cs）并添加以下代码：

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

此代码应相当透明：每次调用函数`clickCount` `OnButtonPress`时，实例变量都会递增。 的文本`myLabel`将更改以反映此计数;`myLabel`当然，是在 XCode 中创建的插座之一的名称。 `partial`函数是与指定的操作名称关联的函数的实现。

如果它还不是启动项目，

1. 右键单击 "监视扩展" 项目，并选择 "**设为启动项目**"，

1. 将部署目标设置为监视工具包兼容的模拟器映像（如 iPhone 6 iOS 8.2），

1. 按 "**调试**" 按钮触发生成和模拟器启动。

    [![](hello-watch-images/readytodebug-sml.png "Visual Studio 接口元素")](hello-watch-images/readytodebug.png#lightbox)

当模拟器启动时，按按钮以递增标签。
恭喜，你已获得了一个手表应用！

![](hello-watch-images/running.png "在模拟器中运行的应用")


## <a name="related-links"></a>相关链接

- [入门（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gettingstarted)
- [设置和安装](~/ios/watchos/get-started/installation.md)
- [第一个 Watch 应用视频](https://blog.xamarin.com/your-first-watch-kit-app/)
