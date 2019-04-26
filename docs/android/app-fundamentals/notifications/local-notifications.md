---
title: 本地通知
description: 本部分演示如何在 Xamarin.Android 中实现本地通知。 它介绍 Android 通知的各种 UI 元素，并讨论了 API 的涉及创建并显示一条通知。
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 362041efc5a19dfb70430054f3e4636d4fdfbd7e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021627"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>本地通知

_本部分演示如何在 Xamarin.Android 中实现本地通知。它介绍 Android 通知的各种 UI 元素，并讨论了 API 的涉及创建并显示一条通知。_

## <a name="local-notifications-overview"></a>本地通知概述

Android 提供用于向用户显示通知图标和通知信息的两个系统控制的区域。 在首次发布一个通知，其图标显示在*通知区域*，如以下屏幕截图中所示：

![在设备上的示例通知区域](local-notifications-images/01-notification-shade.png)

若要获取有关通知的详细信息，用户可以打开通知抽屉 （它可以扩展每个通知图标可显示通知内容），并执行与通知关联的任何操作。 以下屏幕截图显示*通知抽屉*，它对应于上面显示的通知区域：

[![示例通知抽屉显示三个通知](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Android 通知使用两种类型的布局：

-   ***基本布局*** &ndash; compact、 固定的显示格式。

-   ***展开的布局***&ndash;可以扩展到更大的大小以显示详细信息的显示格式。

以下各节介绍了每个布局类型 （以及如何创建它们）。

> [!NOTE]
> 本指南重点[NotificationCompat Api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html)从[Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 这些 Api 将确保最大值向后兼容到 Android 4.0 （API 级别 14）。


### <a name="base-layout"></a>基本布局

所有 Android 通知是基于基本布局格式，其中至少包括以下元素：

1.  一个*通知图标*，如果应用支持不同类型的通知表示发起应用程序或通知类型。

2.  通知*标题*，或如果通知是个人消息发件人的名称。

3.  通知消息。

4.  一个*时间戳*。

这些元素将显示如以下关系图中所示：

[![通知元素的位置](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

基本布局仅限于 64 密度无关的像素 (dp) 的高度。 Android 默认情况下创建此基本通知样式。

（可选） 通知可以显示大图标，表示应用程序或发件人的照片。 当通知 Android 5.0 及更高版本中使用大图标时，小的通知图标大图标上方显示的徽章：

![简单的通知照片](local-notifications-images/04-simple-notification-photo.png)

从 Android 5.0 开始，通知也会出现在锁定屏幕上：

[![示例锁定屏幕通知](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

用户可以双击锁定屏幕通知来解锁设备，并跳转到源自该通知的应用或轻扫以取消通知。 应用程序可以设置通知，以控制锁定屏幕上显示的内容的可见性级别和用户可以选择是否允许敏感内容要在锁定屏幕通知中显示。

Android 5.0 引入了名为以高优先级通知显示格式*平视*。 危险警告通知从屏幕顶部几秒钟向下滑动，然后备份到通知区域参加：

[![示例 heads-up 通知](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

危险警告通知使系统用户界面将呈现给用户的重要信息而不会中断当前正在运行的活动的状态。

Android 包括对通知的元数据的支持，因此可以进行排序和以智能方式显示通知。 通知的元数据还可以控制如何在锁定屏幕上和格式平视显示通知。 应用程序可以设置以下类型的通知的元数据：

-   **优先级**&ndash;优先级别确定如何以及何时显示通知。 例如，在 Android 5.0，高优先级的通知将显示为危险警告通知。

-   **可见性**&ndash;指定多少通知内容为在锁定屏幕上会显示通知时要显示。

-   **类别**&ndash;通知系统如何处理在各种情况下，例如设备时通知*请勿打扰*模式。

**注意：** **可见性** 并 **类别** 引入在 Android 5.0 和早期版本的 Android 中不可用。 从开始 Android 8.0[通知通道](#notif-chan)用于控制如何向用户显示通知。


### <a name="expanded-layouts"></a>扩展的布局

从 Android 4.1 开始，可以使用允许用户以展开该通知查看更多的内容的高度扩展的布局样式配置通知。 例如，下面的示例演示了在约定的模式下的展开的布局通知：

![收缩的通知](local-notifications-images/07-contracted-notification.png)

在展开此通知后，它将显示整个消息：

![扩展的通知](local-notifications-images/08-expanded-notification.png)

Android 支持单事件通知的三个扩展的布局样式：

-   ***大文本***&ndash;在约定的模式下，将显示一段摘录的两个句点后, 跟的消息的第一行。 在展开模式下，显示整个消息 （如在上面的示例所示）。

-   ***收件箱***&ndash;在约定的模式下，将显示新的消息数。 在展开模式显示的第一个电子邮件消息或收件箱中的消息列表。

-   ***图像***&ndash;在约定的模式下，仅显示消息文本。 在展开模式显示的文本和图像。

[基本通知超出](#beyond-the-basic-notification)（在本文后面介绍） 说明如何创建*大文本*，*收件箱*，和*映像*通知。

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>通知通道

从 Android 8.0 (Oreo) 开始，你可以使用*通知通道*功能来创建用于每种类型的通知，你想要显示的用户可自定义通道。 通知通道使您组通知，以便所有通知都发布到通道附录相同的行为。 例如，可能有适用于需要立即关注的通知的通知通道和单独的"更安静"通道用于信息性消息。

**YouTube**与 Android Oreo 一起安装的应用列出了两个通知类别：**下载通知**并**常规通知**:

[![YouTube Android Oreo 中的通知屏幕](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

每个类别对应于通知通道。 YouTube 应用实现**下载通知**通道和一个**常规通知**通道。 用户可以点击**下载通知**，这将显示设置屏幕的应用程序的下载通知通道：

[![下载 YouTube 应用的通知屏幕](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

在此屏幕中，用户可以修改的行为**下载**通知通道通过执行以下操作：

-   重要性级别设置为**紧急**，**高**，**中等**，或者**低**，配置级别的声音和视频的中断。

-   打开或关闭通知点。

-   打开或关闭指示灯闪烁。

-   显示或隐藏在锁定屏幕上的通知。

-   重写**请勿打扰**设置。

**常规通知**通道具有相似的设置：

[![YouTube 应用的常规通知屏幕](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

请注意，没有绝对控制通知通道如何与用户交互&ndash;用户可以修改任何设备上的通知通道的设置，如上面的屏幕截图中所示。 但是，可以配置默认值 （如将如下所述）。 如这些示例所示，新的通知通道功能使你能够向用户授予对不同类型的通知进行精细控制。


## <a name="notification-creation"></a>创建通知

若要在 Android 中创建一条通知，请使用[NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder)类派生[Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet 包。 此类使用可以创建并发布较旧版本的 Android 上的通知。 有关使用详细信息`NotificationCompat.Builder`，请参阅[兼容性](#compatibility)本主题中更高版本。

`NotificationCompat.Builder` 提供在通知中，如设置各种选项的方法：

-   内容，包括标题、 消息文本和通知图标。

-   样式的通知，如*大文本*，*收件箱*，或*图像*样式。

-   通知的优先级： 最小值、 低，默认情况下，高，或最大值。 在 Android 8.0 及更高版本，通过设置优先级[_通知通道_](#notification-channels)。

-   在锁定屏幕上通知的可见性： 公用、 专用或机密。

-   类别的元数据，可帮助 Android 进行分类和筛选通知。

-   可选意向，该值指示用于启动点击通知时的活动。

-   通知通道，将 (Android 8.0 及更高版本) 上发布通知的 ID。

在生成器中设置这些选项后，您将生成一个包含设置的通知对象。 若要发布通知，则此通知将对象传递给*通知管理器*。 Android 提供[NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)类，该类负责发布通知并向用户显示它们。 可以从任何上下文中，例如活动或服务获取对此类的引用。


### <a name="creating-a-notification-channel"></a>创建通知通道

在 Android 8.0 运行的应用程序必须创建其通知的通知通道。 通知通道需要以下三个信息片段：

* 仅适用于将标识信道的包 ID 字符串。
* 将向用户显示的通道的名称。  该名称必须是介于 1 和 40 之间的字符。
* 通道的重要性。

应用将需要检查的正在运行的 Android 版本。
运行版本早于 Android 8.0 设备不应创建通知通道。 下面的方法是如何创建通知通道在活动中的一个示例：

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

每次创建活动时，应创建通知通道。 有关`CreateNotificationChannel`方法，它应调用`OnCreate`活动的方法。

### <a name="creating-and-publishing-a-notification"></a>创建和发布通知

若要在 Android 中生成一条通知，请执行以下步骤：

1.  实例化`NotificationCompat.Builder`对象。

2.  在调用各种方法`NotificationCompat.Builder`对象，以设置通知选项。

3.  调用[构建](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/)方法的`NotificationCompat.Builder`对象实例化通知对象。

4.  调用[通知](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification))要发布通知的通知管理器的方法。

必须至少提供每个通知的以下信息：

-   小图标 (24x24 dp 的大小)

-   短标题

-   通知文本

下面的代码示例演示了如何使用`NotificationCompat.Builder`生成基本的通知。 请注意，`NotificationCompat.Builder`方法支持[方法链接](https://en.wikipedia.org/wiki/Method_chaining); 也就是说，每个方法返回生成器对象，因此可以使用的最后一次方法调用结果调用下一个方法调用：

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此示例中，一个新`NotificationCompat.Builder`对象调用`builder`实例化，以及要使用通知通道的 ID。 设置的标题和通知的文本，并从加载的通知图标**Resources/drawable/ic_notification.png**。 通知生成器对`Build`方法使用这些设置创建的通知对象。 下一步是调用`Notify`通知管理器的方法。 若要查找通知管理器，请调用`GetSystemService`，如上所示。

`Notify`方法接受两个参数： 通知标识符和通知对象。 通知标识符是一个唯一的整数，它标识为你的应用程序的通知。 在此示例中，通知标识符设置为零 (0);但是，在生产应用程序中，你将想要为每个通知提供唯一标识符。 重复使用以前的标识符值对的调用中`Notify`导致最后一个通知被覆盖。

当在 Android 5.0 设备上运行此代码时，它会生成一条通知，如以下示例所示：

![有关示例代码通知结果](local-notifications-images/09-hello-world.png)

通知图标显示通知左侧&ndash;此映像的带圆圈&ldquo;我&rdquo;具有 alpha 通道，以便 Android 可以绘制它后面灰色圆圈为背景。 此外可以提供一个图标，而无需 alpha 通道。 若要以图标形式显示照片的图像，请参阅[大型图标格式](#large-icon-format)本主题中更高版本。

时间戳设置自动进行，但可以替代此设置通过调用[集](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/)通知生成器方法。 例如，下面的代码示例将时间戳设置为当前时间：

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>启用声音和振动

如果想要在通知还播放声音，则可以调用通知生成器[SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/)方法并传入`NotificationDefaults.Sound`标志：

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

此调用`SetDefaults`将导致设备发布通知时播放声音。 如果你想要振动而不播放声音的设备，可以将传递`NotificationDefaults.Vibrate`到`SetDefaults.`如果你想要播放的声音和振动设备的设备，可以将传递到这两个标志`SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

如果您启用声音而无需指定要播放声音，Android 使用默认系统通知声音。 但是，你可以通过调用通知生成器，都将播放的声音[SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/)方法。 例如，播放警报声音通知 （而不是默认的通知声音），但你可以获取的 URI 警报中的声音[RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/)并将其传递给`SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

或者，您可以使用系统默认铃声声音通知：

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

创建通知对象后，就可以通知对象上设置通知属性 (而不是将其配置为预先通过`NotificationCompat.Builder`方法)。 例如，而不是调用`SetDefaults`方法，以使振动通知，可以直接修改该通知的位标志[默认](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/)属性：

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

此示例会导致设备振动时发布通知。

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>正在更新通知

如果你想要更新通知的内容，已发布后，可以重复使用现有`NotificationCompat.Builder`对象来创建新的通知对象，并发布此具有标识符一次通知的通知。 例如：

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

在此示例中，现有`NotificationCompat.Builder`对象用于创建新的通知对象具有不同的标题和消息。
使用标识符前一次通知，发布新的通知对象，这会更新之前发布通知的内容：

![更新后的通知](local-notifications-images/12-updated-notification.png)

重复使用前一次通知的正文&ndash;仅标题和通知更改时通知显示通知抽屉中的文本。 标题文本从"示例通知"更改为"更新通知"，并从"Hello World 消息文本更改 ！ 这是我第一次通知"！ 为"已更改为此邮件"。

通知保持可见，直到发生下列三个操作之一：

-   在用户关闭通知 (或点击*全部清除*)。

-   应用程序会调用`NotificationManager.Cancel`、 传入通知已发布时分配的唯一通知 ID。

-   应用程序调用`NotificationManager.CancelAll`。

有关更新 Android 通知的详细信息，请参阅[修改通知](https://developer.android.com/training/notify-user/managing.html#Updating)。


### <a name="starting-an-activity-from-a-notification"></a>从通知启动活动

在 Android 中，很常见的与之关联的通知*操作*&ndash;用户点击通知时启动的活动。 此活动可以位于另一个应用程序或甚至在另一个任务中。 若要将操作添加到一条通知，您创建[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)对象，并将关联`PendingIntent`与通知。 一个`PendingIntent`是一种特殊的允许接收方应用程序的发送应用程序的权限运行预定义的一段代码的意图。 当用户点击该通知时，Android 启动指定的活动`PendingIntent`。

以下代码片段演示如何创建一个通知，其中`PendingIntent`这将启动活动的发起应用程序， `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

此代码是非常类似于在前面部分中，不同之处在于通知代码`PendingIntent`将添加到通知对象。 在此示例中，`PendingIntent`是与原始应用的活动关联之前将它传递到通知生成器[SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/)方法。 `PendingIntentFlags.OneShot`标志传递给`PendingIntent.GetActivity`方法，以便`PendingIntent`只能使用一次。 此代码运行时，将显示以下通知：

![第一个操作通知](local-notifications-images/10-first-action-notification.png)

点击此通知将返回到原始活动用户。

在生产应用中，您的应用程序必须处理*back 堆栈*当用户按**回**通知活动中的按钮 （如果您不熟悉 Android 任务和 back 堆栈，请参阅[任务和后退堆栈](https://developer.android.com/guide/components/tasks-and-back-stack.html))。
在大多数情况下，在向后导航出通知活动应返回用户退出应用程序并返回到主屏幕。 若要管理 back 堆栈中，您的应用程序使用[TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/)类，以创建`PendingIntent`back 堆栈使用。

现实世界的另一个注意事项是，原始活动可能需要将数据发送到通知活动。 例如，通知可能表示文本消息已到达，并通知活动 （消息查看屏幕），需要向用户显示消息的消息的 ID。 创建的活动`PendingIntent`可以使用[Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/)方法将数据 （例如，字符串） 添加到意向，以便此数据将传递给通知活动。

下面的代码示例演示了如何使用`TaskStackBuilder`用于管理 back 堆栈中，和它包括如何将单个消息字符串发送到名为通知活动的一个示例`SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

在此代码示例中，应用程序包含两个活动： `MainActivity` （包含上面的通知代码） 和`SecondActivity`，用户点击该通知后将看到的屏幕。 当运行此代码时，会看到简单的通知 （类似于前面的示例）。 点击该通知，用户会`SecondActivity`屏幕：

![第二个活动屏幕截图](local-notifications-images/11-second-activity.png)

字符串消息 (传入的意图`PutExtra`方法) 中检索`SecondActivity`通过这行代码：

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

中会显示此检索到的消息，"Greetings 从 MainActivity ！、"`SecondActivity`屏幕上，如上面的屏幕截图中所示。 当用户按**回**按钮，同时在`SecondActivity`，导航会导致退出应用程序并返回到前面的应用程序的启动屏幕。

有关创建挂起意向的详细信息，请参阅[PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)。


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>除了基本通知

通知默认设置为简单的基本布局格式在 Android 中，但可以通过使用更多增强此基本格式`NotificationCompat.Builder`方法调用。 在本部分中，您将学习如何将大型照片图标添加到你的通知，并将看到有关如何创建扩展的布局通知的示例。

<a name="large-icon-format" />

### <a name="large-icon-format"></a>大图标格式

Android 通知通常显示发起应用程序的图标 （在左侧和右侧的通知）。 但是，通知可以显示图像或照片 (*大图标*) 而不是标准的小图标。 例如，消息传送应用程序可以显示照片的发件人，而不是应用程序图标。

下面是基本的 Android 5.0 通知的示例&ndash;显示只有的小型应用图标：

![示例一般的通知](local-notifications-images/13-sample-notification.png)

下面是修改后的通知显示大图标的屏幕截图&ndash;它使用 Xamarin 代码 monkey 的映像中创建的图标：

![示例大图标通知](local-notifications-images/14-large-icon-sample.png)

请注意，如果以大图标格式显示一条通知，小型应用程序图标将显示大图标右下角上的徽章。

若要为大图标在通知中使用的映像，请调用通知生成器[SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/)方法并传入的图像的位图。 与不同`SetSmallIcon`，`SetLargeIcon`只接受一个位图。 若要将转换位图的图像文件，请使用[BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/)类。 例如：

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

此示例代码随即会打开在映像文件**Resources/drawable/monkey_icon.png**，将其转换为位图，并将传递到生成的位图`NotificationCompat.Builder`。 通常情况下，源图像分辨率大于的小图标&ndash;但不是大得多。 太大的图像可能会导致不必要的调整大小操作可能会延迟通知的发布。


### <a name="big-text-style"></a>大文本样式

*大文本*样式是扩展的布局模板，用于在通知中显示长消息。 所有扩展的布局与通知相似，大文本通知中的初始显示 compact 显示格式：

![示例大文本通知](local-notifications-images/15-big-text-notification.png)

在这种格式，仅消息的一段摘录显示，两个句点来终止。 当用户向下拖动的通知时，它将展开以显示整个通知消息：

![扩展大文本通知](local-notifications-images/16-big-text-expanded.png)

此扩展的布局格式还包括摘要文本底部的通知。 最大高度*大文本*通知是 256 个分发点。

若要创建*大文本*通知，您实例化`NotificationCompat.Builder`对象，与之前一样，然后实例化并添加[BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/)对象传递给`NotificationCompat.Builder`对象。 下面是一个示例：

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

在此示例中，消息文本和摘要文本存储在`BigTextStyle`对象 (`textStyle`) 传递给之前 `NotificationCompat.Builder.`


### <a name="image-style"></a>图像样式

*图像*样式 (也称为*大局*样式) 是一种扩展的通知格式，可用于通知的正文中显示的图像。 例如，屏幕截图应用或照片应用程序可以使用*图像*捕获要向用户提供的最后一个通知样式图像的通知。 请注意，最大高度*图像*通知是 256 个 dp &ndash; Android 将调整图像以适合此最大高度限制，可用内存的限制范围内的大小。

例如所有扩展布局通知*图像*通知第一次显示紧凑的形式显示随附的消息文本的一段摘录：

![Compact 映像通知会显示任何图像](local-notifications-images/17-image-compact.png)

当用户拖动*图像*通知，它将展开以显示图像。 例如，下面是前一次通知的扩展的版本：

![展开的图像通知显示图像](local-notifications-images/18-image-expanded.png)

请注意，当通知显示在紧凑的格式，它将显示通知文本 (传递给通知生成器的文本`SetContentText`方法，如前面所示)。 但是，通知扩展以显示图像时，它将显示在图像上方的摘要文本。

若要创建*图像*通知，您实例化`NotificationCompat.Builder`对象与之前一样，然后创建并插入[BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/)对象插入`NotificationCompat.Builder`对象。 例如：

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

像`SetLargeIcon`方法`NotificationCompat.Builder`，则[BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/)方法`BigPictureStyle`需要你想要通知的正文中显示的图像的位图。 在此示例中， [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32))方法`BitmapFactory`读取图像文件位于**Resources/drawable/x_bldg.png**并将其转换成位图。

此外可以作为资源显示不会打包到的映像。 例如，下面的示例代码从本地的 SD 卡加载图像并将其显示*图像*通知：

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

在此示例中，图像文件位于 **/sdcard/Pictures/my-tshirt.jpg**是加载、 大小调整回其原始大小的一半，然后转换为在通知中使用的位图：

![示例通知中的 t 恤图像](local-notifications-images/19-tshirt-notification.png)

如果你在事先不知道图像文件的大小，则包装对调用一个好办法[BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/)中的异常处理程序&ndash;`OutOfMemoryError`如果图像太大，可能会引发异常若要调整大小的 android。

有关加载和解码大型位图图像的详细信息，请参阅[高效加载大位图](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently)。


### <a name="inbox-style"></a>收件箱样式

*收件箱*格式是一个扩展的布局模板，用于通知的正文中显示的文本 （例如电子邮件收件箱摘要） 单独的行。 *收件箱*格式通知第一次紧凑的形式显示：

![示例 compact 收件箱通知](local-notifications-images/20-inbox-compact.png)

当用户向下拖动的通知时，它将展开以显示下面的屏幕截图中所示的电子邮件摘要：

![示例扩展的收件箱通知](local-notifications-images/21-inbox-expanded.png)

若要创建*收件箱*通知，您实例化`NotificationCompat.Builder`对象，与之前一样，并添加[InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/)对象传递给`NotificationCompat.Builder`。 下面是一个示例：

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

若要将新的文本行添加到通知正文，请调用[Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/)方法`InboxStyle`对象 (的最大高度*收件箱*通知是 256 个分发点)。 请注意，与不同*大文本*样式*收件箱*样式支持通知正文中的各行文本。

此外可以使用*收件箱*样式扩展格式显示独立的文本行所需的任何通知。 例如，*收件箱*通知样式可用于合并多个挂起通知到篕璶硄 &ndash;可以更新单个*收件箱*样式与新的通知通知内容的行 (请参阅[更新通知](#updating-a-notification)上方)，而不是不是生成一个连续的新，主要是类似的通知流。


## <a name="configuring-metadata"></a>配置元数据

`NotificationCompat.Builder` 包含可调用以设置你的通知，例如优先级、 可见性和类别的元数据的方法。 Android 使用此信息&mdash;以及用户首选项设置&mdash;来确定如何以及何时可以显示通知。


### <a name="priority-settings"></a>优先级设置

运行在 Android 7.1 和更低版本的应用需要直接在通知本身上设置的优先级。 发布通知时，通知的优先级设置确定的两个结果：

-   其中会显示通知，相对于其他通知。
    例如，高优先级的通知时呈现上面较低优先级的通知在通知抽屉中，而不考虑每个通知已发布。

-   是否在 Heads-up 通知格式 (Android 5.0 及更高版本) 中显示通知。 仅*高*并*最大*优先级的通知显示为危险警告通知。

Xamarin.Android 定义以下枚举来设置通知优先级：

-   `NotificationPriority.Max` &ndash; 提醒用户的紧急或关键条件 （例如，传入呼叫、 打开的打开方向或紧急警报）。 在 Android 5.0 和更高版本设备，最高优先级别通知平视格式显示。

-   `NotificationPriority.High` &ndash; 将通知用户的重要事件 （如重要电子邮件或实时聊天消息抵达）。 在 Android 5.0 和更高版本设备，危险警告格式显示高优先级的通知。

-   `NotificationPriority.Default` &ndash; 通知用户具有的重要性中等级别的条件。

-   `NotificationPriority.Low` &ndash; 有关非紧急，用户必须是明智的 （例如，软件更新提醒或社交网络更新） 的信息。

-   `NotificationPriority.Min` &ndash; 用户通知时，才的背景信息查看通知 （例如，位置或天气信息）。

若要设置通知的优先级，请调用[SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/)方法的`NotificationCompat.Builder`对象，传入的优先级别。 例如：

```csharp
builder.SetPriority (NotificationPriority.High);
```

在下面的示例、 高优先级通知、"重要邮件 ！" 显示在通知抽屉的顶部：

![示例-优先级较高的通知](local-notifications-images/22-hi-priority-drawer.png)

由于这是优先级较高的通知，它还显示为 Android 5.0 中用户的当前活动屏幕上方的危险警告通知：

![示例危险警告通知](local-notifications-images/23-heads-up-example.png)

在下一步的示例中，低优先级"的那一天认为"通知显示在优先级较高的电池电量级别通知：

![示例低优先级通知](local-notifications-images/24-lo-priority-drawer.png)

因为"一天的思想"通知是一个低优先级的通知，Android 不会显示它 Heads-up 格式。

> [!NOTE]
> 在 Android 8.0 及更高版本，则通知通道和用户设置的优先级将确定通知的优先级。

### <a name="visibility-settings"></a>可见性设置

从开始 Android 5.0*可见性*设置才可用来控制安全锁定屏幕上显示的内容量通知。
Xamarin.Android 定义以下枚举来设置通知可见性：

-   `NotificationVisibility.Public` &ndash; 安全锁定屏幕上显示通知的完整内容。

-   `NotificationVisibility.Private` &ndash; 仅基本显示的信息 （例如通知图标和把它发布的应用的名称），在安全锁定屏幕上但通知的详细信息的其余部分隐藏。 所有通知都默认为`NotificationVisibility.Private`。

-   `NotificationVisibility.Secret` &ndash; 不会显示在安全锁定屏幕上，甚至不通知图标。 仅在用户解锁设备后才能提供通知内容。

若要设置通知，应用程序调用的可见性`SetVisibility`方法的`NotificationCompat.Builder`对象，传入的可见性设置。 例如，对此调用`SetVisibility`使通知`Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

当`Private`发布通知，安全锁定屏幕上显示的名称和应用程序的图标。 而不是通知消息，用户将看到"解锁你的设备以查看此通知":

![解锁设备的通知消息](local-notifications-images/25-lockscreen-private.png)

在此示例中， **NotificationsLab**发起应用程序的名称。 此版本经过修订的通知时才出现在锁定屏幕安全 （即，通过 PIN、 图案或密码保护）&ndash;锁定屏幕不安全，通知的完整内容是否可用在锁定屏幕上。


### <a name="category-settings"></a>类别设置

从 Android 5.0 开始，预定义的类别是可用于进行排名和筛选通知。 Xamarin.Android 提供了以下枚举这些类别：

-   `Notification.CategoryCall` &ndash; 传入的电话呼叫。

-   `Notification.CategoryMessage` &ndash; 传入的文本消息。

-   `Notification.CategoryAlarm` &ndash; 警报条件或计时器过期。

-   `Notification.CategoryEmail` &ndash; 传入的电子邮件消息。

-   `Notification.CategoryEvent` &ndash; 日历事件。

-   `Notification.CategoryPromo` &ndash; 促销邮件或广告。

-   `Notification.CategoryProgress` &ndash; 后台操作的进度。

-   `Notification.CategorySocial` &ndash; 社交网络的更新。

-   `Notification.CategoryError` &ndash; 失败的后台操作或身份验证进程。

-   `Notification.CategoryTransport` &ndash; 媒体播放更新。

-   `Notification.CategorySystem` &ndash; 保留供系统使用 （系统或设备状态）。

-   `Notification.CategoryService` &ndash; 指示后台服务正在运行。

-   `Notification.CategoryRecommendation` &ndash; 建议消息，与当前正在运行的应用。

-   `Notification.CategoryStatus` &ndash; 有关设备的信息。

当通知进行排序时，通知的优先级将优先于其类别设置。 例如，高优先级通知将显示为平视即使其所属`Promo`类别。 若要设置通知的类别，请调用`SetCategory`方法的`NotificationCompat.Builder`对象，传入的类别设置。 例如：

```csharp
builder.SetCategory (Notification.CategoryCall);
```

*请勿打扰*功能 （Android 5.0 中的新增功能） 筛选器基于类别的通知。 例如，*不会妨碍*屏幕中**设置**免除通知用户进行电话呼叫和消息：

![不会妨碍屏幕开关](local-notifications-images/26-do-not-disturb.png)

当用户配置*请勿打扰*若要阻止所有中断除了电话呼叫 （如上面的屏幕截图中所示），Android 允许的类别设置通知`Notification.CategoryCall`设备时显示处于*请勿打扰*模式。 请注意，`Notification.CategoryAlarm`中，永远不会被阻止通知*请勿打扰*模式。

[LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications)示例演示如何使用`NotificationCompat.Builder`以启动从通知第二个活动。 此代码示例所述[在 Xamarin.Android 中使用本地通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)演练。


### <a name="notification-styles"></a>通知样式

若要创建*大文本*，*映像*，或*收件箱*样式与通知`NotificationCompat.Builder`，您的应用程序必须使用这些样式的兼容性版本。 例如，若要使用*大文本*样式、 实例化`NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

同样，可以使用您的应用程序`NotificationCompat.InboxStyle`和`NotificationCompat.BigPictureStyle`有关*收件箱*和*图像*分别设计样式。


### <a name="notification-priority-and-category"></a>通知的优先级和类别

`NotificationCompat.Builder` 支持`SetPriority`方法 （可从 Android 4.1）。 但是，`SetCategory`方法是*不*受`NotificationCompat.Builder`由于类别是 Android 5.0 中引入了新通知的元数据系统的一部分。

若要支持较旧版本的 Android、 where`SetCategory`是不可用，你的代码可以检查在运行时有条件地调用的 API 级别`SetCategory`API 级别时为等于或大于 Android 5.0 （API 级别 21）：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

在此示例中，应用程序的**目标框架**设置为 Android 5.0 和**最低 Android 版本**设置为**Android 4.1 (API 级别为 16)**。 因为`SetCategory`是在 API 级别 21 和更高版本中可用，此示例代码将调用`SetCategory`仅当有可用&ndash;不会调用`SetCategory`API 级别时是小于 21。


### <a name="lock-screen-visibility"></a>锁定屏幕可见性

因为 Android 不支持 Android 5.0 （API 级别 21） 之前, 的锁定屏幕通知`NotificationCompat.Builder`不支持`SetVisibility`方法。 如上文所述的`SetCategory`，你的代码可以检查在运行时和调用的 API 级别`SetVisiblity`它时才可用：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>总结

本文介绍了如何在 Android 中创建本地通知。 所述的通知剖析，它介绍了如何使用`NotificationCompat.Builder`若要创建通知，如何在大图标样式通知*大文本*，*图像*和*收件箱*格式、 如何设置通知的元数据设置，如优先级、 可见性和类别，以及如何启动通知中的活动。 本文还介绍了这些通知设置如何使用新的危险警告，锁定屏幕和*请勿打扰*Android 5.0 中引入的功能。 最后，您学习了如何使用`NotificationCompat.Builder`以保持与早期版本的 Android 通知兼容。

设计适用于 Android 的通知的指南，请参阅[通知](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)。


## <a name="related-links"></a>相关链接

- [NotificationsLab （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications （示例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [在 Android 演练中的本地通知](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [通知用户](https://developer.android.com/training/notify-user/index.html)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
