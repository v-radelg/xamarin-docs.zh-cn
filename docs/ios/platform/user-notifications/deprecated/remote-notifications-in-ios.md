---
title: 在 iOS 中的推送通知
description: 本文档介绍如何使用 iOS 9 及更早版本中的推送通知。 它讨论了证书，使用 Apple 推送通知网关服务 (APNS)，和的详细信息来注册。
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 8ad742607e506df436a5526d31621ac7636ac29b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086954"
---
# <a name="push-notifications-in-ios"></a>在 iOS 中的推送通知

> [!IMPORTANT]
> 在本部分中的信息与 iOS 9 相关和之前，它已被保留此处以支持较旧 iOS 版本。 适用于 iOS 10 及更高版本，请参阅[用户通知框架指南](~/ios/platform/user-notifications/index.md)的 iOS 设备上支持本地和远程通知。

推送通知应保持简短，并且只能包含足够的数据来通知它应与服务器应用程序以获取更新的移动应用程序。 例如，当新的电子邮件到达时，服务器应用程序仅会通知新的电子邮件到达的移动应用程序。 通知不会包含新的电子邮件本身。 移动应用程序便可检索新的电子邮件服务器时适当

是在 iOS 中的通知中心的推送*Apple 推送通知网关服务 (APNS)*。 这是由负责将通知从应用程序服务器路由到 iOS 设备的 Apple 提供的服务。
下图演示了适用于 iOS 推送通知拓扑：![](remote-notifications-in-ios-images/image4.png "此映像演示了适用于 iOS 推送通知拓扑")

