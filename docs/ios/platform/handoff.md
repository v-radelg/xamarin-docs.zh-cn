---
title: Xamarin 中的移交
description: 本文介绍如何在 Xamarin iOS 应用程序中使用转换, 以便在用户的其他设备上运行的应用程序之间传输用户活动。
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 28c5086833ceb1dc8550e513b120f7355aa9bebe
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656582"
---
# <a name="handoff-in-xamarinios"></a>Xamarin 中的移交

_本文介绍如何在 Xamarin iOS 应用程序中使用转换, 以便在用户的其他设备上运行的应用程序之间传输用户活动。_

Apple 在 iOS 8 和 OS X Yosemite (10.10) 中引入了移交, 以提供一种通用机制, 使用户能够将其设备上启动的活动传输到另一台运行相同应用程序的设备, 或其他支持相同活动的应用程序。

[![](handoff-images/handoff02.png "执行移交操作的示例")](handoff-images/handoff02.png#lightbox)

本文将简要介绍如何在 Xamarin iOS 应用程序中启用活动共享并详细介绍了移交框架:

## <a name="about-handoff"></a>关于移交

IOS 8 和 OS X Yosemite (10.10) 中的 Apple 引入了移交 (也称为连续性), 以使用户能够在其某个设备 (iOS 或 Mac) 上启动活动, 并在其设备上继续此相同活动 (由用户的 iClou 标识)d 帐户)。

在 iOS 9 中扩展了移交, 还支持新的增强搜索功能。 有关详细信息, 请参阅我们的[搜索增强](~/ios/platform/search/index.md)文档。

例如, 用户可以在其 iPhone 上启动一封电子邮件, 并在其 Mac 上无缝地继续发送电子邮件, 并在其中填充相同的所有消息信息, 并将光标置于 iOS 中的相同位置。

共享同一_团队 ID_的任何应用都有资格使用移交在应用中继续执行用户活动, 只要这些应用通过 ITunes 应用商店交付或由注册开发人员签署 (适用于 Mac、企业或即席应用)。

任何`NSDocument` 或`UIDocument`基于的应用程序都将自动提供提供支持, 并需要最少的更改来支持提交。

### <a name="continuing-user-activities"></a>继续执行用户活动

类 (以及`UIKit` 和`AppKit`的一些小更改) 支持定义用户的活动, 该活动可能会在用户的另一台设备上继续。 `NSUserActivity`

对于要传递到另一个用户设备的活动, 必须将它封装在一个实例`NSUserActivity`中, 将其标记为_当前活动_, 使其有效负载 (用于执行延续的数据), 并且该活动必须为传输到该设备。

移交会传递最少的信息来定义要继续的活动, 同时通过 iCloud 同步更大的数据包。

在接收设备上, 用户将收到一条通知, 告知活动可用于继续。 如果用户选择继续新设备上的活动, 将启动指定的应用 (如果尚未运行), 并使用中`NSUserActivity`的负载来重新启动该活动。

