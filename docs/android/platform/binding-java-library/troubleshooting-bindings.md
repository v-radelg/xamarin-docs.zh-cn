---
title: 绑定疑难解答
description: 本文汇总了生成绑定，以及可能的原因和解决这些问题的建议的方法时可能发生的多种常见错误。
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: b0bb7cbb6160865af5b1e40d40c7b999a8bd5ebc
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668512"
---
# <a name="troubleshooting-bindings"></a>绑定疑难解答

_本文汇总了生成绑定，以及可能的原因和解决这些问题的建议的方法时可能发生的多种常见错误。_


## <a name="overview"></a>概述

绑定 Android 库 ( **.aar**或 **.jar**) 文件很少是简单的会议时间; 它通常需要进行其他工作来缓解问题而导致的 Java 和.NET 之间的差异。
这些问题将阻止 Xamarin.Android 绑定 Android 库和它们自身显示为生成日志中的错误消息。 本指南将提供一些提示，用于解决问题，列出了一些较为常见的问题/情景，并提供可能的解决方案成功绑定到 Android 库。

绑定现有 Android 库时，必须要时刻牢记以下几点：

- **库的外部依赖关系**&ndash;必须为 Xamarin.Android 项目中包含所需的 Android 库的任何 Java 依赖项**ReferenceJar**或是**EmbeddedReferenceJar**。

- **Android 库所面向的 Android API 级别**&ndash;它无法"降级"的 Android API 级别; 确保，Xamarin.Android 绑定项目面向的相同的 API 级别 （或更高） 为 Android 库。

- **用于打包 Android 库的 Android jdk 版本**&ndash;绑定错误可能会发生，如果使用不同版本的 JDK 比使用 xamarin.android 生成 Android 库。 如果可能，请重新编译使用相同的 Xamarin.Android 安装使用的 jdk 版本的 Android 库。