远程通知本身是 JSON 格式遵循格式的字符串，协议中指定[的通知负载](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)一部分[本地和推送通知编程指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)中[iOS 开发人员文档](https://developer.apple.com/devcenter/ios/index.action)。

Apple 维护两个环境的 APNS:*沙盒*和一个*生产*环境。 沙盒环境适用于在开发阶段测试，请参阅`gateway.sandbox.push.apple.com`TCP 端口上 2195年。 在生产环境并表示用于在应用程序中的已部署并且可以在找到`gateway.push.apple.com`TCP 端口上 2195年。

## <a name="requirements"></a>要求

推送通知必须遵守的 APNS 的体系结构规定的以下规则：

-  **256 字节消息限制**-通知的整个消息大小不能超过 256 个字节。
-  **没有收到确认**-APNS 不提供一条消息已到达目标接收方的任何通知发件人。 如果设备不可访问，并发送多个顺序通知后，除最新的所有通知将都会丢失。 仅最新通知将发送到设备。
-  **每个应用程序需要的安全证书**-与 APNS 的通信必须通过 SSL 来完成。


## <a name="creating-and-using-certificates"></a>创建和使用证书

在上一部分中所述的环境的每个需要自己的证书。 本部分将介绍如何创建证书、 将其预配配置文件，与关联，然后 PushSharp 用于获取个人信息交换证书。

1.  若要创建证书请转到 iOS 预配门户在 Apple 网站，如以下屏幕截图 （注意，在左侧的应用 Id 菜单项） 中所示：

    [![](remote-notifications-in-ios-images/image5new.png "IOS Apple 网站上的预配门户")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  接下来，导航到应用 ID 部分，并创建新的应用 ID，如以下屏幕截图中所示：

    [![](remote-notifications-in-ios-images/image6new.png "导航到应用 Id 部分并创建新的应用 ID")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  当您单击 **+** 按钮，可以输入说明和捆绑包标识符的应用程序 ID，如下面的屏幕截图中所示：

    [![](remote-notifications-in-ios-images/image7new.png "输入应用 ID 说明和捆绑标识符")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. 请务必选择**显式应用 ID**并且捆绑包标识符并不止于`*`。 这将创建适合用于多个应用程序的标识符和推送通知证书必须是单个应用程序。

1. 在应用服务下选择**推送通知**:

    [![](remote-notifications-in-ios-images/image8new.png "选择推送通知")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. 然后按**提交**以确认新的应用 ID 的注册：

    [![](remote-notifications-in-ios-images/image9new.png "确认注册新的应用 ID")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  接下来，您必须为应用程序 id。 创建证书 在左侧导航栏中，浏览到**证书 > 所有**，然后选择`+`按钮，如以下屏幕截图中所示：

    [![](remote-notifications-in-ios-images/image10new.png "为应用程序 ID 创建证书")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  选择是否想要使用的开发或生产证书：

    [![](remote-notifications-in-ios-images/image11new.png "选择开发或生产证书")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. 然后选择新我们刚刚创建的应用 ID:

    [![](remote-notifications-in-ios-images/image12new.png "选择刚创建的新应用 ID")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  这将显示将引导您完成创建的过程的说明*证书签名请求*使用**Keychain Access**在 mac 上应用程序

7.  现在，已创建的证书，它必须使用作为生成过程的一部分应用程序进行签名，以便它可以向 APNs 注册。 这需要创建和安装使用该证书的预配配置文件。

8.  若要创建开发预配配置文件，导航到**预配配置文件**部分，并按照步骤来创建它，使用我们刚刚创建的应用程序 Id。

9.  一旦你创建预配配置文件，打开**Xcode 管理器**并刷新它。 如果你创建预配配置文件不会出现可能有必要从 iOS 设置门户下载配置文件并手动将其导入。 下面的屏幕截图与添加的预配配置文件显示组织者的示例：  
    [![](remote-notifications-in-ios-images/image13new.png "此屏幕截图显示组织者的示例与添加的预配配置文件")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  此时，我们需要配置 Xamarin.iOS 项目以使用这个新创建的预配配置文件。 这是从**项目选项**对话框下**iOS 捆绑签名**选项卡，如以下屏幕截图中显示：  
    [![](remote-notifications-in-ios-images/image11.png "配置 Xamarin.iOS 项目以使用这个新创建的预配配置文件")](remote-notifications-in-ios-images/image11.png#lightbox)

此时应用程序配置为使用推送通知。 但是，仍有几个步骤所需的证书。 此证书是 DER PushSharp，需要的个人信息交换 (PKCS12) 证书与不兼容的格式。 若要将证书转换，这样就可供 PushSharp，执行这些最后的步骤：

1.  **下载的证书文件**-登录到 iOS 设置门户，选择证书选项卡中，选择正确的预配配置文件并选择与关联的证书**下载**。
1.  **打开密钥链访问**-这是应用程序是在 OS X 中的密码管理系统的 GUI 界面。
1.  **将证书导入**-如果尚未安装证书，**文件...导入项目**从密钥链访问菜单。 导航到更高版本，导出的证书，并选择它。
1.  **导出证书**-展开证书相关联的私钥是否可见以便中，右键单击项并选择导出。 系统将提示您输入文件名和导出的文件的密码。

现在我们已完成的证书。 我们已创建将用于 iOS 应用程序进行签名的证书并转换为可用于 PushSharp 服务器应用程序中的格式的该证书。 下一步让我们看看 iOS 应用程序如何使用 APNS 进行交互。

## <a name="registering-with-apns"></a>向 APNS 注册

之前 iOS 应用程序可以接收远程通知它必须向 APNS 注册。 将生成唯一的设备令牌 APNS 并将其返回到 iOS 应用程序。 IOS 应用程序将设备令牌时，将，然后将自身注册应用程序服务器。 一旦发生所有这种情况，注册完成，并且应用程序服务器可能会将通知推送到移动设备。

从理论上讲，但实际上这不会发生，通常，设备令牌可能会更改 iOS 应用程序本身将向 APNS 注册，每次。 作为一种优化应用程序可能缓存的最新的设备令牌，并仅更新应用程序服务器时它会更改。 下图说明了注册和获取设备令牌的过程：

 ![](remote-notifications-in-ios-images/image12.png "此图描述了注册和获取设备令牌的过程")

在中处理注册到 APNS`FinishedLaunching`应用程序委托类通过调用方法`RegisterForRemoteNotificationTypes`上当前`UIApplication`对象。 当向 APNS 注册的 iOS 应用程序时，它还必须指定其希望接收远程通知哪些类型。 这些远程通知类型声明的枚举中`UIRemoteNotificationType`。 下面的代码段是 iOS 应用程序可以注册以接收远程警报和锁屏提醒通知的示例：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

在后台-发生 APNS 注册请求收到响应后，iOS 将调用的方法`RegisteredForRemoteNotifications`在`AppDelegate`类，并将已注册的设备令牌。 该令牌将包含在`NSData`对象。 下面的代码段演示如何检索设备令牌提供的 APNS:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

如果出于某种原因 （如设备未连接到 Internet），则注册将失败，将调用 iOS`FailedToRegisterForRemoteNotifications`上应用程序委托类。 下面的代码段演示如何向用户告知他们注册失败，显示警报：

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>设备令牌内务处理

设备令牌将过期或随时间而变化。 因为这一事实，服务器应用程序将需要进行一些房屋清理，然后清除这些已过期或已更改的标记。 当应用程序发送推送通知已过期的令牌的设备作为时，APNS 将记录并保存该到期的令牌。 然后，服务器可能会查询 APNS 找出哪些令牌已过期。

用于提供 APNS*反馈服务*-通过创建推送通知并将其发送后将数据发送有关哪些令牌已过期的证书进行身份验证的 HTTPS 终结点。 这已由 Apple 已弃用并删除。

取而代之的是这种情况的反馈服务以前报告的一个新 HTTP 状态代码：

> 410-设备令牌不再处于活动状态的主题。

此外，新`timestamp`JSON 数据密钥将在响应正文中：

> 如果中的值： 状态标头为 410，此项的值为 APNs 确认设备令牌不再是有效的主题的最后一个时间。
>
> 停止将推送通知直到设备使用更高版本的时间戳与您的提供程序注册令牌。

## <a name="summary"></a>总结

本部分介绍围绕在 iOS 中的推送通知的关键概念。 它介绍了角色的 Apple 推送通知网关服务 (APNS)。 然后，它包括创建和使用 APNS 至关重要的安全证书。 本文档讨论了如何使用应用程序服务器已完成的最后*反馈服务*停止跟踪过期的设备令牌。


## <a name="related-links"></a>相关链接

- [通知-演示本地和远程通知 （示例）](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [本地和推送通知面向开发人员](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