[![](handoff-images/handoffinteractions.png "继续执行用户活动的概述")](handoff-images/handoffinteractions.png#lightbox)

只有共享同一开发人员团队 ID 并响应给定_活动类型_的应用才有资格继续进行。 应用在其`NSUserActivityTypes` **info.plist**文件的密钥下定义其支持的活动类型。 为此, 正在继续的设备会根据团队 ID、活动类型和_活动标题_(可选) 选择应用程序以执行延续任务。

接收应用使用来自的`NSUserActivity` `UserInfo`字典中的信息来配置其用户界面并还原给定活动的状态, 以便向最终用户显示无缝转换。

如果延续需要的信息超过了可以通过有效的`NSUserActivity`形式发送的信息, 则继续执行的应用程序可以发送对原始应用程序的调用并建立一个或多个流来传输所需的数据。 例如, 如果活动正在编辑包含多个图像的大型文本文档, 则需要进行流式传输来传输继续接收设备上的活动所需的信息。 有关详细信息, 请参阅下面的[支持继续流](#supporting-continuation-streams)部分。

如上所述, `NSDocument`或`UIDocument`基于的应用程序自动提供内置支持。 有关详细信息, 请参阅下面的[基于文档的应用中的支持移交](#supporting-handoff-in-document-based-apps)部分。

### <a name="the-nsuseractivity-class"></a>NSUserActivity 类

`NSUserActivity`类是切换交换中的主要对象, 用于封装可用于继续的用户活动的状态。 应用将实例化其支持的`NSUserActivity`任何活动的副本, 并希望在另一台设备上继续操作。 例如, 文档编辑器将为当前打开的每个文档创建一个活动。 但是, 只有最前面的文档 (显示在最前面的窗口或选项卡中) 是_当前活动_和可供继续使用的因此。

的`NSUserActivity`实例由其`ActivityType`和`Title`属性标识。 `UserInfo` Dictionary 属性用于携带有关活动状态的信息。 如果要延迟通过`true` `NSUserActivity`的委托加载状态信息, 请将属性设置为。`NeedsSave` 使用方法可根据需要将其他客户端中的`UserInfo`新数据合并到字典中, 以保留活动状态。 `AddUserInfoEntries`

### <a name="the-nsuseractivitydelegate-class"></a>NSUserActivityDelegate 类

用于使`NSUserActivity`中的`UserInfo`信息保持最新, 并与活动的当前状态保持同步。 `NSUserActivityDelegate` 当系统需要更新活动中的信息 (例如在另一台设备上继续进行) 时, 它将调用`UserActivityWillSave`委托的方法。

`UserActivityWillSave`你将需要实现方法并对`NSUserActivity` (例如`UserInfo`、 `Title`等) 进行任何更改, 以确保它仍反映当前活动的状态。 当系统调用`UserActivityWillSave`方法时`NeedsSave` , 将清除标志。 如果修改活动的任何数据属性, 则需要再次将设置`NeedsSave`为。 `true`

您可以选择自动`UserActivityWillSave`拥有`UIKit`或`AppKit`管理用户活动, 而不是使用上面提供的方法。 为此, 请设置响应方对象的`UserActivity`属性并`UpdateUserActivityState`实现方法。 有关详细信息, 请参阅下面的[响应程序中的支持移交](#supporting-handoff-in-responders)部分。

### <a name="app-framework-support"></a>应用框架支持

( `UIKit` IOS) 和`AppKit` (OS X) 都为`NSDocument`、响应方 (`NSResponder``UIResponder`/) 和`AppDelegate`类中的移交提供内置支持。 虽然每个 OS 的实现方式略有不同, 但基本机制和 Api 是相同的。

#### <a name="user-activities-in-document-based-apps"></a>基于文档的应用中的用户活动

基于文档的 iOS 和 OS X 应用程序自动提供内置的移交支持。 若要激活此支持, 需要在应用的`NSUbiquitousDocumentUserActivityType` **info.plist**文件中为每个`CFBundleDocumentTypes`条目添加一个密钥和值。

如果此项存在, `NSDocument`则和`UIDocument`将自动为`NSUserActivity`指定的类型的基于 iCloud 的文档创建实例。 你将需要为应用支持的每种类型的文档提供一种活动类型, 并且多个文档类型可以使用相同的活动类型。 和`NSDocument` `UserInfo` `NSUserActivity`都用其`FileURL`属性的值自动填充的属性。 `UIDocument`

在 OS X 上, `NSUserActivity`在文档`AppKit`的窗口变成主窗口时, 由管理的和与响应方关联的将自动成为当前活动。 在 iOS 上, `NSUserActivity`对于由`UIKit`管理的对象, 你必须`BecomeCurrent`显式调用方法, 或者当应用`UserActivity`程序到达前台时`UIViewController` , 将文档的属性设置为。

`AppKit`将在 OS X `UserActivity`上自动还原以这种方式创建的任何属性。如果`ContinueUserActivity`方法返回`false`或未实现, 则会发生这种情况。 在这种情况下, 将使用`OpenDocument`的方法`NSDocumentController`打开文档, 然后`RestoreUserActivityState`它将接收方法调用。

有关详细信息, 请参阅下面的[基于文档的应用中的支持移交](#supporting-handoff-in-document-based-apps)部分。

#### <a name="user-activities-and-responders"></a>用户活动和响应程序

如果`UIKit`将`AppKit`用户活动设置为响应方对象的`UserActivity`属性, 则和都可以自动管理该活动。 如果已修改状态, 则需要将`NeedsSave` `UserActivity`响应方的属性设置为`true`。 系统将`UserActivity`在需要时通过调用其`UpdateUserActivityState`方法来更新状态, 从而在需要时自动保存。

如果多个响应程序共享`NSUserActivity`单个实例, 则当`UpdateUserActivityState`系统更新用户活动对象时, 会收到回调。 响应方在此时需要调用`AddUserInfoEntries`方法来`NSUserActivity`更新的`UserInfo`字典, 以反映当前的活动状态。 在每次`UpdateUserActivityState`调用前清除字典。`UserInfo`

若要取消自身与活动之间的关联, 响应方`UserActivity`可以将`null`其属性设置为。 当应用程序框架托管`NSUserActivity`实例没有更多关联的响应程序或文档时, 它会自动失效。

有关详细信息, 请参阅下面的[响应程序中的支持移交](#supporting-handoff-in-responders)部分。

#### <a name="user-activities-and-the-appdelegate"></a>用户活动和 AppDelegate

在处理移交`AppDelegate`延续时, 你的应用程序是其主要入口点。 当用户响应移交通知时, 将启动相应的应用 (如果尚未运行), 并`WillContinueUserActivityWithType` `AppDelegate`将调用的方法。 此时, 应用程序应通知用户延续任务正在启动。

调用`NSUserActivity` `AppDelegate`的方法`ContinueUserActivity`时, 将提供实例。 此时, 应配置应用的用户界面, 并继续执行给定的活动。

有关详细信息, 请参阅下面的[实现移交](#implementing-handoff)部分。

## <a name="enabling-handoff-in-a-xamarin-app"></a>在 Xamarin 应用中启用切换

由于移交施加了安全要求, 因此必须在 Apple 开发人员门户和 Xamarin iOS 项目文件中正确配置使用移交框架的 Xamarin iOS 应用。

请执行以下操作：

1. 登录到[Apple 开发人员门户](https://developer.apple.com)。
2. 单击 "**证书、标识符 & 配置文件**。
3. 如果尚未执行此操作, 请单击 "**标识符**", 为应用创建一个 ID (例如`com.company.appname`), 或者编辑现有 id。
4. 确保已针对给定 ID 检查**iCloud**服务:

    [![](handoff-images/provision01.png "为给定 ID 启用 iCloud 服务")](handoff-images/provision01.png#lightbox)
5. 保存更改。
6. 单击 "**预配配置文件** > **开发**", 为你的应用创建新的开发预配配置文件:

    [![](handoff-images/provision02.png "为应用程序创建新的开发预配配置文件")](handoff-images/provision02.png#lightbox)
7. 下载并安装新的预配配置文件, 或使用 Xcode 下载并安装该配置文件。
8. 编辑 Xamarin iOS 项目选项, 确保使用的是刚才创建的预配配置文件:

    [![](handoff-images/provision03.png "选择刚刚创建的预配配置文件")](handoff-images/provision03.png#lightbox)
9. 接下来, 请编辑**info.plist**文件, 并确保使用的是用于创建预配配置文件的应用 ID:

    [![](handoff-images/provision04.png "设置应用 ID")](handoff-images/provision04.png#lightbox)
10. 滚动到 "**背景模式**" 部分, 并检查以下各项:

    [![](handoff-images/provision05.png "启用所需的后台模式")](handoff-images/provision05.png#lightbox)
11. 保存对所有文件所做的更改。

设置好这些设置后, 应用程序便可访问移交框架 Api。 有关预配的详细信息, 请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)和[预配应用](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="implementing-handoff"></a>实现移交

用户活动可以在用同一开发人员团队 ID 进行签名并支持相同活动类型的应用之间继续。 在 Xamarin iOS 应用中实施移交要求你创建一个用户活动对象 (在或`UIKit` `AppKit`中)、更新该对象的状态以跟踪活动, 并在接收设备上继续活动。

### <a name="identifying-user-activities"></a>确定用户活动

实施移交的第一步是确定你的应用程序支持的用户活动类型, 并查看哪些活动适合在另一台设备上继续进行。 例如: ToDo 应用可能支持将项编辑为一个用户活动类型, 并支持将可用项列表作为另一个_用户活动类型_进行浏览。

应用程序可以根据需要创建任意数量的用户活动类型, 一个用于应用程序提供的任何函数。 对于每个用户活动类型, 应用需要跟踪类型的活动开始和结束的时间, 并需要维护最新的状态信息, 以在另一台设备上继续该任务。

用户活动可以在具有相同团队 ID 的任何应用上继续进行, 而在发送和接收应用之间不存在任何一对一映射。 例如, 给定的应用可以创建四种不同类型的活动, 这些活动由另一台设备上的不同应用程序使用。 这是应用程序的 Mac 版本 (可能具有很多特性和功能) 和 iOS 应用程序之间的常见情况, 其中每个应用程序都较小并专注于特定的任务。

### <a name="creating-activity-type-identifiers"></a>创建活动类型标识符

_活动类型标识符_是添加到`NSUserActivityTypes`应用的**info.plist**文件数组的短字符串, 用于唯一标识给定的用户活动类型。 对于应用程序支持的每个活动, 数组中将有一个条目。 Apple 建议对活动类型标识符使用反向 DNS 样式表示法, 以避免冲突。 例如: `com.company-name.appname.activity`对于基于应用的特定活动或`com.company-name.activity`可跨多个应用运行的活动。

创建`NSUserActivity`实例时, 将使用活动类型标识符来标识活动的类型。 在另一台设备上继续活动时, 活动类型 (以及应用的团队 ID) 确定要启动哪个应用以继续执行该活动。

例如, 我们将创建一个名为**MonkeyBrowser**的示例应用 (此处为 "[下载](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)")。 此应用将显示四个选项卡, 每个选项卡都在 web 浏览器视图中打开了不同的 URL。 用户可以继续运行该应用的不同 iOS 设备上的任何选项卡。

若要创建所需的活动类型标识符以支持此行为, 请编辑**info.plist**文件, 并切换到 "**源**" 视图。 `NSUserActivityTypes`添加密钥, 并创建以下标识符:

[![](handoff-images/type01.png "Info.plist 编辑器中的 NSUserActivityTypes 键和必需的标识符")](handoff-images/type01.png#lightbox)

我们创建了四个新的活动类型标识符, 一个用于示例**MonkeyBrowser**应用中的每个选项卡。 创建自己的应用时, 将`NSUserActivityTypes`数组的内容替换为特定于应用支持的活动的活动类型标识符。

### <a name="tracking-user-activity-changes"></a>跟踪用户活动更改

当我们创建`NSUserActivity`类的新实例时, 我们将指定一个`NSUserActivityDelegate`实例来跟踪对活动状态的更改。 例如, 以下代码可用于跟踪状态更改:

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

当`UserActivityReceivedData`延续流接收到发送设备中的数据时, 将调用方法。 有关详细信息, 请参阅下面的[支持继续流](#supporting-continuation-streams)部分。

当`UserActivityWasContinued`另一个设备已从当前设备中接管活动时, 将调用方法。 根据活动的类型 (如向 ToDo 列表中添加新项), 应用可能需要中止发送设备上的活动。

在对活动所做的任何更改保存并在本地可用设备上同步之前, 将调用方法。`UserActivityWillSave` 在发送`NSUserActivity`实例之前, 可以使用此方法对该实例的`UserInfo`属性进行最后一分钟的更改。

### <a name="creating-a-nsuseractivity-instance"></a>创建 NSUserActivity 实例

应用希望能够在另一台设备上继续提供的每个活动都必须封装在一个`NSUserActivity`实例中。 应用程序可以根据需要创建任意数量的活动, 这些活动的性质取决于相关应用程序的功能和功能。 例如, 电子邮件应用程序可以创建一个用于创建新邮件的活动, 另一个用于读取消息。

对于我们的示例应用, 每`NSUserActivity`次用户在选项卡式 web 浏览器视图中输入新的 URL 时, 都会创建一个新的。 以下代码存储给定选项卡的状态:

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

它使用上面创建`NSUserActivity`的用户活动类型之一创建新的, 并为活动提供可读的标题。 它会附加到上面创建的`NSUserActivityDelegate`实例, 以监视状态更改, 并通知 iOS 此用户活动是当前活动。

### <a name="populating-the-userinfo-dictionary"></a>填充用户信息字典

如上所述, `UserInfo` `NSUserActivity`类的属性是`NSDictionary`键/值对的, 用于定义给定活动的状态。 中`UserInfo`存储的值必须是以下类型之一`NSDate` `NSSet`: `NSArray`、 `NSData`、、 `NSDictionary`、 `NSNull`、 `NSNumber` `NSURL`、 、或。`NSString` `NSURL`指向 iCloud 文档的数据值将自动进行调整, 以使其指向接收设备上的相同文档。

在上面的示例中, 我们创建`NSMutableDictionary`了一个对象, 并在其中填充了一个提供用户当前在给定选项卡上查看的 URL 的键。用户活动的方法用于使用将用于在接收设备上还原活动的数据更新活动: `AddUserInfoEntries`

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple 建议将发送到使得的信息保持最小, 以确保将活动及时发送到接收设备。 如果需要更大的信息, 则需要发送与要编辑的文档相关联的图像, 你应该使用继续流。 有关更多详细信息, 请参阅下面的[支持继续流](#supporting-continuation-streams)部分。

### <a name="continuing-an-activity"></a>继续活动

移交会自动通知本地 iOS 和 OS X 设备, 这些设备在物理上接近于始发设备, 并登录到相同的 iCloud 帐户, 这是可持续用户活动的可用性。 如果用户选择在新设备上继续活动, 系统将启动相应的应用 (基于团队 ID 和活动类型) 以及需要继续执行`AppDelegate`该操作的信息。

首先, `WillContinueUserActivityWithType`调用方法, 以便应用程序可以通知用户延续任务即将开始。 我们使用示例应用程序的**AppDelegate.cs**文件中的以下代码来处理延续任务:

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

在上面的示例中, 每个视图控制器都`AppDelegate`向注册并具有`PreparingToHandoff`一个公共方法, 该方法显示活动指示器和一条消息, 告知用户要将活动移交给当前设备。 示例:

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

`ContinueUserActivity` 将调用以实际`AppDelegate`继续给定的活动。 同样, 从我们的示例应用程序:

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

每个`PerformHandoff`视图控制器的公共方法实际上完成了移交, 并还原了当前设备上的活动。 在此示例中, 它在给定的选项卡中显示与用户在其他设备上浏览的 URL 相同的 URL。 示例:

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

方法包括一个`UIApplicationRestorationHandler` , 您可以为基于文档或响应程序的活动恢复调用。 `ContinueUserActivity` 调用时, 需要将`NSArray`或可还原的对象传递给还原处理程序。 例如：

```csharp
completionHandler (new NSObject[]{Tab4});
```

对于传递的每个对象`RestoreUserActivityState` , 将调用其方法。 然后, 每个对象可以使用`UserInfo`字典中的数据来还原其自己的状态。 例如:

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

对于基于文档的应用`ContinueUserActivity` , 如果未实现方法或返回`false`, `UIKit` `AppKit`则可以自动恢复活动。 有关详细信息, 请参阅下面的[基于文档的应用中的支持移交](#supporting-handoff-in-document-based-apps)部分。

### <a name="failing-handoff-gracefully"></a>正常移交失败

由于移交依赖于收集松散连接的 iOS 和 OS X 设备之间的信息传输, 因此传输过程有时会失败。 应该将应用程序设计为适当地处理这些故障, 并通知用户发生的任何情况。

发生故障时, `DidFailToContinueUserActivitiy` `AppDelegate`将调用的方法。 例如：

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

应使用提供`NSError`的向用户提供有关失败的信息。

## <a name="native-app-to-web-browser-handoff"></a>本机应用到 Web 浏览器的切换

如果未在所需的设备上安装适当的本机应用, 用户可能需要继续执行某个活动。 在某些情况下, 基于 web 的界面可能会提供所需的功能, 并且活动仍可继续。 例如, 用户的电子邮件帐户可能提供用于撰写和阅读消息的 web 基本 UI。

如果原始的本机应用程序知道 web 接口的 URL (以及用于标识要继续的给定项的所需语法), 则它可以在`WebpageURL` `NSUserActivity`实例的属性中对此信息进行编码。 如果接收设备没有安装适当的本机应用来处理延续, 则可以调用提供的 web 界面。

## <a name="web-browser-to-native-app-handoff"></a>Web 浏览器到本机应用的切换

如果用户在始发设备上使用基于 web 的界面, 而接收设备上的本机应用声明`WebpageURL`属性的域部分, 则系统将使用该应用来处理延续任务。 新`NSUserActivity`设备将接收标记活动`BrowsingWeb`类型的实例, 并且`WebpageURL`将包含用户访问的 URL, `UserInfo`字典将为空。

对于要参与这种类型的提交的应用程序, 该应用程序必须使用格式`com.apple.developer.associated-domains` `<service>:<fully qualified domain name>` (例如: `activity continuation:company.com`) 声明具有权限的域。

如果指定的域与`WebpageURL`属性的值匹配, 则提交会从该域的网站下载已批准的应用 id 列表。 该网站必须在名为 " **apple-应用-站点-关联**" `https://company.com/apple-app-site-association`的已签名 JSON 文件中提供批准的 id 列表 (例如)。

此 JSON 文件包含一个字典, 该字典指定窗体`<team identifier>.<bundle identifier>`中的应用 id 列表。 例如：

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

若要对 JSON 文件 ( `Content-Type`使其具有正确的`application/pkcs7-mime`) 进行签名, 请使用**终端**应用和`openssl`命令, 其中包含由 iOS 信任的证书颁发机构颁发的证书和密钥 ( [https://support.apple.com/kb/ht5012](https://support.apple.com/kb/ht5012)有关列表, 请参阅)。 例如:

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

此`openssl`命令输出你放置在网站上的**apple-site 关联**URL 中的已签名 JSON 文件。 例如:

```csharp
https://example.com/apple-app-site-association.
```

应用将接收其`WebpageURL`域处于其`com.apple.developer.associated-domains`权限的任何活动。 `http`只有和`https`协议支持, 任何其他协议都将引发异常。

## <a name="supporting-handoff-in-document-based-apps"></a>支持基于文档的应用的移交

如上所述, 在 iOS 和 OS X 上, 如果应用的**info.plist**文件包含`CFBundleDocumentTypes`的`NSUbiquitousDocumentUserActivityType`密钥, 则基于文档的应用将自动支持基于 iCloud 的文档的移交。 例如:

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

在此示例中, 字符串是一个反向 DNS 应用指示符, 其中包含追加的活动的名称。 如果以这种方式输入, 则不需要在`NSUserActivityTypes` **info.plist**文件的数组中重复活动类型项。

自动创建的用户活动对象 (可通过文档的`UserActivity`属性提供) 可由应用中的其他对象引用, 并用于在延续时还原状态。 例如, 跟踪项选择和文档位置。 如果状态发生更改, 则`NeedsSave`需要将`true`此活动属性设置为, 并`UserInfo` `UpdateUserActivityState`更新方法中的字典。

可以`UserActivity`从任何线程使用该属性, 并符合键-值观察 (KVO) 协议, 因此它可用于在文档移入和移出 iCloud 时使其保持同步。 当`UserActivity`文档关闭时, 属性将无效。

有关详细信息, 请参阅 Apple[在基于文档的应用文档中的用户活动支持](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5)。

## <a name="supporting-handoff-in-responders"></a>支持响应中的提交

可以通过设置事件的`UIResponder` `UserActivity`属性, 将响应方 ( `NSResponder`从 iOS 或 OS X 上的继承) 关联到活动。 系统将在适当的`UserActivity`时间自动保存属性, 调用响应方的`UpdateUserActivityState`方法, 使用`AddUserInfoEntriesFromDictionary`方法将当前数据添加到用户活动对象。

## <a name="supporting-continuation-streams"></a>支持延续流

在某些情况下, 继续执行活动所需的信息量无法通过初始的移交负载高效传输。 在这些情况下, 接收应用可以在其自身与源应用之间建立一个或多个流以传输数据。

原始应用会将`SupportsContinuationStreams` `NSUserActivity`实例的属性设置为`true`。 例如：

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

然后, 接收应用可以在其`GetContinuationStreams` `AppDelegate`中调用的`NSUserActivity`方法来建立流。 例如:

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

在始发设备上, User 活动委托通过调用其`DidReceiveInputStream`方法来接收流, 以提供在恢复设备上继续用户活动所请求的数据。

你将使用`NSInputStream`来提供对流数据的只读访问权限`NSOutputStream`以及提供只写访问权限。 应在请求和响应方式中使用流, 其中接收应用请求更多数据, 而原始应用提供此流。 这样, 在源设备上写入输出流的数据将从继续设备上的输入流中读取, 反之亦然。

即使在需要延续流的情况下, 这两个应用程序之间的通信也应该降到最低。

有关详细信息, 请参阅[使用延续流](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13)的 Apple 文档。

## <a name="handoff-best-practices"></a>移交最佳实践

通过移交实现用户活动的无缝延续的成功实现需要认真设计, 因为涉及到所有的组件。 Apple 建议采用适用于启用了提交的应用的以下最佳做法:

- 设计你的用户活动, 以要求最小的有效负载将活动状态关联到继续。 有效负载越大, 延续开始所用的时间就越长。
- 如果必须传输大量数据才能成功继续, 请考虑配置和网络开销所涉及的成本。
- 大型 Mac 应用通常会创建由 iOS 设备上的多个更小的、特定于任务的应用处理的用户活动。 不同的应用程序和操作系统版本应设计为可以很好地协同工作或故障转移。
- 指定活动类型时, 请使用反向 DNS 表示法以避免冲突。 如果某个活动特定于给定应用, 则其名称应包括在类型定义中 (例如`com.myCompany.myEditor.editing`)。 如果活动可以跨多个应用, 请从定义中删除应用名称 (例如`com.myCompany.editing`)。
- 如果应用需要更新用户活动的状态 (`NSUserActivity`), 请`NeedsSave`将属性设置为`true`。 在适当的时间, 切换将调用委托的`UserActivityWillSave`方法, 以便可以根据需要`UserInfo`更新字典。
- 由于移交过程可能无法在接收设备上立即进行初始化, 因此你应该实现`AppDelegate`的`WillContinueUserActivity` , 并通知用户延续即将开始。

## <a name="example-handoff-app"></a>提交示例应用

作为在 Xamarin iOS 应用程序中使用移交的示例, 我们在本指南中包含了[**MonkeyBrowser**](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)示例应用。 应用具有四个选项卡, 用户可以使用这些选项卡浏览 web, 每个选项卡都具有给定的活动类型:天气、喜爱、咖啡和工作。

在任何选项卡上, 当用户输入新的 URL 并点击 "**开始**" 按钮时`NSUserActivity` , 将为该选项卡创建一个新的, 其中包含用户当前正在浏览的 url:

[![](handoff-images/handoff01.png "提交示例应用")](handoff-images/handoff01.png#lightbox)

如果用户设备中的另一个设备安装了**MonkeyBrowser**应用, 则使用相同的用户帐户登录到 iCloud, 位于同一网络上, 并接近于上述设备, 提交活动将显示在主屏幕上 (在下左角):

[![](handoff-images/handoff02.png "显示在左下角的主屏幕上的移交活动")](handoff-images/handoff02.png#lightbox)

如果用户向上拖动到 "切换" 图标上, 将启动该应用, 并且中`NSUserActivity`指定的用户活动将继续在新设备上:

[![](handoff-images/handoff03.png "新设备上的用户活动继续")](handoff-images/handoff03.png#lightbox)

如果已成功将用户活动发送到其他 Apple 设备, 则发送设备`NSUserActivity`将会在其`NSUserActivityDelegate`上收到对`UserActivityWasContinued`方法的调用, 以使其知道用户活动已成功传输到另一个装置.

## <a name="summary"></a>总结

本文提供了用于在用户的多个 Apple 设备之间继续执行用户活动的移交框架简介。 接下来, 它展示了如何在 Xamarin iOS 应用程序中启用和实施移交。 最后, 它讨论了不同类型的移交继续功能, 并提供了移交最佳实践。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [MonkeyBrowser 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-monkeybrowser)
- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit Framework 参考](https://developer.apple.com/library/ios/home_kit_framework_ref)
