---
title: 绑定 Java 库
description: Android 社区包含很多你可能想要在应用中使用的 Java 库;本指南说明如何通过创建绑定库将 Java 库合并到 Xamarin Android 应用程序中。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 4c01022e01c5ba6a9099b88e99558bd7d7ce728d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524555"
---
# <a name="binding-a-java-library"></a>绑定 Java 库

_Android 社区包含很多你可能想要在应用中使用的 Java 库;本指南说明如何通过创建绑定库将 Java 库合并到 Xamarin Android 应用程序中。_

## <a name="overview"></a>概述

适用于 Android 的第三方库生态系统是巨大的。 因此, 与创建新的 Android 库相比, 使用现有的 Android 库通常是有意义的。 Xamarin 提供两种方法来使用这些库:

- 创建一个使用C#包装自动包装库的*绑定库*, 以便可以通过C#调用调用 Java 代码。

- 使用*Java 本机接口*(*JNI*) 直接调用 Java 库代码中的调用。 JNI 是一个编程框架, 允许 Java 代码调用并由本机应用程序或库调用。

本指南介绍第一种方法: 如何创建将一个或多个现有 Java 库包装到可在应用程序中链接到的程序集的*绑定库*。 有关使用 JNI 的详细信息, 请参阅使用[JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin 使用*托管可调用包装*(*MCW*) 实现绑定。 MCW 是托管代码需要调用 Java 代码时使用的 JNI 桥。 托管的可调用包装器还支持对 Java 类型和 Java 类型重写虚方法的支持。 同样, 每当 Android 运行时 (ART) 代码希望调用托管代码时, 它都是通过其他称为 Android 可调用包装 (ACW) 的 JNI 桥来实现的。 下图演示了此[体系结构](~/android/internals/architecture.md):

[![Android JNI 桥体系结构](images/architecture.png)](images/architecture.png#lightbox)

绑定库是包含 Java 类型的托管可调用包装器的程序集。 例如, 下面是一个 Java 类型, `MyClass`要在绑定库中换行:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

为包含`MyClass`的 **.jar**生成绑定库后, 可对其进行实例化并从C#其调用方法:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

若要创建此绑定库, 请使用 Xamarin *.Java Java 绑定库*模板。 生成的绑定项目将创建包含 MCW 类的 .NET 程序集、 **.jar**文件以及嵌入其中的 Android 库项目的资源。 你还可以创建适用于 Android 存档的绑定库 (。AAR) 文件和 Eclipse Android 库项目。 通过引用生成的绑定库 DLL 程序集, 你可以在 Xamarin Android 项目中重用现有 Java 库。

引用绑定库中的类型时, 必须使用绑定库的命名空间。 通常, 您在`using` C#源文件的顶部添加一个指令, 该指令是 Java 包名称的 .net 命名空间版本。 例如, 如果绑定的 **.jar**的 Java 包名称如下:

```csharp
com.company.package
```

然后, 将下面`using`的语句放置在C#源文件的顶部, 以访问绑定的 **.jar**文件中的类型:

```csharp
using Com.Company.Package;
```


绑定现有 Android 库时, 需要记住以下几点:

* **库是否有任何外部依赖项？** &ndash;Android 库所需的任何 Java 依赖项都必须包含在 Xamarin 项目中作为**ReferenceJar**或**EmbeddedReferenceJar**。 必须以**EmbeddedNativeLibrary**的形式将任何本机程序集添加到绑定项目。  

* **Android 库面向哪个版本的 Android API？** &ndash;不能 "降级" Android API 级别;确保 Xamarin 绑定项目的目标 API 级别 (或更高版本) 为 Android 库。

* **用于编译库的 JDK 版本是什么？** &ndash;如果使用不同于 Xamarin 的 JDK 版本生成 Android 库, 则可能会发生绑定错误。 如果可能, 请使用安装 Xamarin 所用的同一版本的 JDK 重新编译 Android 库。


## <a name="build-actions"></a>生成操作

创建绑定库时, 将在 **.jar**或上设置*生成操作*。将合并到绑定库项目&ndash;中的 AAR 文件每个生成操作确定了 **.jar**或的方式。AAR 文件将嵌入到绑定库 (或被其引用) 中。 下面的列表总结了这些生成操作:

* `EmbeddedJar`将 .jar 作为嵌入资源嵌入到生成的绑定库 DLL 中。 &ndash; 这是最简单且最常用的生成操作。 如果希望将 **.jar**自动编译到字节代码中并打包到绑定库中, 请使用此选项。

* `InputJar`不会将 .jar 嵌入到生成的绑定库。 &ndash;.DLL. 绑定库。DLL 将在运行时依赖于此 **.jar** 。 如果你不希望在绑定库中包含 **.jar** (例如, 出于许可原因), 请使用此选项。 如果使用此选项, 则必须确保输入 **.jar**在运行应用的设备上可用。

* `LibraryProjectZip`&ndash;嵌入。AAR 文件转换为生成的绑定库.DLL. 这类似于 EmbeddedJar, 但你可以在绑定中访问资源 (以及代码)。AAR 文件。 如果要嵌入, 请使用此选项。AAR 到绑定库。

* `ReferenceJar`指定引用 .jar: 引用 .jar 是您的一个或多个绑定的 .jar。 &ndash;AAR 文件依赖于。 此引用 **.jar**仅用于满足编译时依赖关系。 使用此生成操作时, C#不会为引用 **.jar**创建绑定, 也不会将其嵌入到生成的绑定库中.DLL. 当你将绑定库用于引用 **.jar**后, 请使用此选项。 此生成操作适用于打包多个 **.jar**(和/或。AARs) 转换为多个相互依赖的绑定库。

* `EmbeddedReferenceJar`将一个引用 jar 嵌入到生成的绑定库。 &ndash;.DLL. 若要为输入 **.jar** (或) 创建C#绑定, 请使用此生成操作。AAR) 及其所有引用 **.jar**的绑定库。

* `EmbeddedNativeLibrary`将本机嵌入到绑定中。 &ndash; 此生成操作用于 **。因此**, 所绑定的 **.jar**文件所需的文件。 可能需要在执行 Java 库中的代码之前手动加载 **。** 下面对此进行了介绍。

以下指南中更详细地介绍了这些生成操作。

此外, 以下生成操作用于帮助导入 Java API 文档并将其转换为C# XML 文档:

* `JavaDocJar`用于指向符合 Maven 包样式的 Java 库的 Javadoc 存档 Jar (通常`FOOBAR-javadoc**.jar**`为)。
* `JavaDocIndex`用于指向`index.html` API 参考文档 HTML 中的文件。
* `JavaSourceJar`用于进行补充`JavaDocJar`, 以便首先从源生成 JavaDoc, 然后将`JavaDocIndex`结果视为符合 Maven 包样式的 Java 库 (通常`FOOBAR-sources**.jar**`为)。

API 文档应是来自 Java8、Java7 或 Java6 SDK 的默认 doclet (它们都是不同的格式) 或 DroidDoc 样式。

## <a name="including-a-native-library-in-a-binding"></a>在绑定中包含本机库

在绑定 Java 库的过程中, 可能需要在 Xamarin Android 绑定项目中包含 **。** 当执行包装的 Java 代码时, Xamarin 将无法进行 JNI 调用和错误消息_UnsatisfiedLinkError:找不到本机方法_ : 应用程序的 logcat 中将显示该方法。

解决此问题的方法是, 通过调用 `Java.Lang.JavaSystem.LoadLibrary`手动加载。 例如, 假设 Xamarin Android 项目具有共享库**libpocketsphinx_jni。因此**, 在绑定项目中包含**EmbeddedNativeLibrary**的生成操作 (在使用共享库之前执行)将加载 **。因此**库:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>使 Java Api 适应 C&eparsl;

Xamarin 绑定生成器会将一些 Java 惯例和模式更改为与 .NET 模式相对应。 以下列表说明了如何将 Java 映射到C#/.NET:

- Java 中的_Setter/Getter 方法_是 .net 中的_属性_。

- Java 中的_字段_是 .net 中的_属性_。

- Java 中的_侦听器/侦听器接口_是 .net 中的_事件_。 回调接口中的方法的参数将由`EventArgs`子类表示。

- Java 中的_静态嵌套类_是 .net 中的_嵌套类_。

- Java 中的_内部类_是具有中C#的实例构造函数的_嵌套类_。



## <a name="binding-scenarios"></a>绑定方案

以下绑定方案指南可帮助你绑定要合并到你的应用中的 Java 库 (或库):

- [绑定。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是为 **.jar**文件创建绑定库的演练。

- [绑定。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是用于为创建绑定库的演练。AAR 文件。 阅读本演练以了解如何绑定 Android Studio 库。

- [绑定 Eclipse 库项目](~/android/platform/binding-java-library/binding-a-library-project.md)是从 Android 库项目创建绑定库的演练。 阅读此演练以了解如何绑定 Eclipse Android 库项目。

- [自定义绑定](~/android/platform/binding-java-library/customizing-bindings/index.md)说明了如何对绑定进行手动修改, 以解决生成错误并调整生成的 API, 使其更 "C#类似"。

- [故障排除绑定](~/android/platform/binding-java-library/troubleshooting-bindings.md)列出了常见的绑定错误情况、说明了可能的原因, 并提供了解决这些错误的建议。


## <a name="related-links"></a>相关链接

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 元数据](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用本机库](~/android/platform/native-libraries.md)
