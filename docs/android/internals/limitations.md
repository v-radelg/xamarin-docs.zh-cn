---
title: Xamarin 和单色运行时中的桌面差异
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 18e6e82011460a51a96df4694f15b36c5ec94ab5
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510713"
---
# <a name="limitations"></a>限制

由于 Android 应用程序需要在生成过程中生成 Java 代理类型, 因此不能在运行时生成所有代码。

与桌面 Mono 相比, 这些是 Xamarin 的 Xamarin 限制:

## <a name="limited-dynamic-language-support"></a>有限的动态语言支持

 当 Android 运行时需要调用托管代码时, 需要有[android 可调用的包装](~/android/platform/java-integration/android-callable-wrappers.md)。 基于 IL 的静态分析, 在编译时生成 Android 可调用包装。 此操作的最终结果: 在需要使用 Java 类型的子类 (包括间接子类) 的任何情况下,*不能*使用动态语言 (IronPython、IronRuby 等), 因为在编译时无法将这些动态类型提取到生成必需的 Android 可调用包装。

## <a name="limited-java-generation-support"></a>有限的 Java 生成支持

为了使 Java 代码调用托管代码, 需要生成[Android 可调用包装](~/android/platform/java-integration/android-callable-wrappers.md)。 *默认情况*下, Android 可调用包装只包含 (特定) 声明的构造函数和方法, 这些方法重写虚拟 Java 方法[`RegisterAttribute`](xref:Android.Runtime.RegisterAttribute)(即它具有) 或实现 Java 接口方法 ( `Attribute`接口同样具有)。
  
在4.1 版本之前, 不能声明其他方法。 在4.1 版本中, [ `Export`和`ExportField`自定义属性可用于在 Android 可调用包装器中声明 Java 方法和字段](~/android/platform/java-integration/working-with-jni.md)。

### <a name="missing-constructors"></a>缺少构造函数

除非使用, 否则[`ExportAttribute`](xref:Java.Interop.ExportAttribute)构造函数会比较棘手。 生成 Android 可调用包装器构造函数的算法是:

1. 对于所有参数类型, 都有一个 Java 映射
2. 基类声明相同的构造函数&ndash; , 这是必需的, 因为 Android 可调用包装器*必须*调用相应的基类构造函数; 不能使用任何默认参数 (因为没有简单的方法来确定哪些值应在 Java 中使用)。

例如，请考虑以下类：

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

虽然这看起来完全合乎逻辑,*版本中*生成的 Android 可调用包装将不包含默认构造函数。 因此, 如果尝试启动此服务 (例如[`Context.StartService`](xref:Android.Content.Context.StartService*)), 则会失败:

```shell
E/AndroidRuntime(31766): FATAL EXCEPTION: main
E/AndroidRuntime(31766): java.lang.RuntimeException: Unable to instantiate service example.MyIntentService: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2347)
E/AndroidRuntime(31766):        at android.app.ActivityThread.access$1600(ActivityThread.java:130)
E/AndroidRuntime(31766):        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1277)
E/AndroidRuntime(31766):        at android.os.Handler.dispatchMessage(Handler.java:99)
E/AndroidRuntime(31766):        at android.os.Looper.loop(Looper.java:137)
E/AndroidRuntime(31766):        at android.app.ActivityThread.main(ActivityThread.java:4745)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invokeNative(Native Method)
E/AndroidRuntime(31766):        at java.lang.reflect.Method.invoke(Method.java:511)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:786)
E/AndroidRuntime(31766):        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:553)
E/AndroidRuntime(31766):        at dalvik.system.NativeStart.main(Native Method)
E/AndroidRuntime(31766): Caused by: java.lang.InstantiationException: can't instantiate class example.MyIntentService; no empty constructor
E/AndroidRuntime(31766):        at java.lang.Class.newInstanceImpl(Native Method)
E/AndroidRuntime(31766):        at java.lang.Class.newInstance(Class.java:1319)
E/AndroidRuntime(31766):        at android.app.ActivityThread.handleCreateService(ActivityThread.java:2344)
E/AndroidRuntime(31766):        ... 10 more
```

解决方法是声明默认构造函数, 使用将`ExportAttribute`其修饰, 并[`ExportAttribute.SuperStringArgument`](xref:Java.Interop.ExportAttribute.SuperArgumentsString)设置: 

```csharp
[Service]
class MyIntentService : IntentService {
    [Export (SuperArgumentsString = "\"value\"")]
    public MyIntentService (): base("value")
    {
    }

    // ...
}
```


### <a name="generic-c-classes"></a>泛型C#类

仅C#部分支持泛型类。 存在以下限制:


-   泛型类型不能使用`[Export]`或`[ExportField`]。 如果尝试这样做, 将会`XA4207`生成错误。

    ```csharp
    public abstract class Parcelable<T> : Java.Lang.Object, IParcelable
    {
        // Invalid; generates XA4207
        [ExportField ("CREATOR")]
        public static IParcelableCreator CreateCreator ()
        {
            ...
    }
    ```

-   泛型方法不能使用`[Export]`或`[ExportField]`:

    ```csharp
    public class Example : Java.Lang.Object
    {
        
        // Invalid; generates XA4207
        [Export]
        public static void Method<T>(T value)
        {
            ...
        }
    }
    ```

-   `[ExportField]`不能用于返回`void`的方法:

    ```csharp
    public class Example : Java.Lang.Object
    {
        // Invalid; generates XA4208
        [ExportField ("CREATOR")]
        public static void CreateSomething ()
        {
        }
    }
    ```

-   _不_能从 Java 代码创建泛型类型的实例。
    只能从托管代码安全地创建它们:

    ```csharp
    [Activity (Label="Die!", MainLauncher=true)]
    public class BadGenericActivity<T> : Activity
    {
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);
        }
    }
    ```

## <a name="partial-java-generics-support"></a>部分 Java 泛型支持

Java 泛型绑定支持受到限制。 特别是, 派生自另一个泛型 (非实例化) 类的泛型实例类中的成员将作为 .Java 公开。 例如, [GetParcelableExtra](xref:Android.Content.Intent.GetParcelableExtra*)方法将返回 ".java"。 这是由于已擦除的 Java 泛型。
我们有一些类不应用此限制, 而是手动调整它们。

## <a name="related-links"></a>相关链接

- [Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](xref:Java.Interop.ExportAttribute)
- [SuperString](xref:Java.Interop.ExportAttribute.SuperArgumentsString)
- [RegisterAttribute](xref:Android.Runtime.RegisterAttribute)
