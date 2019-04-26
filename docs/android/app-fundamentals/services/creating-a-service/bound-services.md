---
title: 在 Xamarin.Android 中绑定服务
description: 绑定的服务是 Android 提供客户端 （例如 Android 活动） 可与之交互的客户端-服务器接口的服务。 本指南介绍了与创建绑定的服务以及如何在 Xamarin.Android 应用程序中使用它所涉及的关键组件。
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: c0adee0dae1135bdfd076082e85a471db1cd1ecf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013213"
---
# <a name="bound-services-in-xamarinandroid"></a>在 Xamarin.Android 中绑定服务

_绑定的服务是 Android 提供客户端 （例如 Android 活动） 可与之交互的客户端-服务器接口的服务。本指南介绍了与创建绑定的服务以及如何在 Xamarin.Android 应用程序中使用它所涉及的关键组件。_

## <a name="bound-services-overview"></a>绑定服务概述

向客户端进行直接交互的客户端-服务器接口提供服务的服务被称为_绑定服务_。  可以有多个客户端同时连接到服务的单一实例。 绑定的服务和客户端是相互隔离的。 相反，Android 提供了一系列管理这两个之间的连接状态的中间对象。 此状态由对象实现[ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)接口。  客户端创建此对象并将其作为参数传递给[ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/)方法。 `BindService`是出现在任何[ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context)对象 （如活动）。 它是对 Android 操作系统启动该服务并绑定到该客户端的请求。 有三种方式向客户端可能会将绑定到服务使用`BindService`方法：

* **服务活页夹**&ndash;服务联编程序是一个类，实现[ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder)接口。 大多数应用程序将不会直接实现此接口，它们将改为扩展[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)类。 这是最常用的方法，适用于在同一进程中时存在服务和客户端。
* **使用 Messenger** &ndash;这种技术适合时该服务可能存在于单独的进程中。 相反，服务请求客户端和服务通过之间封送[ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger)。 [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler)将要用于处理在服务中创建`Messenger`请求。 另一指南中包含此文件。
* **使用 Android 接口定义语言 (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl)是一种高级的技术，将此指南中未提及。

一旦客户端具有已绑定到的服务，二者之间的通信是通过时`Android.OS.IBinder`对象。  此对象负责将允许客户端与服务进行交互的接口。 没有必要为每个 Xamarin.Android 应用程序以实现此接口从零开始，Android SDK 提供[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)负责大部分所需的对象之间的封送处理代码的类客户端和服务。

完成客户端与服务操作后，它必须通过调用从它取消绑定`UnbindService`方法。 一旦从一种服务，最后一个客户端具有未绑定，Android 将停止并释放绑定服务。

此图描述了如何将活动、 服务连接、 联编程序和服务所有彼此相关的：

![一个显示每个服务组件之间的关系的图表](bound-services-images/bound-services-02.png "关系图显示每个服务组件之间的关系。")

本指南介绍了如何扩展`Service`类，以实现绑定的服务。 它还将介绍实现`IServiceConnection`和扩展`Binder`以允许客户端与服务进行通信。 示例应用程序附带本指南中，其中包含一个名为 Xamarin.Android 项目使用的一种解决方案 **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** 。 这是非常基本的应用程序，该示例演示了如何实现的服务以及如何将活动绑定到它。 绑定的服务具有只有一个方法，使用非常简单的 API `GetFormattedTimestamp`，这会返回一个字符串，告知用户已启动该服务时，多长时间运行。 应用程序还允许用户手动解除绑定，并将绑定到该服务。

