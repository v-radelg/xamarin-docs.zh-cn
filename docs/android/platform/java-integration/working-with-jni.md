---
title: 使用 JNI 和 Xamarin
description: Xamarin 允许编写带C#而不是 Java 的 Android 应用。 Xamarin 随附了多个程序集，其中提供了 Java 库（包括 GoogleMaps）的绑定。 但是，不会为每个可能的 Java 库提供绑定，而提供的绑定可能不绑定每个 Java 类型和成员。 若要使用未绑定的 Java 类型和成员，可以使用 Java 本机接口（JNI）。 本文说明如何使用 JNI 与 Xamarin 应用程序中的 Java 类型和成员进行交互。
ms.prod: xamarin
ms.assetid: A417DEE9-7B7B-4E35-A79C-284739E3838E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4d4274770263b120e856cf8db01a71f7ed124a63
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027186"
---
# <a name="working-with-jni-and-xamarinandroid"></a>使用 JNI 和 Xamarin

_Xamarin 允许编写带有C# Instead of Java 的 Android 应用。Xamarin 随附了多个程序集，其中提供了 Java 库（包括 GoogleMaps）的绑定。但是，不会为每个可能的 Java 库提供绑定，而提供的绑定可能不绑定每个 Java 类型和成员。若要使用未绑定的 Java 类型和成员，可以使用 Java 本机接口（JNI）。本文说明如何使用 JNI 与 Xamarin 应用程序中的 Java 类型和成员进行交互。_

## <a name="overview"></a>概述

创建托管的可调用包装器（MCW）时，并不总是有必要或无法创建调用 Java 代码。 在许多情况下，"内联" JNI 完全可接受，适用于非绑定 Java 成员的一次性使用。 通常，使用 JNI 在 Java 类上调用单个方法比生成整个 .jar 绑定更为简单。

Xamarin 提供 `Mono.Android.dll` 程序集，该程序集为 Android 的 `android.jar` 库提供绑定。 在 `android.jar` 中不存在的 `Mono.Android.dll` 和类型中不存在的类型和成员可以通过手动绑定来使用。 若要绑定 Java 类型和成员，请使用**Java 本机接口**（**JNI**）查找类型、读取和写入字段，以及调用方法。

Xamarin 中的 JNI API 在概念上非常类似于 .NET 中的 `System.Reflection` API：它使你可以按名称、读取和写入字段值、调用方法等来查找类型和成员。 您可以使用 JNI 和 `Android.Runtime.RegisterAttribute` 自定义属性声明可绑定以支持重写的虚拟方法。 可以绑定接口，以便可以在中C#实现这些接口。

本文档介绍：

- JNI 如何引用类型。
- 如何查找、读取和写入字段。
- 如何查找和调用方法。
- 如何公开虚拟方法以允许从托管代码重写。
- 如何公开接口。

## <a name="requirements"></a>要求

JNI 通过[JNIEnv 命名空间](xref:Android.Runtime.JNIEnv)公开，适用于每个版本的 Xamarin。
若要绑定 Java 类型和接口，必须使用 Xamarin 4.0 或更高版本。

## <a name="managed-callable-wrappers"></a>托管可调用包装器

托管的可**调用包装**（**MCW**）是一个 Java 类或接口的*绑定*，它封装了所有 JNI 机械，使客户端C#代码无需担心 JNI 的底层复杂性。 大多数 `Mono.Android.dll` 都包含托管的可调用包装器。

托管的可调用包装器有两个用途：

1. 封装 JNI 使用，以便客户端代码不需要了解底层复杂性。
1. 使其成为子类 Java 类型并实现 Java 接口。

第一个目的是专门为便利性和复杂程度的封装，以便使用者可以使用一组简单、托管的类。 这需要使用各种[JNIEnv](xref:Android.Runtime.JNIEnv)成员，如本文后面所述。 请记住，托管的可调用包装并不是绝对必要的 &ndash; "内联" JNI 使用完全可接受，适用于非绑定 Java 成员的一次性使用。 子分类和接口实现需要使用托管可调用包装器。

## <a name="android-callable-wrappers"></a>Android 可调用包装器

