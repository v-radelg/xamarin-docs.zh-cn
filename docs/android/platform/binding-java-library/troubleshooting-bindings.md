---
title: 绑定疑难解答
description: 本文总结了生成绑定时可能出现的多种常见错误，以及可能的原因和解决方法的建议方法。
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 0c273797d7512f062260e49e0f71fdd1132f037b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723801"
---
# <a name="troubleshooting-bindings"></a>绑定疑难解答

_本文总结了生成绑定时可能出现的多种常见错误，以及可能的原因和解决方法的建议方法。_

## <a name="overview"></a>概述

绑定 Android 库（ **aar**或 **.jar**）文件很少事情;它通常需要额外的精力来缓解由 Java 和 .NET 的差异导致的问题。
这些问题将阻止 Xamarin 绑定 Android 库，并在生成日志中显示为错误消息。 本指南将提供一些用于解决问题的提示、列出一些更常见的问题/方案，并提供可能的解决方案来成功绑定 Android 库。

绑定现有 Android 库时，需要记住以下几点：

- **库的外部依赖项**&ndash; android 库所需的任何 Java 依赖项都必须包含在 Xamarin 项目中作为**ReferenceJar**或**EmbeddedReferenceJar**。

- Android**库针对 ANDROID api 级别**&ndash; 不能 "降级" android api 级别;确保 Xamarin 绑定项目的目标 API 级别（或更高版本）为 Android 库。

- 如果 Android 库是使用不同于 Xamarin 所用版本的 JDK 生成的，则在使用不同版本的 JDK 生成 android 库时，可能会出现**用于打包 android 库的 ANDROID JDK 版本**&ndash; 绑定错误。 如果可能，请使用安装 Xamarin 所用的同一版本的 JDK 重新编译 Android 库。

