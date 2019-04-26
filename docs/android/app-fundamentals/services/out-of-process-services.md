---
title: 远程进程中的运行 Android 服务
description: 通常情况下，Android 应用程序中的所有组件将在同一进程中都运行。 Android 服务是一个值得注意的例外的可以配置为在其自身的进程中运行并与其他应用程序，包括来自其他 Android 开发人员共享。 本指南介绍了如何创建和使用使用 Xamarin Android 远程服务。
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: db312c4c102feb98791109af19185762bb25856e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012886"
---
# <a name="running-android-services-in-remote-processes"></a>远程进程中的运行 Android 服务

_通常情况下，Android 应用程序中的所有组件将在同一进程中都运行。Android 服务是一个值得注意的例外的可以配置为在其自身的进程中运行并与其他应用程序，包括来自其他 Android 开发人员共享。本指南介绍了如何创建和使用使用 Xamarin Android 远程服务。_

## <a name="out-of-process-services-overview"></a>不足的进程服务概述

应用程序启动时，Android 创建要在其中运行应用程序的进程。 通常情况下，所有组件应用程序将在都运行此进程。 Android 服务是一个值得注意的例外的可以配置为在其自身的进程中运行并与其他应用程序，包括来自其他 Android 开发人员共享。 这些类型的服务嘿 _远程服务_或_进程外服务_。 这些服务的代码将包含在与主应用程序; 相同的 APK但是，当服务启动 Android 将创建只是该服务的新进程。 与此相反，在与应用程序的其余部分相同的进程中运行的服务有时称为_本地服务_。

一般情况下，不应用程序来实现远程服务。 本地服务是足够 （和好） 在许多情况下的应用程序的要求。 扩展的进程具有其自己的内存空间必须管理的 Android。 虽然整个应用程序，这确实产生更多的开销，但在某些情况下，它可以是在其自己的进程中运行服务更有利：

1. **共享功能**&ndash;某些应用程序开发人员可能有多个应用程序和所有应用程序之间共享的功能。 打包 Android 服务在其自己的进程中运行可以简化应用程序维护中该功能。 还有可能在其自己独立的 APK 中的服务打包并将其单独部署应用程序的其余部分。
2. **改善用户体验**&ndash;有两种方法，进程外服务可以提高应用程序的用户体验。 第一种方式处理内存管理。 当垃圾回收 (GC) 周期时，Android 会暂停进程中的所有活动，直到 GC 已完成。 用户可能会发现已作为"出现断断续续的问题"或"jank"暂停。 当服务在运行时是自己的过程，它是服务进程已暂停，不是应用程序进程。 暂停将用户得很明显，因为未暂停应用程序进程 （和用户界面）。

    其次，如果一个进程的内存要求变得太大，Android 可能终止该进程以释放设备资源。 如果服务有很大的内存需求量，用作 UI 在同一进程中运行，然后当 Android 强制回收这些资源时用户界面将关闭，迫使用户在启动应用程序。 但是，如果在其自己的进程中运行的服务关闭的情况下通过 Android，UI 过程保持不受影响。 用户界面可以绑定 （和重新启动） 服务，透明的用户，并恢复正常功能。

3. **提高应用程序性能**&ndash;的 UI 进程可能会终止或关闭的情况下独立于服务进程。 通过将较长的启动任务移动到进程外服务，就可以 UI 的启动时间可能改进 （假定服务进程保持活动状态的时间启动 UI 之间）。

在许多方面，绑定到另一个进程中运行的服务是与相同[绑定到本地服务](~/android/app-fundamentals/services/creating-a-service/bound-services.md)。 客户端将调用`BindService`绑定 （和启动，如有必要） 服务。 `Android.OS.IServiceConnection`将创建对象来管理客户端和服务之间的连接。 如果客户端成功绑定到的服务，则 Android 将返回的对象通过`IServiceConnection`可用于在服务上调用方法。 客户端然后使用此对象与服务进行交互。 若要查看，下面是绑定到服务的步骤：

* **创建意向**&ndash;目的在于明确指示必须使用与绑定到该服务。
* **实现和实例化`IServiceConnection`对象** &ndash; `IServiceConnection`对象充当客户端和服务之间的中介。  它负责监视客户端和服务器之间的连接。
* **调用`BindService`方法**&ndash;调用`BindService`将调度意图和 Android 将负责启动服务并建立通信的上一步骤中创建的服务连接客户端和服务。

