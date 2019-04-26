---
title: 适用于 Xamarin.iOS 应用程序生命周期演示
description: 本文档将检查所处理的应用程序委托的 iOS 应用程序，演示了何时以及如何处理这些事件的各种生命周期事件。
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/17/2018
ms.openlocfilehash: 3beb511c03b328ecea824bf89355d056df003f3e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946144"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>适用于 Xamarin.iOS 应用程序生命周期演示

这篇文章并[示例代码](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)演示了在 iOS 中，四种应用程序状态和角色的`AppDelegate`中通知的状态获取更改时应用程序的方法。 每当应用程序改变状态时，应用程序将打印到控制台的更新：

[![](application-lifecycle-demo-images/image3-sml.png "示例应用")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "每当应用程序改变状态时，应用程序将打印到控制台的更新")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>演练

1. 打开**生命周期**项目中**LifecycleDemo**解决方案。
1. 打开`AppDelegate`类。 日志记录已添加到生命周期方法，以指示当应用程序已更改状态：

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. 启动应用程序在模拟器中或在设备上。 `OnActivated` 将在应用启动时调用。 应用程序现在处于_Active_状态。
1. 点击主页按钮上的模拟器或设备将在后台应用程序。 `OnResignActivation` 并`DidEnterBackground`将从应用程序转换为调用`Active`到`Inactive`到`Backgrounded`状态。 由于没有任何应用程序代码设置为在后台执行，该应用程序被视为_挂起_在内存中。
1. 导航回到应用程序以使其重新置于前台。 `WillEnterForeground` 和`OnActivated`将同时调用：

    ![](application-lifecycle-demo-images/image4.png "打印到控制台的状态更改")

    应用程序已进入前台从背景，并在屏幕上显示的文本更改时，执行以下视图控制器中的代码行：

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. 按**主页**按钮放置在后台将应用程序。 然后，双击**主页**按钮以显示应用程序切换器。 在 iPhone X 上往下轻扫向上从屏幕的底部：

    [![应用程序切换器](application-lifecycle-demo-images/app-switcher-sml.png "应用程序切换器")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. 找到应用程序中应用切换器，并向上轻扫以将其删除 （在 iOS 11，长按直到角出现的红色图标）：

    [![最多删除正在运行的应用的轻扫](application-lifecycle-demo-images/app-switcher-swipe-sml.png "轻扫最多删除正在运行的应用")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS 将终止该应用程序。 请注意，`WillTerminate`不会调用它，因为应用程序是否已_挂起_背景中。

## <a name="related-links"></a>相关链接

- [LifecycleDemo （示例）](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
