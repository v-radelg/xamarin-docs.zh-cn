---
title: 在 Xamarin.iOS 中移交
description: 本文介绍使用用于传输的 Xamarin.iOS 应用中的切换用户上运行的应用之间的用户活动的其他设备。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 899e40460371933a3e1cb694618c7d33a124e76c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672698"
---
# <a name="handoff-in-xamarinios"></a>在 Xamarin.iOS 中移交

_本文介绍使用用于传输的 Xamarin.iOS 应用中的切换用户上运行的应用之间的用户活动的其他设备。_

Apple 引入切换 iOS 8 和 OS X Yosemite (10.10) 提供常见的机制，用户将启动其中一个其设备上的活动中运行同一应用程序或支持的相同活动的另一个应用程序的另一台设备。

[![](handoff-images/handoff02.png "执行切换操作的一个示例")](handoff-images/handoff02.png#lightbox)

本文快速看一下启用 Xamarin.iOS 应用中共享的活动，并介绍详细信息中的切换框架：

## <a name="about-handoff"></a>有关切换

切换 （也称为连续性） 已作为一种方式为用户启动一个其设备 （iOS 或 Mac） 上的一个活动并在另一台设备 （如标识的用户的 iClou 继续该同一活动中引入的 Apple 在 iOS 8 和 OS X Yosemite (10.10)d 帐户)。

切换已扩展在 iOS 9，还支持新的、 增强的搜索功能。 有关详细信息，请参阅我们[搜索增强功能](~/ios/platform/search/index.md)文档。

例如，用户可以启动其 iPhone 上的一封电子邮件和无缝继续在其使用的所有相同的消息信息填充和它们在 iOS 中剩余的同一位置中的光标的 Mac 上的电子邮件。

任何应用共享同一_团队 ID_ （适用于 Mac、 企业版是符合条件的使用切换以跨应用继续用户活动，只要这些应用程序都是通过 iTunes 应用商店提供或由已注册的开发人员签名或即席的应用程序）。

任何`NSDocument`或`UIDocument`基于的应用自动具有支持内置的并且要求最少的更改支持切换的切换。

### <a name="continuing-user-activities"></a>执行完用户活动

`NSUserActivity`类 (以及一些小改动`UIKit`和`AppKit`) 用于定义用户的活动，可能可以继续在另一用户的设备提供支持。

为用于传递到另一个用户的设备的活动，它都必须封装在一个实例`NSUserActivity`，标记为_当前活动_，具有有效负载已设置 （用于执行延续任务的数据） 和然后，活动必须传输给该设备。

切换将传递的最基本信息，以定义要继续使用更大的数据数据包通过 iCloud 要同步的活动。

在接收设备上，用户将收到通知活动是可用于继续符。 如果用户选择在新设备上继续活动时，指定的应用启动 （如果尚未运行） 和中的有效负载`NSUserActivity`用于重新启动该活动。

