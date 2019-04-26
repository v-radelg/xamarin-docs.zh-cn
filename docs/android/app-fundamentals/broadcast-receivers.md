---
title: 在 Xamarin.Android 中的广播的接收器
description: 本部分讨论如何使用广播接收器。
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: a411d4d85877c9868ec49f92b53ca8d7a81f9959
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61018639"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>在 Xamarin.Android 中的广播的接收器

_本部分讨论如何使用广播接收器。_

## <a name="broadcast-receiver-overview"></a>广播的接收器概述

一个_广播的接收器_是允许对消息作出响应的应用程序的 Android 组件 (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) 的广播 Android 操作系统或应用程序。 请按照广播_发布-订阅_模型&ndash;导致广播发布和接收感兴趣事件的那些组件的事件。

Android 标识两种类型的广播：

* **显式广播**&ndash;广播这些类型面向特定的应用程序。 显式广播的最常见用途是启动活动。 举例说明了当应用需要拨打的电话号码; 显式广播它会调度意向面向 Android 和电话号码沿传递拨打的手机应用。 Android 然后将路由到 Phone 应用程序的意图。
* **隐式广播**&ndash;这些广播调度到设备上的所有应用。 隐式广播的一个示例是`ACTION_POWER_CONNECTED`意向。 Android 检测到设备上的电池正在充电每次发布此目的。 Android 将路由到已注册此事件的所有应用此目的。

广播的接收器是一个的子类`BroadcastReceiver`类型和它必须重写[ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/)方法。 Android 将执行`OnReceive`上主线程，因此此方法应设计为可执行速度快。 在生成中的线程时应小心`OnReceive`因为 Android 可能会终止进程，在方法结束时。 如果广播的接收器必须执行长时间运行的工作，则我们建议安排_作业_使用`JobScheduler`或_Firebase 作业调度程序_。 计划作业的工作将在单独的指南中讨论。

_意向筛选器_用于注册，以便 Android 能够正确地将消息广播的接收器。 意向筛选器可以指定在运行时 (这有时称为_上下文注册接收方_或是_动态注册_)，或在 Android 清单 (可以以静态方式定义_清单注册接收方_)。 Xamarin.Android 提供了C#属性， `IntentFilterAttribute`，，将以静态方式注册意向筛选器 （这将在本指南后面的更详细地讨论）。 从 Android 8.0，它不能进行隐式广播静态注册的应用程序。

清单注册接收方和上下文注册接收方之间的主要区别是到将仅针对广播响应上下文注册接收方运行时应用程序，而清单注册的接收方可以响应即使可能未在运行该应用将广播。  

有两组 Api 用于管理广播接收方和发送广播：

