---
title: 体系结构
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: ea66cda0e2a1935a430c064c9cebd4134d295729
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669227"
---
# <a name="architecture"></a>体系结构

Mono 的执行环境中运行 Xamarin.Android 应用程序。
此执行环境运行的并行与 Android 运行时 （艺术） 虚拟机。 这两个运行时环境在 Linux 内核顶层运行，并公开到用户代码，允许开发人员访问基础系统的各种 Api。 Mono 运行时是用 C 语言编写的。

你可以使用[系统](xref:System)， [System.IO](xref:System.IO)， [System.Net](xref:System.Net)和其余部分的.net 类库以访问基础 Linux 操作系统功能。

在 Android 上，大部分系统机制，例如音频、 图形、 OpenGL 和电话服务不可用直接到本机应用程序，它们仅公开通过 Android 运行时 Java Api 中的一个驻留[Java](https://developer.xamarin.com/api/namespace/Java.Lang/)。 *命名空间或[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 命名空间。 体系结构是大致如下：

[![关系图的 Mono 和画内核上方和下方.NET/Java + 绑定](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin.Android 开发人员访问操作系统通过调入他们知道的.NET Api （用于低级别的访问权限） 或使用 Android 的 Java api 公开的架起了桥梁的命名空间中公开的类中的各种功能Android 运行时。

Android 类如何与 Android 运行时类进行通信的详细信息请参阅[API 设计](~/android/internals/api-design.md)文档。


## <a name="application-packages"></a>应用程序包

Android 应用程序包是包含 ZIP 容器 *.apk*文件扩展名。 Xamarin.Android 应用程序包具有相同的结构和布局作为常规 Android 包，增加了以下信息：

-   （包含 IL） 的应用程序程序集都*存储*内未压缩*程序集*文件夹。 在过程中启动版本中的生成 *.apk*是*mmap()* ed 到过程和程序集是从内存中加载。 这将允许更快的应用程序启动，因为程序集不需要在执行之前提取。  
-   *注意：* 程序集位置信息，例如[Assembly.Location](xref:System.Reflection.Assembly.Location)并[Assembly.CodeBase](xref:System.Reflection.Assembly.CodeBase)
    *不能依赖*发布版本中。 为不同的文件系统条目不存在，并且必须没有可用的位置。


-   包含 Mono 运行时本机库中有 *.apk* 。 Xamarin.Android 应用程序必须包含本机库所需目标 Android 体系结构，例如*armeabi* ， *armeabi-v7a* ， *x86* 。 Xamarin.Android 应用程序不能在平台上运行，除非它包含相应的运行时库。


Xamarin.Android 应用程序还包含*Android 可调用包装器*为允许 Android 来调入托管代码。



## <a name="android-callable-wrappers"></a>Android 可调用包装器

- **Android 可调用包装器**都[JNI](https://en.wikipedia.org/wiki/Java_Native_Interface)桥用于 Android 运行时需要调用托管的代码的任何时间。 Android 可调用包装器是如何虚拟方法可以重写并且可以实现 Java 接口。 请参阅[Java 集成概述](~/android/platform/java-integration/index.md)文档的详细信息。


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>托管的可调用包装器

托管的可调用包装器是用于托管的代码需要调用 Android 代码，并提供对重写虚拟方法和实现 Java 接口支持的任何时间的 JNI 桥梁。 整个[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 和相关命名空间都是通过生成的托管可调用包装[.jar 绑定](~/android/platform/binding-java-library/index.md)。
托管的可调用包装器负责托管和 Android 的类型之间进行转换和调用 JNI 通过基础的 Android 平台方法。

每个 Java 全局引用，该类可通过创建托管的可调用包装器持有[Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)属性。 全局引用用于提供 Java 实例和托管的实例之间的映射。 全局引用是一种有限的资源： 仿真程序使得仅 2000年全局引用存在一次，而大多数硬件允许超过 52,000 全局引用，以同时存在。

若要跟踪时创建和销毁全局引用，可以设置[debug.mono.log](~/android/troubleshooting/index.md)系统属性以包含[gref](~/android/troubleshooting/index.md)。

全局引用可以显式释放通过调用[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)上托管的可调用包装器。 这将删除 Java 实例和托管的实例之间的映射，并允许收集的 Java 实例。 如果从托管代码重新访问 Java 实例，将为其创建新的托管可调用包装器。

当释放的托管可调用包装器，如果该实例可以在无意中线程之间共享，以释放该实例将会影响任何其他线程的引用，因此必须小心谨慎。 为了最大的安全，仅`Dispose()`的实例都通过已分配`new`*或*方法从在*知道*始终分配新实例并将可能不缓存的实例会导致意外实例线程间共享。



## <a name="managed-callable-wrapper-subclasses"></a>托管可调用包装器子类

托管的可调用包装器子类是可能所在的"有意义"的所有特定于应用程序逻辑。 其中包括自定义[Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)子类 (如[Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13)默认项目模板中的类型)。 (具体而言，这些是任何*Java.Lang.Object*来执行此操作的子类*不*包含[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自定义特性或[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)是*false*，这是默认设置。)

如管理托管的可调用包装可调用包装器子类还包含全局引用，可通过访问[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)属性。 就像与托管的可调用包装器，通过调用全局引用可以显式释放操作[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)。
与托管的可调用包装器，不同*谨慎*应为释放的这种情况下前, 采取*dispose （)* 定义是该实例将中断 Java 实例之间的映射 (的实例Android 可调用包装器） 和托管的实例。


### <a name="java-activation"></a>Java 激活

当[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 创建从 Java，ACW 构造函数将导致相应 C# 要调用构造函数。 例如，对于 ACW *MainActivity*将包含默认构造函数将调用*MainActivity*的默认构造函数。 (这是通过*TypeManager.Activate()* ACW 构造函数内调用。)

没有结果的一个其他构造函数签名： *（IntPtr，JniHandleOwnership）* 构造函数。 *（IntPtr，JniHandleOwnership）* 每当 Java 对象公开给托管代码和托管的可调用包装器需要构造管理 JNI 句柄调用构造函数。 这通常是自动完成。

有两种情况下， *（IntPtr，JniHandleOwnership）* 上托管的可调用包装器子类必须手动提供构造函数：

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/)子类化。 *应用程序*是特殊; 默认值*应用程序*构造函数将*永远不会*调用，和[（IntPtr，JniHandleOwnership） 必须改为提供构造函数](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. 从基类构造函数的虚拟方法调用。


请注意，(2) 是一个有漏洞的抽象。 在 Java 中，与 C# 中，从一个构造函数调用虚拟方法始终调用派生程度最高的方法实现。 例如， [TextView (上下文，AttributeSet，int) 构造函数](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/)调用虚拟方法[TextView.getDefaultMovementMethod()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod())，该绑定为[TextView.DefaultMovementMethod 属性](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/)。
因此，如果某种[LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs)为 (1) 已[子类 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26)，(2)[重写 TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)，和 (3)[激活它的实例类的 XML，通过](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29)重写*DefaultMovementMethod*之前 ACW 构造函数有机会执行时，它会发生之前将调用属性C#机会到构造函数执行。

这通过实例化实例 LogTextBox 支持通过[LogTextView （IntPtr，JniHandleOwnership）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28)构造函数时 ACW LogTextBox 实例首次进入托管代码中，然后再调用[(上下文，IAttributeSet，int) LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41)构造函数*同一个实例上*ACW 构造函数执行时。

事件的发生顺序：

1.  布局 XML 加载到[ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41)。

2.  Android 实例化布局的对象图，并实例化的实例*monodroid.apidemo.LogTextBox* ，为 ACW *LogTextBox* 。

3.  *Monodroid.apidemo.LogTextBox*构造函数执行[android.widget.TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29)构造函数。

4.  *TextView*构造函数调用*monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* 。

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* 调用*LogTextBox.n_getDefaultMovementMethod()* ，这样就可调用*TextView.n_GetDefaultMovementMethod()* ，这样就可调用[Java.Lang.Object.GetObject&lt;TextView&gt; （处理 JniHandleOwnership.DoNotTransfer）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) 。

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;（)* 检查，以查看是否已经存在相应的 C# 实例*处理*。 如果不存在，则返回它。 在此方案中，不存在，这样*Object.GetObject&lt;T&gt;（)* 必须先创建一个。

7.  *Object.GetObject&lt;T&gt;（)* 寻找*LogTextBox （IntPtr，JniHandleOwneship）* 构造函数，对其进行调用，之间创建映射*处理*和创建的实例，并返回所创建的实例。

8.  *TextView.n_GetDefaultMovementMethod()* 调用*LogTextBox.DefaultMovementMethod*属性 getter。

9.  控制权将返回给*android.widget.TextView*构造函数，完成执行。

10. *Monodroid.apidemo.LogTextBox*构造函数执行时，调用*TypeManager.Activate()* 。

11. *LogTextBox (上下文，IAttributeSet，int)* 构造函数执行 *(7) 中创建的同一实例上*。

12. 如果 （IntPtr，JniHandleOwnership） 找不到构造函数，则将引发 System.MissingMethodException](xref:System.MissingMethodException)。

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>过早 dispose （） 调用

没有 JNI 句柄和对应的 C# 实例之间的映射。 Java.Lang.Object.Dispose() 将中断此映射。 如果映射已中断后，JNI 句柄将进入托管的代码，它类似于 Java 激活并 *（IntPtr，JniHandleOwnership）* 构造函数将检查并调用。 如果构造函数不存在，将引发异常。

例如，给定以下托管可调用 Wraper 子类：

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

如果我们创建一个实例，dispose （），并且会导致托管可调用包装器以重新创建：

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

该程序会死亡：

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

如果包含子类 *（IntPtr，JniHandleOwnership）* 构造函数，则*新*将创建类型的实例。 因此，实例将显示"丢失"所有实例数据，因为它是一个新实例。 （请注意的值为 null）。

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

仅*dispose （)* 的当您知道将不再，使用 Java 对象或子类不包含任何实例数据和托管可调用包装器子类 *（IntPtr，JniHandleOwnership）* 提供了构造函数。



## <a name="application-startup"></a>应用程序启动

当一种活动、 服务时，等启动时，Android 将首先检查以查看是否已运行托管活动/service/等的进程。如果不存在任何此类进程，则将创建一个新进程， [AndroidManifest.xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html)是读取，和中指定的类型[ /manifest/application/@android:name ](https://developer.android.com/guide/topics/manifest/application-element.html#nm)加载和实例化属性。 接下来，通过指定的所有类型[ /manifest/application/provider/@android:name ](https://developer.android.com/guide/topics/manifest/provider-element.html#nm)属性值进行实例化，并且具有其[ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/)调用方法。 通过添加到此 Xamarin.Android 挂钩*mono。MonoRuntimeProvider* *ContentProvider*到在生成过程中的 AndroidManifest.xml。 *Mono。MonoRuntimeProvider.attachInfo()* 方法负责加载到进程中的 Mono 运行时。
在此点之前使用 Mono 的任何尝试将失败。 (*注意*:这就是为什么类型的子类[Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/)需要提供[（IntPtr，JniHandleOwnership） 构造函数](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103)，可以初始化 Mono 之前创建的应用程序实例。)

完成过程初始化后，`AndroidManifest.xml`参考查找以启动活动/service/等的类名称。 例如， [ /manifest/application/activity/@android:name属性](https://developer.android.com/guide/topics/manifest/activity-element.html#nm)用于确定要加载的活动的名称。 为活动，此类型必须继承[android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)。
通过加载指定的类型[Class.forName()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (这需要的类型为 Java 类型，因此 Android 可调用包装器)，然后实例化。 Android 可调用包装器实例的创建将触发相应的 C# 类型的实例的创建。 Android 将随后调用[Activity.onCreate(Bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) ，这将导致相应[Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)被调用，就可以关闭到的资源争用。
