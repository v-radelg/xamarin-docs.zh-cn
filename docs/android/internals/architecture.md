---
title: 体系结构
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: fe0903eca5c907fc104728ca0ad7c676a45a5180
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027914"
---
# <a name="architecture"></a>体系结构

Xamarin Android 应用程序在 Mono 执行环境中运行。
此执行环境与 Android 运行时（ART）虚拟机并行运行。 这两个运行时环境都在 Linux 内核的顶层运行，并向用户代码公开各种 Api，使开发人员能够访问基础系统。 Mono 运行时以 C 语言编写。

您可以使用[System](xref:System)、 [System.IO](xref:System.IO)、 [System.Net](xref:System.Net)和 .net 类库的其余部分来访问基础 Linux 操作系统设备。

在 Android 上, 大多数系统功能 (如音频、图形、OpenGL 和电话服务) 都不能直接用于本机应用程序, 它们只能通过驻留在某个[Java](xref:Java.Lang). * 命名空间或 Android 中的 Android 运行时 Java api 公开。 [Android](xref:Android). * 命名空间。 该体系结构大致如下所示：

[在内核上方和 .NET/Java + 绑定之上的 Mono 和 ART![关系图](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin Android 开发人员通过调入已知的 .NET Api （适用于低级别访问）或使用 Android 命名空间中公开的类（可为公开的 Java Api 提供桥梁）访问操作系统中的各种功能。Android 运行时。

有关 Android 类如何与 Android 运行时类通信的详细信息，请参阅[API 设计](~/android/internals/api-design.md)文档。

## <a name="application-packages"></a>应用程序包

Android 应用程序包是具有*apk*文件扩展名的 ZIP 容器。 Xamarin Android 应用程序包与普通 Android 包具有相同的结构和布局，其中添加了以下内容：

- 应用程序程序集（包含 IL）以未压缩的形式*存储*在*程序集*文件夹中。 在发布过程中，在发布过程中，apk 是*mmap （）* ed 到进程中，并从内存中加载程序集 *。* 这样就可以更快地启动应用程序，因为程序集在执行之前无需提取。  
- *注意：* 程序集位置信息，例如[程序集、位置](xref:System.Reflection.Assembly.Location)和[程序集。](xref:System.Reflection.Assembly.CodeBase)在发布版本中*无法依赖*于基本代码。 它们不作为 distinct filesystem 条目存在，它们没有可用的位置。

- 包含 Mono 运行时的本机库出现在*apk*中。 Xamarin Android 应用程序必须包含适用于或目标 Android 体系结构的本机库，如*armeabi* 、 *armeabi-armeabi-v7a* 、 *x86* 。 Xamarin Android 应用程序不能在平台上运行，除非它包含相应的运行时库。

Xamarin Android 应用程序还包含*android*可调用包装，以允许 android 调入托管代码。

## <a name="android-callable-wrappers"></a>Android 可调用包装器

- **Android 可调用包装**器是一种[JNI](https://en.wikipedia.org/wiki/Java_Native_Interface)桥，在 Android 运行时需要调用托管代码时使用。 可以通过 Android 可调用的包装器来重写虚拟方法，并可以实现 Java 接口。 有关详细信息，请参阅[Java 集成概述](~/android/platform/java-integration/index.md)文档。

<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>托管可调用包装器

托管的可调用包装器是一种 JNI 桥，托管代码在任何时候都需要调用 Android 代码，并为重写虚方法和实现 Java 接口提供支持。 整个[Android](xref:Android). * 和相关命名空间是通过[.jar 绑定](~/android/platform/binding-java-library/index.md)生成的托管可调用包装器。
托管的可调用包装器负责在托管和 Android 类型之间进行转换，并通过 JNI 调用基础 Android 平台方法。

每个创建的托管可调用包装器都包含 Java 全局引用，可通过[IJavaObject](xref:Android.Runtime.IJavaObject.Handle)属性进行访问。 全局引用用于提供 Java 实例与托管实例之间的映射。 全局引用是一种有限的资源：仿真程序一次只允许存在2000的全局引用，而大多数硬件允许一次存在超过52000个全局引用。

若要跟踪创建和销毁全局引用的时间, 可以[debug.mono.log](~/android/troubleshooting/index.md) gref" 系统属性设置为包含 " [gref](~/android/troubleshooting/index.md)"。

全局引用可以通过在托管可调用包装上调用[.java （）](xref:Java.Lang.Object.Dispose)来进行显式释放。 这将删除 Java 实例与托管实例之间的映射，并允许收集 Java 实例。 如果从托管代码重新访问 Java 实例，将为其创建新的托管可调用包装器。

如果在线程之间不小心共享了实例，则在释放托管的可调用包装器时必须小心谨慎，因为释放实例会影响任何其他线程的引用。 为获得最大安全性，只 `Dispose()` 通过 `new`*或*从你*知道*的方法分配新实例而不是缓存实例的实例，这可能会导致线程之间发生意外的实例共享。

## <a name="managed-callable-wrapper-subclasses"></a>托管的可调用包装器子类

托管的可调用包装子类是指所有 "有趣的" 特定于应用程序的逻辑。 其中包括自定义的[Android. Activity](xref:Android.App.Activity)子类（如默认项目模板中的[Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13)类型）。 （具体而言，这些是*不*包含[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)自定义属性或 RegisterAttribute 的任何 *.java 子类。* [DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)为*false*，这是默认值。）

与托管的可调用包装器子类一样，托管的可调用包装器子类也包含全局引用，可通过[.java](xref:Java.Lang.Object.Handle)属性访问。 与托管可调用包装一样，全局引用可以通过调用 .Java 来显式释放[（）](xref:Java.Lang.Object.Dispose)。
与托管可调用包装器不同，处理此类实例之前应*格外小心*，因为*Dispose （）* -实例的 ing 将中断 Java 实例（Android 可调用包装器的实例）和托管实例.

### <a name="java-activation"></a>Java 激活

当从 Java 创建[Android 可调用包装](~/android/platform/java-integration/android-callable-wrappers.md)器（ACW）时，ACW 构造函数将导致调用C#相应的构造函数。 例如， *MainActivity*的 ACW 将包含一个默认构造函数，该构造函数将调用*MainActivity*的默认构造函数。 （这是通过 ACW 构造函数中的*TypeManager （）* 调用来完成的。）

结果有另一个构造函数签名： *（IntPtr，JniHandleOwnership）* 构造函数。 只要向托管代码公开 Java 对象，就会调用 *（IntPtr，JniHandleOwnership）* 构造函数，并且需要构造托管的可调用包装器来管理 JNI 句柄。 通常会自动执行此操作。

在以下两种情况下，必须在托管的可调用包装子类上手动提供 *（IntPtr，JniHandleOwnership）* 构造函数：

1. [Android. 应用程序](xref:Android.App.Application)为子类。 *应用程序*是特殊的;默认*应用程序*构造函数将*永远*不会被调用，[必须提供（IntPtr，JniHandleOwnership）构造函数](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105)。

2. 从基类构造函数中的虚拟方法调用。

请注意，（2）是一个谨防抽象。 在 Java 中，与C#在中一样，对构造函数的虚方法的调用始终调用派生程度最大的方法实现。 例如， [TextView （Context，AttributeSet，int）构造函数](xref:Android.Widget.TextView#ctor*)调用虚拟方法 TextView， [getDefaultMovementMethod （）](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod())被绑定为[TextView 属性](xref:Android.Widget.TextView.DefaultMovementMethod)。
因此，如果类型[LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs)为（1）[子类 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26)，（2）[重写 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)，并（3）[通过 XML 激活该类的实例，](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29)则重写的*DefaultMovementMethod*属性将在 ACW 构造函数有机会执行之前调用，并在C#构造函数有机会执行之前发生。

这种支持的方法是：在 ACW LogTextBox 实例第一次进入托管代码时，通过[LogTextView （IntPtr，JniHandleOwnership）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28)构造函数实例化实例 LogTextBox [int）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41)构造*函数*执行。

事件的顺序：

1. 布局 XML 加载到[ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41)中。

2. Android 实例化布局对象图，并实例化 monodroid 的实例*Apidemo LogTextBox* ，ACW for *LogTextBox* 。

3. *Monodroid. apidemo. LogTextBox*构造函数执行[android. TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29)构造函数。

4. *TextView*构造函数调用*monodroid （）* 。

5. *monodroid* （ *）调用 LogTextBox （），* 后者调用 apidemo （），后者将调用 LogTextBox *（）* ，这会调用[&lt;TextView&gt; （handle，JniHandleOwnership. DoNotTransfer）](xref:Java.Lang.Object.GetObject*) 。

6. *TextView&gt;（）的*将检查是否已存在一个对应C#的*句柄*实例。&lt; 如果有，则返回。 在这种情况下，不存在，因此*Object&lt;t&gt;（）* 必须创建一个。

7. *对象。 GetObject&lt;t&gt;（）* 查找*LogTextBox （IntPtr，JniHandleOwneship）* 构造函数，调用它，创建*句柄*和创建的实例之间的映射，并返回创建的实例。

8. *TextView. n_GetDefaultMovementMethod （）* 调用*LogTextBox*属性 getter。

9. 控件返回到完成执行的*TextView*构造函数。

10. *Monodroid. apidemo. LogTextBox*构造函数执行，调用*TypeManager （）* 。

11. *LogTextBox （Context，IAttributeSet，int）* 构造函数*在（7）中创建的同一个实例上*执行。

12. 如果找不到（IntPtr，JniHandleOwnership）构造函数，则将引发 MissingMethodException] （x： MissingMethodException）。

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>过早释放（）调用

JNI 句柄和对应C#的实例之间存在映射。 .Java （）中断此映射。 如果 JNI 句柄在映射中断后进入托管代码，则它看起来像 Java 激活，并将检查和调用 *（IntPtr，JniHandleOwnership）* 构造函数。 如果构造函数不存在，则将引发异常。

例如，给定以下托管的可调用 Wraper 子类：

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

如果创建实例，请释放它的（），并导致重新创建托管的可调用包装器：

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

该程序将会骰子：

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

如果子类包含 *（IntPtr，JniHandleOwnership）* 构造函数，则将创建该类型的*新*实例。 因此，实例将显示为 "丢失" 所有实例数据，因为它是一个新实例。 （请注意，值为 null。）

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

仅当你知道不会再使用 Java 对象或子类不包含实例数据，并且提供了 *（IntPtr，JniHandleOwnership）* 构造函数时，才会*释放*托管的可调用包装子类。

## <a name="application-startup"></a>应用程序启动

启动活动、服务等时，Android 将首先检查是否已有运行的进程来承载活动/服务/等。如果不存在这样的进程，则将创建一个新进程，读取[androidmanifest.xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html) ，并且加载并实例化在[/manifest/application/@android:name](https://developer.android.com/guide/topics/manifest/application-element.html#nm)特性中指定的类型。 接下来，将实例化[/manifest/application/provider/@android:name](https://developer.android.com/guide/topics/manifest/provider-element.html#nm)特性值指定的所有类型，并调用其[attachInfo %28）](xref:Android.Content.ContentProvider.AttachInfo*)方法。 通过添加 mono 在此中进行 Xamarin 挂钩 *。* 在生成过程中将*ContentProvider* mono.monoruntimeprovider 到 androidmanifest.xml。 *Mono。Mono.monoruntimeprovider. attachInfo （）* 方法负责将 Mono 运行时加载到进程中。
在此点之前使用 Mono 的任何尝试都将失败。 （*注意*：这就是为什么在可以初始化 Mono 之前创建应用程序实例时，[应用](xref:Android.App.Application)程序实例需要提供[（IntPtr，JniHandleOwnership）构造函数](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103)的类型。

完成进程初始化后，请参考 `AndroidManifest.xml` 来查找活动/服务的类名称，等等。 例如， [/manifest/application/activity/@android:name 特性](https://developer.android.com/guide/topics/manifest/activity-element.html#nm)用于确定要加载的活动的名称。 对于活动，此类型必须继承 " [android](xref:Android.App.Activity)"。
指定的类型通过[forName （）](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String))加载（这要求类型为 Java 类型，因此是 Android 可调用包装），然后实例化。 创建 Android 可调用包装器实例将触发创建相应C#类型的实例。 Android 随后将调用[onCreate （捆绑）](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) ，这会导致调用相应的[onCreate （绑定）](xref:Android.App.Activity.OnCreate*) ，并且您已对争用进行了准备。
