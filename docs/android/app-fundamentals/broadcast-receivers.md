---
title: Xamarin 中的广播接收器
description: 本部分讨论如何使用广播接收器。
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 9f490bec121481d9f3f661913d8aefcef999bb4a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016973"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Xamarin 中的广播接收器

_本部分讨论如何使用广播接收器。_

## <a name="broadcast-receiver-overview"></a>广播接收方概述

_广播接收器_是一种 android 组件，它允许应用程序响应由 android 操作系统或应用程序广播的消息（android [`Intent`](xref:Android.Content.Intent)）。 广播按照_发布-订阅_模式 &ndash; 事件会导致广播发布并由对事件感兴趣的那些组件接收。

Android 标识广播的两种类型：

- **显式广播**&ndash; 这些类型的广播面向特定的应用程序。 显式广播的最常见用途是启动活动。 在应用需要拨打电话号码时的显式广播示例它会分派一个目标，该意向面向 Android 上的手机应用，并沿要拨打的电话号码传递。 Android 随后会将意向路由到 Phone 应用。
- **隐式广播**&ndash; 将这些广播发送到设备上的所有应用。 隐式广播的一个示例是 `ACTION_POWER_CONNECTED` 意向。 每次 Android 检测到设备上的电池正在充电时，都会发布此意向。 Android 会将此目的路由到已为此事件注册的所有应用。

广播接收器是 `BroadcastReceiver` 类型的子类，必须重写[`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*)方法。 Android 将在主线程上执行 `OnReceive`，因此应将此方法设计为快速执行。 在 `OnReceive` 中生成线程时应格外小心，因为在方法完成时，Android 可能会终止进程。 如果广播接收方必须执行长时间运行的工作，则建议使用 `JobScheduler` 或_Firebase 作业调度_程序来计划_作业_。 使用作业计划工作将在单独的指南中讨论。

使用_意向筛选器_注册广播接收器，以便 Android 可以正确路由消息。 可以在运行时指定意向筛选器（这有时称为_上下文注册的接收方_或_动态注册_），也可以在 Android 清单中静态定义（由_清单注册的接收方_）。 Xamarin 提供了一个C#用于静态注册意向筛选器的属性`IntentFilterAttribute`（本指南稍后将对此进行更详细的讨论）。 从 Android 8.0 开始，应用程序无法静态注册隐式广播。

清单注册的接收方和上下文注册的接收方之间的主要区别在于，上下文注册的接收方将仅在应用程序运行时响应广播，而清单注册的接收方可以响应即使应用程序未运行也进行广播。  

有两组 Api 用于管理广播接收方和发送广播：

1. **`Context`** &ndash; `Android.Content.Context` 类可用于注册将响应系统范围事件的广播接收器。 `Context` 还用于发布系统范围的广播。
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; 这是通过[Xamarin 支持库 v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供的 API。 此类用于在使用的应用程序的上下文中阻止广播和广播接收器的隔离。 此类可用于防止其他应用程序响应仅限应用程序的广播或向专用接收方发送消息。

广播接收方可能不会显示对话框，因此强烈建议从广播接收方内启动某个活动。 如果广播接收方必须通知用户，则它应发布通知。

不能从广播接收器内绑定到或启动服务。 

本指南将介绍如何创建广播接收器，以及如何注册它，以便它可以接收广播。

## <a name="creating-a-broadcast-receiver"></a>创建广播接收器

若要在 Xamarin Android 中创建广播接收器，应用程序应将 `BroadcastReceiver` 类的子类，使用 `BroadcastReceiverAttribute`对其进行修饰，并重写 `OnReceive` 方法：

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

当 Xamarin 编译类时，它还将用必要的元数据来更新 Androidmanifest.xml 以注册接收方。 对于静态注册的广播接收器，`Enabled` 必须设置为 `true`，否则 Android 将无法创建接收方的实例。

`Exported` 属性控制广播接收器是否可以从应用程序外部接收消息。 如果未显式设置属性，则默认情况下，属性的默认值由 Android 确定，前提是存在与广播接收器关联的任何意向筛选器。 如果广播接收方至少有一个意向筛选器，则 Android 将假定 `Exported` 属性为 `true`。 如果没有与广播接收方关联的意向筛选器，则 Android 会假设值为 `false`。 

`OnReceive` 方法接收对已调度到广播接收器的 `Intent` 的引用。 这使得发送方可以将值传递给广播接收器。

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>使用意向筛选器静态注册广播接收器

使用[`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute)修饰 `BroadcastReceiver` 时，Xamarin 会在编译时将必要的 `<intent-filter>` 元素添加到 Android 清单中。 以下代码片段是在设备完成启动时运行的广播接收器（如果用户授予了相应的 Android 权限）的示例：

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