解决问题的绑定 Xamarin.Android 库的第一步是能够[诊断 MSBuild 输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
启用诊断输出后, 重新生成 Xamarin.Android 绑定项目并检查生成日志，以找到有关问题的原因的线索。

它可以证明反编译 Android 库并检查的类型和 Xamarin.Android 尝试绑定的方法很有帮助。 这一点在稍后在本指南中的更多详细信息。


## <a name="decompiling-an-android-library"></a>反编译的 Android 库

检查的类和方法的 Java 类可以提供有价值的信息会帮助你绑定库。
[JD GUI](http://jd.benow.ca/)是一个图形实用工具，可以显示从 Java 源代码**类**JAR 中包含的文件。 它可以作为独立应用程序或插件为 IntelliJ 或运行 Eclipse。

反编译一个 Android 库打开 **。JAR** Java 反编译器使用的文件。 如果库 **。AAR**文件，但有必要从中提取文件**classes.jar**从存档文件。 以下是使用 JD GUI 来分析的示例屏幕截图[看毕加索会如何](http://square.github.io/picasso/)JAR:

![使用 Java 反编译器分析看毕加索会如何 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

一旦具有反编译程序 Android 库，检查源代码。 通常情况下，查找：

- **具有模糊处理的特征的类**&ndash;经过模糊处理类的特征包括：

    - Class 名称中包含 **$** ，即 **$.class** 
    - 类名完全破坏的小写字符，即**a.class**      

- **`import` 语句未引用的库**&ndash;标识未引用的库并将这些依赖项添加到 Xamarin.Android 绑定项目与**生成操作**的**ReferenceJar**或**EmbedddedReferenceJar**。

> [!NOTE]
> 反编译 Java 库可能会禁止或受到法律限制的约束根据当地的法律或在其下发布 Java 库的许可证。 如有必要，然后再尝试反编译 Java 库和检查的源代码登记法律专业人员的服务。


## <a name="inspect-apixml"></a>检查 API。XML

生成一个绑定项目的一部分，Xamarin.Android 将生成 XML 文件名**obj/Debug/api.xml**:

![在 obj / 调试下生成的 api.xml](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此文件提供了 Xamarin.Android 正尝试绑定的所有 Java Api 的列表。 此文件的内容可以帮助确定任何缺少的类型或方法，重复的绑定。 尽管此文件进行检查是单调乏味并且耗时，但可提供用于在什么可能会导致任何绑定问题的线索。 例如， **api.xml**属性返回一个不适当的类型，或有两个类型为该共享相同的托管名称可能会显示。


## <a name="known-issues"></a>已知问题

本部分将列出的一些常见的错误消息或症状，我尝试绑定一个 Android 库时发生。


### <a name="problem-java-version-mismatch"></a>问题：Java 版本不匹配

有时不会生成类型或意外的故障可能是由于使用的 Java 库已与编译相比更高版本或较旧版本。 重新编译使用相同版本的 Xamarin.Android 项目使用的 JDK 的 Android 库。


### <a name="problem-at-least-one-java-library-is-required"></a>问题：至少一个 Java 库是必需的

您会收到错误"至少一个 Java 库是必需的"即使。已添加 JAR。

#### <a name="possible-causes"></a>可能的原因：

请确保生成操作设置为`EmbeddedJar`。 由于没有对应的多个生成操作。JAR 文件 (如`InputJar`， `EmbeddedJar`，`ReferenceJar`和`EmbeddedReferenceJar`)，不能自动在默认情况下使用哪一个猜出绑定生成器。 有关生成操作的详细信息，请参阅[生成操作](~/android/platform/binding-java-library/index.md)。


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>问题：绑定工具无法加载。JAR 库

绑定库生成器无法加载。JAR 库。

#### <a name="possible-causes"></a>可能的原因

一些。Java 工具不能加载使用 （通过 Proguard 等工具） 的代码混淆的 JAR 库。 由于我们的工具可以利用 Java 反射和工程库的 ASM 字节代码，这些从属工具可能会拒绝的经过模糊处理的库，虽然 Android 运行时工具可能会将传递。 对此解决方法是手动绑定而不是使用绑定生成器这些库。



### <a name="problem-missing-c-types-in-generated-output"></a>问题：缺少C#中生成的输出类型。

绑定 **.dll**生成，但未命中某些 Java 数据类型，或生成C#源未生成由于一个错误，指出缺少的类型。

#### <a name="possible-causes"></a>可能的原因：

此错误可能有多种原因，如下所示：

-   要绑定的库可能引用第二个 Java 库。 如果绑定库的公共 API 使用从第二个库类型，则必须引用第二个库的托管的绑定。

-   有可能库被注入由于 Java 反射，类似于上面，从而导致意外的元数据加载库加载错误的原因。 Xamarin.Android 的工具当前不能解决此问题。 在这种情况下，必须手动绑定库。

-   未能加载程序集时应具有的.NET 4.0 运行时中出现 bug。 在.NET 4.5 运行时中已修复此问题。

-   Java 允许从非公共类，派生的公共类，但这在.NET 中不支持。 因为绑定生成器不生成绑定的非公共类，派生类，如这些无法正确生成。 若要解决此问题，请删除这些派生类使用中的删除节点的元数据条目**Metadata.xml**，或修复公开非公共类的元数据。 尽管后一种解决方案将创建绑定，以便C#将生成源，不应使用非公共类。

    例如：

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   模糊处理 Java 库的工具可能会影响 Xamarin.Android 绑定生成器，并生成其功能与C#包装器类。 以下代码片段演示如何更新**Metadata.xml**以 unobfuscate 类名：

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>问题：生成C#源未生成由于参数类型不匹配

生成C#不生成源。 重写方法的参数类型不匹配。

#### <a name="possible-causes"></a>可能的原因：

Xamarin.Android 包含了多种映射到枚举中的 Java 字段的C#绑定。 这些会导致生成的绑定中的类型不兼容问题。 若要解决此问题，从绑定生成器创建的方法签名需要进行修改以使用枚举。 有关详细信息，请参阅[更正枚举](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)。

### <a name="problem-noclassdeffounderror-in-packaging"></a>问题：在包装 NoClassDefFoundError

`java.lang.NoClassDefFoundError` 在打包步骤中，将引发。

#### <a name="possible-causes"></a>可能的原因：

此错误最可能的原因是必需的 Java 库需要添加到应用程序项目 (**.csproj**)。 .JAR 文件不自动解决。 针对目标设备或仿真程序中不存在的用户程序集始终不生成 Java 库绑定 (如 Google Maps **maps.jar**)。 这是与库的不用于 Android 库项目支持这种情况。在类库 dll 中嵌入 JAR。 例如：[Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>问题：重复的自定义 EventArgs 类型

由于重复的自定义 EventArgs 类型，生成失败。 发生如下错误：

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因：

这是因为来自多个共享具有相同名称的方法的接口"侦听器"类型的事件类型之间的某些冲突。 例如，如果在下面的示例所示，有两个 Java 接口，生成器将创建`DismissScreenEventArgs`同时`MediationBannerListener`和`MediationInterstitialListener`，从而导致错误。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

这是特意设计，以便避免了事件自变量类型上的耗时较长名称。 若要避免这些冲突，某些元数据转换是必需的。 编辑[ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) ，并添加`argsType`接口中的任何一个 （或在接口方法） 的属性：

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>问题：类未实现接口方法

指示生成的类不实现所需的生成的类实现的接口的方法来生成一条错误消息。 但是，看一下生成的代码，您可以看到此方法实现。

下面是错误的示例：

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>可能的原因：

这是协变返回类型与绑定 Java 方法会出现一个问题。 在此示例中，该方法`Oauth.Signpost.Http.IHttpRequest.UnWrap()`需要返回`Java.Lang.Object`。 但是，该方法`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()`的返回类型为`HttpURLConnection`。 有两种方法来解决此问题：

-   添加的分部类声明`HttpURLConnectionRequestAdapter`和显式实现`IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   从生成中删除协方差C#代码。 这涉及到添加到以下转换**Transforms\Metadata.xml**这将导致生成C#代码的返回类型`Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>问题：名称冲突上的内部类 / 属性

继承的对象的冲突可见性。

在 Java 中，这不被必需的派生的类具有相同的可见性作为其父级。 Java 只需修复，为您。 在C#，具有可以明确，因此您需要确保层次结构中的所有类都有相应的可见性。 下面的示例演示如何从 Java 包名称更改`com.evernote.android.job`到`Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>问题：一个 **.so**由绑定所需的库是未加载

某些绑定项目还取决于中的功能 **.so**库。 很可能不会自动加载 Xamarin.Android **.so**库。 Xamarin.Android 已包装的 Java 代码执行时，将无法进行 JNI 调用和错误消息_java.lang.UnsatisfiedLinkError:找不到的本机方法：_ 会在 logcat 出应用程序中显示。

此解决方法是手动加载 **.so**库通过调用`Java.Lang.JavaSystem.LoadLibrary`。 例如假设 Xamarin.Android 项目已共享库**libpocketsphinx_jni.so**绑定项目的生成操作中包含**EmbeddedNativeLibrary**，以下代码片段（使用共享的库之前执行） 将加载 **.so**库：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>总结

在本文中，我们将列出与 Java 绑定相关联的常见故障排除问题并说明了如何解决这些问题。


## <a name="related-links"></a>相关链接

- [库项目](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [启用诊断输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [适用于 Android 开发人员的 Xamarin](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
