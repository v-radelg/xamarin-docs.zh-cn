---
title: 在 Xamarin.iOS 中的交互式通知用户界面
description: 与 iOS 12，则可以创建本地和远程通知的交互式用户界面。 本指南介绍如何通过 Xamarin.iOS 中使用这些功能。
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: e6dc2f14b36c9d6f67f1df5ad3d118fa423e0d4d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669385"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>在 Xamarin.iOS 中的交互式通知用户界面

[通知内容扩展](~/ios/platform/user-notifications/advanced-user-notifications.md)，在 iOS 10 中引入，使其可以创建通知的自定义用户界面。 从 iOS 12 开始，通知用户接口可以包含按钮和滑块等交互元素。

## <a name="sample-app-redgreennotifications"></a>应用程序示例：RedGreenNotifications

[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)示例应用包含通知内容扩展，使用交互式用户界面。

本指南中的代码段来自于本示例。

## <a name="notification-content-extension-infoplist-file"></a>通知内容扩展 Info.plist 文件

在示例应用**Info.plist**中的文件**RedGreenNotificationsContentExtension**项目包含以下配置：

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

请注意以下功能：

- `UNNotificationExtensionCategory`数组指定通知类别的类型的内容扩展句柄。
- 通知内容扩展为支持交互式内容，设置`UNNotificationExtensionUserInteractionEnabled`关键`true`。
- `UNNotificationExtensionInitialContentSizeRatio`键指定内容扩展的接口的初始高度/宽度比率。

## <a name="interactive-interface"></a>交互式接口

**MainInterface.storyboard**，用于定义通知内容扩展，接口是标准的情节提要包含单个视图控制器。 在示例应用中，视图控制器是类型的`NotificationViewController`，其中包含的图像视图、 三个按钮和滑块。 情节提要将这些控件与中定义的处理程序相关联**NotificationViewController.cs**:

- **启动应用程序**按钮处理程序调用`PerformNotificationDefaultAction`上的操作方法`ExtensionContext`，它将启动该应用程序：

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    在应用中，用户通知中心的`Delegate`(在示例应用`AppDelegate`) 可以响应在交互`DidReceiveNotificationResponse`方法：

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- **消除通知**按钮处理程序调用`DismissNotificationContentExtension`上`ExtensionContext`，用于关闭通知：

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- **删除通知**按钮处理程序将关闭通知，并将其删除从通知中心：

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- 处理在滑块上的值发生更改的方法更新通知的界面中显示的图像的 alpha:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>相关链接

- [示例应用程序 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中使用的用户通知框架](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [什么是用户通知 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳实践和什么是用户通知 (WWDC 2017) 中的新增功能](https://developer.apple.com/videos/play/wwdc2017/708/)
- [丰富的通知 (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [生成远程通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