跨进程边界的需求会引入额外的复杂性： 的通信是单向 （客户端到服务器） 和客户端不能直接调用服务类上的方法。 回想一下，当服务作为客户端运行时相同的过程，提供了 Android`IBinder`对象，这可能会让为双向通信。 这不是与在其自己的进程中运行服务的情况。 客户端通信与远程服务的帮助`Android.OS.Messenger`类。

当客户端请求与远程服务绑定时，将调用 Android`Service.OnBind`生命周期方法，它将返回内部`IBinder`对象，它封装由`Messenger`。 `Messenger`是通过一种特殊的薄包装`IBinder`Android SDK 提供的实现。 `Messenger`负责的两个不同进程之间的通信。 开发人员是关心的序列化消息跨进程边界封送消息，然后在客户端上对它进行反序列化的详细信息。 这项工作由`Messenger`对象。 此图显示了客户端启动绑定到进程外服务时，会涉及的客户端的 Android 组件：

![图示显示绑定到服务的客户端的步骤和组件](out-of-process-services-images/ipc-01.png "图示显示绑定到服务的客户端的步骤和组件。")

`Service`远程进程中的类将经历中的本地进程的绑定的服务将经历相同生命周期回调和许多相关的 Api 是相同的。 `Service.OnCreate` 用于初始化`Handler`，并插入到`Messenger`对象。 同样，`OnBind`重写，但而不是返回`IBinder`对象，该服务将返回`Messenger`。  此图描述了时会发生什么情况在服务客户端绑定到它：

![要绑定到由远程客户端将关系图显示的步骤和组件服务的经历](out-of-process-services-images/ipc-02.png "要绑定到由远程客户端将关系图显示的步骤和组件服务的经历。")

当`Message`收到的服务，它由处理的实例中`Android.OS.Handler`。 该服务将实现其自己`Handler`子类必须重写`HandleMessage`方法。 通过调用此方法`Messenger`，并接收`Message`作为参数。 `Handler`会检查`Message`元数据和使用这些信息来调用该服务的方法。

当客户端创建单向通信时发生`Message`对象，并将其调度到服务使用`Messenger.Send`方法。 `Messenger.Send` 将序列化`Message`和手动关闭序列化数据，会将消息路由跨进程边界的 Android 和服务。  `Messenger` ，它由承载该服务将创建`Message`从传入数据的对象。 这`Message`放入队列，其中的消息是到一次提交的一个`Handler`。 `Handler`将检查中包含的元数据`Message`上调用适当的方法和`Service`。 下图说明了这些操作中的各种概念：

![显示如何将消息传递两个进程之间的关系图](out-of-process-services-images/ipc-03.png "关系图显示两个进程之间传递消息的方式。")

