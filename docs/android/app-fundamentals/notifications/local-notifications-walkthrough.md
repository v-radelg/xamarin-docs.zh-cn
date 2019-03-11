---
title: 演练-在 Xamarin.Android 中使用本地通知
description: 本演练演示如何在 Xamarin.Android 应用程序中使用本地通知。 它演示了创建和发布本地通知的基础知识。 当用户单击通知区域中的通知时，在启动第二个活动。
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: e60ed6cc49921fc7b6e8e2616a6b0bf6f8abb401
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670085"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>演练-在 Xamarin.Android 中使用本地通知

_本演练演示如何在 Xamarin.Android 应用程序中使用本地通知。它演示了创建和发布本地通知的基础知识。当用户单击通知区域中的通知时，在启动第二个活动。_


## <a name="overview"></a>概述

在本演练中，我们将创建 Android 应用程序，当用户单击按钮在活动中的引发的通知。 当用户单击通知时，它将启动的第二个活动，显示用户已单击的第一个活动中的按钮的次数。

下面的屏幕截图演示了此应用程序的一些示例：

[![通知的示例屏幕快照](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> 本指南重点[NotificationCompat Api](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html)从[Android 支持库](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 这些 Api 将确保最大值向后兼容到 Android 4.0 （API 级别 14）。

## <a name="creating-the-project"></a>创建项目

若要开始，让我们使用以下工具创建新的 Android 项目**Android 应用**模板。 让我们称此项目**LocalNotifications**。 (如果不熟悉创建 Xamarin.Android 项目，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)。)

编辑资源文件**values/Strings.xml** ，使其包含两个额外的字符串资源时就可以创建通知通道时使用：


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>添加 Android.Support.V4 NuGet 包

在本演练中，我们将使用`NotificationCompat.Builder`来构建我们本地通知。 中所述[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)，我们必须包括[Android 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)我们的项目中的 NuGet，若要使用`NotificationCompat.Builder`。

接下来，让我们编辑**MainActivity.cs**并添加以下`using`语句，以便在类型`Android.Support.V4.App`可供我们的代码：

```csharp
using Android.Support.V4.App;
```

此外，我们必须使我们将使用编译器清楚地`Android.Support.V4.App`新版`TaskStackBuilder`而不是`Android.App`版本。 以下代码添加到`using`语句，若要解决不明确性：

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>创建通知通道

接下来，将方法添加到`MainActivity`，将创建通知通道 （如有必要）：

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

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

更新`OnCreate`方法来调用这种新方法：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>定义通知 ID

我们将需要我们的通知和通知通道的唯一 ID。 让我们编辑**MainActivity.cs**并添加以下静态实例变量`MainActivity`类：

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>添加代码以生成通知

接下来，我们需要创建一个新的事件处理程序，为按钮`Click`事件。 添加以下方法`MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

`OnCreate` MainActivity 方法必须进行调用以创建通知通道并分配`ButtonOnClick`方法`Click`按钮 （替换该模板提供的委托事件处理程序） 的事件：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>创建第二个活动

现在，我们需要创建 Android 将显示在用户单击通知时的另一个活动。 将另一个 Android 活动添加到你的项目称为**SecondActivity**。 打开**SecondActivity.cs**并将其内容替换此代码：

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

我们还必须创建适用于的资源布局**SecondActivity**。 添加一个新**Android 布局**到名为你的项目文件**Second.axml**。 编辑**Second.axml**并粘贴以下布局代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>添加通知图标

最后，添加启动通知时将出现在通知区域中的小图标。 可以将复制[此图标](local-notifications-walkthrough-images/ic-stat-button-click.png)到你的项目或创建你自己的自定义图标。 图标文件命名**ic\_stat\_按钮\_click.png**并将其复制到**资源/drawable**文件夹。 请记住使用**添加 > 现有项...** 此图标文件包含在项目中。


### <a name="run-the-application"></a>运行此应用程序

生成并运行应用程序。 您应看到类似于以下屏幕截图的第一个活动：

[![第一个活动屏幕截图](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

单击按钮时，您会注意到该通知的小图标显示在通知区域中：

[![会显示通知图标](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

如果向下轻扫并公开通知抽屉，你应该会看到通知：

[![通知消息](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

如果您单击该通知，它应会消失，并且我们其他的活动应启动&ndash;看起来有点像下面的屏幕截图：

[![第二个活动屏幕截图](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

祝贺你！ 现在您已经完成了 Android 本地通知演练，您必须可以引用的工作示例。 有很多通知的详细信息比我们已经演示了在这里，因此如果要了解详细信息，看一看[通知上的 Google 文档](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)。


## <a name="summary"></a>总结

使用本演练`NotificationCompat.Builder`来创建和显示通知。 它介绍了如何启动作为一种方法来响应用户交互并发出通知，第二个活动的基本示例，它从第一个活动中演示的数据传输到第二个活动。


## <a name="related-links"></a>相关链接

- [LocalNotifications （示例）](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Android Oreo 通知通道](https://blog.xamarin.com/android-oreo-notification-channels/)
- [通知](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