Android 可调用包装器（ACW）是每次需要调用托管代码时需要的;这些包装是必需的，因为无法在运行时用 ART 注册类。
（具体而言，Android 运行时不支持[DefineClass](https://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/functions.html#wp15986) JNI 函数。 因此，Android 可调用包装器使缺少运行时类型注册支持。）

只要 Android 代码需要执行在托管代码中重写或实现的虚拟或接口方法，Xamarin 就必须提供 Java 代理，以便将此方法调度到适当的托管类型。 这些 Java 代理类型是 Java 代码，它具有 "相同" 基类和 Java 接口列表作为托管类型，实现相同的构造函数和声明任何重写的基类和接口方法。

Android 可调用的包装在[生成过程](~/android/deploy-test/building-apps/build-process.md)中由**monodroid**程序生成，并为（直接或间接）继承了[.java](xref:Java.Lang.Object)的所有类型生成。

### <a name="implementing-interfaces"></a>实现接口

有时，你可能需要实现 Android 接口（如[IComponentCallbacks](xref:Android.Content.IComponentCallbacks)）的。

所有 Android 类和接口都扩展了[IJavaObject](xref:Android.Runtime.IJavaObject)接口;因此，所有 Android 类型都必须实现 `IJavaObject`。
Xamarin 利用这一事实 &ndash; 它使用 `IJavaObject` 为给定托管类型的 Java 代理（Android 可调用包装器）提供 Android。 由于**monodroid**仅查找 `Java.Lang.Object` 子类（必须实现 `IJavaObject`），因此，子类化 `Java.Lang.Object` 为我们提供了一种在托管代码中实现接口的方法。 例如:

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

*本文的剩余部分提供了在不通知的情况下随时更改的实现详细信息*（仅在此处提供，因为开发人员可能会对即将发生的事情感到好奇）。

例如，给定以下C#源：

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

**Mandroid**程序将生成以下 Android 可调用包装器：

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

请注意，将保留基类，并为托管代码中重写的每个方法提供本机方法声明。

### <a name="exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute

通常，Xamarin 会自动生成包含 ACW 的 Java 代码;当类派生自 Java 类并重写现有 Java 方法时，此代基于类和方法名称。 但是，在某些情况下，代码生成是不够的，如下所述：

- Android 支持布局 XML 特性中的操作名称，例如[android： onClick](xref:Android.Views.View.IOnClickListener.OnClick*) xml 特性。 如果指定此设置，放大的视图实例将尝试查找 Java 方法。

- [Java. Serializable](https://developer.android.com/reference/java/io/Serializable.html)接口需要 `readObject` 和 `writeObject` 方法。 由于它们不是此接口的成员，因此，对应的托管实现不会向 Java 代码公开这些方法。

- [Parcelable](xref:Android.OS.Parcelable)接口要求实现类必须具有类型 `Parcelable.Creator`的静态字段 `CREATOR`。 生成的 Java 代码需要一些显式字段。 对于我们的标准方案，无法从托管代码在 Java 代码中输出字段。

由于代码生成并不提供用于生成任意名称的任意 Java 方法的解决方案，因此从 Xamarin 4.2 开始，引入了[ExportAttribute](xref:Java.Interop.ExportAttribute)和[ExportFieldAttribute](xref:Java.Interop.ExportFieldAttribute)来提供上述解决方案各种. 这两个属性都位于 `Java.Interop` 命名空间中：

- `ExportAttribute` &ndash; 指定方法名称及其预期的异常类型（在 Java 中给予显式 "引发"）。 在方法上使用该方法时，该方法会 "导出" 一个 Java 方法，该方法将生成一个调度代码，以便向托管方法调用相应的 JNI 调用。 这可用于 `android:onClick` 和 `java.io.Serializable`。

- `ExportFieldAttribute` &ndash; 指定字段名称。 它驻留在用作字段初始值设定项的方法上。 这可以与 `android.os.Parcelable`一起使用。

[ExportAttribute](https://docs.microsoft.com/samples/xamarin/monodroid-samples/exportattribute)示例项目演示了如何使用这些属性。

#### <a name="troubleshooting-exportattribute-and-exportfieldattribute"></a>ExportAttribute 和 ExportFieldAttribute 疑难解答

- 由于缺少 &ndash; 的 `ExportAttribute` 或 `ExportFieldAttribute` 在代码或依赖库中的某些方法上使用或，因此打包失败。**导出** **.dll。** 此程序集是独立的，支持 Java 的回调代码。 它独立于**Mono** ，因为它将增加应用程序的大小。

- 在发布版本中，对于在发布版本中 &ndash; 的导出方法 `MissingMethodException` 发生，`MissingMethodException` 导出方法发生。 （此问题已在最新版本的 Xamarin 中解决。）

### <a name="exportparameterattribute"></a>ExportParameterAttribute

`ExportAttribute` 和 `ExportFieldAttribute` 提供 Java 运行时代码可以使用的功能。 此运行时代码通过由这些特性驱动的生成的 JNI 方法访问托管代码。 因此，托管方法不会绑定现有 Java 方法;因此，Java 方法是从托管方法签名生成的。

但是，这种情况并不完全决定。 最值得注意的是，在托管类型和 Java 类型（如）之间的一些高级映射中，这一点很重要。

- InputStream
- OutputStream
- XmlPullParser
- XmlResourceParser

如果导出的方法需要这样的类型，则必须使用 `ExportParameterAttribute` 显式为相应的参数或返回值指定类型。

### <a name="annotation-attribute"></a>Annotation 特性

在 Xamarin 4.2 中，我们将 `IAnnotation` 实现类型转换为特性（system.string），并添加了对 Java 包装中的注释生成的支持。

这表示以下方向变化：

- 绑定生成器从 `java.Lang.Deprecated` 生成 `Java.Lang.DeprecatedAttribute` （在托管代码中应 `[Obsolete]`）。

- 这并不意味着现有 `Java.Lang.Deprecated` 类将会消失。 这些基于 Java 的对象仍可用作常用 Java 对象（如果存在此类用法）。 将有 `Deprecated` 类和 `DeprecatedAttribute` 类。

- `Java.Lang.DeprecatedAttribute` 类被标记为 `[Annotation]`。 如果存在从该 `[Annotation]` 特性继承的自定义特性，则 msbuild 任务将为 Android 可调用包装器（ACW）中的该自定义属性（@Deprecated）生成 Java 批注。

- 可以在类、方法和导出的字段（托管代码中的方法）上生成注释。

如果未注册包含类（带批注的类本身或包含批注成员的类），则不会生成整个 Java 类源（包括批注）。 对于方法，你可以指定 `ExportAttribute` 以显式生成和批注该方法。 此外，它并不是 "生成" Java 批注类定义的一项功能。 换而言之，如果为某个批注定义了一个自定义托管特性，则必须添加另一个包含相应 Java 批注类的 .jar 库。 添加定义批注类型的 Java 源文件是不够的。 Java 编译器的工作方式与**apt**相同。

此外，还存在以下限制：

- 到目前为止，此转换过程并不考虑批注类型 `@Target` 批注。

- 属性不起作用。 请改用属性 getter 或 setter 的属性。

## <a name="class-binding"></a>类绑定

绑定类意味着编写托管的可调用包装器以简化对基础 Java 类型的调用。

绑定虚拟和抽象方法以允许从中C#重写需要 Xamarin 4.0。 但是，任何版本的 Xamarin 都可以绑定非虚拟方法、静态方法或虚方法，而不支持重写。

绑定通常包含以下项：

- [要绑定的 Java 类型的 JNI 句柄](#_Looking_up_Java_Types)。

- [JNI 每个绑定字段的字段 id 和属性](#_Instance_Fields)。

- [每个绑定方法的 JNI 方法 id 和方法](#_Instance_Methods)。

- 如果需要子分类，则类型需要在类型声明上具有[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)自定义特性，并将[RegisterAttribute](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)设置为 `true`。

### <a name="declaring-type-handle"></a>声明类型句柄

字段和方法查找方法需要引用其声明类型的对象引用。 按照约定，此操作保存在 `class_ref` 字段中：

```csharp
static IntPtr class_ref = JNIEnv.FindClass(CLASS);
```

有关 `CLASS` 令牌的详细信息，请参阅[JNI Type 引用](#_JNI_Type_References)部分。

### <a name="binding-fields"></a>绑定字段

Java 字段作为C#属性公开，例如，java 字段[java.lang.System.in](https://developer.android.com/reference/java/lang/System.html#in)绑定为C#属性[Java.Lang.JavaSystem.In](xref:Java.Lang.JavaSystem.In)。
此外，由于 JNI 在静态字段和实例字段之间进行区分，因此在实现属性时使用不同的方法。

字段绑定涉及三组方法：

1. *获取字段 id*方法。 *获取字段 id*方法负责返回*获取字段值*和*设置字段值*方法将使用的字段句柄。 获取字段 id 需要知道声明类型、字段名称和字段的[JNI 类型签名](#JNI_Type_Signatures)。

1. *获取字段值*方法。 这些方法需要字段句柄，负责从 Java 读取字段的值。
    要使用的方法取决于字段的类型。

1. *设置字段值*方法。 这些方法需要字段句柄，负责在 Java 中写入字段的值。 要使用的方法取决于字段的类型。

[静态字段](#_Static_Fields)使用[JNIEnv](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)、`JNIEnv.GetStatic*Field`和[SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)方法。

 [实例字段](#_Instance_Fields)使用[JNIEnv](xref:Android.Runtime.JNIEnv.GetFieldID*)、`JNIEnv.Get*Field`和[SetField](xref:Android.Runtime.JNIEnv.SetField*)方法。

例如，可以将静态属性 `JavaSystem.In` 实现为：

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

注意:我们使用[InputStreamInvoker](xref:Android.Runtime.InputStreamInvoker.FromJniHandle*)将 JNI 引用转换为 `System.IO.Stream` 实例，并且我们正在使用 `JniHandleOwnership.TransferLocalRef`，因为[JNIEnv](xref:Android.Runtime.JNIEnv.GetStaticObjectField*)返回了本地引用。

许多[Android. 运行时](xref:Android.Runtime)类型都有 `FromJniHandle` 方法，这些方法会将 JNI 引用转换为所需的类型。

### <a name="method-binding"></a>方法绑定

Java 方法作为C#方法和C#属性公开。 例如, Java 方法[java.lang.Runtime.runFinalizersOnExit](https://developer.android.com/reference/java/lang/Runtime.html#runFinalizersOnExit(boolean)) 方法被绑定为 [Java.Lang.Runtime.RunFinalizersOnExit](xref:Java.Lang.Runtime.RunFinalizersOnExit*) 方法, 并将方法绑定为方法, 并将[java.lang.Object.getClass](https://developer.android.com/reference/java/lang/Object.html#getClass) 方法绑定为[Java.Lang.Object.Class](xref:Java.Lang.Object.Class)方法的对象, 并将其属性。

方法调用分为两个步骤：

1. 要调用的方法的*get 方法 id* 。 *Get 方法 id*方法负责返回方法调用方法将使用的方法句柄。 获取方法 id 需要知道声明类型、方法的名称以及方法的[JNI 类型签名](#JNI_Type_Signatures)。

1. 调用方法。

与字段一样，用于获取方法 id 并调用方法的方法在静态方法和实例方法之间有所不同。

[静态方法](#_Static_Methods_1)使用[JNIEnv. GetStaticMethodID （）](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)查找方法 id，并使用 `JNIEnv.CallStatic*Method` 系列方法进行调用。

[实例方法](#_Instance_Methods)使用[JNIEnv](xref:Android.Runtime.JNIEnv.GetMethodID*)查找方法 id，并使用方法的 `JNIEnv.Call*Method` 和 `JNIEnv.CallNonvirtual*Method` 系列来进行调用。

方法绑定可能不仅仅是方法调用。 方法绑定还包括允许重写方法（对于抽象方法和非最终方法）或实现（对于接口方法）。 [支持继承、接口](#_Supporting_Inheritance,_Interfaces_1)部分涵盖了支持虚拟方法和接口方法的复杂性。

<a name="_Static_Methods_1" />

#### <a name="static-methods"></a>静态方法

绑定静态方法涉及使用 `JNIEnv.GetStaticMethodID` 获取方法句柄，然后使用适当的 `JNIEnv.CallStatic*Method` 方法，具体取决于方法的返回类型。 下面是[getRuntime](https://developer.android.com/reference/java/lang/Runtime.html#getRuntime())方法的绑定示例：

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

请注意，我们将方法句柄存储在静态字段中，`id_getRuntime`。 这是一种性能优化，因此不需要在每次调用时都查找方法句柄。 不需要以这种方式缓存方法句柄。 获取方法句柄后，JNIEnv 将使用[CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)来调用方法。 `JNIEnv.CallStaticObjectMethod` 返回一个 `IntPtr`，其中包含返回的 Java 实例的句柄。
使用[&lt;t&gt;（IntPtr，JniHandleOwnership）](xref:Java.Lang.Object.GetObject*)将 java 句柄转换为强类型对象实例。

#### <a name="non-virtual-instance-method-binding"></a>非虚拟实例方法绑定

绑定 `final` 实例方法或不需要重写的实例方法，需要使用 `JNIEnv.GetMethodID` 获取方法句柄，然后根据方法的返回类型使用适当的 `JNIEnv.Call*Method` 方法。 下面是 `Object.Class` 属性的绑定示例：

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

请注意，我们将方法句柄存储在静态字段中，`id_getClass`。
这是一种性能优化，因此不需要在每次调用时都查找方法句柄。 不需要以这种方式缓存方法句柄。 获取方法句柄后，JNIEnv 将使用[CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*)来调用方法。 `JNIEnv.CallStaticObjectMethod` 返回一个 `IntPtr`，其中包含返回的 Java 实例的句柄。
使用[&lt;t&gt;（IntPtr，JniHandleOwnership）](xref:Java.Lang.Object.GetObject*)将 java 句柄转换为强类型对象实例。

### <a name="binding-constructors"></a>绑定构造函数

构造函数是 `"<init>"`名称的 Java 方法。 与 Java 实例方法一样，`JNIEnv.GetMethodID` 用于查找构造函数句柄。 与 Java 方法不同， [JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*)方法用于调用构造函数方法句柄。 `JNIEnv.NewObject` 的返回值为 JNI 本地引用：

```csharp
int value = 42;
IntPtr class_ref    = JNIEnv.FindClass ("java/lang/Integer");
IntPtr id_ctor_I    = JNIEnv.GetMethodID (class_ref, "<init>", "(I)V");
IntPtr lrefInstance = JNIEnv.NewObject (class_ref, id_ctor_I, new JValue (value));
// Dispose of lrefInstance, class_ref…
```

通常，类绑定会将[.java](xref:Java.Lang.Object)子类。
`Java.Lang.Object`子类时，会出现一个额外的语义： `Java.Lang.Object` 实例通过 `Java.Lang.Object.Handle` 属性维护对 Java 实例的全局引用。

1. `Java.Lang.Object` 的默认构造函数将分配一个 Java 实例。

1. 如果该类型具有 `RegisterAttribute`，并且 `RegisterAttribute.DoNotGenerateAcw` `true`，则 `RegisterAttribute.Name` 类型的实例是通过其默认构造函数创建的。

1. 否则，与 `this.GetType` 相对应的[Android 可调用包装](~/android/platform/java-integration/android-callable-wrappers.md)器（ACW）通过其默认构造函数进行实例化。 在包创建过程中，将为 `RegisterAttribute.DoNotGenerateAcw` 未设置为 `true`的每个 `Java.Lang.Object` 子类生成 Android 可调用包装。

对于不是类绑定的类型，这是预期的语义：实例化 `Mono.Samples.HelloWorld.HelloAndroid` C#实例应构造一个 Java `mono.samples.helloworld.HelloAndroid`实例，该实例是生成的 Android 可调用包装器。

对于类绑定，如果 Java 类型包含默认构造函数和/或不需要调用其他构造函数，则这可能是正确的行为。 否则，必须提供构造函数来执行以下操作：

1. 调用 JniHandleOwnership [（IntPtr，）](xref:Java.Lang.Object#ctor*)而不是默认的 `Java.Lang.Object` 构造函数。 这是避免创建新的 Java 实例所必需的。

1. 请在创建任何 Java 实例之前检查 " [.java](xref:Java.Lang.Object.Handle) " 的值。 如果在 Java 代码中构造了 Android 可调用包装器，并且构造了类绑定以包含创建的 Android 可调用包装器实例，则 `Object.Handle` 属性将具有 `IntPtr.Zero` 以外的值。 例如，当 Android 创建 `mono.samples.helloworld.HelloAndroid` 实例时，将首先创建 Android 可调用包装，并且 Java `HelloAndroid` 构造函数将创建相应 `Mono.Samples.HelloWorld.HelloAndroid` 类型的实例，并将 `Object.Handle` 属性设置为 Java 实例在执行构造函数之前。

1. 如果当前运行时类型与声明类型不相同，则必须创建相应的 Android 可调用包装器的实例，并使用[SetHandle](xref:Java.Lang.Object.SetHandle*)来存储[JNIEnv](xref:Android.Runtime.JNIEnv.CreateInstance*)返回的句柄。

1. 如果当前运行时类型与声明类型相同，则调用 Java 构造函数并使用[SetHandle](xref:Java.Lang.Object.SetHandle*)存储 `JNIEnv.NewInstance` 返回的句柄。

例如，请考虑[.java （int）](https://developer.android.com/reference/java/lang/Integer.html#Integer(int))构造函数。 此限制如下：

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

[JNIEnv](xref:Android.Runtime.JNIEnv.CreateInstance*)方法用于对从 `JNIEnv.FindClass`返回的值执行 `JNIEnv.FindClass`、`JNIEnv.GetMethodID`、`JNIEnv.NewObject`和 `JNIEnv.DeleteGlobalReference`。 有关详细信息，请参阅下一节。

<a name="_Supporting_Inheritance,_Interfaces_1" />

### <a name="supporting-inheritance-interfaces"></a>支持继承，接口

在打包过程中，为每个 `Java.Lang.Object` 子类生成的[Android 可调用包装](~/android/platform/java-integration/android-callable-wrappers.md)（ACWs）是为 java 类型或实现 java 接口的子类。 ACW 生成通过[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)自定义特性进行控制。

对于C#类型，`[Register]`自定义特性构造函数需要一个参数：对应 Java 类型的[JNI 简化类型引用](#_Simplified_Type_References_1)。 这允许在 Java 和C#之间提供不同的名称。

在 Xamarin 4.0 之前，`[Register]` 自定义属性不可用于 "alias" 现有 Java 类型。 这是因为，ACW 生成过程将为遇到的每个 `Java.Lang.Object` 子类生成 ACWs。

Xamarin 4.0 引入了[RegisterAttribute. DoNotGenerateAcw](xref:Android.Runtime.RegisterAttribute.DoNotGenerateAcw)属性。 此属性指示 ACW 生成过程*跳过*注释类型，允许声明新的托管可调用包装器，而不会导致在包创建时生成 ACWs。 这允许绑定现有 Java 类型。 例如，请考虑以下简单的 Java 类，`Adder`，其中包含一个方法 `add`，该方法将添加到整数并返回结果：

```java
package mono.android.test;
public class Adder {
    public int add (int a, int b) {
        return a + b;
    }
}
```

`Adder` 类型可以绑定为：

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

此处，`Adder` C#类型将*别名*`Adder`Java 类型。 `[Register]` 特性用于指定 `mono.android.test.Adder` Java 类型的 JNI 名称，`DoNotGenerateAcw` 属性用于阻止生成 ACW。 这将导致为 `ManagedAdder` 类型生成 ACW，这会正确地将 `mono.android.test.Adder` 类型为子类。 如果未使用 `RegisterAttribute.DoNotGenerateAcw` 属性，则 Xamarin 生成过程将生成新的 `mono.android.test.Adder` Java 类型。 这会导致编译错误，因为在两个单独的文件中，`mono.android.test.Adder` 类型会出现两次。

### <a name="binding-virtual-methods"></a>绑定虚方法

`ManagedAdder` 为 Java `Adder` 类型的子类，但这并不是特别C#有意思：`Adder`类型不定义任何虚方法，因此`ManagedAdder`不能覆盖任何内容。

绑定 `virtual` 方法允许子类重写需要执行几项操作，这些操作分为以下两个类别：

1. **方法绑定**

1. **方法注册**

#### <a name="method-binding"></a>方法绑定

方法绑定要求将两个支持成员添加到C#`Adder`定义：`ThresholdType`和`ThresholdClass`。

##### <a name="thresholdtype"></a>ThresholdType

`ThresholdType` 属性返回绑定的当前类型：

```csharp
partial class Adder {
    protected override System.Type ThresholdType {
        get {
            return typeof (Adder);
        }
    }
}
```

在方法绑定中使用 `ThresholdType`，以确定何时应执行虚方法与非虚方法调度。 它应始终返回一个与声明C#类型相对应的 `System.Type` 实例。

##### <a name="thresholdclass"></a>ThresholdClass

`ThresholdClass` 属性返回绑定类型的 JNI 类引用：

```csharp
partial class Adder {
    protected override IntPtr ThresholdClass {
        get {
            return class_ref;
        }
    }
}
```

调用非虚方法时在方法绑定中使用 `ThresholdClass`。

#### <a name="binding-implementation"></a>绑定实现

方法绑定实现负责 Java 方法的运行时调用。 它还包含作为方法注册一部分的 `[Register]` 自定义属性声明，并将在 "方法注册" 一节中讨论：

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

`id_add` 字段包含要调用的 Java 方法的方法 ID。 `id_add` 值是从 `JNIEnv.GetMethodID`获取的，它需要声明类（`class_ref`）、Java 方法名称（`"add"`）和方法的 JNI 签名（`"(II)I"`）。

获取方法 ID 后，`GetType` 将与 `ThresholdType` 进行比较，以确定是否需要虚拟或非虚拟调度。 如果 `GetType` 匹配 `ThresholdType`，则需要虚拟调度，因为 `Handle` 可能会引用 Java 分配的子类，该子类会重写方法。

当 `GetType` 与 `ThresholdType`不匹配时，`Adder` 已划分为子类（例如 `ManagedAdder`），并且仅当调用的子类时才会调用 `Adder.Add` 实现。 这是非虚拟调度事例，其中 `ThresholdClass` 进入。 `ThresholdClass` 指定哪个 Java 类将提供要调用的方法的实现。

#### <a name="method-registration"></a>方法注册

假设我们有一个更新的 `ManagedAdder` 定义，该定义将覆盖 `Adder.Add` 方法：

```csharp
partial class ManagedAdder : Adder {
    public override int Add (int a, int b) {
        return (a*2) + (b*2);
    }
}
```

请记住，`Adder.Add` 具有 `[Register]` 自定义属性：

```csharp
[Register ("add", "(II)I", "GetAddHandler")]
```

`[Register]` 自定义特性构造函数接受三个值：

1. Java 方法的名称，在这种情况下 `"add"`。

1. 方法的 JNI 类型签名，在这种情况下 `"(II)I"`。

1. 在此示例中， *connector 方法*`GetAddHandler`。
    稍后将讨论连接器方法。

前两个参数允许 ACW 生成过程生成方法声明以重写方法。 生成的 ACW 将包含以下代码：

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

请注意，将声明一个 `@Override` 方法，该方法委托给同名的 `n_`前缀方法。 这可确保在 Java 代码调用 `ManagedAdder.add`时，`ManagedAdder.n_add` 将被调用，这将允许执行C#重写的`ManagedAdder.Add`方法。

因此，最重要的问题： `ManagedAdder.n_add` 如何挂钩到 `ManagedAdder.Add`？

Java `native` 方法通过[JNI RegisterNatives 函数](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp17734)向 Java （Android 运行时）运行时注册。
`RegisterNatives` 采用包含 Java 方法名称、JNI 类型签名的结构的数组，并使用[JNI 调用约定](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/design.html#wp715)调用的函数指针。
函数指针必须是一个函数，该函数使用后跟方法参数的两个指针参数。 Java `ManagedAdder.n_add` 方法必须通过具有以下 C 原型的函数实现：

```csharp
int FunctionName(JNIEnv *env, jobject this, int a, int b)
```

Xamarin 不公开 `RegisterNatives` 方法。 相反，ACW 和 MCW 一起提供调用 `RegisterNatives`所需的信息： ACW 包含方法名称和 JNI 类型签名，则唯一缺少的是要挂钩的函数指针。

这是*连接器方法*的来源。 第三个 `[Register]` 自定义特性参数是在注册类型中定义的方法的名称，或者不接受任何参数并返回 `System.Delegate`的已注册类型的基类。 返回的 `System.Delegate` 依次引用具有正确 JNI 函数签名的方法。 最后，连接器方法返回的委托*必须*为根，以便 GC 不会收集该委托，因为向 Java 提供委托。

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

`GetAddHandler` 方法创建一个 `Func<IntPtr, IntPtr, int, int,
int>` 委托，该委托引用 `n_Add` 方法，然后调用[JNINativeWrapper. system.delegate.createdelegate](xref:Android.Runtime.JNINativeWrapper.CreateDelegate*)。
`JNINativeWrapper.CreateDelegate` 将所提供的方法包装在 try/catch 块中，以便处理任何未经处理的异常，并导致引发[AndroidEvent UnhandledExceptionRaiser](xref:Android.Runtime.AndroidEnvironment.UnhandledExceptionRaiser)事件。 生成的委托存储在静态 `cb_add` 变量中，以便 GC 不会释放该委托。

最后，`n_Add` 方法负责将 JNI 参数封送到相应的托管类型，然后委托方法调用。

注意:在通过 Java 实例获取 MCW 时，请始终使用 `JniHandleOwnership.DoNotTransfer`。 将它们视为本地引用（并因此调用 `JNIEnv.DeleteLocalRef`）会中断托管&gt; Java&gt; 托管堆栈转换。

### <a name="complete-adder-binding"></a>完成 "完成" 绑定

`mono.android.tests.Adder` 类型的完整托管绑定为：

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

写入与以下条件匹配的类型时：

1. 子类 `Java.Lang.Object`

1. 具有 `[Register]` 自定义属性

1. `RegisterAttribute.DoNotGenerateAcw` 为 `true`

然后，对于 GC 交互，该类型*不*能具有在运行时可能引用 `Java.Lang.Object` 或 `Java.Lang.Object` 子类的任何字段。 例如，不允许使用 `System.Object` 类型的字段和任何接口类型。 允许使用不引用 `Java.Lang.Object` 实例的类型，如 `System.String` 和 `List<int>`。 此限制是为了防止 GC 提前收集对象。

如果类型必须包含可引用 `Java.Lang.Object` 实例的实例字段，则字段类型必须为 `System.WeakReference` 或 `GCHandle`。

## <a name="binding-abstract-methods"></a>绑定抽象方法

绑定 `abstract` 方法与绑定虚拟方法大致相同。 只有两个不同之处：

1. Abstract 方法是抽象的。 它仍保留 `[Register]` 特性和关联的方法注册，方法绑定只会移动到 `Invoker` 类型。

1. 创建了一个子类抽象类型的非 `abstract` `Invoker` 类型。 `Invoker` 类型必须重写基类中声明的所有抽象方法，而重写的实现是方法绑定实现，尽管可以忽略非虚拟调度事例。

例如，假定 `abstract`上述 `mono.android.test.Adder.add` 方法。 C#绑定将更改，以便 `Adder.Add`是抽象的，并且将定义`Adder.Add`实现的新`AdderInvoker`类型：

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

仅当获取对 Java 创建的实例的 JNI 引用时 `Invoker`，才需要此类型。

## <a name="binding-interfaces"></a>绑定接口

绑定接口在概念上类似于包含虚拟方法的绑定类，但很多细节在微妙（而不是微妙）方面有所不同。 请考虑以下[Java 接口声明](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/Adder.java#L14)：

```csharp
public interface Progress {
    void onAdd(int[] values, int currentIndex, int currentSum);
}
```

接口绑定包含两部分： C#接口定义和接口的调用程序定义。

### <a name="interface-definition"></a>接口定义

C#接口定义必须满足以下要求：

- 接口定义必须具有 `[Register]` 自定义属性。

- 接口定义必须扩展 `IJavaObject interface`。
    如果不这样做，会阻止 ACWs 从 Java 接口继承。

- 每个接口方法都必须包含一个 `[Register]` 属性，该属性指定相应的 Java 方法名称、JNI 签名和连接器方法。

- 连接器方法还必须指定连接器方法可以找到的类型。

绑定 `abstract` 和 `virtual` 方法时，将在要注册的类型的继承层次结构中搜索连接器方法。 接口不能包含包含主体的方法，因此，这不起作用，因此，需要指定一个类型来指示连接器方法所在的位置。 该类型在连接器方法字符串中指定，在冒号 `':'`之后，必须是包含调用程序的类型的程序集限定类型名称。

接口方法声明是使用*兼容*类型的对应 Java 方法的翻译。 对于 Java 内置类型，兼容类型为相应C#的类型，例如 Java `int`是C#`int`的。 对于引用类型，兼容类型是一种类型，它可以提供适当 Java 类型的 JNI 句柄。

接口成员将不会由 Java 直接调用 &ndash; 调用将通过调用程序类型进行经过调谐 &ndash; 因此允许一定程度的灵活性。

可以[在中C#声明 Java 进度接口，如下所](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L83)示：

```csharp
[Register ("mono/android/test/Adder$Progress", DoNotGenerateAcw=true)]
public interface IAdderProgress : IJavaObject {
    [Register ("onAdd", "([III)V",
            "GetOnAddHandler:Mono.Samples.SanityTests.IAdderProgressInvoker, SanityTests, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null")]
    void OnAdd (JavaArray<int> values, int currentIndex, int currentSum);
}
```

请注意，我们将 Java `int[]` 参数映射到[JavaArray&lt;int&gt;](xref:Android.Runtime.JavaArray`1)。
这并不是必需的：我们可以将其C#绑定到`int[]`、`IList<int>`或其他内容。 无论选择哪种类型，`Invoker` 都需要能够将其转换为 Java `int[]` 类型以便调用。

### <a name="invoker-definition"></a>调用程序定义

`Invoker` 类型定义必须继承 `Java.Lang.Object`，实现相应的接口，并提供接口定义中引用的所有连接方法。 还有一个与类绑定不同的建议： `class_ref` 字段和方法 Id 应该是实例成员，而不是静态成员。

首选实例成员的原因与 Android 运行时中的 `JNIEnv.GetMethodID` 行为有关。 （这可能是 Java 行为，但尚未经过测试。）查找来自已实现接口而不是已声明接口的方法时，`JNIEnv.GetMethodID` 将返回 null。 请考虑[SortedMap&lt;k，v&gt;](https://developer.android.com/reference/java/util/SortedMap.html) java 接口，该接口实现[util&lt;K，v&gt;](https://developer.android.com/reference/java/util/Map.html)接口。 Map 提供了一个[明确](https://developer.android.com/reference/java/util/Map.html#clear())的方法，因此 SortedMap 的看似合理的 `Invoker` 定义如下：

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

上述方法将失败，因为在通过 `SortedMap` 类实例查找 `Map.clear` 方法时 `JNIEnv.GetMethodID` 将返回 `null`。

此方法有两个解决方案：跟踪每个方法来自哪个接口，并为每个接口提供一个 `class_ref`，或将所有内容保持为实例成员，并对派生程度最高的类类型（而不是接口类型）执行方法查找。 后者是在**Mono**中完成的。

调用程序定义包含六个部分：构造函数、`Dispose` 方法、`ThresholdType` 和 `ThresholdClass` 成员、`GetObject` 方法、接口方法实现和连接器方法实现。

#### <a name="constructor"></a>构造函数

构造函数需要查找正在调用的实例的运行时类，并将运行时类存储在实例 `class_ref` 字段中：

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

注意:`Handle` 属性必须在构造函数主体中使用，而不能在 `handle` 参数中使用，因为在 Android v2.0 上，`handle` 参数在基构造函数完成执行后可能会无效。

#### <a name="dispose-method"></a>Dispose 方法

`Dispose` 方法需要释放在构造函数中分配的全局引用：

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

`ThresholdType` 和 `ThresholdClass` 成员与类绑定中的内容相同：

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

需要静态 `GetObject` 方法才能支持[JavaCast&lt;t&gt;（）](xref:Android.Runtime.Extensions.JavaCast*)：

```csharp
partial class IAdderProgressInvoker {
    public static IAdderProgress GetObject (IntPtr handle, JniHandleOwnership transfer)
    {
        return new IAdderProgressInvoker (handle, transfer);
    }
}
```

#### <a name="interface-methods"></a>接口方法

接口的每个方法都需要具有实现，这将通过 JNI 调用相应的 Java 方法：

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

连接器方法和支持基础结构负责将 JNI 参数封送到适当C#的类型。 Java `int[]` 参数将作为 JNI `jintArray`进行传递，这是中C#的 `IntPtr`。 必须将 `IntPtr` 封送到 `JavaArray<int>`，以便支持调用C#接口：

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

如果 `int[]` 优先于 `JavaList<int>`，则可以改为使用[GetArray （）](xref:Android.Runtime.JNIEnv.GetArray*) ：

```csharp
int[] _values = (int[]) JNIEnv.GetArray(values, JniHandleOwnership.DoNotTransfer, typeof (int));
```

但请注意，`JNIEnv.GetArray` 会在 Vm 之间复制整个数组，因此对于大型阵列，这可能会导致大量的垃圾回收压力。

### <a name="complete-invoker-definition"></a>完成调用方定义

[完整的 IAdderProgressInvoker 定义](https://github.com/xamarin/monodroid-samples/blob/master/SanityTests/ManagedAdder.cs#L88)：

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

许多 JNIEnv 方法返回*JNI* *对象引用*，它们类似于 `GCHandle`。 JNI 提供了三种不同类型的对象引用：局部引用、全局引用和弱全局引用。 所有三个均表示为 `System.IntPtr`，*但*根据 JNI 函数类型部分，不是从 `JNIEnv` 方法返回的所有 `IntPtr`都是引用。 例如， [JNIEnv](xref:Android.Runtime.JNIEnv.GetMethodID*)返回 `IntPtr`，但它不返回对象引用，而是返回一个 `jmethodID`。 有关详细信息，请参阅[JNI 函数文档](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)。

本地引用由*大多数*引用创建方法创建。
Android 只允许在任何给定时间存在有限数量的本地引用，通常为512。 可以通过 DeleteLocalRef 删除本地引用[JNIEnv](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)。
与 JNI 不同，返回对象引用的所有引用 JNIEnv 方法返回的都是局部引用;[JNIEnv FindClass](xref:Android.Runtime.JNIEnv.FindClass*)返回*全局*引用。 强烈建议您尽可能快地删除本地引用，这可能是通过构造[对象周围的](xref:Java.Lang.Object)JniHandleOwnership 并指定对 .java 的 `JniHandleOwnership.TransferLocalRef` [（IntPtr 句柄，传输）](xref:Java.Lang.Object#ctor*)构造.

全局引用由 NewGlobalRef 和[JNIEnv](xref:Android.Runtime.JNIEnv.FindClass*)创建[JNIEnv](xref:Android.Runtime.JNIEnv.NewGlobalRef*) 。
可以通过[JNIEnv](xref:Android.Runtime.JNIEnv.DeleteGlobalRef*)销毁它们。
仿真程序限制为2000个未完成的全局引用，而硬件设备的限制为大约52000个全局引用。

弱全局引用仅适用于 Android v2.0 （Froyo）及更高版本。 可以通过 DeleteWeakGlobalRef 删除弱全局引用[JNIEnv](xref:Android.Runtime.JNIEnv.DeleteWeakGlobalRef*)。

### <a name="dealing-with-jni-local-references"></a>处理 JNI 本地引用

The [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*), [JNIEnv.GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*), [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*), [JNIEnv.CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) 和 [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) 方法返回一个`IntPtr`包含对 java 对象的 JNI 本地引用; 如果返回了 java `null`，则为`IntPtr.Zero`。 由于可以同时完成的本地引用的数量有限（512个条目），因此最好确保引用及时删除。 可以通过三种方式来处理本地引用：显式删除它们，创建 `Java.Lang.Object` 实例以保存它们，并使用 `Java.Lang.Object.GetObject<T>()` 在它们周围创建托管可调用包装器。

### <a name="explicitly-deleting-local-references"></a>显式删除本地引用

[JNIEnv. DeleteLocalRef](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)用于删除本地引用。 删除本地引用后，不能再使用它，因此必须小心谨慎，确保 `JNIEnv.DeleteLocalRef` 是使用本地引用完成的最后一步。

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
try {
    // Do something with `lref`
}
finally {
    JNIEnv.DeleteLocalRef (lref);
}
```

### <a name="wrapping-with-javalangobject"></a>用 .Java 包装

`Java.Lang.Object` 提供了一个可用于包装现有 JNI 引用的 JniHandleOwnership [（IntPtr 句柄、传输）](xref:Java.Lang.Object#ctor*)构造函数。 [JniHandleOwnership](xref:Android.Runtime.JniHandleOwnership)参数确定应如何处理 `IntPtr` 参数：

- [JniHandleOwnership. DoNotTransfer](xref:Android.Runtime.JniHandleOwnership.DoNotTransfer) &ndash; 创建的 `Java.Lang.Object` 实例将从 `handle` 参数创建新的全局引用，并且 `handle` 保持不变。
    调用方负责释放 `handle` （如有必要）。

- [JniHandleOwnership. TransferLocalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; 创建的 `Java.Lang.Object` 实例将从 `handle` 参数创建新的全局引用，并使用[JNIEnv](xref:Android.Runtime.JNIEnv.DeleteLocalRef*)删除 `handle`。 调用方不能释放 `handle`，并且在构造函数执行完成后不得使用 `handle`。

- [JniHandleOwnership. TransferGlobalRef](xref:Android.Runtime.JniHandleOwnership.TransferLocalRef) &ndash; 创建的 `Java.Lang.Object` 实例将接管 `handle` 参数的所有权。 调用方不能释放 `handle`。

由于 JNI 方法调用方法返回本地 refs，因此通常会使用 `JniHandleOwnership.TransferLocalRef`：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef);
```

在对 `Java.Lang.Object` 实例进行垃圾回收之前，将不会释放创建的全局引用。 如果能够，释放实例将释放全局引用，从而加快垃圾回收：

```csharp
IntPtr lref = JNIEnv.CallObjectMethod(instance, methodID);
using (var value = new Java.Lang.Object (lref, JniHandleOwnership.TransferLocalRef)) {
    // use value ...
}
```

### <a name="using-javalangobjectgetobjectlttgt"></a>使用&lt;T&gt;（）

`Java.Lang.Object` 提供了一个可用于创建指定类型的托管可调用包装器的[&lt;t&gt;（IntPtr handle，JniHandleOwnership transfer）](xref:Java.Lang.Object.GetObject*)方法。

类型 `T` 必须满足以下要求：

1. `T` 必须是引用类型。

1. `T` 必须实现 `IJavaObject` 接口。

1. 如果 `T` 不是抽象类或接口，则 `T` 必须提供 `(IntPtr,
    JniHandleOwnership)` 参数类型的构造函数。

1. 如果 `T` 是抽象类或接口，则*必须*有可用于 `T` 的*调用*程序。 调用程序是继承 `T` 或实现 `T` 的非抽象类型，与具有调用程序后缀的 `T` 具有相同的名称。 例如，如果 T 是 `Java.Lang.IRunnable` 的接口，则类型 `Java.Lang.IRunnableInvoker` 必须存在并且必须包含所需的 `(IntPtr,
    JniHandleOwnership)` 构造函数。

由于 JNI 方法调用方法返回本地 refs，因此通常会使用 `JniHandleOwnership.TransferLocalRef`：

```csharp
IntPtr lrefString = JNIEnv.CallObjectMethod(instance, methodID);
Java.Lang.String value = Java.Lang.Object.GetObject<Java.Lang.String>( lrefString, JniHandleOwnership.TransferLocalRef);
```

<a name="_Looking_up_Java_Types" />

## <a name="looking-up-java-types"></a>查找 Java 类型

若要在 JNI 中查找字段或方法，必须首先查找字段或方法的声明类型。 [JNIEnv. FindClass （string）](xref:Android.Runtime.JNIEnv.FindClass*)）方法用于查找 Java 类型。 String 参数是 Java 类型的*简化类型引用*或*完整的类型引用*。 有关简化的和完整类型引用的详细信息，请参阅[JNI 类型引用部分](#_JNI_Type_References)。

注意:与返回对象实例的每个其他 `JNIEnv` 方法不同，`FindClass` 返回全局引用，而不是本地引用。

<a name="_Instance_Fields" />

## <a name="instance-fields"></a>实例字段

通过*字段 id*操作字段。 字段 Id 是通过[JNIEnv](xref:Android.Runtime.JNIEnv.GetFieldID*)获取的，后者需要在其中定义字段的类、字段的名称和字段的[JNI 类型签名](#JNI_Type_Signatures)。

字段 Id 无需释放，只要加载相应的 Java 类型，就会有效。 （Android 当前不支持类卸载。）

有两组方法用于操作实例字段：一个用于读取实例字段，一个用于写入实例字段。 所有方法组都需要字段 ID 才能读取或写入字段值。

### <a name="reading-instance-field-values"></a>读取实例字段值

用于读取实例字段值的方法集遵循命名模式：

```csharp
* JNIEnv.Get*Field(IntPtr instance, IntPtr fieldID);
```

其中 `*` 是字段的类型：

- [JNIEnv.GetObjectField](xref:Android.Runtime.JNIEnv.GetObjectField*) &ndash;  读取不是内置类型的任何实例字段的值, 例如`java.lang.Object`、数组和接口类型。 返回的值为 JNI 本地引用。

- [JNIEnv. GetBooleanField](xref:Android.Runtime.JNIEnv.GetBooleanField*) &ndash; 读取 `bool` 实例字段的值。

- [JNIEnv. GetByteField](xref:Android.Runtime.JNIEnv.GetByteField*) &ndash; 读取 `sbyte` 实例字段的值。

- [JNIEnv. GetCharField](xref:Android.Runtime.JNIEnv.GetCharField*) &ndash; 读取 `char` 实例字段的值。

- [JNIEnv. GetShortField](xref:Android.Runtime.JNIEnv.GetShortField*) &ndash; 读取 `short` 实例字段的值。

- [JNIEnv. GetIntField](xref:Android.Runtime.JNIEnv.GetIntField*) &ndash; 读取 `int` 实例字段的值。

- [JNIEnv. GetLongField](xref:Android.Runtime.JNIEnv.GetLongField*) &ndash; 读取 `long` 实例字段的值。

- [JNIEnv. GetFloatField](xref:Android.Runtime.JNIEnv.GetFloatField*) &ndash; 读取 `float` 实例字段的值。

- [JNIEnv. GetDoubleField](xref:Android.Runtime.JNIEnv.GetDoubleField*) &ndash; 读取 `double` 实例字段的值。

### <a name="writing-instance-field-values"></a>编写实例字段值

用于编写实例字段值的方法集遵循命名模式：

```csharp
JNIEnv.SetField(IntPtr instance, IntPtr fieldID, Type value);
```

其中*type*是字段的类型：

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入不是内置类型（如 `java.lang.Object`、数组和接口类型）的任何字段的值。 `IntPtr` 值可以是 JNI 本地引用、JNI 全局引用、JNI 弱全局引用或 `IntPtr.Zero` （用于 `null`）。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `bool` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `sbyte` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `char` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `short` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `int` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `long` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `float` 实例字段的值。

- [JNIEnv. SetField](xref:Android.Runtime.JNIEnv.SetField*)） &ndash; 写入 `double` 实例字段的值。

<a name="_Static_Fields" />

## <a name="static-fields"></a>静态字段

静态字段通过*字段 id*进行操作。 字段 Id 是通过[JNIEnv](xref:Android.Runtime.JNIEnv.GetStaticFieldID*)获取的，后者需要在其中定义字段的类、字段的名称和字段的[JNI 类型签名](#JNI_Type_Signatures)。

字段 Id 无需释放，只要加载相应的 Java 类型，就会有效。 （Android 当前不支持类卸载。）

有两组方法可用于操作静态字段：一个用于读取实例字段，一个用于写入实例字段。 所有方法组都需要字段 ID 才能读取或写入字段值。

### <a name="reading-static-field-values"></a>读取静态字段值

用于读取静态字段值的方法集遵循命名模式：

```csharp
* JNIEnv.GetStatic*Field(IntPtr class, IntPtr fieldID);
```

其中 `*` 是字段的类型：

- [JNIEnv. GetStaticObjectField](xref:Android.Runtime.JNIEnv.GetStaticObjectField*) &ndash; 读取非内置类型（如 `java.lang.Object`、数组和接口类型）的任何静态字段的值。 返回的值为 JNI 本地引用。

- [JNIEnv. GetStaticBooleanField](xref:Android.Runtime.JNIEnv.GetStaticBooleanField*) &ndash; 读取 `bool` 静态字段的值。

- [JNIEnv. GetStaticByteField](xref:Android.Runtime.JNIEnv.GetStaticByteField*) &ndash; 读取 `sbyte` 静态字段的值。

- [JNIEnv. GetStaticCharField](xref:Android.Runtime.JNIEnv.GetStaticCharField*) &ndash; 读取 `char` 静态字段的值。

- [JNIEnv. GetStaticShortField](xref:Android.Runtime.JNIEnv.GetStaticShortField*) &ndash; 读取 `short` 静态字段的值。

- [JNIEnv. GetStaticLongField](xref:Android.Runtime.JNIEnv.GetStaticLongField*) &ndash; 读取 `long` 静态字段的值。

- [JNIEnv. GetStaticFloatField](xref:Android.Runtime.JNIEnv.GetStaticFloatField*) &ndash; 读取 `float` 静态字段的值。

- [JNIEnv. GetStaticDoubleField](xref:Android.Runtime.JNIEnv.GetStaticDoubleField*) &ndash; 读取 `double` 静态字段的值。

### <a name="writing-static-field-values"></a>编写静态字段值

用于编写静态字段值的方法集遵循命名模式：

```csharp
JNIEnv.SetStaticField(IntPtr class, IntPtr fieldID, Type value);
```

其中*type*是字段的类型：

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入不是内置类型的任何静态字段的值，如 `java.lang.Object`、数组和接口类型。 `IntPtr` 值可以是 JNI 本地引用、JNI 全局引用、JNI 弱全局引用或 `IntPtr.Zero` （用于 `null`）。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `bool` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `sbyte` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `char` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `short` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `int` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `long` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `float` 静态字段的值。

- [JNIEnv. SetStaticField](xref:Android.Runtime.JNIEnv.SetStaticField*)） &ndash; 写入 `double` 静态字段的值。

<a name="_Instance_Methods" />

## <a name="instance-methods"></a>实例方法

实例方法通过*方法 id*调用。 方法 Id 通过[JNIEnv](xref:Android.Runtime.JNIEnv.GetMethodID*)获取，后者要求在中定义方法的类型、方法的名称以及方法的[JNI 类型签名](#JNI_Type_Signatures)。

方法 Id 无需释放，只要加载相应的 Java 类型，就会有效。 （Android 当前不支持类卸载。）

有两组方法可用于调用方法：一个用于虚拟方法，一个用于非虚拟方法调用方法。 两组方法都需要方法 ID 才能调用方法，而非虚拟调用还要求您指定应调用哪个类实现。

接口方法只能在声明类型内查找;无法查找来自扩展/继承接口的方法。 有关更多详细信息，请参阅后面的绑定接口/调用程序实现部分。

可以查找在类中声明的任何方法或任何基类或实现的接口。

### <a name="virtual-method-invocation"></a>虚方法调用

用于调用方法的方法集基本上遵循命名模式：

```csharp
* JNIEnv.Call*Method( IntPtr instance, IntPtr methodID, params JValue[] args );
```

其中 `*` 是方法的返回类型。

- [JNIEnv.CallObjectMethod](xref:Android.Runtime.JNIEnv.CallObjectMethod*) &ndash; 调用返回非内置类型 (如`java.lang.Object`、数组和接口) 的方法。 返回的值为 JNI 本地引用。

- [JNIEnv.CallBooleanMethod](xref:Android.Runtime.JNIEnv.CallBooleanMethod*) &ndash; JNIEnv 调用返回`bool`值的方法。

- [JNIEnv.CallByteMethod](xref:Android.Runtime.JNIEnv.CallByteMethod*) &ndash; 调用返回`sbyte`值的方法

- [JNIEnv.CallCharMethod](xref:Android.Runtime.JNIEnv.CallCharMethod*) &ndash; 调用返回`char`值的方法。

- [JNIEnv.CallShortMethod](xref:Android.Runtime.JNIEnv.CallShortMethod*) &ndash;  调用返回`short`值的方法。

- [JNIEnv.CallLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; 调用返回`long`值的方法。

- [JNIEnv.CallFloatMethod](xref:Android.Runtime.JNIEnv.CallFloatMethod*) &ndash; 调用返回`float`值的方法。

- [JNIEnv.CallDoubleMethod](xref:Android.Runtime.JNIEnv.CallDoubleMethod*) &ndash; 调用返回`double`值的方法。

### <a name="non-virtual-method-invocation"></a>非虚拟方法调用

非实际调用方法的方法集遵循命名模式：

```csharp
* JNIEnv.CallNonvirtual*Method( IntPtr instance, IntPtr class, IntPtr methodID, params JValue[] args );
```

其中 `*` 是方法的返回类型。 非虚拟方法调用通常用于调用虚方法的基方法。

- [JNIEnv. CallNonvirtualObjectMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualObjectMethod*) &ndash; 非虚拟调用返回非内置类型的方法，如 `java.lang.Object`、数组和接口。 返回的值为 JNI 本地引用。

- [JNIEnv. CallNonvirtualBooleanMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualBooleanMethod*) &ndash; 非实际调用返回 `bool` 值的方法。

- [JNIEnv. CallNonvirtualByteMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualByteMethod*) &ndash; 非实际调用返回 `sbyte` 值的方法。

- [JNIEnv. CallNonvirtualCharMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualCharMethod*) &ndash; 非实际调用返回 `char` 值的方法。

- [JNIEnv. CallNonvirtualShortMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualShortMethod*) &ndash; 非实际调用返回 `short` 值的方法。

- [JNIEnv. CallNonvirtualLongMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualLongMethod*) &ndash; 非实际调用返回 `long` 值的方法。

- [JNIEnv. CallNonvirtualFloatMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualFloatMethod*) &ndash; 非实际调用返回 `float` 值的方法。

- [JNIEnv. CallNonvirtualDoubleMethod](xref:Android.Runtime.JNIEnv.CallNonvirtualDoubleMethod*) &ndash; 非实际调用返回 `double` 值的方法。

<a name="_Static_Methods" />

## <a name="static-methods"></a>静态方法

静态方法通过*方法 id*调用。 方法 Id 通过[JNIEnv](xref:Android.Runtime.JNIEnv.GetStaticMethodID*)获取，后者要求在中定义方法的类型、方法的名称以及方法的[JNI 类型签名](#JNI_Type_Signatures)。

方法 Id 无需释放，只要加载相应的 Java 类型，就会有效。 （Android 当前不支持类卸载。）

### <a name="static-method-invocation"></a>静态方法调用

用于调用方法的方法集基本上遵循命名模式：

```csharp
* JNIEnv.CallStatic*Method( IntPtr class, IntPtr methodID, params JValue[] args );
```

其中 `*` 是方法的返回类型。

- [JNIEnv.CallStaticObjectMethod](xref:Android.Runtime.JNIEnv.CallStaticObjectMethod*) &ndash; 调用返回非内置`java.lang.Object`类型 (如、数组和接口) 的静态方法。 返回的值为 JNI 本地引用。

- [JNIEnv.CallStaticBooleanMethod](xref:Android.Runtime.JNIEnv.CallStaticBooleanMethod*) &ndash; 调用返回`bool`值的静态方法。

- [JNIEnv.CallStaticByteMethod](xref:Android.Runtime.JNIEnv.CallStaticByteMethod*) &ndash; 调用返回`sbyte`值的静态方法。

- [JNIEnv.CallStaticCharMethod](xref:Android.Runtime.JNIEnv.CallStaticCharMethod*) &ndash; 调用返回`char`值的静态方法。

- [JNIEnv.CallStaticShortMethod](xref:Android.Runtime.JNIEnv.CallStaticShortMethod*) &ndash; 调用返回`short`值的静态方法。

- [JNIEnv.CallStaticLongMethod](xref:Android.Runtime.JNIEnv.CallLongMethod*) &ndash; 调用返回`long`值的静态方法。

- [JNIEnv.CallStaticFloatMethod](xref:Android.Runtime.JNIEnv.CallStaticFloatMethod*) &ndash; 调用返回`float`值的静态方法。

- [JNIEnv.CallStaticDoubleMethod](xref:Android.Runtime.JNIEnv.CallStaticDoubleMethod*) &ndash; 调用返回`double`值的静态方法。

<a name="JNI_Type_Signatures" />

## <a name="jni-type-signatures"></a>JNI 类型签名

[JNI 类型签名](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/types.html#wp16432)是[JNI 类型引用](#_JNI_Type_References)（尽管不是简化类型引用），但方法除外。 使用方法时，JNI 类型签名是左括号 `'('`，后跟一起连接的所有参数类型的类型引用（不分隔逗号或其他任何内容），后跟右括号 `')'`，后面是方法返回类型的 JNI 类型引用。

例如，给定 Java 方法：

```java
long f(int n, String s, int[] array);
```

JNI 类型签名将为：

```csharp
(ILjava/lang/String;[I)J
```

通常，*强烈*建议使用 `javap` 命令来确定 JNI 的签名。 例如，JNI 的[valueOf （string）](https://developer.android.com/reference/java/lang/Thread.State.html#valueOf(java.lang.String))方法的类型签名为 "（Ljava/Lang/string;） Ljava/Lang/Thread $ State;"，而方法的 JNI 类型签名为 "（） [Ljava// [）。](https://developer.android.com/reference/java/lang/Thread.State.html#values)lang/Thread $ State; "。 注意结尾的分号;这些*是*JNI 类型签名的一部分。

<a name="_JNI_Type_References" />

## <a name="jni-type-references"></a>JNI 类型引用

JNI 类型引用不同于 Java 类型引用。 不能对 JNI 使用完全限定的 Java 类型名称（如 `java.lang.String`），而必须改用 JNI 变体 `"java/lang/String"` 或 `"Ljava/lang/String;"`，具体取决于上下文;请参阅下面的详细信息。
有四种类型的 JNI 类型引用：

- **built-in**
- **简化**
- **type**
- **array**

### <a name="built-in-type-references"></a>内置类型引用

内置类型引用是一个用于引用内置值类型的字符。 映射如下所示：

- `sbyte` 的 `"B"`。
- `short` 的 `"S"`。
- `int` 的 `"I"`。
- `long` 的 `"J"`。
- `float` 的 `"F"`。
- `double` 的 `"D"`。
- `char` 的 `"C"`。
- `bool` 的 `"Z"`。
- `"V"` `void` 方法返回类型。

<a name="_Simplified_Type_References_1" />

### <a name="simplified-type-references"></a>简化类型引用

简化类型引用只能在[JNIEnv. FindClass （string）](xref:Android.Runtime.JNIEnv.FindClass*)）中使用。
可以通过两种方法来派生简化类型引用：

1. 从完全限定的 Java 名称中，用 `'/'` 替换包名称和类型名称中的每个 `'.'`，并使用 `'$'` 在类型名称内替换每个 `'.'`。

1. 读取 `'unzip -l android.jar | grep JavaName'` 的输出。

两者中的任何一个都将导致 Java 类型为[.java](https://developer.android.com/reference/java/lang/Thread.State.html) ，并将其映射到简化类型引用 `java/lang/Thread$State`。

### <a name="type-references"></a>类型引用

类型引用为内置类型引用或具有 `'L'` 前缀和 `';'` 后缀的简化类型引用。 对于 Java 类型为[.java](https://developer.android.com/reference/java/lang/String.html)，简化类型引用 `"java/lang/String"`，而类型引用则 `"Ljava/lang/String;"`。

类型引用与数组类型引用和 JNI 签名一起使用。

获得类型引用的另一种方法是读取 `'javap -s -classpath android.jar fully.qualified.Java.Name'`的输出。
根据所涉及的类型，可以使用构造函数声明或方法返回类型来确定 JNI 名称。 例如:

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

`Thread.State` 是一种 Java 枚举类型，因此我们可以使用 `valueOf` 方法的签名来确定该类型引用是 Ljava/lang/Thread $ State;。

### <a name="array-type-references"></a>数组类型引用

数组类型引用 `'['` 作为 JNI 类型引用的前缀。
指定数组时，不能使用简化类型引用。

例如，`int[]` `"[I"`，`int[][]` `"[[I"`，`java.lang.Object[]` `"[Ljava/lang/Object;"`。

## <a name="java-generics-and-type-erasure"></a>Java 泛型和类型擦除

*大多数*情况下，在 JNI 中，Java 泛型*不存在*。
有一些 "wrinkles"，但这些 wrinkles 是 Java 与泛型交互的方式，而不是 JNI 查找和调用泛型成员的方式。

当泛型类型或成员与非泛型类型或成员通过 JNI 交互时，不存在任何差异。 例如，泛型类型[&lt;t&gt;](https://developer.android.com/reference/java/lang/Class.html)也是 "原始" 泛型类型 `java.lang.Class`，这两者具有相同的简化类型引用，`"java/lang/Class"`。

## <a name="java-native-interface-support"></a>Java 本机接口支持

[JNIEnv](xref:Android.Runtime.JNIEnv)是 Jave 本机接口（JNI）的托管包装器。 虽然已将方法更改为删除显式`JNIEnv*`参数并`IntPtr`使用`jobject`, 而不是使用、 `jclass`、 `jmethodID`、、等[Java Native Interface Specification](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html).例如, 请考虑[JNI NewObject 函数](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp4517):

```csharp
jobject NewObjectA(JNIEnv *env, jclass clazz, jmethodID methodID, jvalue *args);
```

这会公开为[JNIEnv. NewObject](xref:Android.Runtime.JNIEnv.NewObject*)方法：

```csharp
public static IntPtr NewObject(IntPtr clazz, IntPtr jmethod, params JValue[] parms);
```

两次调用之间的转换相当简单。 在 C 中，你将具有：

```c
jobject CreateMapActivity(JNIEnv *env)
{
    jclass    Map_Class   = (*env)->FindClass(env, "mono/samples/googlemaps/MyMapActivity");
    jmethodID Map_defCtor = (*env)->GetMethodID (env, Map_Class, "<init>", "()V");
    jobject   instance    = (*env)->NewObject (env, Map_Class, Map_defCtor);

    return instance;
}
```

C#等效项应为：

```csharp
IntPtr CreateMapActivity()
{
    IntPtr Map_Class   = JNIEnv.FindClass ("mono/samples/googlemaps/MyMapActivity");
    IntPtr Map_defCtor = JNIEnv.GetMethodID (Map_Class, "<init>", "()V");
    IntPtr instance    = JNIEnv.NewObject (Map_Class, Map_defCtor);

    return instance;
}
```

在 IntPtr 中保存 Java 对象实例后，可能需要执行一些操作。 你可以使用 JNIEnv 方法（如[JNIEnv）](xref:Android.Runtime.JNIEnv.CallVoidMethod*)来执行此操作，但如果已有模拟C#包装器，则需要在 JNI 引用上构造一个包装器。 可以通过[JavaCast\<t >](xref:Android.Runtime.Extensions.JavaCast*)扩展方法来执行此操作：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = new Java.Lang.Object(lrefActivity, JniHandleOwnership.TransferLocalRef)
    .JavaCast<Activity>();
```

你还可以使用[\<t >](xref:Java.Lang.Object.GetObject*)方法：

```csharp
IntPtr lrefActivity = CreateMapActivity();

// imagine that Activity were instead an interface or abstract type...
Activity mapActivity = Java.Lang.Object.GetObject<Activity>(lrefActivity, JniHandleOwnership.TransferLocalRef);
```

此外，通过删除每个 JNI 函数中的 `JNIEnv*` 参数，修改了所有 JNI 函数。

## <a name="summary"></a>总结

直接处理 JNI 是一种不太丰富的体验，应尽量避免这种情况。 遗憾的是，它并不总是能够避免;如果你遇到适用于 Android 的未绑定 Java 案例，此指南将提供一些帮助。

## <a name="related-links"></a>相关链接

- [Java 本机接口规范](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/jniTOC.html)
- [Java 本机接口函数](https://download.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html)
