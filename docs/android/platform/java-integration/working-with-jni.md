---
title: 使用 JNI
description: Xamarin.Android 允许编写 Android 应用中的C#而不是 Java。 多个程序集提供使用 Xamarin.Android 提供的 Java 库，包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll 绑定。 但是，对于每个可能的 Java 库，不提供了绑定，并且提供的绑定可能不会绑定每个 Java 类型和成员。 若要使用未绑定的 Java 类型和成员，可以使用 Java 本机接口 (JNI)。 本文将演示如何使用 JNI 与 Java 类型和成员从 Xamarin.Android 应用程序进行交互。
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 8ad2dde701814c0977e25e6e58272c0aa01ca4ca
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672841"
---
# <a name="working-with-jni"></a>使用 JNI

_Xamarin.Android 允许编写 Android 应用中的C#而不是 Java。多个程序集提供使用 Xamarin.Android 提供的 Java 库，包括 Mono.Android.dll 和 Mono.Android.GoogleMaps.dll 绑定。但是，对于每个可能的 Java 库，不提供了绑定，并且提供的绑定可能不会绑定每个 Java 类型和成员。若要使用未绑定的 Java 类型和成员，可以使用 Java 本机接口 (JNI)。本文将演示如何使用 JNI 与 Java 类型和成员从 Xamarin.Android 应用程序进行交互。_


## <a name="overview"></a>概述

它并不总是需要或不能创建托管可调用包装 (MCW) 来调用 Java 代码。 在许多情况下，"内联"JNI 是完全可接受和用于一次性使用未绑定 Java 成员。 通常是使用 JNI 调用上比以生成整个.jar 绑定一个 Java 类的单个方法简单得多。

Xamarin.Android 提供了`Mono.Android.dll`程序集，提供适用于 Android 的绑定`android.jar`库。 类型和成员中不存在`Mono.Android.dll`中不存在类型和`android.jar`可能由手动将其绑定。 若要将绑定 Java 类型和成员，请使用**Java 本机接口**(**JNI**) 若要查找类型、 读写字段，并调用方法。

在 Xamarin.Android 中的 JNI API 是从概念上讲非常类似于`System.Reflection`在.NET 中的 API： 它可以为您要查找类型和成员名称，通过读取和写入字段值，调用方法和的详细信息。 可以使用 JNI 和`Android.Runtime.RegisterAttribute`自定义特性来声明可以绑定到支持重写的虚方法。 可以将绑定接口，以便它们可以实现在C#。

本文档说明：

-  如何 JNI 引用类型。
-  如何查找、 读取和写入的字段。
-  如何查找和调用方法。
-  如何公开虚方法来从托管代码重写。
-  如何公开接口。



## <a name="requirements"></a>要求