[![](handoff-images/handoffinteractions.png "执行完用户活动概述")](handoff-images/handoffinteractions.png#lightbox)

共享相同的开发人员团队 ID 和响应的应用给定_活动类型_适合继续符。 应用程序定义它在支持的活动类型`NSUserActivityTypes`键及其**Info.plist**文件。 鉴于此，执行完设备选择应用程序以执行延续任务根据团队 ID，活动类型和 （可选）_活动标题_。

接收应用程序使用中的信息`NSUserActivity`的`UserInfo`字典配置其用户界面并还原给定活动的状态，以便显示给最终用户无缝转换。

如果该延续需要详细信息不是可以有效地通过发送`NSUserActivity`、 恢复应用程序可以发送到发起应用程序的调用并建立一个或多个流传输所需的数据。 例如，如果活动已编辑大文本文档包含多个映像，流式处理所需传输接收的设备上继续活动所需的信息。 有关详细信息，请参阅[支持延续流](#Supporting-Continuation-Streams)下面一节。

如上面所述`NSDocument`或`UIDocument`基于的应用自动具有支持内置的切换。 有关详细信息，请参阅[基于文档的应用程序中支持的移交](#Supporting-Handoff-in-Document-Based-Apps)下面一节。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 类

`NSUserActivity`类是移交 exchange 中的主要对象，用于封装可继续用于用户活动的状态。 应用将实例化一份`NSUserActivity`对于它支持，并且希望继续在另一台设备上的任何活动。 例如，文档编辑器将创建用于每个文档的活动当前打开。 但是，只有最前面文档 （在最前面窗口或选项卡中显示） 是_当前活动_，因此可用于继续符。

实例`NSUserActivity`都由其`ActivityType`和`Title`属性。 `UserInfo`字典属性用于携带有关的活动的状态的信息。 设置`NeedsSave`属性设置为`true`如果你想要为延迟加载状态信息通过`NSUserActivity`的委托。 使用`AddUserInfoEntries`方法来合并到其他客户端中的新数据`UserInfo`字典，因为需要保留活动的状态。

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 类

`NSUserActivityDelegate`用于将信息保存在`NSUserActivity`的`UserInfo`字典最新状态并且与该活动的当前状态同步。 需要在要更新的活动中的信息系统 (如之前在另一台设备上的继续符)，它将调用`UserActivityWillSave`委托的方法。

您将需要实现`UserActivityWillSave`方法，并对进行任何更改`NSUserActivity`(如`UserInfo`，`Title`等) 以确保它仍反映当前活动的状态。 当系统调用`UserActivityWillSave`方法，`NeedsSave`标志将被清除。 如果您修改任何活动的数据属性，您将需要设置`NeedsSave`到`true`试。

而不是使用`UserActivityWillSave`上面介绍的方法，可以选择让`UIKit`或`AppKit`自动管理的用户活动。 若要执行此操作，设置响应方对象的`UserActivity`属性并实现`UpdateUserActivityState`方法。 请参阅[响应程序中支持的移交](#Supporting-Handoff-in-Responders)部分获取详细信息。

### <a name="app-framework-support"></a>应用程序框架支持

这两`UIKit`(iOS) 和`AppKit`(OS X) 提供内置支持，以便在提交`NSDocument`，响应方 (`UIResponder`/`NSResponder`)，和`AppDelegate`类。 而每个 OS 稍有不同实现移交，基本的机制和 Api 都相同。

#### <a name="user-activities-in-document-based-apps"></a>基于文档的应用程序中的用户活动

基于文档的 iOS 和 OS X 应用自动具有内置的切换支持。 若要激活此支持，您将需要添加`NSUbiquitousDocumentUserActivityType`键和值为每个`CFBundleDocumentTypes`在应用中的条目**Info.plist**文件。

如果此项存在，同时`NSDocument`并`UIDocument`自动创建`NSUserActivity`iCloud 基于文档的指定的类型的实例。 将需要提供该应用支持的文档的每个类型的活动类型和多个文档类型可以使用相同的活动类型。 这两`NSDocument`并`UIDocument`自动填充`UserInfo`的属性`NSUserActivity`使用其`FileURL`属性的值。

在 OS X 上`NSUserActivity`由`AppKit`和关联的响应程序自动成为当前活动文档的窗口成为主窗口时。 在 iOS 上，对于`NSUserActivity`管理的对象`UIKit`，必须调用`BecomeCurrent`方法显式或拥有此文档`UserActivity`属性设置的`UIViewController`时应用程序转入前台。

`AppKit` 将自动还原任何`UserActivity`以这种方式在 OS X 上创建属性。如果发生这种情况`ContinueUserActivity`方法将返回`false`或如果未实现。 在此情况下，打开该文档与`OpenDocument`方法`NSDocumentController`随后会收到和`RestoreUserActivityState`方法调用。

请参阅[基于文档的应用程序中支持的移交](#Supporting-Handoff-in-Document-Based-Apps)部分获取详细信息。

#### <a name="user-activities-and-responders"></a>用户活动和响应程序

这两`UIKit`并`AppKit`可以自动管理用户活动，如果将其设置为响应方对象的`UserActivity`属性。 如果已修改状态，你将需要设置`NeedsSave`响应方的属性`UserActivity`到`true`。 系统会自动保存`UserActivity`时必需的之后让响应方时间进行更新的状态，通过调用, 其`UpdateUserActivityState`方法。

如果多个响应程序共享单个`NSUserActivity`实例，它们接收`UpdateUserActivityState`回调时系统会更新用户活动对象。 响应方需要调用`AddUserInfoEntries`方法来更新`NSUserActivity`的`UserInfo`字典以此时反映当前的活动状态。 `UserInfo`在每个之前清除字典`UpdateUserActivityState`调用。

若要解除本身与活动关联，响应方可以设置其`UserActivity`属性设置为`null`。 管理应用程序框架时`NSUserActivity`实例都有没有更多相关联响应程序或文档，它是自动失效。

请参阅[响应程序中支持的移交](#Supporting-Handoff-in-Responders)部分获取详细信息。

#### <a name="user-activities-and-the-appdelegate"></a>用户活动和 AppDelegate

应用程序的`AppDelegate`处理切换延续时是其主入口点。 当用户响应提交通知，启动相应的应用程序 （如果未尚未运行） 和`WillContinueUserActivityWithType`方法的`AppDelegate`将调用。 此时，应用应通知用户开始延续。

`NSUserActivity`实例传递时`AppDelegate`的`ContinueUserActivity`调用方法。 此时，应配置应用程序的用户界面，并继续给定的活动。

请参阅[实现切换](#Implementing-Handoff)部分获取详细信息。

## <a name="enabling-handoff-in-a-xamarin-app"></a>在 Xamarin 应用中启用切换

由于切换所规定的安全要求，必须正确配置 Xamarin.iOS 应用程序使用切换框架和 Xamarin.iOS 项目文件中 Apple 开发人员门户。

请执行以下操作：

1. 登录到[Apple 开发人员门户](https://developer.apple.com)。
2. 单击**证书、 标识符和配置文件**。
3. 如果尚未这样做，请单击**标识符**并创建您的应用程序的 ID (例如`com.company.appname`)，否则编辑现有的 id。
4. 絋粄**iCloud**服务具有给定 ID 的复选框：

    [![](handoff-images/provision01.png "启用给定 ID 的 iCloud 服务")](handoff-images/provision01.png#lightbox)
5. 保存更改。
4. 单击**预配配置文件** > **开发**并创建为你预配配置文件的新开发应用：

    [![](handoff-images/provision02.png "创建新的开发预配配置文件的应用")](handoff-images/provision02.png#lightbox)
5. 请下载并安装新的预配配置文件或使用 Xcode 下载并安装该配置文件。
6. 编辑 Xamarin.iOS 项目选项，并确保使用你刚刚创建的预配配置文件：

    [![](handoff-images/provision03.png "选择刚创建的预配配置文件")](handoff-images/provision03.png#lightbox)
7. 接下来，编辑你**Info.plist**文件，并确保将用于创建预配配置文件的应用程序 ID:

    [![](handoff-images/provision04.png "设置应用程序 ID")](handoff-images/provision04.png#lightbox)
8. 滚动到**后台模式**部分，并检查以下各项：

    [![](handoff-images/provision05.png "启用所需的后台模式")](handoff-images/provision05.png#lightbox)
9. 将所做的更改保存到的所有文件。

在准备好这些设置，该应用程序现在已准备好访问切换 Framework Api。 有关预配的详细信息，请参阅我们[设备预配](~/ios/get-started/installation/device-provisioning/index.md)并[预配您的应用程序](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="implementing-handoff"></a>实现切换

用户活动可以继续使用相同的开发人员团队 ID 进行签名并支持相同的活动类型在应用之间。 在 Xamarin.iOS 应用程序中实现切换要求您创建用户活动对象 (在`UIKit`或`AppKit`)、 更新对象的状态来跟踪活动，并接收设备上继续活动。

### <a name="identifying-user-activities"></a>确定用户活动

实现切换的第一步是确定你的应用支持的用户活动的类型和看到的这些活动是另一台设备上继续符的最佳候选项。 例如： ToDo 应用程序可能支持编辑的项作为一个_用户活动类型_，并支持浏览与另一个可用的项目列表。

应用程序可以创建任意多个用户活动类型是必需的因为另一个用于应用程序提供的任何函数。 对于每个用户活动类型，该应用将需要跟踪类型的活动开始和结束，并且需要在另一台设备上继续该任务的最新的状态信息进行维护时。

用户活动可以继续使用相同的团队 ID，而无需任何之间发送和接收应用程序的一对一映射签名的任何应用程序。 例如，给定的应用程序可以创建四个不同的活动，是由另一台设备上的不同，其中的个别应用程序类型。 这是一种常见情况之间的应用程序 （可能有许多特性和功能） 的 Mac 版本和 iOS 应用，其中每个应用程序是较小并专注于特定任务。

### <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

_活动类型标识符_短字符串添加到`NSUserActivityTypes`的应用程序的数组**Info.plist**用于唯一标识给定的用户的活动类型的文件。 该应用支持的每个活动的数组中将有一个条目。 Apple 建议使用活动类型标识符的反向 DNS 样式表示法以避免冲突。 例如：`com.company-name.appname.activity`对于特定的应用程序基于活动或`com.company-name.activity`，可以跨多个应用运行的活动。

在创建时使用的活动类型标识符`NSUserActivity`实例以确定活动的类型。 当在另一台设备上继续执行活动时，活动类型 （以及应用程序的团队 ID) 确定哪些应用才能继续活动。

例如，我们将创建名为示例应用**MonkeyBrowser** ([此处下载](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/))。 此应用将显示四个选项卡，每个都有不同的 URL 打开 web 浏览器视图中。 用户将能够继续运行应用程序的不同的 iOS 设备上的任何选项卡。

若要创建所需的活动类型标识符，以支持此行为，请编辑**Info.plist**文件，并切换到**源**视图。 添加`NSUserActivityTypes`密钥，然后创建以下标识符：

[![](handoff-images/type01.png "NSUserActivityTypes 密钥和 plist 编辑器中的所需的标识符")](handoff-images/type01.png#lightbox)

我们创建了四个新的活动类型标识符，一个用于每个示例中的选项卡**MonkeyBrowser**应用。 创建你自己的应用时, 的内容替换为`NSUserActivityTypes`您的应用程序使用特定于活动的活动类型标识符数组支持。

### <a name="tracking-user-activity-changes"></a>跟踪用户活动更改

当我们创建的新实例`NSUserActivity`类中，我们将指定`NSUserActivityDelegate`实例，以跟踪活动的状态更改。 例如，下面的代码可以用于跟踪状态更改：

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

`UserActivityReceivedData`时延续 Stream 从发送设备收到数据后调用方法。 有关详细信息，请参阅[支持延续流](#Supporting-Continuation-Streams)下面一节。

`UserActivityWasContinued`时另一台设备具有对当前的设备中的活动所调用方法。 根据类型的活动，如将新项添加到 ToDo 列表中，应用可能需要中止发送设备上的活动。

`UserActivityWillSave`之前保存对活动的任何更改并在本地可用的设备上同步调用方法。 可以使用此方法以更改到任何最近一分钟`UserInfo`属性的`NSUserActivity`实例，然后才能将其发送。

### <a name="creating-a-nsuseractivity-instance"></a>创建 NSUserActivity 实例

您的应用程序想要继续操作可能会提供另一台设备上的每个活动都必须封装在`NSUserActivity`实例。 应用程序可以创建所需任意数量的活动和这些活动的性质所依赖的功能和相关应用的功能。 例如，电子邮件应用可能会创建一个活动，以便创建新消息，另一个用于读取消息。

对于我们的示例应用，一个新`NSUserActivity`每次用户在其中一个选项卡式的 web 浏览器视图中输入新的 URL 时创建。 下面的代码将存储给定的选项卡的状态：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

创建一个新`NSUserActivity`使用其中一个用户活动类型的上述步骤中创建和为活动提供的可读的标题。 它将附加到的实例`NSUserActivityDelegate`创建上面要监视的状态更改，并通知 iOS 用户此活动是当前活动。

### <a name="populating-the-userinfo-dictionary"></a>填充 UserInfo 字典

如我们所见，上面`UserInfo`的属性`NSUserActivity`类是`NSDictionary`的键 / 值对，用来定义给定活动的状态。 中存储的值`UserInfo`必须是以下类型之一： `NSArray`， `NSData`， `NSDate`， `NSDictionary`， `NSNull`， `NSNumber`， `NSSet`， `NSString`，或`NSURL`。 `NSURL` iCloud 文档所指向的数据值会自动进行调整，以便它们指向相同的文档上接收的设备。

在上述示例中，我们创建了`NSMutableDictionary`对象，并填入单个密钥提供用户当前正在查看给定的选项卡的 URL。`AddUserInfoEntries`用户活动的方法用于将用于还原接收设备上的活动的数据更新的活动：

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建议保持发送给只具备最小值，以确保活动发送到接收设备及时的信息。 如果像编辑映像附加到文档，更大的信息是必需的需要发送，则应使用继续符流。 请参阅[支持延续流](#Supporting-Continuation-Streams)部分获取更多详细信息。

### <a name="continuing-an-activity"></a>继续活动

切换自动将通知本地 iOS 和 OS X 设备的物理方式进入源设备中并登录到的持续性的用户活动的可用性的同一 iCloud 帐户。 如果用户选择在新设备上继续活动时，系统将启动相应应用程序中 （基于团队 ID 和活动类型） 和信息其`AppDelegate`延续需要出现。

首先，`WillContinueUserActivityWithType`方法称为使应用程序可以通知用户即将开始延续。 我们使用下面的代码**AppDelegate.cs**我们的示例应用程序处理继续启动文件：

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

在上述示例中，每个视图控制器注册`AppDelegate`并具有公共`PreparingToHandoff`显示活动指示器和让用户知道该活动是要传递给当前设备的消息的方法。 示例:

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

`ContinueUserActivity`的`AppDelegate`将调用以实际继续给定的活动。 同样，从我们的示例应用：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

公共`PerformHandoff`的每个视图控制器方法实际完成切换，并还原当前设备上的活动。 在示例中，它在给定的选项卡的用户已浏览不同设备显示的相同 URL。 示例:

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

`ContinueUserActivity`方法包含`UIApplicationRestorationHandler`你能够调用用于文档或响应程序基于活动恢复。 你将需要传递`NSArray`或还原操作处理程序调用时可还原对象。 例如：

```csharp
completionHandler (new NSObject[]{Tab4});
```

每个对象传递，其`RestoreUserActivityState`将调用方法。 每个对象然后可以使用中的数据`UserInfo`字典以还原其自己的状态。 例如：

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

对于基于文档的应用程序，如果不实现`ContinueUserActivity`方法或其返回`false`，`UIKit`或`AppKit`自动恢复活动。 请参阅[基于文档的应用程序中支持的移交](#Supporting-Handoff-in-Document-Based-Apps)部分获取详细信息。

### <a name="failing-handoff-gracefully"></a>正常故障切换

由于提交依赖之间松散连接的集合 iOS 和 OS X 设备的信息的传输，传输过程中有时可能会失败。 您应设计您的应用程序正确地处理这些失败，并通知出现的任何情况的用户。

出现故障时，`DidFailToContinueUserActivitiy`方法的`AppDelegate`将调用。 例如：

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

应使用所提供`NSError`向与失败有关的用户提供信息。

## <a name="native-app-to-web-browser-handoff"></a>Web 浏览器切换到本机应用

用户可能想要继续活动而无需在设备上安装适当的本机应用。 在某些情况下，基于 web 的界面可能会提供所需的功能和活动仍可继续运行。 例如，用户的电子邮件帐户可能会提供编写和读取消息的基于 web 的 UI。

如果原始的本机应用已知道 URL 的 web 接口 （以及用于标识给定的项正在继续执行所需的语法），它可以中编码此信息`WebpageURL`属性的`NSUserActivity`实例。 如果接收的设备没有安装来处理延续任务的相应本机应用，可以调用提供的 web 界面。

## <a name="web-browser-to-native-app-handoff"></a>本机应用程序切换到 web 浏览器

如果用户已在源设备上使用基于 web 的界面和接收的设备上的本机应用声明的域部分`WebpageURL`属性，则系统将使用该应用程序的句柄将延续任务。 新设备会收到`NSUserActivity`将标记为活动类型的实例`BrowsingWeb`并`WebpageURL`将包含用户已访问的 URL`UserInfo`字典将为空。

若要参与这种类型的切换应用，它必须声明中的域`com.apple.developer.associated-domains`格式的权利`<service>:<fully qualified domain name>`(例如： `activity continuation:company.com`)。

如果指定的域匹配`WebpageURL`属性的值，切换从位于该域的网站下载的已批准的应用 Id 列表。 该网站必须提供的名为已签名的 JSON 文件中的已批准 Id 的列表**apple 应用站点关联**(例如， `https://company.com/apple-app-site-association`)。

此 JSON 文件包含一个字典，其中在窗体中指定的应用程序 Id 列表`<team identifier>.<bundle identifier>`。 例如：

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

JSON 文件进行签名 (以使其具有正确`Content-Type`的`application/pkcs7-mime`)，使用**终端**应用程序和一个`openssl`命令使用证书和 iOS 的受信任证书颁发机构颁发的密钥 (请参阅[https://support.apple.com/kb/ht5012 ](https://support.apple.com/kb/ht5012)列表)。 例如：

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

`openssl`命令将输出放在网站的已签名的 JSON 文件**apple 应用站点关联**URL。 例如：

```csharp
https://example.com/apple-app-site-association.
```

应用程序将接收的任何活动的`WebpageURL`域处于其`com.apple.developer.associated-domains`权利。 仅`http`和`https`协议支持，任何其他协议将引发异常。

## <a name="supporting-handoff-in-document-based-apps"></a>在基于文档的应用程序中支持切换

如果如上所述，在 iOS 和 OS X 上基于文档的应用程序将自动支持切换 iCloud 基于文档的应用**Info.plist**文件包含`CFBundleDocumentTypes`密钥的`NSUbiquitousDocumentUserActivityType`。 例如：

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

在此示例中的字符串是活动的具有附加名称的反向 DNS 应用程序指示符。 如果输入了这种方式，不需要活动类型条目中重复`NSUserActivityTypes`构成的数组**Info.plist**文件。

自动创建用户活动对象 (可通过文档的`UserActivity`属性) 可以由应用程序中的其他对象引用和用于还原延续状态。 例如，若要跟踪项选择和文档定位。 您需要设置此活动`NeedsSave`属性设置为`true`时的状态更改和更新`UserInfo`字典中的`UpdateUserActivityState`方法。

`UserActivity`属性可以用于从任何线程，并且符合键-值观察 (KVO) 协议，因此它可以用于使文档保持同步，因为它将移入和签出的 iCloud。 `UserActivity`文档关闭时，将无效属性。

有关详细信息，请参阅 Apple[基于文档的应用程序中的用户活动支持](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5)文档。

## <a name="supporting-handoff-in-responders"></a>在响应中支持切换

可以将响应程序相关联 (从继承`UIResponder`在 iOS 上或`NSResponder`OS X 上) 与活动通过设置其`UserActivity`属性。 系统会自动保存`UserActivity`在适当时间调用响应方的属性`UpdateUserActivityState`方法将当前数据添加到用户活动对象使用`AddUserInfoEntriesFromDictionary`方法。

## <a name="supporting-continuation-streams"></a>支持延续流

可能在某些情况下，其中继续活动所需信息的量不能进行有效地传输初始提交有效负载。 在这些情况下，接收应用程序可以建立在本身与发起的应用程序将数据传输之间的一个或多个流。

发起应用程序将设置`SupportsContinuationStreams`的属性`NSUserActivity`实例向`true`。 例如：

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

然后，接收应用程序可以调用`GetContinuationStreams`方法`NSUserActivity`在其`AppDelegate`建立流。 例如：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

源设备上用户活动委托，它接收流通过调用其`DidReceiveInputStream`方法提供的数据请求以继续恢复设备上的用户活动。

将使用`NSInputStream`来提供对数据进行流处理的只读访问权限和`NSOutputStream`提供只写访问权限。 流应使用以请求和响应的方式，其中接收应用程序请求更多数据并将其发起应用程序提供。 以便从输入流在执行完设备上读取写入到输出流源设备上的数据，反之亦然。

即使在延续 Stream 是必需的情况下，应该有最小和规定的后两个应用之间的通信。

有关详细信息，请参阅 Apple[使用延续流](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13)文档。

## <a name="handoff-best-practices"></a>提交最佳做法

通过切换用户活动的无缝的延续任务的成功实现要求仔细设计，因为所有各种所涉及的组件。 Apple 建议采用您切换已启用的应用程序的以下最佳实践：

- 设计用户活动，需要最小的负载可能将继续执行的活动状态。 更大的有效负载的时间越长，需要继续符来启动。
- 如果您必须传输大量数据的成功延续，考虑到成本参与配置和网络开销。
- 很普遍适用于大型的 Mac 应用，以创建将由多个，较小，在 iOS 设备上的特定于任务的应用的用户活动。 应设计不同的应用程序和操作系统版本到很好地协同工作，或正常退出。
- 在指定活动类型时，使用反向 DNS 表示法以避免冲突。 如果某个活动是特定于给定的应用程序，应在类型定义中包含其名称 (例如`com.myCompany.myEditor.editing`)。 如果该活动可以跨多个应用程序工作，从定义中删除应用程序名称 (例如`com.myCompany.editing`)。
- 如果你的应用需要更新的用户活动的状态 (`NSUserActivity`) 设置`NeedsSave`属性设置为`true`。 在适当时间切换将调用的委托`UserActivityWillSave`方法，以便可以更新`UserInfo`所需的字典。
- 由于提交进程可能会接收设备上立即初始化，应实现`AppDelegate`的`WillContinueUserActivity`，并通知用户即将开始延续。

## <a name="example-handoff-app"></a>切换应用示例

作为在 Xamarin.iOS 应用程序中使用切换的示例，我们通过一些[ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)使用本指南的示例应用程序。 应用程序具有四个选项卡，用户可以使用浏览 web，每个都具有给定的活动类型：天气、 收藏夹、 茶歇和工作。

当用户输入新的 URL 并点击任何选项卡上**转**按钮，一个新`NSUserActivity`为包含的 URL 的用户当前正在浏览该选项卡创建：

[![](handoff-images/handoff01.png "切换应用示例")](handoff-images/handoff01.png#lightbox)

如果另一个用户的设备具有**MonkeyBrowser**应用程序安装，登录到 iCloud 使用相同的用户帐户，是在同一个网络和更高版本设备的临近，切换活动将显示在主页屏幕 （在较低的左下角）：

[![](handoff-images/handoff02.png "在左下角的主屏幕上显示切换活动")](handoff-images/handoff02.png#lightbox)

如果用户在提交图标上向上拖动，将启动应用程序和用户活动中指定`NSUserActivity`将继续在新设备上：

[![](handoff-images/handoff03.png "用户活动继续执行新的设备上")](handoff-images/handoff03.png#lightbox)

当用户活动已成功发送到另一个 Apple 设备时，发送设备的`NSUserActivity`将接收到调用`UserActivityWasContinued`方法在其`NSUserActivityDelegate`，让它知道用户活动已成功传输到另一个设备。

## <a name="summary"></a>总结

本文提供使用继续之间的用户的 Apple 设备的多个用户的活动的切换框架的简介。 接下来，它介绍了如何启用并在 Xamarin.iOS 应用程序中实现切换。 最后，它介绍了不同类型的切换延续可用和提交最佳做法。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [MonkeyBrowser 示例](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [What's New iOS 9.0 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 框架引用](https://developer.apple.com/library/ios/home_kit_framework_ref)
