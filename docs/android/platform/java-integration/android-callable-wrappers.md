---
title: 适用于 Xamarin 的 android 可调用包装器
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: b55cffc19eec5ae95a0a0aba8053bdaaa49e7747
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761480"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>适用于 Xamarin 的 android 可调用包装器

只要 Android 运行时调用托管代码，就需要 android 可调用包装器（ACWs）。 这些包装是必需的，因为在运行时无法将类注册到 ART （Android 运行时）。 （具体而言，Android 运行时不支持[JNI DefineClass （）函数](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986)。 因此，Android 可调用的包装器使缺少运行时类型注册支持。 

*每次*Android 代码需要执行`virtual`在托管代码中实现或实现的接口方法`overridden` ，Xamarin 必须提供 Java 代理，以便将此方法调度到适当的托管类型。 这些 Java 代理类型是 Java 代码，它具有 "相同" 基类和 Java 接口列表作为托管类型，实现相同的构造函数和声明任何重写的基类和接口方法。 

Android 可调用的包装由**monodroid**程序在[生成过程](~/android/deploy-test/building-apps/build-process.md)中生成：它们是为直接或间接继承[.java](xref:Java.Lang.Object)的所有类型生成的。 

## <a name="android-callable-wrapper-naming"></a>Android 可调用包装器命名

适用于 Android 可调用包装的包名称基于要导出的类型的程序集限定名称的 MD5SUM。 这种命名技术使得不同的程序集可以提供相同的完全限定类型名称，而不会引入打包错误。 

由于此 MD5SUM 命名方案，因此不能按名称直接访问类型。 例如，以下`adb`命令将不起作用，因为默认情况下`my.ActivityType`不生成类型名称： 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

此外，如果尝试按名称引用类型，可能会看到如下错误：

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

如果确实*需要按*名称访问类型，则可以在属性声明中声明该类型的名称。 例如，以下代码声明了一个具有完全限定名称`My.ActivityType`的活动：

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

可以`ActivityAttribute.Name`设置属性以显式声明此活动的名称： 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

添加此属性设置后， `my.ActivityType`可以通过名称从外部代码`adb`和脚本访问。 `Service` `BroadcastReceiver` `Application` `Activity`可为多种不同类型（包括、、、和`ContentProvider`）设置特性：`Name` 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

Xamarin 5.0 中引入了基于 MD5SUM 的 ACW 命名。 有关属性命名的详细信息，请参阅[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)。 

## <a name="implementing-interfaces"></a>实现接口

有时，你可能需要实现 Android 接口，如 " [IComponentCallbacks](xref:Android.Content.IComponentCallbacks)"。 由于所有 Android 类和接口都扩展了[IJavaObject](xref:Android.Runtime.IJavaObject)接口，因此出现问题：我们如何实现`IJavaObject`？ 

此问题的解答如下：所有 Android 类型需要实现`IJavaObject`的原因是为了使 Xamarin 具有可提供给 android 的 android 可调用包装器，即给定类型的 Java 代理。 由于**monodroid**仅查找`Java.Lang.Object`子类，因此`Java.Lang.Object`实现`IJavaObject,`的答案显而易见：子类`Java.Lang.Object`： 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```

## <a name="implementation-details"></a>实现详细信息

*此页的其余部分提供了在不通知的情况下可能更改的实现详细信息*（仅在此处提供，因为开发人员将会对所发生的事情感到好奇）。 

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

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

请注意，基类被保留，并且`native`为在托管代码中重写的每个方法提供了方法声明。 
