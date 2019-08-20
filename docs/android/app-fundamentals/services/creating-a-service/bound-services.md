---
title: Xamarin 中的绑定服务
description: 绑定服务是一项 Android 服务, 提供客户端-服务器接口, 客户端 (如 Android 活动) 可以与进行交互。 本指南将讨论创建绑定服务所涉及的关键组件以及如何在 Xamarin Android 应用程序中使用它。
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: 6b585783f21cc18112ef766819c9851baac96ef1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644192"
---
# <a name="bound-services-in-xamarinandroid"></a>Xamarin 中的绑定服务

_绑定服务是一项 Android 服务, 提供客户端-服务器接口, 客户端 (如 Android 活动) 可以与进行交互。本指南将讨论创建绑定服务所涉及的关键组件以及如何在 Xamarin Android 应用程序中使用它。_

## <a name="bound-services-overview"></a>绑定服务概述

为客户端提供客户端-服务器接口以与服务直接交互的服务称为_绑定服务_。  可以同时将多个客户端连接到服务的单个实例。 绑定的服务和客户端彼此隔离。 相反, Android 提供一系列中间对象, 这些对象管理两者之间的连接状态。 此状态由实现[`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection)接口的对象维护。  此对象由客户端创建并作为参数传递给[`BindService`](xref:Android.Content.Context.BindService*)方法。 可用于任何[`Android.Content.Context`](xref:Android.Content.Context)对象 (例如活动)。 `BindService` 这是对 Android 操作系统的请求, 用于启动服务并将客户端绑定到它。 有三种方法可以使用`BindService`方法将客户端绑定到服务:

* **服务联编**程序服务绑定器是[`Android.OS.IBinder`](xref:Android.OS.IBinder)实现接口的类。 &ndash; 大多数应用程序将不会直接实现此接口, 而是扩展[`Android.OS.Binder`](xref:Android.OS.Binder)该类。 这是最常用的方法, 适用于服务和客户端位于同一进程中的情况。
* **使用 Messenger**&ndash;此方法适用于在单独的进程中可以存在该服务的情况。 相反, 服务请求通过[`Android.OS.Messenger`](xref:Android.OS.Messenger)在客户端和服务之间进行封送处理。 [`Android.OS.Handler`](xref:Android.OS.Handler)在将`Messenger`处理请求的服务中创建。 这将在其他指南中介绍。
* **使用 Android 接口定义语言 (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl) 是一种高级技术, 不在本指南中介绍。

将客户端绑定到服务后, 这两者之间的通信将通过`Android.OS.IBinder`对象进行。  此对象负责使客户端能够与服务交互的接口。 每个 Xamarin 应用程序都不需要从头开始实现此接口, Android SDK 提供[`Android.OS.Binder`](xref:Android.OS.Binder)类, 它负责在客户端和服务之间对对象进行封送处理所需的大部分代码。

使用服务完成客户端时, 必须通过调用`UnbindService`方法将其从其解除绑定。 最后一台客户端与服务解除绑定后, Android 将停止并释放绑定的服务。

此图说明了活动、服务连接、联编程序和服务之间的相互关系:

![显示服务组件彼此关联的关系图](bound-services-images/bound-services-02.png "显示服务组件彼此关联的关系图。")

本指南将介绍如何扩展`Service`类以实现绑定服务。 它还将介绍如何`IServiceConnection`实现和`Binder`扩展, 以允许客户端与服务进行通信。 示例应用程序附带本指南中，其中包含一个名为 Xamarin.Android 项目使用的一种解决方案 **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** 。 这是一个非常基本的应用程序, 演示如何实现服务, 以及如何将活动绑定到该服务。 绑定服务具有一个非常简单的 API, 该 API 只包含`GetFormattedTimestamp`一种方法, 该方法返回一个字符串, 该字符串告知用户服务启动的时间和运行时间。 应用还允许用户手动取消绑定并绑定到服务。

[![Android 手机上运行的应用程序的屏幕截图](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>实现和使用绑定服务

为了使 Android 应用程序使用绑定的服务, 必须实现三个组件:

1. **扩展`Service`类并实现生命周期回调方法** &ndash;此类将包含执行服务所需工作的代码。 下面将更详细地介绍这一点。
2. **创建一个实现`IServiceConnection`**  &ndash;此接口的类提供回调方法, Android 将调用该方法, 以便在服务连接发生更改时通知客户端, 即客户端已连接或断开连接到服务. 服务连接还将提供对对象的引用, 客户端可使用该对象直接与服务交互。 此引用称为_联编_程序。
3. **创建实现`IBinder`**  联编&ndash;程序实现的类提供客户端用来与服务进行通信的 API。 联编程序可以提供对绑定服务的引用、允许直接调用方法或联编程序可以提供一个客户端 API, 用于封装和隐藏应用程序中的绑定服务。 `IBinder`必须提供远程过程调用所需的代码。 不需要 (或建议) 直接实现`IBinder`接口。 相反, 应用程序应`Binder`扩展提供所需`IBinder`的大多数基本功能的类型。
4. **启动并绑定到服务**&ndash;创建服务连接、联编程序和服务后, Android 应用程序负责启动该服务并将其绑定到它。

以下各部分将更详细地讨论其中的每个步骤。

### <a name="extend-the-service-class"></a>`Service`扩展类

若要使用 Xamarin 创建服务, 需要`Service` [`ServiceAttribute`](xref:Android.App.ServiceAttribute)使用对类进行子类化和修饰。 Xamarin 版本工具使用特性来正确地在应用程序的**androidmanifest.xml**文件中注册服务, 这与活动非常相似, 绑定服务具有自己的生命周期以及与中的重要事件关联的回调方法其生命周期。 下面的列表是服务将实现的一些更常见的回调方法的示例:

* `OnCreate`&ndash;此方法由 Android 调用, 因为它将实例化服务。 它用于初始化服务在生存期内所需的任何变量或对象。 此方法是可选的。
* `OnBind`&ndash;此方法必须由所有绑定服务实现。 它在第一个客户端尝试连接到该服务时调用。 它将返回一个实例`IBinder` , 以便客户端可以与该服务进行交互。 只要服务正在运行, `IBinder`就会使用对象来完成以后要绑定到服务的客户端请求。
* `OnUnbind`&ndash;当所有绑定的客户端都未绑定时, 将调用此方法。 通过从此`true`方法返回, 在新客户端绑定到`OnRebind`服务的情况下, `OnUnbind`服务将在以后调用。 如果服务在未绑定后继续运行, 则可以执行此操作。 如果最近未绑定的服务也是已启动的服务, `StopService`或`StopSelf`未调用, 则会发生这种情况。 在这种情况下`OnRebind` , 允许检索意向。 默认返回`false` , 它不执行任何操作。 可选。
* `OnDestroy`&ndash;当 Android 销毁服务时, 将调用此方法。 在此方法中, 应执行任何必要的清理 (如释放资源)。 可选。

此图显示了绑定服务的关键生命周期事件:

![显示生命周期方法调用顺序的关系图](bound-services-images/bound-services-01.png "显示生命周期方法调用顺序的关系图。")

以下代码片段 (来自本指南附带的配套应用程序) 演示如何在 Xamarin 中实现绑定服务:

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

在此示例中, `OnCreate`方法初始化一个对象, 该对象包含用于检索和格式化客户端请求的时间戳的逻辑。 当第一个客户端尝试绑定到服务时, Android 将调用`OnBind`方法。 此服务将实例化`TimestampBinder`对象, 以允许客户端访问正在运行的服务的此实例。 下一节将讨论该类。`TimestampBinder`

### <a name="implementing-ibinder"></a>实现 IBinder

如前所述, `IBinder`对象提供客户端和服务之间的信道。 Android 应用程序不应实现`IBinder`接口[`Android.OS.Binder`](xref:Android.OS.Binder) , 应扩展。 此`Binder`类提供了许多必要的基础结构, 需要将该服务中的联编程序对象 (可能在单独的进程中运行) 封送到客户端。 在大多数情况下, `Binder`子类只是几行代码, 并包装对服务的引用。 在此示例中`TimestampBinder` , 有一个向客户端`TimestampService`公开的属性:

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

这样`Binder`就可以在服务上调用公共方法; 例如:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

扩展`Binder`后, 必须实现`IServiceConnection`将所有内容连接在一起。

### <a name="creating-the-service-connection"></a>创建服务连接

将显示 | 引入 | 公开 | 将`Binder`对象连接到客户端。 `IServiceConnection` 除了实现`IServiceConnection`接口外, 类还必须扩展`Java.Lang.Object`。 服务连接还应提供客户端可以访问的`Binder`某种方式 (并因此与绑定的服务进行通信)。

此代码来自随附的示例项目, 这是实现`IServiceConnection`的一种可行方法:

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

作为绑定过程的一部分, Android 将调用`OnServiceConnected`方法, 并`name`提供要绑定的服务的和`binder`包含对服务本身的引用的。 在此示例中, 服务连接具有两个属性, 一个用于保存对联编程序的引用, 另一个用于在客户端连接到服务时为的布尔标志。

仅`OnServiceDisconnected`当客户端和服务之间的连接意外丢失或损坏时, 才会调用方法。 此方法允许客户端响应服务中断。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>使用显式意图启动并绑定到服务

若要使用绑定服务, 客户端 (例如活动) 必须实例化实现`Android.Content.IServiceConnection`和`BindService`调用方法的对象。 `BindService`如果服务`true`绑定到, 则将返回, `false`如果不是, 则返回。 `BindService` 方法采用三个参数：

* 目的应显式标识要连接到的服务。 **`Intent`** &ndash;
* **对象`IServiceConnection`**  此对象是一个中介,它提供回调方法,以便在启动和停止绑定&ndash;服务时通知客户端。
* 枚举此参数是在绑定对象时系统使用的一组标志。 **[`Android.Content.Bind`](xref:Android.Content.Bind)** &ndash; 最常使用的值为[`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate), 如果服务尚未运行, 则将自动启动该服务。

下面的代码片段举例说明如何使用显式意图在活动中启动绑定服务:

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
> 从 Android 5.0 (API 级别 21) 开始, 只能通过显式意图绑定到服务。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>有关服务连接和联编程序的体系结构说明。

某些 OOP 纯粹主义者可能会否决`TimestampBinder`类的上一个实现, 因为它违反[迪米特法则](https://en.wikipedia.org/wiki/Law_of_Demeter)这一 , 这是最简单的形式状态 "不与陌生人交谈;仅与朋友交谈。 此特定实现向所有客户`TimestampService`端公开具体的类。

严格地说, 客户端不必知道`TimestampService`具体类并向客户端公开该具体类, 就能使应用程序更脆弱, 并在其生存期内更难维护。 另一种方法是使用接口, 该接口公开`GetFormattedTimestamp()`方法, 并`Binder`通过 (或可能是服务连接类) 对服务进行代理调用:  

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

此特定示例允许活动对服务本身调用方法:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>相关链接

- [Android.App.Service](xref:Android.App.Service)
- [Android.Content.Bind](xref:Android.Content.Bind)
- [Android.Content.Context](xref:Android.Content.Context)
- [Android.Content.IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android.OS.Binder](xref:Android.OS.Binder)
- [Android.OS.IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)
