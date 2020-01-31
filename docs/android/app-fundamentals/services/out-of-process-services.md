---
title: 在远程进程中运行 Android 服务
description: 通常，Android 应用程序中的所有组件都将在同一进程中运行。 Android 服务非常值得注意，因为可以将其配置为在自己的进程中运行，并与其他 Android 开发人员共享。 本指南将介绍如何使用 Xamarin 创建和使用 Android 远程服务。
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: f546a1403aa0af07fc69187c4cfbec8982ed7a2a
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556504"
---
# <a name="running-android-services-in-remote-processes"></a>在远程进程中运行 Android 服务

_通常，Android 应用程序中的所有组件都将在同一进程中运行。Android 服务非常值得注意，因为可以将其配置为在自己的进程中运行，并与其他 Android 开发人员共享。本指南将介绍如何使用 Xamarin 创建和使用 Android 远程服务。_

## <a name="out-of-process-services-overview"></a>进程外服务概述

当应用程序启动时，Android 会创建要在其中运行应用程序的进程。 通常，应用程序将在此过程中运行的所有组件。 Android 服务非常值得注意，因为可以将其配置为在自己的进程中运行，并与其他 Android 开发人员共享。 这些类型的服务嘿 _远程服务_或_进程外服务_。 这些服务的代码将包含在与主应用程序相同的 APK 中;但是，当服务启动时，Android 将只为该服务创建一个新的进程。 相反，在与应用程序的其余部分相同的进程中运行的服务有时称为 "_本地服务_"。

通常，应用程序无需实现远程服务。 在许多情况下，本地服务足以（并且需要）满足应用要求。 一个进程外的内存空间必须由 Android 管理。 尽管这会对整个应用程序带来更多的开销，但在某些情况下，在其自身的进程中运行服务可能会有好处：

1. **共享功能**&ndash; 一些应用程序开发人员可能会有多个应用程序和功能在所有应用程序之间共享。 将运行在其自身进程中的 Android 服务中的该功能打包可简化应用程序维护。 还可以将该服务打包到其自己的独立 APK 中，并将其与应用程序的其余部分分开部署。
2. **提高用户体验**&ndash; 有两种可能的方法：进程外服务可以改进应用程序的用户体验。 第一种处理内存管理的方式。 发生垃圾回收（GC）循环时，Android 将暂停进程中的所有活动，直到 GC 完成。 用户可能会认为此暂停为 "断断续续" 或 "jank"。 当服务在其自己的进程中运行时，它是暂停的服务进程，而不是应用程序进程。 当应用程序进程（因此，用户界面）未暂停时，这种暂停对用户的感觉将不太明显。

    其次，如果进程的内存要求过大，则 Android 可能会终止该进程以释放设备的资源。 如果某个服务的内存占用量较大，并且它在与 UI 相同的进程中运行，则当 Android 强制回收这些资源时，UI 将关闭，并强制用户启动该应用程序。 但是，如果服务在其自己的进程中运行时由 Android 关闭，则 UI 进程将不受影响。 UI 可以绑定（并重新启动）该服务，对用户透明，然后恢复正常运行。

3. 提高 UI 进程 &ndash; 的**应用程序性能**可能与服务进程无关或关闭。 通过将较长的启动任务移到进程外服务，可能会提高 UI 的启动时间（假定服务进程在启动 UI 的时间之间保持活动状态）。

在许多方面，绑定到在另一个进程中运行的服务与[绑定到本地服务](~/android/app-fundamentals/services/creating-a-service/bound-services.md)的操作相同。 客户端将调用 `BindService` 来绑定（并在必要时）服务。 将创建一个 `Android.OS.IServiceConnection` 对象用于管理客户端和服务之间的连接。 如果客户端成功绑定到服务，则 Android 将通过可用于对服务调用方法的 `IServiceConnection` 返回对象。 然后，客户端使用此对象与服务进行交互。 若要查看，请执行以下步骤以绑定到服务：

- **创建意向**&ndash; 必须使用显式目的才能绑定到服务。
- **实现并实例化 `IServiceConnection` 对象**&ndash; `IServiceConnection` 对象充当客户端和服务之间的中介。  它负责监视客户端与服务器之间的连接。
- **调用 `BindService` 方法**&ndash; 调用 `BindService` 会将在之前步骤中创建的意向和服务连接调度到 Android，这将负责启动服务并建立客户端与服务之间的通信。