[![在 Android 手机上运行的应用程序的屏幕截图](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>实现和使用绑定的服务

有三个组件必须实现中使用的绑定的服务的 Android 应用程序的顺序：

1. **扩展`Service`类并实现生命周期回调方法**&ndash;此类将包含将执行将服务的请求的工作的代码。 下面更详细地包含此文件。
2. **创建一个类实现`IServiceConnection`**  &ndash;回叫方法将调用此接口提供通过 Android 通知客户端与服务连接发生更改时，即客户端已连接或断开连接到服务。 服务连接还会提供对客户端可用来直接与服务交互的对象的引用。 此引用名为_联编程序_。
3. **创建一个类实现`IBinder`**  &ndash; A_联编程序_实现提供的 API 的客户端用于与服务通信。 此联编程序可以提供对绑定的服务中，引用允许直接调用方法或联编程序可以提供一个客户端 API 的封装并隐藏该应用程序中的绑定的服务。 `IBinder`必须为远程过程调用提供所需的代码。 不需要 （或不建议） 若要实现`IBinder`直接接口。 应用程序应改为扩展`Binder`提供了大部分所需的基本功能的类型`IBinder`。
4. **启动并绑定到服务** &ndash; Android 应用程序的服务连接，联编程序和服务创建后是负责启动服务并将绑定到它。

将以下各节更详细地讨论每个步骤。

### <a name="extend-the-service-class"></a>扩展`Service`类

若要创建使用 Xamarin.Android 的服务，有必要为子类`Service`并进行修饰的类[ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)。 Xamarin.Android 生成工具使用该属性来正确注册该服务在应用中**AndroidManifest.xml**文件非常类似活动中，绑定的服务具有其自己的生命周期和回叫方法与相关联在其生命周期中的重要事件。 以下列表是某些较常用服务将实现的回调方法的示例：

* `OnCreate` &ndash; 通过 Android 调用此方法，因为它实例化服务。 它用于初始化的任何变量或对象的生存期内所需的服务。 此方法是可选的。
* `OnBind` &ndash; 所有绑定的服务必须实现此方法。 第一个客户端尝试连接到服务时，调用此操作。 它将返回的实例`IBinder`，以便客户端可能会与服务交互。 只要服务正在运行，`IBinder`对象将用于满足绑定到服务的任何将来的客户端请求。
* `OnUnbind` &ndash; 具有未绑定的绑定的所有客户端时，调用此方法。 通过返回`true`该服务将更高版本调用此方法中，从`OnRebind`传递到意向`OnUnbind`时将新的客户端绑定到它。 服务继续运行之后未绑定时将执行此操作。 如果最近未绑定的服务也已启动的服务，会发生此情况并`StopService`或`StopSelf`尚未调用。 在此类方案中，`OnRebind`允许检索的意图。 默认值返回`false`，这不执行任何操作。 可选。
* `OnDestroy` &ndash; Android 销毁该服务时，调用此方法。 在这种方法，应执行任何必要的清理，释放资源，例如。 可选。

绑定服务的密钥生命周期事件显示在此图中：

![图示的生命周期方法的调用的顺序](bound-services-images/bound-services-01.png "图示的生命周期方法的调用的顺序。")

附带本指南的配套应用程序中的以下的代码段显示了如何在 Xamarin.Android 中实现的绑定的服务：

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

在示例中，`OnCreate`方法初始化一个对象，保存用于检索和格式设置客户端将请求的时间戳的逻辑。 当第一个客户端尝试绑定到服务时，将调用 Android`OnBind`方法。 此服务将实例化`TimestampBinder`将允许客户端访问正在运行的服务的此实例的对象。 `TimestampBinder`类下一节中所述。

### <a name="implementing-ibinder"></a>实现 IBinder

如前文所述，`IBinder`对象提供客户端和服务之间的通信通道。 Android 应用程序不应实现`IBinder`接口， [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/)应进行扩展。 `Binder`类提供了许多必要的基础结构这是必要的封送到客户端从服务 （这可能会在单独的进程中运行） 联编程序对象。 在大多数情况下，`Binder`子类是只有少量的代码行，并将包装对服务的引用。 在此示例中，`TimestampBinder`具有一个属性，它公开`TimestampService`到客户端：

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

这`Binder`就可以调用该服务; 上的公共方法为例：

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

一次`Binder`已被扩展，它是实现所需`IServiceConnection`将所有内容连接在一起。

### <a name="creating-the-service-connection"></a>创建服务连接

`IServiceConnection`将显示 | 引入 | 公开 | 连接`Binder`到客户端对象。 除了实现`IServiceConnection`接口，类必须扩展`Java.Lang.Object`。 服务连接还应提供客户端可以访问某些方式`Binder`（并因此与其绑定的服务）。

此代码摘自随附的示例项目是一种实现`IServiceConnection`:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

在绑定过程的一部分，将调用 Android`OnServiceConnected`方法，提供`name`要绑定的服务和`binder`，保存对服务本身的引用。 在此示例中，服务连接具有两个属性，如果客户端连接到服务，或不保存对此联编程序和为一个布尔型标志的引用的其中一个。

`OnServiceDisconnected`客户端和服务之间的连接意外丢失或损坏时，才会调用方法。 此方法允许客户端有机会响应对的服务中断。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>启动并绑定到具有目的在于明确指示服务

若要使用的绑定的服务，客户端 （例如活动） 必须实例化实现的对象`Android.Content.IServiceConnection`并调用`BindService`方法。` BindService` 将返回`true`如果该服务所绑定到`false`如果不是。 `BindService` 方法采用三个参数：

* **`Intent`**  &ndash;意图应显式标识要连接到的服务。
* **`IServiceConnection`对象**&ndash;此对象是提供回调方法以在绑定的服务启动和停止时通知客户端的中介。
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) 枚举**&ndash;此参数是一组标志用于系统时，用于绑定对象。 最常使用的值是[ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/)，这将自动启动服务，如果它尚未运行。

以下代码片段示范了如何在使用目的在于明确指示活动中启动绑定的服务：

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> 启动 Android 5.0 （API 级别 21） 它才可以绑定到的目的在于明确指示服务。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>有关服务连接和联编程序的体系结构说明。

某些 OOP 纯粹主义者可能会否决的以前的实现`TimestampBinder`类，如不符合[Demeter 定律](https://en.wikipedia.org/wiki/Law_of_Demeter)其中，最简单窗体中指出"不与陌生人;仅与您的朋友"。 此特定的实现公开具体`TimestampService`给所有客户端类。

严格地说，不需要客户端了解的有关`TimestampService`和公开给客户端的具体类可能会导致应用程序更加脆弱且难以维护它的生存期内。 另一种方法是使用接口公开`GetFormattedTimestamp()`方法，并对通过该服务的代理调用`Binder`（或可能的服务连接类）：  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

此特定示例允许用于在服务本身上调用方法的活动：

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>相关链接

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