JNI，如通过公开[Android.Runtime.JNIEnv 命名空间](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，可在每个版本的 Xamarin.Android。
若要将绑定 Java 类型和接口，必须使用 Xamarin.Android 4.0 或更高版本。


## <a name="managed-callable-wrappers"></a>托管的可调用包装器

一个**托管可调用包装器**(**MCW**) 是*绑定*Java 类或接口，从而将所有的 JNI 机制因此该客户端C#无需代码担心的 JNI 底层的复杂性。 大多数的`Mono.Android.dll`包含托管的可调用包装器。

托管的可调用包装器有两种用途：

1.  封装 JNI 使用，从而使客户端代码不需要了解的有关底层的复杂性。
1.  让可以子类 Java 类型和实现 Java 接口。

第一个用途是纯粹是为了方便使用和复杂程度的封装，以便使用者具有一套简单、 托管的要使用的类。 这需要使用的各种[JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)成员在本文后面部分所述。 请记住，管理可调用包装器并不是必需&ndash;"内联"JNI 使用是完全可以接受，并可用于一次性使用未绑定 Java 成员。 子类和接口的实现需要使用托管的可调用包装器。



## <a name="android-callable-wrappers"></a>Android 可调用包装器

Android 可调用包装器 (ACW) 是必需的每当需要调用托管的代码; Android 运行时 （图片）需要这些包装器，因为没有方法在运行时与艺术注册类。
(具体而言， [DefineClass](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI 函数不受 Android 运行时。 Android 可调用包装器因此弥补缺少的运行时类型注册支持。）

每当 Android 代码需要执行的虚拟或接口方法重写或实现是在托管代码中，Xamarin.Android 必须提供 Java 代理，以便此方法获取调度到相应的托管类型。 这些 Java 代理类型是具有与托管类型实现相同的构造函数并声明任何重写的基类和接口方法"相同"的基类和 Java 接口列表的 Java 代码。

Android 可调用包装器生成的**monodroid.exe**程序期间[生成过程](~/android/deploy-test/building-apps/build-process.md)，并为 （直接或间接） 继承的所有类型生成[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。



### <a name="implementing-interfaces"></a>实现接口

有时您可能需要实现一个 Android 接口 (如[Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/))。

所有 Android 类和接口扩展[Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)接口; 因此，所有 Android 类型必须实现`IJavaObject`。
Xamarin.Android 利用这一事实&ndash;它使用`IJavaObject`，让 Android Java 代理 （Android 可调用包装） 针对给定托管类型。 因为**monodroid.exe**只寻找`Java.Lang.Object`子类 (必须实现`IJavaObject`)、 生成子类`Java.Lang.Object`为我们提供了一种在托管代码中实现接口方法。 例如：

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {
    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig) {
        // implementation goes here...
    }
    public void OnLowMemory () {
        // implementation goes here...
    }
}
```


### <a name="implementation-details"></a>实现详细信息

*本文的其余部分提供了实现详细信息，如有更改，恕不另行通知*（和此处只是因为开发人员可能会好奇这怎么回事实质上提供）。

例如，假定有以下C#源：

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

**Mandroid.exe**程序将生成以下 Android 可调用包装器：

```java
package mono.samples.helloWorld;

public class HelloAndroid extends android.app.Activity {
    static final String __md_methods;
    static {
        __md_methods =
            "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" +
            "";
        mono.android.Runtime.register (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                HelloAndroid.class,
                __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null",
                "", this, new java.lang.Object[] { });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

请注意保留的基类，并为托管代码中重写每个方法提供了本机方法声明。



### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute

通常情况下，Xamarin.Android 会自动生成包含 ACW; 的 Java 代码这一代基于时的类派生自 Java 类和重写现有的 Java 方法的类和方法名称。 但是，在某些情况下，代码生成是不足够，如下所述：

-   Android 支持操作名称中的布局 XML 特性，例如[android: onClick](https://developer.xamarin.com/api/member/Android.Views.View+IOnClickListener.OnClick/p/Android.Views.View/) XML 特性。 如果指定，增加的视图实例将尝试查找 Java 方法。

-   [Java.io.Serializable](https://developer.android.com/reference/java/io/Serializable.html)接口要求`readObject`和`writeObject`方法。 由于它们不是此接口的成员，因此我们相应的托管的实现不公开这些方法对 Java 代码。

-   [Android.os.Parcelable](https://developer.xamarin.com/api/type/Android.Os.Parcelable/)接口需要实现类必须具有一个静态字段`CREATOR`类型的`Parcelable.Creator`。 生成的 Java 代码需要一些显式字段。 与我们的标准方案，是用 Java 代码的输出字段无法从托管代码。


代码生成不提供解决方案，以生成具有随机名称的任意 Java 方法，因为从 Xamarin.Android 4.2 开始[ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)并[ExportFieldAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportFieldAttribute/)已引入了提供上述方案的解决方案。 这两个属性位于`Java.Interop`命名空间：

-   `ExportAttribute` &ndash; 指定方法名称和其预期的异常类型 （若要为提供显式"抛出"在 Java 中）。 在方法上使用它时，该方法将"导出"生成调度代码对相应 JNI 调用托管方法的 Java 方法。 这可以用于`android:onClick`和`java.io.Serializable`。

-   `ExportFieldAttribute` &ndash; 指定的字段名称。 它驻留在充当字段初始值设定项的方法。 这可以用于`android.os.Parcelable`。

[ExportAttribute](https://developer.xamarin.com/samples/monodroid/ExportAttribute/)示例项目演示了如何使用这些属性。


#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute 故障排除

-   由于缺少打包失败**Mono.Android.Export.dll** &ndash;如果您使用过`ExportAttribute`或`ExportFieldAttribute`您的代码或依赖库中的一些方法，您必须添加**Mono.Android.Export.dll**。 此程序集是隔离，以支持通过 Java 回调代码。 它是独立于**Mono.Android.dll**因为它对应用程序添加了额外的大小。

-   在发布版本`MissingMethodException`发生的导出方法&ndash;中发布版本，`MissingMethodException`发生的导出方法。 （此问题已修复在 Xamarin.Android 的最新版本。）



### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` 和`ExportFieldAttribute`运行时代码可以使用该 Java 提供的功能。 此运行时代码生成的 JNI 方法取决于这些属性通过访问托管的代码。 因此，没有任何现有的 Java 方法的托管的方法绑定;因此，从托管的方法签名生成 Java 方法。

但是，这种情况下不是完全决定的。 最值得注意的是，这是在托管的类型和 Java 类型，如之间的一些高级映射，则返回 true:

-  InputStream
-  OutputStream
-  XmlPullParser
-  XmlResourceParser

如果需要为导出的方法，这些类型`ExportParameterAttribute`必须用于显式提供相应的参数或返回值的类型。



### <a name="annotation-attribute"></a>批注特性

在 Xamarin.Android 4.2 我们转换`IAnnotation`到属性 (System.Attribute) 和添加了的对 Java 包装器中的批注生成的实现类型。

这意味着以下方向更改：

-   绑定生成器生成`Java.Lang.DeprecatedAttribute`从`java.Lang.Deprecated`(虽然它应该是`[Obsolete]`在托管代码中)。

-   这并不意味着，现有`Java.Lang.Deprecated`类会消失。 （如果存在此类使用情况），可以为常用的 Java 对象仍使用这些基于 Java 的对象。 将有`Deprecated`和`DeprecatedAttribute`类。

-   `Java.Lang.DeprecatedAttribute`类标记为`[Annotation]`。 继承自的自定义属性时`[Annotation]`特性，msbuild 任务将生成该自定义属性的 Java 批注 (@Deprecated) 在 Android 可调用包装器 (ACW)。

-   批注无法生成到类、 方法和导出字段 （这是在托管代码中的方法）。

如果未注册包含类 （批注的类本身或包含带批注的成员的类），整个 Java 类不生成源，包括的批注。 对于方法，可以指定`ExportAttribute`获取显式生成并批注的方法。 此外，它不是一项功能"生成"Java 批注类定义。 换而言之，如果定义特定批注的自定义托管的属性，需要添加另一个.jar 库，它包含相应的 Java 批注类。 添加用于定义批注类型的 Java 源代码文件是不够的。 Java 编译器不能像那样**apt**。

此外，以下限制适用：

-   此转换过程不会考虑`@Target`上批注类型的批注到目前为止。

-   到属性的属性无效。 改为使用为属性 getter 或 setter 的属性。



## <a name="class-binding"></a>类绑定

绑定类意味着编写托管可调用包装器，以简化的基础的 Java 类型的调用。

绑定虚拟和抽象方法，以便允许重写从C#需要 Xamarin.Android 4.0。 但是，任何版本的 Xamarin.Android 可以绑定的非虚拟方法、 静态方法或虚方法不支持替代情况下。

绑定通常包含以下各项：

-  一个[要绑定的 Java 类型的句柄 JNI](#_Looking_up_Java_Types)。

-  [JNI 字段 Id 和每个绑定字段的属性](#_Instance_Fields)。

-  [JNI 方法 Id 和每个方法绑定方法](#_Instance_Methods)。

-  如果子类是必需的需要具有类型[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)上使用的类型声明的自定义属性[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)设置为`true`。



### <a name="declaring-type-handle"></a>声明类型句柄

字段和方法查找方法要求其声明的类型引用的对象引用。 按照约定，这将保存在`class_ref`字段：

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

请参阅[JNI 类型引用](#_JNI_Type_References)有关详细信息部分有关`CLASS`令牌。


### <a name="binding-fields"></a>绑定字段

Java 字段公开为C#属性，例如 Java 字段[java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in)绑定为C#属性[Java.Lang.JavaSystem.In](https://developer.xamarin.com/api/property/Java.Lang.JavaSystem.In/)。
此外，由于 JNI 区分静态字段和实例字段，实现属性时使用不同的方法。

字段绑定涉及到三个集的方法：

1.  *获取字段 id*方法。 *获取字段 id*方法负责返回字段句柄*获取字段值*并*设置字段值*方法将使用。 获取字段 id 需要知道声明类型，该字段的名称和[JNI 类型签名](#JNI_Type_Signatures)的字段。

1.  *获取字段值*方法。 这些方法需要字段句柄，并负责从 Java 读取字段的值。
    要使用的方法取决于字段的类型。

1.  *设置字段值*方法。 这些方法需要字段句柄，并且负责编写在 Java 中的字段的值。 要使用的方法取决于字段的类型。


 [静态字段](#_Static_Fields)使用[JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)， `JNIEnv.GetStatic*Field`，并[JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/)方法。

 [实例字段](#_Instance_Fields)使用[JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/)， `JNIEnv.Get*Field`，并[JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/)方法。

例如，静态属性`JavaSystem.In`可以作为实现：

```csharp
static IntPtr in_jfieldID;
public static System.IO.Stream In
{
    get {
        if (in_jfieldId == IntPtr.Zero)
            in_jfieldId = JNIEnv.GetStaticFieldID (class_ref, "in", "Ljava/io/InputStream;");
        IntPtr __ret = JNIEnv.GetStaticObjectField (class_ref, in_jfieldId);
        return InputStreamInvoker.FromJniHandle (__ret, JniHandleOwnership.TransferLocalRef);
    }
}
```

注意:我们将使用[InputStreamInvoker.FromJniHandle](https://developer.xamarin.com/api/member/Android.Runtime.InputStreamInvoker.FromJniHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))要转换到的 JNI 引用`System.IO.Stream`使用的实例，然后我们`JniHandleOwnership.TransferLocalRef`因为[JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/)返回本地引用。

许多[Android.Runtime](https://developer.xamarin.com/api/namespace/Android.Runtime/)类型具有`FromJniHandle`方法会将转换 JNI 引用到所需的类型。



### <a name="method-binding"></a>方法绑定

Java 方法公开为C#方法以及C#属性。 例如，Java 方法[java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean))方法绑定为[Java.Lang.Runtime.RunFinalizersOnExit](https://developer.xamarin.com/api/member/Java.Lang.Runtime.RunFinalizersOnExit/)方法，和[java.lang.Object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass)方法绑定为[Java.Lang.Object.Class](https://developer.xamarin.com/api/property/Java.Lang.Object.Class/)属性。

方法调用是一个两步过程：

1.  *获取方法 id*为要调用的方法。 *获取方法 id*方法负责返回方法调用方法将使用的方法句柄。 获取方法 id 需要知道声明类型，该方法的名称和[JNI 类型签名](#JNI_Type_Signatures)的方法。

1.  调用方法。

就像使用字段，将使用以获取方法 id 并调用该方法的方法的静态方法和实例方法之间存在差异。

[静态方法](#_Static_Methods_1)使用[JNIEnv.GetStaticMethodID()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)查找方法 id，并使用`JNIEnv.CallStatic*Method`系列方法调用。

[实例方法](#_Instance_Methods)使用[JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)查找方法 id，并使用`JNIEnv.Call*Method`和`JNIEnv.CallNonvirtual*Method`系列的调用的方法。

方法绑定是可能不止是方法调用。 方法绑定还包括允许的方法重写 （适用于抽象的并且非最终方法），或实现 （适用于接口方法）。 [支持继承接口](#_Supporting_Inheritance,_Interfaces_1)部分介绍了支持的虚拟方法和接口方法的复杂性。

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>静态方法

绑定的静态方法涉及到使用`JNIEnv.GetStaticMethodID`若要获取的方法句柄，然后使用相应`JNIEnv.CallStatic*Method`方法，具体取决于方法的返回类型。 以下是有关绑定的示例[Runtime.getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime())方法：

```csharp
static IntPtr id_getRuntime;

[Register ("getRuntime", "()Ljava/lang/Runtime;", "")]
public static Java.Lang.Runtime GetRuntime ()
{
    if (id_getRuntime == IntPtr.Zero)
        id_getRuntime = JNIEnv.GetStaticMethodID (class_ref,
                "getRuntime", "()Ljava/lang/Runtime;");

    return Java.Lang.Object.GetObject<Java.Lang.Runtime> (
            JNIEnv.CallStaticObjectMethod  (class_ref, id_getRuntime),
            JniHandleOwnership.TransferLocalRef);
}
```

请注意，我们在静态字段中，存储方法句柄`id_getRuntime`。 这是一种性能优化，因此方法句柄不需要在每次调用上查找。 不需要缓存方法句柄以这种方式。 获取方法句柄后, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)用于调用该方法。 `JNIEnv.CallStaticObjectMethod` 返回`IntPtr`其中包含返回的 Java 实例的句柄。
[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr，JniHandleOwnership）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)用于将 Java 句柄转换为强类型化的对象实例。



#### <a name="non-virtual-instance-method-binding"></a>非虚拟实例方法绑定

绑定`final`实例方法或实例方法不需要重写时，涉及到使用`JNIEnv.GetMethodID`若要获取的方法句柄，然后使用相应`JNIEnv.Call*Method`方法，具体取决于方法的返回类型。 以下是有关绑定的示例`Object.Class`属性：

```csharp
static IntPtr id_getClass;
public Java.Lang.Class Class {
    get {
        if (id_getClass == IntPtr.Zero)
            id_getClass = JNIEnv.GetMethodID (class_ref, "getClass", "()Ljava/lang/Class;");
        return Java.Lang.Object.GetObject<Java.Lang.Class> (
                JNIEnv.CallObjectMethod (Handle, id_getClass),
                JniHandleOwnership.TransferLocalRef);
    }
}
```

请注意，我们在静态字段中，存储方法句柄`id_getClass`。
这是一种性能优化，因此方法句柄不需要在每次调用上查找。 不需要缓存方法句柄以这种方式。 获取方法句柄后, [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)用于调用该方法。 `JNIEnv.CallStaticObjectMethod` 返回`IntPtr`其中包含返回的 Java 实例的句柄。
[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr，JniHandleOwnership）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)用于将 Java 句柄转换为强类型化的对象实例。


### <a name="binding-constructors"></a>绑定的构造函数

构造函数是具有名称的 Java 方法`"<init>"`。 只需与 Java 实例方法一样，`JNIEnv.GetMethodID`用于查找的构造函数的句柄。 与 Java 方法不同[JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/)方法用于调用构造函数方法句柄。 返回值`JNIEnv.NewObject`是 JNI 本地引用：


```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

类绑定通常将子类[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。
子类化时`Java.Lang.Object`、 其他语义派上用场：`Java.Lang.Object`的实例将保留通过 Java 实例的全局引用`Java.Lang.Object.Handle`属性。

1.  `Java.Lang.Object`默认构造函数将分配一个 Java 实例。

1.  如果该类型具有`RegisterAttribute`，并`RegisterAttribute.DoNotGenerateAcw`是`true`，然后实例`RegisterAttribute.Name`通过其默认构造函数创建类型。

1.  否则为[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 对应于`this.GetType`通过其默认构造函数实例化。 在为包创建过程中生成 android 可调用包装器每隔`Java.Lang.Object`为其子类`RegisterAttribute.DoNotGenerateAcw`未设置为`true`。

类型不类绑定，这是预期语义： 实例化`Mono.Samples.HelloWorld.HelloAndroid`C#实例应构造 Java`mono.samples.helloworld.HelloAndroid`实例，其中是生成 Android 可调用包装器。

对于类的绑定，这可能是正确的行为，如果 Java 类型包含一个默认构造函数和/或任何其他构造函数需要调用。 否则，必须执行以下操作提供一个构造函数：

1.  调用[Java.Lang.Object （IntPtr，JniHandleOwnership）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)而不是默认`Java.Lang.Object`构造函数。 需要这些信息来避免创建新的 Java 实例。

1.  检查的值[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)之前创建的任何 Java 实例。 `Object.Handle`属性将具有一个值，而不`IntPtr.Zero`如果 Android 可调用包装器构造在 Java 代码中，并且正在构造的类绑定以包含所创建的 Android 可调用包装器实例。 例如，当 Android 创建`mono.samples.helloworld.HelloAndroid`实例，将创建 Android 可调用包装器，第一个和 Java`HelloAndroid`构造函数将创建一个实例的相应`Mono.Samples.HelloWorld.HelloAndroid`类型，与`Object.Handle`属性将设置为在构造函数执行之前的 Java 实例。

1.  如果当前的运行时类型不相同声明类型，则相应的 Android 可调用包装器的实例必须创建并使用[Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))用于存储返回的句柄[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/)。

1.  如果当前的运行时类型声明的类型相同，然后调用 Java 构造函数，并使用[Object.SetHandle](https://developer.xamarin.com/api/member/Java.Lang.Object.SetHandle/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership))用于存储返回的句柄`JNIEnv.NewInstance`。


例如，考虑[java.lang.Integer(int)](https://developer.android.com/reference/java/lang/Integer.html#Integer(int))构造函数。 此绑定为：

```csharp
// Cache the constructor's method handle for later use
static IntPtr id_ctor_I;

// Need [Register] for subclassing
// RegisterAttribute.Name is always ".ctor"
// RegisterAttribute.Signature is tye JNI type signature of constructor
// RegisterAttribute.Connector is ignored; use ""
[Register (".ctor", "(I)V", "")]
public Integer (int value)
    // 1. Prevent Object default constructor execution
    : base (IntPtr.Zero, JniHandleOwnership.DoNotTransfer)
{
    // 2. Don't allocate Java instance if already allocated
    if (Handle != IntPtr.Zero)
        return;

    // 3. Derived type? Create Android Callable Wrapper
    if (GetType () != typeof (Integer)) {
        SetHandle (
                Android.Runtime.JNIEnv.CreateInstance (GetType (), "(I)V", new JValue (value)),
                JniHandleOwnership.TransferLocalRef);
        return;
    }

    // 4. Declaring type: lookup &amp; cache method id...
    if (id_ctor_I == IntPtr.Zero)
        id_ctor_I = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
    // ...then create the Java instance and store
    SetHandle (
            JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value)),
            JniHandleOwnership.TransferLocalRef);
}
```

[JNIEnv.CreateInstance](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CreateInstance/)方法是帮助程序执行`JNIEnv.FindClass`， `JNIEnv.GetMethodID`， `JNIEnv.NewObject`，并且`JNIEnv.DeleteGlobalReference`从返回的值上`JNIEnv.FindClass`。 有关详细信息，请参阅下一节。

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>支持继承，接口

子类化的 Java 类型或实现 Java 接口需要的新一代[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)(ACWs) 为生成每个`Java.Lang.Object`在打包过程的子类。 通过控制 ACW 代[Android.Runtime.RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自定义属性。

有关C#类型，`[Register]`自定义特性构造函数需要一个参数： [JNI 简化类型引用](#_Simplified_Type_References_1)为相应的 Java 类型。 这允许提供 Java 之间不同的名称和C#。

Xamarin.Android 4.0 之前`[Register]`自定义属性均不可用"alias"现有的 Java 类型。 这是因为 ACW 生成过程会生成 ACWs 为每个`Java.Lang.Object`遇到子类。

Xamarin.Android 4.0 引入了[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)属性。 此属性指示到 ACW 生成过程*跳过*带批注的类型，允许的新托管可调用包装器不会导致 ACWs 正在创建包时生成的声明。 这允许绑定现有的 Java 类型。 例如，考虑以下简单的 Java 类`Adder`，其中包含一种方法， `add`，用于将添加到整数并返回结果：

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

`Adder`无法作为绑定类型：

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public partial class Adder : Java.Lang.Object {
    static IntPtr class_ref = JNIEnv.FindClass ( "mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }
}
partial class ManagedAdder : Adder {
}
```

在这里， `Adder` C#类型*别名* `Adder` Java 类型。 `[Register]`特性用于指定的 JNI 名称`mono.android.test.Adder`Java 类型和`DoNotGenerateAcw`属性用来抑制 ACW 生成。 这将导致为 ACW 新一代`ManagedAdder`类型，可在正确子类`mono.android.test.Adder`类型。 如果`RegisterAttribute.DoNotGenerateAcw`属性未被使用，则 Xamarin.Android 生成过程应已生成一个新`mono.android.test.Adder`Java 类型。 这会导致编译错误，因为`mono.android.test.Adder`类型都会出现两次，在两个单独的文件。



### <a name="binding-virtual-methods"></a>绑定的虚拟方法

`ManagedAdder` 子类 Java`Adder`类型，但它不是特别有趣： C# `Adder`类型未定义任何虚拟方法，因此`ManagedAdder`不能重写任何内容。

绑定`virtual`允许由子类重写的方法需要多项操作需要执行这分为以下两个类别：

1.  **方法绑定**

1.  **方法注册**


#### <a name="method-binding"></a>方法绑定

方法绑定需要添加到两个支持成员C#`Adder`定义： `ThresholdType`，并`ThresholdClass`。

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType`属性返回当前绑定的类型：

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

`ThresholdType` 用于在方法绑定中确定何时应执行虚拟和非虚方法调度。 应始终返回`System.Type`实例与声明对应的C#类型。

##### <a name="thresholdclass"></a>ThresholdClass

`ThresholdClass`属性返回的绑定类型的 JNI 类引用：

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

`ThresholdClass` 在方法绑定中调用时所使用的非虚拟方法。

#### <a name="binding-implementation"></a>绑定实现

方法绑定实现负责的 Java 方法的运行时调用。 它还包含`[Register]`是方法注册的一部分，将在方法注册部分中讨论的自定义特性声明：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }
}
```

`id_add`字段包含要调用的 Java 方法的方法 ID。 `id_add`值从获取`JNIEnv.GetMethodID`，这需要声明类 (`class_ref`)，Java 方法名称 (`"add"`)，和方法的 JNI 签名 (`"(II)I"`)。

获取方法 ID 后,`GetType`进行比较的`ThresholdType`以确定是否需要虚拟或非虚拟调度。 虚拟调度时，必须`GetType`匹配`ThresholdType`，作为`Handle`可能指 Java 分配的子类，这会重写该方法。

时`GetType`不匹配`ThresholdType`，`Adder`子类别 (例如通过`ManagedAdder`)，并且`Adder.Add`如果子类调用，将只调用实现`base.Add`。 这种非虚拟调度情况，这是 where`ThresholdClass`传入。 `ThresholdClass` 指定的 Java 类将提供要调用的方法的实现。



#### <a name="method-registration"></a>方法注册

假设我们有的已更新`ManagedAdder`定义，这会重写`Adder.Add`方法：

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

请记住，`Adder.Add`有`[Register]`自定义属性：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]`自定义特性构造函数接受三个值：

1.  Java 方法的名称`"add"`这种情况下。

1.  JNI 类型签名的方法，`"(II)I"`这种情况下。

1.  *连接器方法*，`GetAddHandler`这种情况下。
    连接器方法将在下文。


前两个参数允许 ACW 生成过程生成一个方法声明重写的方法。 生成 ACW 将包含某些下面的代码：

```csharp
public class ManagedAdder extends mono.android.test.Adder {
    static final String __md_methods;
    static {
        __md_methods = "n_add:(II)I:GetAddHandler\n" +
            "";
        mono.android.Runtime.register (...);
    }
    @Override
    public int add (int p0, int p1) {
        return n_add (p0, p1);
    }
    private native int n_add (int p0, int p1);
    // ...
}
```

请注意，`@Override`方法声明，该委托给`n_`-前缀相同名称的方法。 这确保 Java 代码调用时`ManagedAdder.add`，`ManagedAdder.n_add`将调用，以便重写C#`ManagedAdder.Add`要执行方法。

因此，最重要的问题： 如何将`ManagedAdder.n_add`挂接到`ManagedAdder.Add`？

Java`native`与 Java （Android 运行时） 运行时通过注册方法[JNI RegisterNatives 函数](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734)。
`RegisterNatives` 将遵循结构包含要调用的 Java 方法名称、 JNI 类型签名和函数指针的数组[JNI 调用约定](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715)。
函数指针必须是采用两个指针自变量跟方法参数的函数。 Java`ManagedAdder.n_add`必须通过具有以下 C 原型的函数的实现方法：

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin.Android 不公开`RegisterNatives`方法。 相反，ACW 和 MCW 一起提供的信息需要调用`RegisterNatives`: ACW 包含方法名称和 JNI 类型签名，现在只缺少挂接的函数指针。

这就是*连接器方法*传入。 第三个`[Register]`自定义特性参数是在已注册的类型或不接受任何参数，并返回的已注册类型的基类中定义的方法名称`System.Delegate`。 返回`System.Delegate`又是指具有正确的 JNI 函数签名的方法。 最后，该连接器方法返回的委托*必须*根路径，使 GC 不会收集它，因为委托提供给 Java。

```csharp
#pragma warning disable 0169
static Delegate cb_add;
// This method must match the third parameter of the [Register]
// custom attribute, must be static, must return System.Delegate,
// and must accept no parameters.
static Delegate GetAddHandler ()
{
    if (cb_add == null)
        cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
    return cb_add;
}
// This method is registered with JNI.
static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
{
    Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
    return __this.Add (a, b);
}
#pragma warning restore 0169
```

`GetAddHandler`方法创建`Func<IntPtr, IntPtr, int, int,
int>`委托，是指`n_Add`方法，然后调用[JNINativeWrapper.CreateDelegate](https://developer.xamarin.com/api/member/Android.Runtime.JNINativeWrapper.CreateDelegate/)。
`JNINativeWrapper.CreateDelegate` 将提供的方法包装在 try/catch 块，以便任何未经处理的异常处理，将导致引发[AndroidEvent.UnhandledExceptionRaiser](https://developer.xamarin.com/api/event/Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser/)事件。 结果委托存储在静态`cb_add`变量，以便 GC 不会释放该委托。

最后，`n_Add`方法负责封送到相应的托管类型的 JNI 参数然后委派方法调用。

注意:始终使用`JniHandleOwnership.DoNotTransfer`MCW 获取对使用 Java 实例时。 把它们当作本地引用 (并因此调用`JNIEnv.DeleteLocalRef`) 将中断托管-&gt; Java-&gt;托管堆栈转换。



### <a name="complete-adder-binding"></a>完成 Adder 绑定

完全托管的绑定`mono.android.tests.Adder`类型是：

```csharp
[Register ("mono/android/test/Adder", DoNotGenerateAcw=true)]
public class Adder : Java.Lang.Object {

    static IntPtr class_ref = JNIEnv.FindClass ("mono/android/test/Adder");

    public Adder ()
    {
    }

    public Adder (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    protected override Type ThresholdType {
        get {return typeof (Adder);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

#region Add
    static IntPtr id_add;

    [Register ("add", "(II)I", "GetAddHandler")]
    public virtual int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        if (GetType () == ThresholdType)
            return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
        return JNIEnv.CallNonvirtualIntMethod (Handle, ThresholdClass, id_add, new JValue (a), new JValue (b));
    }

#pragma warning disable 0169
    static Delegate cb_add;
    static Delegate GetAddHandler ()
    {
        if (cb_add == null)
            cb_add = JNINativeWrapper.CreateDelegate ((Func<IntPtr, IntPtr, int, int, int>) n_Add);
        return cb_add;
    }

    static int n_Add (IntPtr jnienv, IntPtr lrefThis, int a, int b)
    {
        Adder __this = Java.Lang.Object.GetObject<Adder>(lrefThis, JniHandleOwnership.DoNotTransfer);
        return __this.Add (a, b);
    }
#pragma warning restore 0169
#endregion
}
```



### <a name="restrictions"></a>限制

编写一种类型时满足以下条件：

1.  子类 `Java.Lang.Object`

1.  具有`[Register]`自定义属性

1.  `RegisterAttribute.DoNotGenerateAcw` 为 `true`


然后 GC 交互类型对于*不得*有可能引用的任何字段`Java.Lang.Object`或`Java.Lang.Object`在运行时的子类。 例如，类型的字段`System.Object`和不允许使用任何接口类型。 不能引用的类型`Java.Lang.Object`允许使用实例，如`System.String`和`List<int>`。 此限制是为了防止 gc 过早对象集合。

如果该类型必须包含一个实例字段，它可以指`Java.Lang.Object`实例，则字段类型必须为`System.WeakReference`或`GCHandle`。



## <a name="binding-abstract-methods"></a>绑定抽象方法

绑定`abstract`方法在很大程度上等同于绑定的虚拟方法。 有只有两个区别：

1.  抽象方法是抽象类。 它仍将保留`[Register]`属性和关联的方法注册，方法绑定只需移动到`Invoker`类型。

1.  非`abstract``Invoker`创建类型的子类的抽象类型。 `Invoker`类型必须重写基类中声明的所有抽象方法和重写的实现是绑定方法的实现，但可以忽略非虚拟调度大小写。


例如，假设上述`mono.android.test.Adder.add`方法已`abstract`。 C#绑定会更改，以便`Adder.Add`了抽象，和一个新`AdderInvoker`将其实现定义的类型`Adder.Add`:

```csharp
partial class Adder {
    [Register ("add", "(II)I", "GetAddHandler")]
    public abstract int Add (int a, int b);

    // The Method Registration machinery is identical to the
    // virtual method case...
}

partial class AdderInvoker : Adder {
    public AdderInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
    }

    static IntPtr id_add;
    public override int Add (int a, int b)
    {
        if (id_add == IntPtr.Zero)
            id_add = JNIEnv.GetMethodID (class_ref, "add", "(II)I");
        return JNIEnv.CallIntMethod (Handle, id_add, new JValue (a), new JValue (b));
    }
}
```

`Invoker`时获取对 Java 创建实例的 JNI 引用，类型才是必需。


## <a name="binding-interfaces"></a>绑定接口

绑定接口是从概念上讲类似于绑定类，其中包含的虚拟方法，但许多细节在细微 （和小） 方面存在差异。 请考虑以下[Java 接口声明](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14):

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

接口绑定都具有两个部分：C#接口定义和调用程序定义的接口。



### <a name="interface-definition"></a>接口定义

C#接口定义必须满足以下要求：

-   接口定义必须具有`[Register]`自定义属性。

-   接口定义必须扩展`IJavaObject interface`。
    如果不这样做将阻止 ACWs 从 Java 接口继承。

-   每个接口方法必须包含`[Register]`属性，用于指定相应的 Java 方法名称、 JNI 签名和连接器方法。

-   连接器方法还必须指定可以位于连接器方法的类型。

绑定时`abstract`和`virtual`内正在注册的类型的继承层次结构中搜索方法，该连接器方法。 接口可具有包含正文，因此这不起作用，因此要求没有方法的指定类型，该值指示连接器方法所在的位置。 在连接器方法字符串中，指定一个冒号之后的类型`':'`，并且必须包含调用程序的类型的程序集限定的类型名称。

接口方法声明为相应的 Java 方法使用的翻译*兼容*类型。 Java 内置类型兼容的类型是相应C#类型，例如 Java`int`是C# `int`。 对于引用类型兼容的类型是可以提供适当的 Java 类型的 JNI 句柄的类型。

接口成员，将不会直接调用由 Java&ndash;将通过调用程序类型间接调用&ndash;以便允许一定程度的灵活性。

Java 进度接口可以是[中声明C#作为](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83):

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

请注意，在上述我们映射 Java`int[]`参数[JavaArray&lt;int&gt;](https://developer.xamarin.com/api/type/Android.Runtime.JavaArray%601/)。
没有必要这样做： 我们可能已经绑定到C# `int[]`，或`IList<int>`，或其他内容完全。 选择的任何类型，则`Invoker`需要能够将其转换为 Java`int[]`调用的类型。


### <a name="invoker-definition"></a>调用程序定义

`Invoker`类型定义必须继承`Java.Lang.Object`、 实现相应的接口，并提供在接口定义中引用的所有连接方法。 还有一个不同于类绑定的更多建议：`class_ref`字段和方法 Id 应为实例成员，非静态成员。

优先使用实例成员的原因都与`JNIEnv.GetMethodID`中 Android 运行时的行为。 （这可能是 Java 的行为; 它尚未测试）。`JNIEnv.GetMethodID`查找来自于实现的接口和未声明的接口的方法时返回 null。 请考虑[java.util.SortedMap&lt;K，V&gt; ](https://developer.android.com/reference/java/util/SortedMap.html) Java 接口，实现[java.util.Map&lt;K，V&gt; ](https://developer.android.com/reference/java/util/Map.html)接口。 映射提供[清除](https://developer.android.com/reference/java/util/Map.html#clear())方法，因此看起来合理`Invoker`SortedMap 的定义将是：

```csharp
// Fails at runtime. DO NOT FOLLOW
partial class ISortedMapInvoker : Java.Lang.Object, ISortedMap {
    static IntPtr class_ref = JNIEnv.FindClass ("java/util/SortedMap");
    static IntPtr id_clear;
    public void Clear()
    {
        if (id_clear == IntPtr.Zero)
            id_clear = JNIEnv.GetMethodID(class_ref, "clear", "()V");
        JNIEnv.CallVoidMethod(Handle, id_clear);
    }
     // ...
}
```

上述将失败，因为`JNIEnv.GetMethodID`将返回`null`查找时`Map.clear`方法通过`SortedMap`类实例。

有两个解决方案： 跟踪每个方法的来源，哪个接口，并具有`class_ref`为每个接口，或保留为实例成员的所有内容，并派生程度最高的类类型，不是接口类型上执行方法查找。 完成后者**Mono.Android.dll**。

该调用程序定义了六个部分： 构造函数中，`Dispose`方法，`ThresholdType`并`ThresholdClass`成员，`GetObject`方法、 接口方法实现和连接器方法实现。



#### <a name="constructor"></a>构造函数

需要查找正在调用的实例的运行时类和实例中存储的运行时类的构造函数`class_ref`字段：

```csharp
partial class IAdderProgressInvoker {
    IntPtr class_ref;
    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref   = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }
}
```

注意:`Handle`属性必须使用在构造函数体内，而不`handle`参数，截止时间 Android v4.0`handle`基构造函数完成执行后参数可能无效。


#### <a name="dispose-method"></a>Dispose 方法

`Dispose`方法需要释放的构造函数中分配的全局引用：

```csharp
partial class IAdderProgressInvoker {
    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }
}
```


#### <a name="thresholdtype-and-thresholdclass"></a>ThresholdType 和 ThresholdClass

`ThresholdType`和`ThresholdClass`成员是相同的类绑定中找到的内容：

```csharp
partial class IAdderProgressInvoker {
    protected override Type ThresholdType {
        get {
            return typeof (IAdderProgressInvoker);
        }
    }
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```


#### <a name="getobject-method"></a>GetObject 方法

一个静态`GetObject`支持所需的方法[Extensions.JavaCast&lt;T&gt;（)](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/):

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```


#### <a name="interface-methods"></a>接口方法

需要有一个实现，调用相应的 Java 方法通过 JNI 接口的每个方法：

```csharp
partial class IAdderProgressInvoker {
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd", "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd, new JValue (JNIEnv.ToJniHandle (values)), new JValue (currentIndex), new JValue (currentSum));
    }
}
```



#### <a name="connector-methods"></a>连接器方法

连接器方法和支持基础结构负责封送处理到相应的 JNI 参数C#类型。 Java`int[]`参数将传递为 JNI `jintArray`，即`IntPtr`中C#。 `IntPtr`必须封送到`JavaArray<int>`为了支持调用C#接口：

```csharp
partial class IAdderProgressInvoker {
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
}
```

如果`int[]`会通过首选`JavaList<int>`，然后[JNIEnv.GetArray()](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetArray/(System.IntPtr%2cAndroid.Runtime.JniHandleOwnership%2cSystem.Type))也可改用：

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

但请注意，`JNIEnv.GetArray`将复制整个数组之间的 Vm，因此对于大型数组这可能导致大量增加的 GC 压力。


### <a name="complete-invoker-definition"></a>完成调用程序定义

[完成 IAdderProgressInvoker 定义](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88):

```csharp
class IAdderProgressInvoker : Java.Lang.Object, IAdderProgress {

    IntPtr class_ref;

    public IAdderProgressInvoker (IntPtr handle, JniHandleOwnership transfer)
        : base (handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass (Handle);
        class_ref = JNIEnv.NewGlobalRef (lref);
        JNIEnv.DeleteLocalRef (lref);
    }

    protected override void Dispose (bool disposing)
    {
        if (this.class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef (this.class_ref);
        this.class_ref = IntPtr.Zero;
        base.Dispose (disposing);
    }

    protected override Type ThresholdType {
        get {return typeof (IAdderProgressInvoker);}
    }

    protected override IntPtr ThresholdClass {
        get {return class_ref;}
    }

    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }

#region OnAdd
    IntPtr id_onAdd;
    public void OnAdd (JavaArray<int> values, int currentIndex, int currentSum)
    {
        if (id_onAdd == IntPtr.Zero)
            id_onAdd = JNIEnv.GetMethodID (class_ref, "onAdd",
                    "([III)V");
        JNIEnv.CallVoidMethod (Handle, id_onAdd,
                new JValue (JNIEnv.ToJniHandle (values)),
                new JValue (currentIndex),
new JValue (currentSum));
    }

#pragma warning disable 0169
    static Delegate cb_onAdd;
    static Delegate GetOnAddHandler ()
    {
        if (cb_onAdd == null)
            cb_onAdd = JNINativeWrapper.CreateDelegate ((Action<IntPtr, IntPtr, IntPtr, int, int>) n_OnAdd);
        return cb_onAdd;
    }

    static void n_OnAdd (IntPtr jnienv, IntPtr lrefThis, IntPtr values, int currentIndex, int currentSum)
    {
        IAdderProgress __this = Java.Lang.Object.GetObject<IAdderProgress>(lrefThis, JniHandleOwnership.DoNotTransfer);
        using (var _values = new JavaArray<int>(values, JniHandleOwnership.DoNotTransfer)) {
            __this.OnAdd (_values, currentIndex, currentSum);
        }
    }
#pragma warning restore 0169
#endregion
}
```



## <a name="jni-object-references"></a>JNI 对象引用

很多 JNIEnv 方法返回*JNI* *的对象引用*，这是类似于`GCHandle`s。 JNI 提供三种不同类型的对象引用： 本地引用、 全局引用和全局的弱引用。 所有这三个表示为`System.IntPtr`，*但*（根据 JNI 函数类型部分中） 不是所有`IntPtr`s 从返回`JNIEnv`方法是引用。 例如， [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)返回`IntPtr`，但它不会返回一个对象引用，它会返回`jmethodID`。 请查阅[JNI 函数文档](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)有关详细信息。

创建本地引用*大多数*引用创建方法。
Android 仅允许有限的数量的本地引用，以在任何给定时间，通常 512 存在。 可以通过删除本地引用[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/)。
与不同的 JNI，并非所有引用 JNIEnv 方法的返回对象的引用返回本地引用;[JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/)返回*全局*引用。 强烈建议快速可以可能是通过构造来删除本地引用[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)围绕对象并指定`JniHandleOwnership.TransferLocalRef`到[Java.Lang.Object (IntPtr处理，JniHandleOwnership 传输）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)构造函数。

创建全局引用[JNIEnv.NewGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewGlobalRef/)并[JNIEnv.FindClass](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/)。
它们可以与销毁[JNIEnv.DeleteGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteGlobalRef/)。
仿真程序具有的限制为 2,000 未完成的全局引用，而硬件设备具有的限制为大约 52,000 全局引用。

Android v2.2 (Froyo) 及更高版本，弱全局引用才可用。 可以使用删除全局的弱引用[JNIEnv.DeleteWeakGlobalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteWeakGlobalRef/(System.IntPtr))。


### <a name="dealing-with-jni-local-references"></a>处理 JNI 本地引用

[JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/)， [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/)， [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/)， [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/)并[JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/)方法返回`IntPtr`其中包含 Java 对象的 JNI 本地引用或`IntPtr.Zero`如果 Java 返回`null`。 由于有限数量的本地 （512 个条目），它是需要确保引用后，可以是在未完成的引用会及时删除。 有三种本地引用可处理的方法： 显式删除它们，创建`Java.Lang.Object`实例，用于保存它们，并使用`Java.Lang.Object.GetObject<T>()`创建针对这些托管的可调用包装器。



### <a name="explicitly-deleting-local-references"></a>显式删除本地引用

[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/)用于删除本地引用。 一旦本地引用已被删除，就不能使用它，因此必须格外小心，确保`JNIEnv.DeleteLocalRef`是通过本地引用的最后一步。

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```



### <a name="wrapping-with-javalangobject"></a>使用 Java.Lang.Object 包装

`Java.Lang.Object` 提供了[Java.Lang.Object （IntPtr 句柄，JniHandleOwnership 传输）](https://developer.xamarin.com/api/constructor/Java.Lang.Object.Object/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)构造函数可以用来包装现有的 JNI 引用它。 [JniHandleOwnership](https://developer.xamarin.com/api/type/Android.Runtime.JniHandleOwnership/)参数确定如何`IntPtr`应视为参数：

-   [JniHandleOwnership.DoNotTransfer](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.DoNotTransfer/) &ndash;创建`Java.Lang.Object`实例中将创建新的全局引用`handle`参数，和`handle`保持不变。
    调用方负责释放到`handle`，如果有必要。

-   [JniHandleOwnership.TransferLocalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash;创建`Java.Lang.Object`实例中将创建新的全局引用`handle`参数，并且`handle`使用删除[JNIEnv.DeleteLocalRef](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.DeleteLocalRef/) . 调用方必须释放`handle`，并且必须使用`handle`构造函数完成执行之后。

-   [JniHandleOwnership.TransferGlobalRef](https://developer.xamarin.com/api/field/Android.Runtime.JniHandleOwnership.TransferLocalRef/) &ndash;创建`Java.Lang.Object`实例将接管其所有权的`handle`参数。 调用方必须释放`handle`。


因为 JNI 方法调用方法返回本地 refs`JniHandleOwnership.TransferLocalRef`通常使用：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

前创建的全局引用将不会释放`Java.Lang.Object`实例进行垃圾回收。 如果可以，则释放该实例将释放全局引用，加快垃圾回收：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```



### <a name="using-javalangobjectgetobjectlttgt"></a>Using Java.Lang.Object.GetObject&lt;T&gt;()

`Java.Lang.Object` 提供了[Java.Lang.Object.GetObject&lt;T&gt;（IntPtr 句柄，JniHandleOwnership 传输）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)方法，可以用来创建指定类型的托管可调用包装器。

类型`T`必须满足以下要求：

1.  `T` 必须是引用类型。

1.  `T` 必须实现`IJavaObject`接口。

1.  如果`T`不是抽象类或接口，然后`T`必须为一个构造函数提供的参数类型`(IntPtr,
    JniHandleOwnership)`。

1.  如果`T`是一个抽象类或接口，有*必须*会*调用程序*可用于`T`。 调用程序是一种非抽象类型，继承`T`或实现`T`，并且具有相同的名称作为`T`与调用程序后缀。 例如，如果 T 是接口`Java.Lang.IRunnable`，然后键入`Java.Lang.IRunnableInvoker`必须存在，并且必须包含所需`(IntPtr,
    JniHandleOwnership)`构造函数。


因为 JNI 方法调用方法返回本地 refs`JniHandleOwnership.TransferLocalRef`通常使用：

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>查找 Java 类型

若要查找的字段或方法中 JNI，字段或方法的声明类型必须查找第一个。 [Android.Runtime.JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String))方法用于查找 Java 类型。 字符串参数是*简化类型引用*或*完整类型引用*Java 类型。 请参阅[JNI 类型参考资料部分](#_JNI_Type_References)有关简化和完整的类型引用的详细信息。

注意:与其他所有不同`JNIEnv`方法可返回对象实例`FindClass`返回全局引用，而不是本地引用。

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>实例字段

通过操作时字段*字段 Id*。 通过获取的字段 Id [JNIEnv.GetFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFieldID/)，这需要在类的字段定义中的字段的名称和[JNI 类型签名](#JNI_Type_Signatures)的字段。

字段 Id 不需要释放，并且有效，只要加载相应的 Java 类型。 （android 不当前不支持卸载类。）

有两个组的操作实例字段的方法： 一个用于读取实例字段，另一个用于编写实例字段。 所有组方法都需要读取或写入的字段值的字段 ID。


### <a name="reading-instance-field-values"></a>读取实例字段值

组用于读取实例字段值的方法遵循命名模式：

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```
其中`*`是字段的类型：

-   [JNIEnv.GetObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetObjectField/) &ndash;读取并不是内置类型，如任何实例字段的值`java.lang.Object`，数组和接口类型。 返回的值是 JNI 本地引用。

-   [JNIEnv.GetBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetBooleanField/) &ndash;读取的值`bool`实例字段。

-   [JNIEnv.GetByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetByteField/) &ndash;读取的值`sbyte`实例字段。

-   [JNIEnv.GetCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetCharField/) &ndash;读取的值`char`实例字段。

-   [JNIEnv.GetShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetShortField/) &ndash;读取的值`short`实例字段。

-   [JNIEnv.GetIntField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetIntField/) &ndash;读取的值`int`实例字段。

-   [JNIEnv.GetLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetLongField/) &ndash;读取的值`long`实例字段。

-   [JNIEnv.GetFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetFloatField/) &ndash;读取的值`float`实例字段。

-   [JNIEnv.GetDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetDoubleField/) &ndash;读取的值`double`实例字段。





### <a name="writing-instance-field-values"></a>正在写入实例字段值

组用于编写实例字段值的方法遵循命名模式：

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

其中*类型*是字段的类型：

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash;编写的并不是内置类型，如任何字段值`java.lang.Object`，数组和接口类型。 `IntPtr`值可能为 JNI 本地引用、 JNI 全局引用、 JNI 弱全局引用，或`IntPtr.Zero`(对于`null`)。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash;的值写入`bool`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash;的值写入`sbyte`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Char)) &ndash;的值写入`char`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash;的值写入`short`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash;的值写入`int`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash;的值写入`long`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Single)) &ndash;的值写入`float`实例字段。

-   [JNIEnv.SetField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetField/(System.IntPtr,System.IntPtr,System.Double)) &ndash;的值写入`double`实例字段。


<a name="_Static_Fields" />

## <a name="static-fields"></a>静态字段

通过操作时的静态字段*字段 Id*。 通过获取的字段 Id [JNIEnv.GetStaticFieldID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFieldID/)，这需要在类的字段定义中的字段的名称和[JNI 类型签名](#JNI_Type_Signatures)的字段。

字段 Id 不需要释放，并且有效，只要加载相应的 Java 类型。 （android 不当前不支持卸载类。）

有两个组的操作的静态字段的方法： 一个用于读取实例字段，另一个用于编写实例字段。 所有组方法都需要读取或写入的字段值的字段 ID。


### <a name="reading-static-field-values"></a>读取静态字段值

组用于读取静态字段值的方法遵循命名模式：

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

其中`*`是字段的类型：

-   [JNIEnv.GetStaticObjectField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticObjectField/) &ndash;读取的任何静态字段，并不是内置类型，如值`java.lang.Object`，数组和接口类型。 返回的值是 JNI 本地引用。

-   [JNIEnv.GetStaticBooleanField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticBooleanField/) &ndash;读取的值`bool`静态字段。

-   [JNIEnv.GetStaticByteField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticByteField/) &ndash;读取的值`sbyte`静态字段。

-   [JNIEnv.GetStaticCharField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticCharField/) &ndash;读取的值`char`静态字段。

-   [JNIEnv.GetStaticShortField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticShortField/) &ndash;读取的值`short`静态字段。

-   [JNIEnv.GetStaticLongField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticLongField/) &ndash;读取的值`long`静态字段。

-   [JNIEnv.GetStaticFloatField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticFloatField/) &ndash;读取的值`float`静态字段。

-   [JNIEnv.GetStaticDoubleField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticDoubleField/) &ndash;读取的值`double`静态字段。



### <a name="writing-static-field-values"></a>正在写入静态字段值

组用于写入静态字段值的方法遵循命名模式：

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

其中*类型*是字段的类型：

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.IntPtr)) &ndash;编写的并不是内置类型，如任何静态字段值`java.lang.Object`，数组和接口类型。 `IntPtr`值可能为 JNI 本地引用、 JNI 全局引用、 JNI 弱全局引用，或`IntPtr.Zero`(对于`null`)。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Boolean)) &ndash;的值写入`bool`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.SByte)) &ndash;的值写入`sbyte`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Char)) &ndash;的值写入`char`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int16)) &ndash;的值写入`short`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int32)) &ndash;的值写入`int`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Int64)) &ndash;的值写入`long`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Single)) &ndash;的值写入`float`静态字段。

-   [JNIEnv.SetStaticField](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.SetStaticField/(System.IntPtr,System.IntPtr,System.Double)) &ndash;的值写入`double`静态字段。


<a name="_Instance_Methods" />

## <a name="instance-methods"></a>实例方法

通过调用实例方法*方法 Id*。 通过获取的方法 Id [JNIEnv.GetMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetMethodID/)，其需要的类型，该方法定义的方法名称中和[JNI 类型签名](#JNI_Type_Signatures)的方法。

方法 Id 不需要释放，并且有效，只要加载相应的 Java 类型。 （android 不当前不支持卸载类。）

有两组用于调用方法的方法： 一个用于几乎，调用方法，一个用于非虚拟调用方法。 这两个组方法需要一个方法 ID 来调用的方法和非虚拟调用还要求您指定应调用哪个类实现。

接口方法内的声明类型; 仅按键查找不能查找来自扩展/继承接口的方法。 请参阅后续绑定接口 / 调用程序实现部分，了解更多详细信息。

在类中声明的任何方法或可以查找任何基类或实现的接口。


### <a name="virtual-method-invocation"></a>虚拟方法调用

组用于调用方法的方法几乎遵循命名模式：

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

其中`*`是该方法的返回类型。

-   [JNIEnv.CallObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallObjectMethod/) &ndash;调用的方法，它会返回非内置类型，如`java.lang.Object`、 数组和接口。 返回的值是 JNI 本地引用。

-   [JNIEnv.CallBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallBooleanMethod/) &ndash;调用的方法可返回`bool`值。

-   [JNIEnv.CallByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallByteMethod/) &ndash;调用的方法可返回`sbyte`值。

-   [JNIEnv.CallCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallCharMethod/) &ndash;调用的方法可返回`char`值。

-   [JNIEnv.CallShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallShortMethod/) &ndash;调用的方法可返回`short`值。

-   [JNIEnv.CallLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash;调用的方法可返回`long`值。

-   [JNIEnv.CallFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallFloatMethod/) &ndash;调用的方法可返回`float`值。

-   [JNIEnv.CallDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallDoubleMethod/) &ndash;调用的方法可返回`double`值。



### <a name="non-virtual-method-invocation"></a>非虚拟方法调用

方法用于调用方法的一非几乎遵循命名模式：

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*`是该方法的返回类型。 非虚拟方法调用通常用于调用虚方法的基方法。

-   [JNIEnv.CallNonvirtualObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualObjectMethod/) &ndash;非虚拟调用的方法，它会返回非内置类型，如`java.lang.Object`、 数组和接口。 返回的值是 JNI 本地引用。

-   [JNIEnv.CallNonvirtualBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod/) &ndash;非虚拟调用的方法可返回`bool`值。

-   [JNIEnv.CallNonvirtualByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualByteMethod/) &ndash;非虚拟调用的方法可返回`sbyte`值。

-   [JNIEnv.CallNonvirtualCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualCharMethod/) &ndash;非虚拟调用的方法可返回`char`值。

-   [JNIEnv.CallNonvirtualShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualShortMethod/) &ndash;非虚拟调用的方法可返回`short`值。

-   [JNIEnv.CallNonvirtualLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualLongMethod/) &ndash;非虚拟调用的方法可返回`long`值。

-   [JNIEnv.CallNonvirtualFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualFloatMethod/) &ndash;非虚拟调用的方法可返回`float`值。

-   [JNIEnv.CallNonvirtualDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod/) &ndash;非虚拟调用的方法可返回`double`值。


<a name="_Static_Methods" />

## <a name="static-methods"></a>静态方法

通过调用静态方法*方法 Id*。 通过获取的方法 Id [JNIEnv.GetStaticMethodID](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.GetStaticMethodID/)，其需要的类型，该方法定义的方法名称中和[JNI 类型签名](#JNI_Type_Signatures)的方法。

方法 Id 不需要释放，并且有效，只要加载相应的 Java 类型。 （android 不当前不支持卸载类。）



### <a name="static-method-invocation"></a>静态方法调用

组用于调用方法的方法几乎遵循命名模式：

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

其中`*`是该方法的返回类型。

-   [JNIEnv.CallStaticObjectMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticObjectMethod/) &ndash;调用的静态方法，它会返回非内置类型，如`java.lang.Object`、 数组和接口。 返回的值是 JNI 本地引用。

-   [JNIEnv.CallStaticBooleanMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticBooleanMethod/) &ndash;调用的静态方法，它将返回`bool`值。

-   [JNIEnv.CallStaticByteMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticByteMethod/) &ndash;调用的静态方法，它将返回`sbyte`值。

-   [JNIEnv.CallStaticCharMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticCharMethod/) &ndash;调用的静态方法，它将返回`char`值。

-   [JNIEnv.CallStaticShortMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticShortMethod/) &ndash;调用的静态方法，它将返回`short`值。

-   [JNIEnv.CallStaticLongMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallLongMethod/) &ndash;调用的静态方法，它将返回`long`值。

-   [JNIEnv.CallStaticFloatMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticFloatMethod/) &ndash;调用的静态方法，它将返回`float`值。

-   [JNIEnv.CallStaticDoubleMethod](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallStaticDoubleMethod/) &ndash;调用的静态方法，它将返回`double`值。


<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI 类型签名

[JNI 类型签名](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432)都[JNI 类型引用](#_JNI_Type_References)（但不简化的类型引用） 的方法除外。 使用方法时，JNI 类型签名是左括号`'('`后, 跟所有类型连接在一起 （不带不分隔开来将以逗号分隔或任何其他内容） 后, 跟右括号的参数的类型引用`')'`，后跟方法返回类型的 JNI 类型引用。

例如，对于给定的 Java 方法：

```java
long f(int n, String s, int[] array);
```

将 JNI 类型签名：

```csharp
(ILjava/lang/String;[I)J
```

一般情况下，它是*强*建议使用`javap`命令，以确定 JNI 签名。 例如，JNI 类型的签名[java.lang.Thread.State.valueOf(String)](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String))方法是"（lang/Ljava/字符串;） Ljava/lang/线程$ 状态;"，而 JNI 键入的签名[java.lang.Thread.State.values](https://developer.android.com/reference/java/lang/Thread.State.html#values)方法是"（) [Ljava/lang/线程$ 状态;"。 尾随分号; 请注意这些*是*JNI 类型签名的一部分。

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>JNI 类型引用

从 Java 类型引用不同 JNI 类型引用。 不能使用完全限定的 Java 类型名称，例如`java.lang.String`使用 JNI，必须改为使用 JNI 变体`"java/lang/String"`或`"Ljava/lang/String;"`，具体取决于上下文; 请参阅下面有关详细信息。
有四种类型的 JNI 类型引用：

-  **built-in**
-  **simplified**
-  **type**
-  **array**


### <a name="built-in-type-references"></a>内置类型的引用

内置类型的引用是用来引用内置值类型的单个字符。 映射为按如下所示：

-  `"B"` 有关`sbyte`。
-  `"S"` 有关`short`。
-  `"I"` 有关`int`。
-  `"J"` 有关`long`。
-  `"F"` 有关`float`。
-  `"D"` 有关`double`。
-  `"C"` 有关`char`。
-  `"Z"` 有关`bool`。
-  `"V"` 有关`void`方法返回类型。


<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>简化的类型引用

简化的类型引用仅可在[JNIEnv.FindClass(string)](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.FindClass/(System.String))。
有两种方法来派生简单的类型引用：

1.  从完全限定的 Java 名称，将为每个`'.'`中的包名称和类型名与之前`'/'`，和每个`'.'`内类型名与`'$'`。

1.  读取输出的`'unzip -l android.jar | grep JavaName'`。


二者会导致 Java 类型[java.lang.Thread.State](https://developer.android.com/reference/java/lang/Thread.State.html)映射到的简化的类型引用`java/lang/Thread$State`。


### <a name="type-references"></a>类型引用

类型引用为内置类型引用或使用简化的类型引用`'L'`前缀和一个`';'`后缀。 Java 类型[java.lang.String](https://developer.android.com/reference/java/lang/String.html)，则简化的类型引用的是`"java/lang/String"`，而类型引用为`"Ljava/lang/String;"`。

与数组类型引用和使用 JNI 签名使用类型引用。

若要获取的类型引用另一种方式是通过读取的输出`'javap -s -classpath android.jar fully.qualified.Java.Name'`。
具体取决于类型涉及到，您可以使用构造函数声明或方法返回类型，以确定 JNI 名称。 例如：

```shell
$ javap -classpath android.jar -s java.lang.Thread.State
Compiled from "Thread.java"
```

```java
public final class java.lang.Thread$State extends java.lang.Enum{
public static final java.lang.Thread$State NEW;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State RUNNABLE;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State BLOCKED;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TIMED_WAITING;
  Signature: Ljava/lang/Thread$State;
public static final java.lang.Thread$State TERMINATED;
  Signature: Ljava/lang/Thread$State;
public static java.lang.Thread$State[] values();
  Signature: ()[Ljava/lang/Thread$State;
public static java.lang.Thread$State valueOf(java.lang.String);
  Signature: (Ljava/lang/String;)Ljava/lang/Thread$State;
static {};
  Signature: ()V
}
```

`Thread.State` 因此，我们可以使用的签名是 Java 枚举类型，`valueOf`方法确定的类型引用为 Ljava/lang/线程$ 状态;。



### <a name="array-type-references"></a>数组类型引用

数组类型引用`'['`JNI 类型引用的前缀。
指定数组时，不能使用简化的类型引用。

例如，`int[]`是`"[I"`，`int[][]`是`"[[I"`，并`java.lang.Object[]`是`"[Ljava/lang/Object;"`。



## <a name="java-generics-and-type-erasure"></a>Java 泛型和类型擦除

*大多数*情况下，通过 JNI，Java 泛型所示*不存在*。
有一些"褶皱，"，但这些褶皱正在 Java 如何与泛型，不使用 JNI 如何查找和调用泛型成员进行交互。

通过 JNI 进行交互时的泛型类型或成员和非泛型类型或成员之间没有差异。 例如，泛型类型[java.lang.Class&lt;T&gt; ](https://developer.android.com/reference/java/lang/Class.html)也是"原始"的泛型类型`java.lang.Class`，这两个具有相同的简化的类型引用， `"java/lang/Class"`。


## <a name="java-native-interface-support"></a>Java 本机接口支持

[Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)是 Jave 本机接口 (JNI) 有关的托管的包装。 内声明的 JNI 函数[Java 本机接口规范](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)中使用，但方法已更改，以删除的显式`JNIEnv*`参数和`IntPtr`而不是使用`jobject`， `jclass`， `jmethodID`，等等。例如，考虑[JNI NewObject 函数](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

这作为公开[JNIEnv.NewObject](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.NewObject/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/)方法：

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

将两个调用之间的转换是相当简单。 在 C 中您必须：

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

C#等效将是：

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

保存在一个 IntPtr 的 Java 对象实例后，可能需要对其执行操作。 您可以使用 JNIEnv 方法，如[ <span class="external">JNIEnv.CallVoidMethod()</span> ](https://developer.xamarin.com/api/member/Android.Runtime.JNIEnv.CallVoidMethod/p/System.IntPtr/System.IntPtr/Android.Runtime.JValue[]/)若要执行此操作，但如果已存在相似之处C#包装器，则你将想要通过 JNI 引用构造一个包装器。 可以通过执行[Extensions.JavaCast <t>（)</t> ](https://developer.xamarin.com/api/member/Android.Runtime.Extensions.JavaCast%7BTResult%7D/p/Android.Runtime.IJavaObject/)扩展方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

此外可以使用[Java.Lang.Object.GetObject <t>（)</t> ](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

此外，所有的 JNI 函数已被修改的删除`JNIEnv*`参数中的每个 JNI 函数存在。


## <a name="summary"></a>总结

直接使用 JNI 处理是一种可怕的体验，应不惜一切代价避免使用。 遗憾的是，它并不总是能够避免;希望本指南适用于 Android 命中与 Mono 的未绑定的 Java 用例时将提供一些帮助。


## <a name="related-links"></a>相关链接

- [SanityTests （示例）](https://developer.xamarin.com/samples/SanityTests/)
- [Java 本机接口规范](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Java 本机接口函数](http://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