跨进程边界的需求会带来额外的复杂性：通信是单向的（客户端到服务器），而客户端不能直接对服务类调用方法。 请记住，当服务运行与客户端相同的进程时，Android 提供了一个 `IBinder` 对象，这可能允许双向通信。 如果服务在其自己的进程中运行，则不会出现这种情况。 客户端通过 `Android.OS.Messenger` 类的帮助与远程服务进行通信。

当客户端请求与远程服务绑定时，Android 将调用 `Service.OnBind` 生命周期方法，该方法将返回 `Messenger`封装的内部 `IBinder` 对象。 `Messenger` 是 Android SDK 提供的特殊 `IBinder` 实现的精简包装器。 `Messenger` 负责处理两个不同进程之间的通信。 开发人员不关心了有关序列化消息、跨进程边界封送消息，然后在客户端上反序列化消息的详细信息。 此工作由 `Messenger` 对象处理。 此图显示了当客户端启动到进程外服务的绑定时所涉及的客户端 Android 组件：

![显示客户端绑定到服务的步骤和组件的关系图](out-of-process-services-images/ipc-01.png "显示客户端绑定到服务的步骤和组件的关系图。")

远程进程中的 `Service` 类将经历本地进程中的绑定服务将经历的相同生命周期回调，其中的许多 Api 是相同的。 `Service.OnCreate` 用于初始化 `Handler` 并将其注入 `Messenger` 对象。 同样，`OnBind` 会被重写，但该服务将返回 `Messenger`，而不是返回 `IBinder` 对象。  此图说明了当客户端绑定到服务时，服务会发生什么情况：

![显示服务通过远程客户端绑定到的步骤和组件的关系图](out-of-process-services-images/ipc-02.png "此图显示了服务通过远程客户端绑定到时所经历的步骤和组件。")

当服务接收到 `Message` 时，它会在 `Android.OS.Handler`的实例中处理。 服务将实现其自己的 `Handler` 子类，该子类必须重写 `HandleMessage` 方法。 此方法由 `Messenger` 调用，并以参数的形式接收 `Message`。 `Handler` 将检查 `Message` 的元数据，并使用该信息对服务调用方法。

当客户端创建 `Message` 对象并使用 `Messenger.Send` 方法将它调度到服务时，将发生单向通信。 `Messenger.Send` 会将序列化数据的 `Message` 并交给 Android，这会将该消息路由到整个进程边界和服务。  服务承载的 `Messenger` 将从传入数据创建 `Message` 对象。 此 `Message` 放置在队列中，其中的消息一次提交到 `Handler`中。 `Handler` 将检查 `Message` 中包含的元数据，并在 `Service`上调用相应的方法。 下图说明了这些各个概念的操作：

![显示如何在进程之间传递消息的关系图](out-of-process-services-images/ipc-03.png "显示进程之间消息传递方式的关系图。")

