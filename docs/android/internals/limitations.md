---
title: Xamarin.Android vs。Desktop-Mono 运行时之间的差异
ms.prod: xamarin
ms.assetid: F953F9B4-3596-8B3A-A8E4-8219B5B9F7CA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 115d715214d7af3174c41d9d82e894ce429dab42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953339"
---
# <a name="limitations"></a>限制

由于 Android 应用程序需要在生成过程中生成 Java 代理类型，不能生成在运行时的所有代码。

与桌面 Mono 相比 Xamarin.Android 限制如下：


## <a name="limited-dynamic-language-support"></a>有限的动态语言支持

 [Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)需要 Android 运行时需要调用托管的代码的任何时间。 基于静态分析的 IL 在编译时，会生成 android 可调用包装器。 此操作的净结果： 您*不能*使用动态语言 （IronPython、 IronRuby 等） 在任何方案中，子类化的 Java 类型在需要时 （包括间接子类化），因为没有任何办法来提取这些动态类型在编译时生成必要的 Android 可调用包装器。


## <a name="limited-java-generation-support"></a>有限的 Java 生成支持

[Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)需要按顺序调用托管的代码的 Java 代码生成。 *默认情况下*，Android 可调用包装器将只包含 （某些） 声明构造函数和方法的重写虚拟的 Java 方法 (即它具有[ `RegisterAttribute` ](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)) 或实现一个 Java 接口方法 （接口同样具有`Attribute`)。
  
4.1 在发布前，无法声明没有其他方法。 4.1 版本中， [`Export`并`ExportField`自定义属性可用于声明 Java 方法和 Android 可调用包装器中的字段](~/android/platform/java-integration/working-with-jni.md)。

### <a name="missing-constructors"></a>缺少构造函数

构造函数仍有些棘手，除非[ `ExportAttribute` ](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute)使用。 生成 Android 可调用包装器构造函数的算法是如果将发出 Java 构造函数：

1. 没有为所有参数类型的 Java 映射
2. 类的基类声明相同的构造函数&ndash;这是必需的因为 Android 可调用包装器*必须*调用相应的基类构造函数; （因为没有任何简单的方法，可以使用任何默认参数确定值应该是什么在 Java 中使用)。

例如，请考虑以下类：

```csharp
[Service]
class MyIntentService : IntentService {
    public MyIntentService (): base ("value")
    {
    }
}
```

虽然此值看上去非常符合逻辑，生成 Android 可调用包装器*发布版本中*将不包含默认构造函数。 因此，如果您尝试启动此服务 (例如[ `Context.StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/))，它将失败：

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

解决方法是声明默认构造函数，修饰其与`ExportAttribute`，并设置[ `ExportAttribute.SuperStringArgument` ](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/): 

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

泛型C#类仅部分受支持。 存在以下限制：


-   不能使用泛型类型`[Export]`或`[ExportField`]。 尝试执行此操作将生成`XA4207`错误。

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

-   `[ExportField]` 不能使用方法返回`void`:

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

-   泛型类型的实例_不得_通过 Java 代码创建。
    他们仅安全地从托管代码创建：

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


## <a name="partial-java-generics-support"></a>泛型部分 Java 支持

Java 绑定的泛型支持是有限的。 具体来说，是派生自另一个 （非实例化） 的泛型类的泛型实例类中的成员将保留作为 Java.Lang.Object 公开。 例如， [Android.Content.Intent.GetParcelableExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.GetParcelableExtra/p/System.String/)方法返回 Java.Lang.Object。 这是因为已清除 Java 泛型。
我们有一些类，不会应用此限制，但手动调整。


## <a name="related-links"></a>相关链接

- [Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [ExportAttribute](https://developer.xamarin.com/api/type/Java.Interop.ExportAttribute/)
- [SuperString](https://developer.xamarin.com/api/property/Java.Interop.ExportAttribute.SuperArgumentsString/)
- [RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)