1. **`Context`** &ndash; `Android.Content.Context`类可用于注册广播的接收器将响应的系统范围内的事件。 `Context`还用于发布系统范围内广播。
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; 这是一个 API，可通过[Xamarin 支持库 v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 此类用于保留广播和在使用它们的应用程序的上下文中隔离的广播的接收器。 此类可用于阻止其他应用程序响应仅应用程序广播或将消息发送到专用的接收方。

广播的接收器可能无法显示对话框，并且强烈建议不要使用，以启动从广播接收器中的某个活动。 如果广播的接收器必须通知用户，它应发布通知。

不能将绑定到或开始从广播接收器中的服务。 

本指南将介绍如何创建广播的接收器以及如何进行注册，以便它可能会收到广播。

## <a name="creating-a-broadcast-receiver"></a>创建广播的接收器

若要创建广播的接收器在 Xamarin.Android 中，应用程序应子类`BroadcastReceiver`类中，与它修饰`BroadcastReceiverAttribute`，并替代`OnReceive`方法：

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

当 Xamarin.Android 编译类时，它还将使用必要的元数据注册接收器更新 AndroidManifest。 静态注册广播接收器中，对于`Enabled`正确必须设置为`true`，否则 Android 将无法再创建接收方的实例。
 
`Exported`属性控制广播的接收器是否可以接收来自外部应用程序消息。 如果未显式设置属性，如果有与广播接收器关联的意向筛选器基于的 android 确定属性的默认值。 如果至少一个意向筛选器的广播接收方则 Android 将假定`Exported`属性是`true`。 如果没有与广播接收器关联的意向筛选器，则 Android 将认为此值越`false`。 

`OnReceive`方法接收到的引用`Intent`，被调度到广播接收器。 这样就可以发送方的意图以将值传递到广播接收器。

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>广播接收器以静态方式注册意向筛选器

当`BroadcastReceiver`用修饰[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)，Xamarin.Android 将添加必需`<intent-filter>`元素 android 清单在编译时。 以下代码片段是广播接收器将时设备完成启动 （如果用户授予了适当的 Android 权限） 运行的示例：

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

还有可能创建意向筛选器将响应的自定义的目的。 请看下面的示例： 

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

面向 Android 8.0 （API 级别 26） 的应用，或更高版本不能以静态方式注册隐式广播。 显式广播可能仍以静态方式注册应用。 没有此限制不受约束的隐式广播的简短列表。 这些异常中所述[隐式广播异常](https://developer.android.com/guide/components/broadcast-exceptions.html)Android 文档中的指南。 对于有兴趣隐式广播的应用程序必须这样做因此动态使用`RegisterReceiver`方法。 这在下一部分介绍。

### <a name="context-registering-a-broadcast-receiver"></a>上下文注册广播的接收器

通过调用执行上下文 （也称为动态注册） 的注册为接收方`RegisterReceiver`方法和广播的接收器必须通过调用注销`UnregisterReceiver`方法。 若要防止泄漏资源，务必之后取消注册接收方已不再相关的上下文 （活动或服务）。 例如，服务可能将广播通知有可用来向用户显示更新的活动的意图。 当在活动开始时，它将注册为这些意图。 当活动移动到后台，并且不再对用户可见，它应注销接收方，因为用于显示更新的 UI 不再可见。 下面的代码段是活动的如何注册和注销广播的接收器上下文中的示例：

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

在上一示例中，当活动进入前台，它将注册将侦听的自定义意向使用广播的接收器`OnResume`生命周期方法。 因为该活动将移到后台，`OnPause()`方法将取消注册接收方。

## <a name="publishing-a-broadcast"></a>发布广播

广播可能会发布到创建的意向对象和调度其与在设备上安装的所有应用`SendBroadcast`或`SendOrderedBroadcast`方法。  

1. **Context.SendBroadcast 方法**&ndash;有几个此方法的实现。
   这些方法将广播到整个系统的目的。 将收到以不确定的顺序意向的广播的接收器。 这提供了大量的灵活性，但意味着其他应用程序可以注册并接收意图。 这可能会造成潜在的安全风险。 应用程序可能需要实现添加安全性以防止未经授权的访问。 一个可能的解决方案是使用`LocalBroadcastManager`这会仅分发应用程序的专用空间中的消息。 此代码片段是举例说明如何分派使用其中一个意向`SendBroadcast`方法：

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    此代码段是另一个示例通过使用发送广播`Intent.SetAction`的方法来确定该操作：

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash;这是方法非常类似于`Context.SendBroadcast`，区别在于目的将已发布的一个时，接收方是否注册了接收方的顺序。

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[Xamarin 支持库 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供了一个名为的帮助器类[ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html)。 `LocalBroadcastManager`适用于不希望发送或接收广播从其他应用在设备上的应用。 `LocalBroadcastManager`时才会发布消息上下文内的应用程序，并且仅向注册到这些广播接收器`LocalBroadcastManager`。 此代码片段是注册广播的接收器使用的示例`LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

在设备上的其他应用程序不能接收通过发布消息`LocalBroadcastManager`。 此代码片段演示如何调度意向使用`LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>相关链接

- [BroadcastReceiver API](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast API](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [意向 API](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter API](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager (Android docs)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [在 Android 中的本地通知](~/android/app-fundamentals/notifications/local-notifications.md)指南
- [Android 支持库 v4 (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