对绑定 Xamarin 类库的问题进行故障排除的第一步是启用[诊断 MSBuild 输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
启用诊断输出后，重新生成 Xamarin 绑定项目并检查生成日志，查找有关问题原因的线索。

它还有助于反编译 Android 库，并检查 Xamarin 正在尝试绑定的类型和方法。 本指南稍后将对此进行更详细的介绍。

## <a name="decompiling-an-android-library"></a>反编译 Android 库

检查 Java 类的类和方法可提供有价值的信息，以帮助绑定库。
[JD](http://jd.benow.ca/)是一种图形实用工具，可以显示 JAR 中包含的**类**文件中的 Java 源代码。 它可以作为独立的应用程序运行，也可以作为 IntelliJ 或 Eclipse 的插件运行。

若要反编译 Android 库，请打开 **。** 带有 Java 反编译程序的 JAR 文件。 如果库为，则为 **。AAR**文件，需要从存档文件中提取文件**类 .jar。** 下面是使用 JD 分析[毕加索](https://square.github.io/picasso/)JAR 的示例屏幕截图：

![使用 Java 反编译程序分析 picasso-2.5。2](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

反编译 Android 库后，请检查源代码。 一般而言，查找：

- 模糊类的特征 &ndash; 特性的**类**包括：

  - 类名称包含一个 **$** ，即 **$. 类**
  - 类名称完全泄露小写字符，即**类**      

- **`import` 未引用库的语句**&ndash; 识别未引用的库，并将这些依赖项添加到具有**ReferenceJar**或**EmbedddedReferenceJar**的**生成操作**的 Xamarin 绑定项目。

> [!NOTE]
> 反编译根据本地法律或发布 Java 库的许可证，可能禁止 Java 库或遵守法律限制。 如有必要，请先登记法律专业人员的服务，再尝试反编译 Java 库并检查源代码。

## <a name="inspect-apixml"></a>检查 API。XML

作为生成绑定项目的一部分，Xamarin 将生成 XML 文件名**obj/Debug/api**：

![在 obj/Debug 下生成的 api](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此文件提供 Xamarin 正在尝试绑定的所有 Java Api 的列表。 此文件的内容可帮助识别任何缺少的类型或方法、重复的绑定。 尽管对此文件的检查非常繁琐且非常耗时，但它可以提供有关导致任何绑定问题的原因的线索。 例如， **api**可能会发现属性返回的类型不合适，或者有两种类型共享同一托管名称。

## <a name="known-issues"></a>已知问题

本节将列出一些常见错误消息或在尝试绑定 Android 库时出现的症状。

### <a name="problem-java-version-mismatch"></a>问题： Java 版本不匹配

有时不会生成类型，或发生意外的崩溃，因为你使用的是较新版本的 Java 或更早版本的 Java （与库的编译内容相比）。 使用你的 Xamarin 项目所使用的同一版本的 JDK 重新编译 Android 库。

### <a name="problem-at-least-one-java-library-is-required"></a>问题：至少需要一个 Java 库

即使有，也会收到错误 "至少需要一个 Java 库"。已添加 JAR。

#### <a name="possible-causes"></a>可能的原因：

请确保将生成操作设置为 `EmbeddedJar`。 由于有多个生成操作。JAR 文件（例如 `InputJar`、`EmbeddedJar`、`ReferenceJar` 和 `EmbeddedReferenceJar`），绑定生成器将无法自动推测默认使用哪一个。 有关生成操作的详细信息，请参阅[生成操作](~/android/platform/binding-java-library/index.md)。

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>问题：绑定工具无法加载。JAR 库

绑定库生成器无法加载。JAR 库。

#### <a name="possible-causes"></a>可能的原因

Some.Java 工具无法加载使用代码模糊处理的 JAR 库（通过工具（如 Proguard）。 由于我们的工具利用了 Java 反射和 ASM 字节代码工程库，因此，在 Android 运行时工具可以通过时，这些依赖工具可能会拒绝经过模糊处理的库。 解决此问题的方法是，手动绑定这些库，而不是使用绑定生成器。

### <a name="problem-missing-c-types-in-generated-output"></a>问题：生成C#的输出中缺少类型。

绑定 **.dll**生成但未命中某些 Java 类型，或生成C#的源由于错误（指出缺少类型）而未生成。

#### <a name="possible-causes"></a>可能的原因：

此错误可能是由以下几个原因导致的：

- 正在绑定的库可能引用第二个 Java 库。 如果绑定库的公共 API 使用第二个库中的类型，则还必须为第二个库引用托管绑定。

- 由于 Java 反射，库可能已注入，这类似于上述库加载错误的原因，导致元数据的意外加载。 Xamarin 的工具当前无法解决这种情况。 在这种情况下，必须手动绑定库。

- .NET 4.0 运行时中有一个 bug，该 bug 在应具有时无法加载程序集。 此问题已在 .NET 4.5 运行时中修复。

- Java 允许从非公共类派生公共类，但 .NET 不支持此类。 由于绑定生成器不生成非公共类的绑定，因此无法正确生成派生类（如）。 若要解决此问题，请使用**metadata**中的 remove 节点删除这些派生类的元数据条目，或者修复使非公共类成为公共类的元数据。 尽管后一种解决方案将创建绑定以便生成C#源，但不应使用非公共类。

  例如：

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- 对 Java 库进行模糊处理的工具可能会影响 Xamarin 绑定生成器及其生成C#包装器类的功能。 以下代码片段演示了**如何将 unobfuscate 更新为**类名：

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>问题：由于C#参数类型不匹配，生成的源未生成

生成C#的源未生成。 重写的方法的参数类型不匹配。

#### <a name="possible-causes"></a>可能的原因：

Xamarin 包含多个映射到C#绑定中的枚举的 Java 字段。 这会导致生成的绑定中出现类型不兼容的情况。 若要解决此问题，需要修改从绑定生成器创建的方法签名，以使用枚举。 有关详细信息，请参阅[更正枚举](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)。

### <a name="problem-noclassdeffounderror-in-packaging"></a>问题：打包中的 NoClassDefFoundError

打包步骤中会引发 `java.lang.NoClassDefFoundError`。

#### <a name="possible-causes"></a>可能的原因：

此错误的最可能原因是需要将必需的 Java 库添加到应用程序项目（ **.csproj**）。 .不自动解析 JAR 文件。 Java 库绑定并非始终针对目标设备或模拟器中不存在的用户程序集（如 Google Maps **maps**）生成。 这不是 Android 库项目支持（作为库）。JAR 嵌入在库 dll 中。 例如： [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>问题：重复的自定义 EventArgs 类型

由于自定义 EventArgs 类型重复，生成失败。 出现如下错误：

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因：

这是因为来自多个接口 "侦听器" 类型的事件类型之间存在某种冲突，这些类型共享具有相同名称的方法。 例如，如果在下面的示例中显示了两个 Java 接口，生成器将为 `MediationBannerListener` 和 `MediationInterstitialListener`创建 `DismissScreenEventArgs`，从而导致错误。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

这是设计使然，以避免事件参数类型上的冗长名称。 若要避免这些冲突，需要进行某些元数据转换。 编辑**Transforms\Metadata.xml** ，并在其中一个接口（或接口方法）上添加 `argsType` 特性：

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

将生成一条错误消息，指示生成的类未实现生成的类所实现的接口所需的方法。 不过，查看生成的代码，可以看到该方法已实现。

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

这是与具有协变返回类型的绑定 Java 方法一起发生的问题。 在此示例中，方法 `Oauth.Signpost.Http.IHttpRequest.UnWrap()` 需要返回 `Java.Lang.Object`。 但是，`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()` 方法的返回类型为 `HttpURLConnection`。 可以通过两种方法解决此问题：

- 为 `HttpURLConnectionRequestAdapter` 添加分部类声明并显式实现 `IHttpRequest.Unwrap()`：

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- 删除生成C#的代码的协方差。 这涉及到将以下转换添加到**Transforms\Metadata.xml** ，这将导致C#生成的代码具有 `Java.Lang.Object`的返回类型：

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>问题：内部类/属性名称冲突

继承对象的可见性冲突。

在 Java 中，不要求派生类具有与其父级相同的可见性。 Java 只是帮你解决问题。 在C#中，这必须是显式的，因此，你需要确保层次结构中的所有类都具有适当的可见性。 下面的示例演示如何将 Java 包名称从 `com.evernote.android.job` 更改为 `Evernote.AndroidJob`：

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>问题 **：答：此绑定**所需的库未加载

某些绑定项目可能还依赖于中的功能 **。因此**库。 Xamarin Android 可能不会自动加载 **. 因此**就会自动加载。 当执行了所包装的 Java 代码时，Xamarin 将无法进行 JNI 调用，并且错误消息_UnsatisfiedLinkError：找不到本机方法：_ 将显示在对应用程序的 logcat 中。

解决此问题的方法是，通过调用 `Java.Lang.JavaSystem.LoadLibrary`来手动加载 **。** 例如，假设 Xamarin Android 项目具有共享库**libpocketsphinx_jni。因此**包含在**EmbeddedNativeLibrary**的生成操作的绑定项目中，以下代码片段（在使用共享库之前执行）将加载 **。因此**库：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>摘要

本文列出了与 Java 绑定相关的常见疑难解答问题，并解释了如何解决这些问题。

## <a name="related-links"></a>相关链接

- [库项目](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [启用诊断输出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [适用于 Android 开发人员的 Xamarin](~/android/get-started/java-developers.md)
- [JD](http://jd.benow.ca/)