本指南介绍了实现进程外服务的详细信息。 本文将讨论如何实现用于在其自己的进程中运行的服务和客户端与该服务使用可能通信的方式`Messenger`框架。 它将还简要讨论的双向通信： 客户端将消息发送到服务和将消息发送回客户端服务。 服务可以在不同的应用程序之间共享，因为本指南还将讨论使用 Android 权限来限制对服务的客户端访问的一项技术。

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-使用隔离的进程和应用程序的自定义类的服务不能正确解析重载](https://github.com/xamarin/xamarin-android/issues/1950)Xamarin.Android 服务将不会启动正确的报表时`IsolatedProcess`设置为`true`。 本指南提供的引用。 Xamarin.Android 应用程序仍应能够与用 Java 编写的进程外服务进行通信。

## <a name="requirements"></a>要求

本指南假定你熟悉创建服务。

尽管可以使用与应用程序面向较旧的隐式的意向，但 Android Api，本指南将重点介绍以独占方式使用显式的意图。 面向 Android 5.0 （API 级别 21） 的应用程序或更高版本必须使用目的在于明确指示要绑定的服务;这是将本指南中演示的技术...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>创建单独的进程中运行的服务

上文所述，在其自己的进程中运行服务的事实意味着一些不同的 Api 是涉及。 作为快速概述，下面是使用绑定和使用远程服务的步骤：  

* **创建`Service`子类**&ndash;子类`Service`键入和实现绑定的服务的生命周期方法。 还有必要设置会通知该服务是在其自己的进程中运行的 Android 的元数据。
* **实现`Handler`**  &ndash; `Handler`负责分析客户端请求、 提取已从客户端，传递任何参数并调用服务上的相应方法。
* **实例化`Messenger`**  &ndash;如上所述，每个`Service`必须保留的实例`Messenger`类将客户端将请求路由到`Handler`上一步中创建。

服务旨在在其自己的进程中运行的是，从根本上说，仍绑定的服务。 服务类将扩展基`Service`类，并使用修饰`ServiceAttribute`包含 Android 需要 Android 清单中捆绑的元数据。 若要开始进行的以下属性`ServiceAttribute`对进程外服务很重要的：

1. `Exported` &ndash; 此属性必须设置为`true`以允许其他应用程序与服务交互。 此属性的默认值为 `false`。
2. `Process` &ndash; 必须设置此属性。 它用于指定该服务将在运行进程的名称。
3. `IsolatedProcess` &ndash; 此属性将启用额外的安全性，告知 Android 在与系统其余部分进行交互的最小权限与独立的沙盒中运行服务。 请参阅[Bugzilla 51940-服务使用的隔离的进程和应用程序的自定义类无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)。
4. `Permission` &ndash; 就可以通过指定客户端必须请求 （并被授予） 的权限来控制对服务的客户端访问。

若要运行其自己的进程的服务`Process`属性上的`ServiceAttribute`必须设置为服务的名称。 与外部应用程序进行交互`Exported`属性应设置为`true`。 如果`Exported`是`false`，然后在相同的 APK （即相同应用程序） 中的唯一客户端和运行在同一进程中的将能够与服务交互。

该服务将在运行的进程的种类取决于的值`Process`属性。 Android 标识三个不同类型的过程：

-   **专用流程**&ndash;专用流程都是一个且仅可用于启动该应用程序。 若要标识为专用的进程，其名称必须启动 **:** （用分号）。 在上一代码段和屏幕截图中所示的服务是专用流程。 以下代码片段示范了`ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **全局进程**&ndash;的全局进程中运行的服务是在设备上运行的所有应用程序可以访问。 全局进程必须是小写字符开头的完全限定的类名。
    （除非采取步骤来保护服务，其他应用程序可能会将绑定并与之交互。 针对未经授权的使用服务的安全将讨论在本指南中的更高版本。）

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **隔离进程**&ndash;隔离的进程是在独立于其他系统的并具有其自己的任何特殊权限自己沙箱中运行的进程。 若要在一个独立的进程中运行服务`IsolatedProcess`的属性`ServiceAttribute`设置为`true`中此代码片段所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 请参阅[Bugzilla 51940-服务使用的隔离的进程和应用程序的自定义类无法正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

独立的服务是安全的应用程序和设备的不受信任代码的简单方法。 例如，应用可能会下载并从网站中执行脚本。 在这种情况下，在一个独立的进程中执行这提供了额外的安全性，以防止破坏 Android 设备不受信任的代码。

> [!IMPORTANT]
> 一旦被导出服务，不应更改服务的名称。 更改服务的名称可能会破坏其他应用程序使用的服务。

若要查看效果的`Process`属性具有，下面的屏幕截图显示了在其自己专用的进程中运行的服务：

![显示在专用的进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-04.png "显示在专用的进程中运行的服务的屏幕截图。")

此下一步的屏幕截图显示了`Process="com.xamarin.xample.messengerservice.timestampservice_process"`和全局进程中运行的服务：

![在全局进程中运行的服务的屏幕截图](out-of-process-services-images/ipc-05.png "全局进程中运行的服务的屏幕截图。")

一次`ServiceAttribute`已设置，需要实现该服务`Handler`。

### <a name="implementing-a-handler"></a>实现一个处理程序

若要处理客户端请求，该服务必须实现`Handler`并重写`HandleMessage`methodThis 是方法采用`Message`实例，其中封装的方法调用从客户端，并将转换到某些操作或任务调用该服务将执行。 `Message`对象会公开一个名为属性`What`这是一个整数值，其含义客户端和服务之间共享以及与该服务是针对客户端执行某些任务。

示例应用程序中的以下代码段说明的一个示例`HandleMessage`。 在此示例中，有服务的客户端可以请求两个操作：

* 第一个操作是_Hello，World_消息，客户端已发送一个简单的消息，到服务。
* 第二个操作将调用服务上的方法和检索字符串，在这种情况下的字符串是一条消息，返回已运行的服务已启动并在多长时间的什么时间：

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
                // The client as sent a simple Hello, say in the Android Log.
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

此外，还可以为包参数中的服务`Message`。 这将更高版本在本指南中讨论。 创建要考虑的下一个主题`Messenger`对象来处理传入`Message`s。

### <a name="instantiating-the-messenger"></a>实例化 Messenger

如前所述，反序列化`Message`对象并调用`Handler.HandleMessage`负责`Messenger`对象。 `Messenger`类还提供了`IBinder`对象在客户端将用于将消息发送到服务。  

服务启动时，它将实例化`Messenger`注入和`Handler`。 执行此初始化的好时机是在`OnCreate`服务的方法。 此代码片段是初始化其自己的服务的一个示例`Handler`和`Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此时，最后一步是为`Service`重写`OnBind`。

### <a name="implementing-serviceonbind"></a>实现 Service.OnBind

所有绑定的服务，无论是，在它们自己的进程中运行而必须实现`OnBind`方法。 此方法的返回值是某些客户端可用来与服务交互的对象。 完全该对象是什么取决于该服务是本地服务或远程服务。 虽然本地服务将返回一个自定义`IBinder`实现中，远程服务将返回`IBinder`的封装，但`Messenger`上一节中的已创建：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

一旦完成这三个步骤，远程服务可被视为完成。

## <a name="consuming-the-service"></a>使用服务

所有客户端必须实现一些代码，以便能够将绑定和使用远程服务。 从概念上讲，从客户端的角度来看，很少之间有差异到本地服务或远程服务绑定。 客户端调用`BindService`方法，并传递显式意向来标识该服务和一个`IServiceConnection`可帮助管理客户端和服务之间的连接。

此代码片段示范了如何创建**目的在于明确指示**向远程服务绑定。 其目的必须标识包含服务和服务的名称的包。 若要设置此信息的一种方法是使用`Android.Content.ComponentName`对象，并提供的到意向。 此代码片段是一个示例：  

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

当绑定服务时，`IServiceConnection.OnServiceConnected`方法调用，并且提供了`IBinder`到客户端。 但是，客户端将不直接使用`IBinder`。 相反，它将实例化`Messenger`中的对象`IBinder`。 这是`Messenger`客户端将用来与远程服务进行交互。

以下是非常基本的示例`IServiceConnection`演示客户端将如何处理连接到和从服务断开连接的实现。 请注意，`OnServiceConnected`方法接收并`IBinder`，并在客户端创建`Messenger`从的`IBinder`:

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

        IsConnected = service != null
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

一旦创建服务连接和意图的方式，就可以为客户端调用`BindService`和启动绑定过程：

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

客户端已顺利绑定到该服务后，`Messenger`是可用，就可以为客户端发送`Messages`到服务。

## <a name="sending-messages-to-the-service"></a>将消息发送到服务

只要客户端连接，并具有`Messenger`对象，就可以与服务通信的调度`Message`对象通过`Messenger`。 此通信为单向，客户端发送消息但没有向客户端从服务返回消息。 在这方面，`Message`是即发即弃的机制。

若要创建的首选的方式`Message`对象是使用[ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods)工厂方法。 此方法将提取`Message`从由 Android 维护的全局池中的对象。 `Message.Obtain` 也有一些重载的方法，允许`Message`要使用该服务所需的参数和值初始化对象。  一次`Message`是实例化，其调度到该服务通过调用`Messenger.Send`。 此代码段是一种创建和调度`Message`到服务进程：

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

有几种不同形式的`Message.Obtain`方法。 上面的示例使用[ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/)。 由于这是向进程外服务; 的异步请求将无响应服务，因此`Handler`设置为`null`。 第二个参数， `Int32 what`，将存储在`.What`属性的`Message`对象。 `.What`服务进程中的代码使用属性对服务调用方法。

`Message`类还公开给接收方使用的两个附加属性：`Arg1`和`Arg2`。 这两个属性都可能有一些特殊达成具有客户端和服务之间的含义的值的整数值。 例如，`Arg1`可能包含客户 ID 和`Arg2`可能保存该客户采购订单号。 [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/)可用于设置两个属性时`Message`创建。 另一种方法来填充这两个值是设置`.Arg`并`.Arg2`属性是直接在`Message`对象后已创建。

### <a name="passing-additional-values-to-the-service"></a>将其他值传递给该服务

可以通过将更复杂的数据传递给该服务`Bundle`。 在这种情况下，可以额外值放入`Bundle`和发送连同`Message`通过设置[`.Data`属性](https://developer.xamarin.com/api/property/Android.OS.Message.Data/)之前发送的属性。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> 一般情况下，`Message`不应具有的有效负载大小超过 1 MB。 大小限制可能会有所不同，根据 Android 版本和任何专有供应商可能具有进行更改到其实现的 Android 开放源项目 (AOSP) 的设备与捆绑在一起。

## <a name="returning-values-from-the-service"></a>从服务返回值

到目前为止讨论过的消息传递体系结构是单向的客户端向服务发送一条消息。 如有必要服务无法将值返回给客户端然后到目前为止讨论过的所有内容被反转。 该服务必须创建`Message`打包任何返回值和调度`Message`通过`Messenger`到客户端。 但是，该服务不会创建其自己`Messenger`; 相反，它依赖于客户端实例化和包`Messenger`初始请求的一部分。 该服务会`Send`使用此客户端提供的消息`Messenger`。  

双向通信的事件的顺序是：

1. 客户端绑定到的服务。 当该服务和客户端连接时，`IServiceConnection`由维护客户端将具有对引用`Messenger`对象，用于传输`Message`到该服务。 为避免混淆，这将称为_服务 Messenger_。
2. 客户端实例化`Handler`(称为_客户端处理程序_)，并使用其初始化其自身`Messenger`(_客户端 Messenger_)。 请注意，服务 Messenger 和客户端 Messenger 是两个不同的对象处理在两个不同的方向的流量。 服务 Messenger 处理到服务，客户端的消息，而客户端 Messenger 将处理从服务向客户端的消息。
3. 客户端创建`Message`对象，并设置`ReplyTo`与客户端 Messenger 的属性。 然后，消息是发送到使用服务 Messenger 服务。
4. 该服务从客户端，接收消息并执行请求的工作。
5. 该服务发送到客户端的响应时间时，它将使用`Message.Obtain`若要创建一个新`Message`对象。
6. 若要将此消息发送到客户端，该服务将提取从客户端 Messenger`.ReplyTo`属性的客户端消息，并使用它向`.Send``Message`返回给客户端。
7. 当客户端收到响应时，它具有自己`Handler`，将处理`Message`通过检查`.What`属性 (如有必要，提取所包含的任何参数和`Message`)。

此代码示例演示如何在客户端将实例化`Message`并将其打包`Messenger`服务应使用用于响应：

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

该服务必须进行一些更改到其自身`Handler`提取`Messenger`并使用它来发送到客户端的答复。 此代码片段示范了如何在服务的`Handler`会创建`Message`并将其发送回客户端：  

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

请注意，在上面的代码示例`Messenger`客户端创建的实例是*不*服务接收的相同对象。 这些是两个不同`Messenger`表示通信通道的两个单独进程中运行的对象。

## <a name="securing-the-service-with-android-permissions"></a>保护服务与 Android 权限

该 Android 设备上运行的所有应用程序可访问的全局进程中运行的服务。 在某些情况下，这种开放性和可用性是不可取，并需要从未经授权的客户端的安全对访问服务。 若要限制对远程服务的访问的一种方法是使用 Android 权限。

可以通过标识的权限`Permission`的属性`ServiceAttribute`修饰`Service`子类别。 这将命名为绑定到该服务时，必须授予客户端的权限。 如果客户端不具有相应的权限，则将引发 Android`Java.Lang.SecurityException`客户端尝试绑定到服务。

有四个 Android 提供的不同权限级别：

* **正常**&ndash;这是默认的权限级别。 它用于标识可以将自动授予 android 请求它的客户端的低风险权限。 用户无需显式授予这些权限，但可以在应用设置中查看权限。
* **签名**&ndash;这是授予的权限将自动由 Android 进行签名的相同证书的应用程序的特殊类别。 此权限旨在使其可轻松地为应用程序开发人员共享组件或自己的应用而无需求助于常量的审批用户之间的数据。
* **signatureOrSystem** &ndash;这是非常类似于**签名**上面所述的权限。 除了自动授予对由相同的证书签名的应用，此权限还将向授予应用进行签名的相同用于对应用进行签名的证书安装的 Android 系统映像。 此权限通常仅用于 Android ROM 开发人员通过允许其使用第三方应用的应用程序。 它不常使用的意为广大公众，常规分发的应用。
* **危险**&ndash;危险的权限是指那些可能会导致用户的问题。 出于此原因，**危险**权限必须明确批准的用户。

因为`signature`并`normal`自动授予权限在安装时通过 Android 至关重要，在安装 APK 托管服务**之前**APK 包含客户端。 如果首次安装客户端，则 Android 将授予的权限。 在这种情况下，它将需要卸载客户端的 APK，安装服务 APK，并重新安装客户端的 APK。

有两种常见的方式与 Android 权限的服务安全：

1.  **实现签名级别安全性**&ndash;签名级别安全性意味着，权限将自动授予对这些应用程序使用使用包含该服务对 APK 进行签名的同一密钥进行签名的。 这是开发人员保护其服务，但保留其可从自己的应用程序访问的简单方法。 通过设置声明签名级权限`Permission`的属性`ServiceAttribute`到`signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **创建自定义权限**&ndash;可以服务的开发人员用来创建该服务的自定义权限。 这是最好的开发人员希望与其他开发人员的应用程序共享其服务。 自定义权限需要更多工作才能实现，在下面将讨论。

创建自定义的简化的示例`normal`权限将在下一节中所述。 有关 Android 权限的详细信息，请参阅 Google 的文档[最佳做法和安全](https://developer.android.com/training/articles/security-tips.html)。 有关 Android 权限的详细信息，请参阅[权限部分](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)的 Android 权限有关的详细信息的应用程序清单的 Android 文档。

> [!NOTE]
> 一般情况下， [Google 不鼓励使用自定义权限](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions)因为它们可能会给用户造成混淆。

### <a name="creating-a-custom-permission"></a>创建自定义权限

若要使用的自定义权限，来声明它的服务时的客户端显式请求该权限。

若要创建 APK，在服务中的权限`permission`元素添加到`manifest`中的元素**AndroidManifest.xml**。 此权限必须具有`name`， `protectionLevel`，和`label`属性集。 `name`属性必须设置为一个字符串，唯一标识该权限。 名称将显示在**应用信息**视图**Android 设置**（如在下一节中所示）。

`protectionLevel`属性必须设置为上面所述的四个字符串值之一。  `label`和`description`必须引用字符串资源，用于向用户提供的用户友好名称和说明。

此代码段是声明一个自定义的一个示例`permission`中的属性**AndroidManifest.xml**包含服务的 apk:

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

然后，将**AndroidManifest.xml**客户端的 APK 必须显式请求此新权限。 这是通过添加`users-permission`归于**AndroidManifest.xml**:

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

### <a name="view-the-permissions-granted-to-an-app"></a>查看向应用授予的权限

若要查看应用程序已被授予的权限，打开 Android 设置应用，然后选择**应用**。 查找和选择列表中的应用程序。 从**应用信息**屏幕上，点击**权限**这将显示一个视图，显示所有应用程序授予的权限：

[![显示如何查找应用程序授予的权限是 Android 设备的屏幕截图](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>总结

本指南是有关如何在远程进程中运行的 Android 服务的高级的讨论。 本地计算机和远程服务之间的差异介绍过的方法，以及为什么远程服务可以是对稳定性和性能的 Android 应用程序很有帮助的一些原因。 介绍如何实现远程服务和客户端可以在与服务进行通信后, 指南往提供一种方法来限制从只有经过授权的客户端访问该服务。


## <a name="related-links"></a>相关链接

- [Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [消息](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [导出属性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [使用隔离的进程和应用程序的自定义类的服务不能正确解析重载](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [进程和线程](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 清单的权限](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全提示](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