还可以创建一个用于响应自定义意向的意图筛选器。 请看下面的示例： 

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

面向 Android 8.0 （API 级别26）或更高版本的应用程序可能无法静态注册隐式广播。 应用可能仍静态注册以进行显式广播。 此限制中有一小部分隐式广播列表。 Android 文档的[隐式广播例外](https://developer.android.com/guide/components/broadcast-exceptions.html)指南中介绍了这些例外。 对隐式广播感兴趣的应用必须使用 `RegisterReceiver` 方法动态地实现此目的。 下面对此进行了介绍。

### <a name="context-registering-a-broadcast-receiver"></a>上下文-注册广播接收器

接收方的上下文注册（也称为动态注册）是通过调用 `RegisterReceiver` 方法执行的，并且必须通过调用 `UnregisterReceiver` 方法取消注册广播接收器。 若要防止资源泄漏，请在不再与上下文（活动或服务）相关的情况下注销接收方，这一点很重要。 例如，服务可能会广播意向，通知活动可向用户显示更新。 活动启动时，它会注册这些意向。 当活动移动到后台并且对用户不再可见时，它应取消注册接收方，因为用于显示更新的 UI 不再可见。 以下代码片段举例说明了如何在活动的上下文中注册和取消注册广播接收器：

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver()

        // Code omitted for clarity
    }

    protected override OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

在上面的示例中，当活动进入前台时，它将注册一个广播接收器，该接收器将通过使用 `OnResume` 生命周期方法来侦听自定义意向。 当活动移动到后台时，`OnPause()` 方法将取消注册接收方。

## <a name="publishing-a-broadcast"></a>发布广播

可以将广播发布到设备上安装的所有应用，创建意向对象并使用 `SendBroadcast` 或 `SendOrderedBroadcast` 方法对其进行调度。  

1. **SendBroadcast 方法**&ndash; 有多个此方法的实现。
   这些方法会将意图广播到整个系统。 将按不确定顺序接收意向的广播接收方。 这提供了很大的灵活性，但意味着其他应用程序可以注册和接收此目的。 这可能会造成潜在的安全风险。 应用程序可能需要实现额外的安全性，以防止未经授权的访问。 一种可能的解决方案是使用仅在应用的专用空间内调度消息的 `LocalBroadcastManager`。 此代码片段举例说明了如何使用 `SendBroadcast` 方法之一分派意向：

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    此代码段是使用 `Intent.SetAction` 方法发送广播的另一个示例，用于标识操作：

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **SendOrderedBroadcast** &ndash; 这种方法非常类似于 `Context.SendBroadcast`，差别在于，这一目的会按接收方的顺序发布到接收方。

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[Xamarin 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供名为[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html)的帮助器类。 `LocalBroadcastManager` 适用于不希望在设备上发送或接收来自其他应用的广播的应用程序。 `LocalBroadcastManager` 将仅在应用程序的上下文中发布消息，并仅在向 `LocalBroadcastManager`注册的广播接收器中发布消息。 此代码片段是使用 `LocalBroadcastManager`注册广播接收器的示例：

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

设备上的其他应用无法接收随 `LocalBroadcastManager`一起发布的消息。 此代码段演示如何使用 `LocalBroadcastManager`分派意向：

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>相关链接

- [BroadcastReceiver API](xref:Android.Content.BroadcastReceiver)
- [RegisterReceiver API](xref:Android.Content.Context.RegisterReceiver*)
- [SendBroadcast API](xref:Android.Content.Context.SendBroadcast*)
- [UnregisterReceiver API](xref:Android.Content.Context.UnregisterReceiver*)
- [意向 API](xref:Android.Content.Intent)
- [IntentFilter API](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager （Android 文档）](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Android 中的本地通知](~/android/app-fundamentals/notifications/local-notifications.md)指南
- [Android 支持库 v4 （NuGet）](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