本指南将讨论实现进程外服务的详细信息。 本文将讨论如何实现应在其自己的进程中运行的服务，以及客户端如何使用 `Messenger` 框架与该服务进行通信。 它还将简要讨论双向通信：客户端将消息发送到服务，并将消息发送回客户端。 由于可以在不同的应用程序之间共享服务，本指南还将讨论一种使用 Android 权限限制客户端对服务的访问的方法。

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-具有隔离进程和自定义应用程序类的服务无法正确地](https://github.com/xamarin/xamarin-android/issues/1950)报告当 `IsolatedProcess` 设置为 `true`时，不会正确启动 Xamarin Android 服务。 本指南提供了参考。 Xamarin Android 应用程序仍应能够与用 Java 编写的进程外服务进行通信。

## <a name="requirements"></a>需求

本指南假定你熟悉如何创建服务。

尽管可以对面向较早 Android Api 的应用程序使用隐式意向，但本指南将专门介绍显式意图的使用。 面向 Android 5.0 （API 级别21）或更高版本的应用程序必须使用显式意向才能与服务绑定;本指南将演示这项技术。

## <a name="create-a-service-that-runs-in-a-separate-process"></a>创建在单独的进程中运行的服务

如上所述，服务在其自己的进程中运行的事实意味着涉及到一些不同的 Api。 下面是使用远程服务进行绑定和使用的步骤：  

- **创建 `Service` 子类**&ndash; `Service` 类型的子类，并实现绑定服务的生命周期方法。 还必须设置元数据，以通知 Android 服务要在自己的进程中运行。
- **实现一个 `Handler`** &ndash; `Handler` 负责分析客户端请求，提取从客户端传递的任何参数，并对服务调用适当的方法。
- 如上文所述**实例化 `Messenger`** &ndash;，每个 `Service` 必须维护一个 `Messenger` 类的实例，该实例将客户端请求路由到在上一步中创建的 `Handler`。

打算在其自身进程中运行的服务从根本上讲，仍是一项绑定服务。 服务类将扩展基本 `Service` 类，并用包含 Android 需要在 Android 清单中捆绑的元数据的 `ServiceAttribute` 进行修饰。 首先，`ServiceAttribute` 的以下属性对进程外服务非常重要：

1. `Exported` &ndash; 必须将此属性设置为 `true`，以允许其他应用程序与该服务进行交互。 此属性的默认值为 `false`。
2. `Process` &ndash; 必须设置此属性。 它用于指定服务将在其中运行的进程的名称。
3. `IsolatedProcess` &ndash; 此属性将实现额外的安全性，告诉 Android 在隔离沙盒中运行该服务，并使用最小的权限与系统的其余部分进行交互。 请参阅[Bugzilla 51940-具有独立进程的服务和自定义应用程序类无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)。
4. `Permission` &ndash; 可以通过指定客户端必须请求（并被授予）的权限来控制对服务的客户端访问。

若要运行服务自己的进程，`ServiceAttribute` 上的 `Process` 属性必须设置为服务的名称。 若要与外部应用程序交互，应将 `Exported` 属性设置为 `true`。 如果 `false``Exported`，则只有同一 APK 中的客户端（即同一个应用程序）和在同一进程中运行，才能与服务交互。

服务将运行的进程类型取决于 `Process` 属性的值。 Android 标识三种不同类型的进程：

- **专用进程 &ndash; 专用**过程仅适用于启动它的应用程序。 若要将进程标识为专用，其名称必须以 **：** （分号）开头。 上面的代码片段中描述的服务和屏幕截图是一个专用进程。 下面的代码片段是 `ServiceAttribute`的示例：

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- **全局进程**&ndash; 设备上运行的所有应用程序都可以访问全局进程中运行的服务。 全局进程必须是以小写字符开头的完全限定类名。
    （除非采取措施来保护服务，否则其他应用程序可能会与之进行绑定和交互。 在本指南的后面部分将讨论如何保护服务不受未经授权的使用。）

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- 独立**进程 &ndash; 隔离进程是**在其自己的沙盒中运行的进程，该进程与系统的其余部分隔离，并且没有自己的特殊权限。 若要在隔离的进程中运行服务，`ServiceAttribute` 的 `IsolatedProcess` 属性设置为 `true` 如以下代码片段所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 请参阅[Bugzilla 51940-具有独立进程的服务和自定义应用程序类无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

隔离服务是保护应用程序和设备不受信任代码的一种简单方法。 例如，应用可以从网站下载并执行脚本。 在这种情况下，在隔离的进程中执行此操作可针对危及 Android 设备的不受信任代码提供额外的安全层。

> [!IMPORTANT]
> 一旦导出了某个服务，该服务的名称将不会更改。 更改服务的名称可能会破坏正在使用该服务的其他应用程序。

若要查看 `Process` 属性具有的效果，请参阅以下屏幕截图，显示在其自己的专用进程中运行的服务：

![显示在专用进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-04.png "显示在专用进程中运行的服务的屏幕截图。")

下一屏幕截图显示了在全局进程中运行 `Process="com.xamarin.xample.messengerservice.timestampservice_process"` 和服务：

![全局进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-05.png "全局进程中运行的服务的屏幕截图。")

设置 `ServiceAttribute` 后，服务需要实现 `Handler`。

### <a name="implementing-a-handler"></a>实现处理程序

若要处理客户端请求，服务必须实现 `Handler` 并重写 `HandleMessage` 方法。 此方法采用一个 `Message` 实例，该实例封装来自客户端的方法调用，并将调用转换为服务将执行的某个操作或任务。 `Message` 对象公开一个名为 `What` 的属性，该属性是一个整数值，这是在客户端和服务之间共享的，并与服务为客户端执行的某些任务相关。

下面的示例应用程序中的代码片段演示了一个 `HandleMessage`的示例。 在此示例中，客户端可以请求服务的两个操作：

- 第一个操作是_Hello，World_消息，客户端已向服务发送了简单消息。
- 第二个操作将对服务调用方法并检索字符串，在这种情况下，该字符串是一条消息，该消息返回服务启动的时间和运行时间：

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client has sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

还可以为 `Message`中的服务打包参数。 本指南稍后将对此进行讨论。 下一个要考虑的主题是创建用于处理传入 `Message`的 `Messenger` 对象。

### <a name="instantiating-the-messenger"></a>实例化 Messenger

如前文所述，反序列化 `Message` 对象和调用 `Handler.HandleMessage` 是 `Messenger` 对象的责任。 `Messenger` 类还提供了一个 `IBinder` 对象，客户端将使用该对象将消息发送到服务。  

当服务启动时，它将实例化 `Messenger` 并注入 `Handler`。 在服务的 `OnCreate` 方法中，可以执行此初始化。 此代码片段是初始化自己的 `Handler` 和 `Messenger`的服务的一个示例：

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此时，最后一步是用于重写 `OnBind`的 `Service`。

### <a name="implementing-serviceonbind"></a>实现 OnBind

所有绑定服务（无论是否在其自己的进程中运行）都必须实现 `OnBind` 方法。 此方法的返回值是一个对象，客户端可以使用它来与服务进行交互。 对象的确切内容取决于该服务是本地服务还是远程服务。 当本地服务将返回自定义 `IBinder` 实现时，远程服务将返回封装但在上一部分中创建的 `Messenger` 的 `IBinder`：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

完成这三个步骤后，即可将远程服务视为已完成。

## <a name="consuming-the-service"></a>使用服务

所有客户端都必须实现某些代码才能绑定和使用远程服务。 从概念上讲，从客户端的角度来看，绑定到本地服务或远程服务之间的差别很小。 客户端调用 `BindService` 方法，并通过显式意图来标识服务，并通过一个 `IServiceConnection` 来帮助管理客户端和服务之间的连接。

此代码片段举例说明如何创建绑定到远程服务的**显式意图**。 目的必须标识包含服务的包和服务的名称。 设置此信息的一种方法是使用 `Android.Content.ComponentName` 对象并将其提供给目的。 此代码段是一个示例：  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

绑定服务时，将调用 `IServiceConnection.OnServiceConnected` 方法，并向客户端提供 `IBinder`。 但是，客户端不会直接使用 `IBinder`。 相反，它将实例化 `IBinder`中的 `Messenger` 对象。 这是客户端将用于与远程服务进行交互的 `Messenger`。

下面是一个非常基本的 `IServiceConnection` 实现的示例，该示例演示客户端如何处理与服务的连接和断开连接。 请注意，`OnServiceConnected` 方法接收并 `IBinder`，客户端将从该 `IBinder`创建 `Messenger`：

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null;
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

创建服务连接和意向之后，客户端可以调用 `BindService` 并启动绑定过程：

```csharp
var serviceConnection = new TimestampServiceConnection(this);
BindService(serviceToStart, serviceConnection, Bind.AutoCreate);
```

当客户端成功绑定到服务并且 `Messenger` 可用之后，客户端可以向服务发送 `Messages`。

## <a name="sending-messages-to-the-service"></a>将消息发送到服务

当客户端连接到 `Messenger` 对象之后，就可以通过 `Messenger`分派 `Message` 对象来与服务进行通信。 此通信是单向的，客户端发送消息，但没有从服务到客户端的返回消息。 就这一点而言，`Message` 是一种暂时的机制。

创建 `Message` 对象的首选方法是使用[`Message.Obtain`](xref:Android.OS.Message)工厂方法。 此方法将从 Android 维护的全局池中提取 `Message` 对象。 `Message.Obtain` 还具有一些重载方法，这些方法允许使用服务所需的值和参数对 `Message` 对象进行初始化。  实例化 `Message` 后，将通过调用 `Messenger.Send`将其调度到服务。 此代码片段是创建 `Message` 并将其调度到服务进程的一个示例：

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

`Message.Obtain` 方法有多种不同的形式。 前面的示例使用[`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain)。 因为这是对进程外服务的异步请求，服务没有响应，因此将 `Handler` 设置为 `null`。 `Int32 what`的第二个参数将存储在 `Message` 对象的 `.What` 属性中。 服务进程中的代码使用 `.What` 属性来调用服务上的方法。

`Message` 类还公开了其他两个属性，这些属性可用于接收方： `Arg1` 和 `Arg2`。 这两个属性是整数值，可能在客户端和服务之间有一些特别同意的值。 例如，`Arg1` 可以保存客户 ID，`Arg2` 可能包含该客户的采购订单号。 创建 `Message` 时，可以使用[`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain)设置这两个属性。 填充这两个值的另一种方法是，在创建后直接在 `Message` 对象上设置 `.Arg` 和 `.Arg2` 属性。

### <a name="passing-additional-values-to-the-service"></a>向服务传递其他值

可以通过使用 `Bundle`将更复杂的数据传递给服务。 在这种情况下，可以通过在发送之前设置[`.Data` 属性](xref:Android.OS.Message.Data)属性，将额外值放在 `Bundle` 中，并与 `Message` 一起发送。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> 通常情况下，`Message` 不应具有大于1MB 的有效负载。 大小限制可能因 Android 版本而异，也可能因供应商对与设备捆绑的 Android 开源项目（AOSP）实现的任何专有更改而异。

## <a name="returning-values-from-the-service"></a>从服务返回值

此点已讨论的消息体系结构是单向的，客户端将向服务发送一条消息。 如果服务需要将值返回到客户端，则会反向讨论到此点的所有内容。 服务必须创建一个 `Message`、打包任何返回值，并通过 `Messenger` 将 `Message` 调度到客户端。 但是，服务不会创建自己的 `Messenger`;相反，它依赖客户端实例化并打包 `Messenger` 作为初始请求的一部分。 服务将使用此客户端提供的 `Messenger``Send` 消息。  

双向通信的事件序列如下：

1. 客户端绑定到服务。 当服务和客户端连接时，客户端维护的 `IServiceConnection` 将具有对用于将 `Message`传输到服务的 `Messenger` 对象的引用。 为避免混淆，这称为_服务信使_。
2. 客户端实例化一个 `Handler` （称为_客户端处理程序_），并使用它来初始化其自己的 `Messenger` （_客户端 Messenger_）。 请注意，服务信使和客户端 Messenger 是两个不同的对象，它们以两个不同的方向处理流量。 服务 Messenger 处理从客户端到服务的消息，而客户端 Messenger 将处理从服务到客户端的消息。
3. 客户端创建一个 `Message` 对象，并使用客户端 Messenger 设置 `ReplyTo` 属性。 然后，使用服务信使将消息发送到服务。
4. 服务从客户端接收消息，并执行所请求的工作。
5. 当服务将响应发送到客户端时，它将使用 `Message.Obtain` 来创建新 `Message` 对象。
6. 若要将此消息发送到客户端，该服务将从客户端消息的 `.ReplyTo` 属性中提取客户端 Messenger，并使用该属性 `.Send` `Message` 返回到客户端。
7. 当客户端收到响应时，它有自己的 `Handler`，它将通过检查 `.What` 属性（如有必要，提取 `Message`中包含的所有参数）处理 `Message`。

此代码示例演示客户端如何实例化 `Message` 并打包服务应用于其响应的 `Messenger`：

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

此服务必须对其自身的 `Handler` 进行一些更改，以便提取 `Messenger` 并使用它将答复发送到客户端。 此代码片段举例说明了服务的 `Handler` 如何创建 `Message` 并将其发送回客户端：  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

请注意，在上面的代码示例中，客户端创建的 `Messenger`*实例与服务接收的对象*不同。 这两个不同的 `Messenger` 对象在两个单独的进程中运行，分别表示信道。

## <a name="securing-the-service-with-android-permissions"></a>通过 Android 权限保护服务

在全局进程中运行的服务可由该 Android 设备上运行的所有应用程序访问。 在某些情况下，这种开放性和可用性是不需要的，而且需要保护服务以防未经授权的客户端的访问。 限制对远程服务的访问的一种方法是使用 Android 权限。

权限可通过修饰 `Service` 子类的 `ServiceAttribute` 的 `Permission` 属性进行标识。 这将命名在绑定到服务时必须授予客户端的权限。 如果客户端没有相应的权限，则当客户端尝试绑定到服务时，Android 会引发 `Java.Lang.SecurityException`。

Android 提供四种不同的权限级别：

- **正常**&ndash; 此为默认权限级别。 它用于标识可由 Android 自动向请求它的客户端授予的低风险权限。 用户不必显式授予这些权限，但可以在应用设置中查看权限。
- **签名**&ndash; 这是一种特殊的权限类别，由 Android 自动授予使用同一证书签名的应用程序。 此权限旨在使应用程序开发人员能够轻松地在应用程序之间共享组件或数据，而无需麻烦用户进行持续审批。
- **signatureOrSystem** &ndash; 这与上述**签名**权限非常类似。 除了自动授予由同一证书签名的应用之外，还会将此权限授予已签名证书的应用，该证书用于对随 Android 系统映像安装的应用进行签名。 此权限通常仅由 Android ROM 开发人员用于允许其应用程序使用第三方应用。 这种情况通常不是指一般的公开分发的应用程序。
- **危险**&ndash; 危险的权限是那些可能会给用户带来麻烦的权限。 出于此原因，必须由用户显式批准**危险**权限。

由于 Android 会自动授予 `signature` 和 `normal` 权限，因此在包含客户端的 APK**之前**，必须安装托管该服务的 APK。 如果首先安装客户端，则 Android 不会授予权限。 在这种情况下，需要卸载客户端 APK，安装服务 APK，然后重新安装客户端 APK。

使用 Android 权限保护服务的方法有两种：

1. **实现 "签名级别安全性**" &ndash; "签名级别安全性" 表示将权限自动授予那些使用同一密钥进行签名的应用程序，该密钥用于对保存该服务的 APK 进行签名。 这是开发人员保护其服务的一种简单方法，使其能够从他们自己的应用程序中进行访问。 签名级别权限是通过将 `ServiceAttribute` 的 `Permission` 属性设置为 `signature`来声明的：

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **创建自定义权限**&ndash; 服务的开发人员可以为该服务创建自定义权限。 这最适用于开发人员想要与其他开发人员的应用程序共享其服务的情况。 自定义权限需要更多精力才能实现并将在下面介绍。

下一节将介绍创建自定义 `normal` 权限的简化示例。 有关 Android 权限的详细信息，请参阅 Google 的文档，以了解[& 安全性的最佳做法](https://developer.android.com/training/articles/security-tips.html)。 有关 Android 权限的详细信息，请参阅应用程序清单的 Android 文档的 "[权限" 部分](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)，了解有关 android 权限的详细信息。

> [!NOTE]
> 通常，Google 会阻止[使用自定义权限](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions)，因为它们可能会给用户造成混淆。

### <a name="creating-a-custom-permission"></a>创建自定义权限

若要使用自定义权限，则在客户端显式请求该权限时，此服务将声明该权限。

若要在服务 APK 中创建权限，请将 `permission` 元素添加到**androidmanifest.xml**中的 `manifest` 元素。 此权限必须设置 `name`、`protectionLevel`和 `label` 属性。 `name` 特性必须设置为唯一标识该权限的字符串。 该名称将显示在**Android 设置**的 "**应用信息**" 视图中（如下一节所示）。

`protectionLevel` 特性必须设置为上面所述的四个字符串值之一。  `label` 和 `description` 必须引用字符串资源，并用于向用户提供用户友好名称和说明。

此代码片段是在包含服务的 APK 的**androidmanifest.xml**中声明自定义 `permission` 属性的示例：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

然后，客户端 APK 的**androidmanifest.xml**必须显式请求此新权限。 这是通过将 `users-permission` 特性添加到**androidmanifest.xml**来完成的：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>查看向应用程序授予的权限

若要查看应用程序的权限，请打开 "Android 设置" 应用，然后选择 "**应用**"。 在列表中找到并选择该应用程序。 在 "**应用信息**" 屏幕上，点击 "**权限**"，这将显示授予应用的所有权限：

[![从 Android 设备 屏幕截图，其中显示了如何查找授予应用程序的权限](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>摘要

本指南是有关如何在远程进程中运行 Android 服务的高级讨论。 介绍了本地和远程服务之间的差异，以及远程服务对 Android 应用程序的稳定性和性能有所帮助的一些原因。 在说明如何实现远程服务以及客户端如何与服务进行通信后，指南会提供一种方法，用于从仅授权客户端限制对服务的访问。

## <a name="related-links"></a>相关链接

- [函数](xref:Android.OS.Handler)
- [消息](xref:Android.OS.Message)
- [Messenger](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [导出的属性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [具有隔离进程和自定义应用程序类的服务无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [进程和线程](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 清单-权限](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全提示](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
