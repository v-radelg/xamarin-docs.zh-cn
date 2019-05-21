---
title: 绑定 Java 库
description: Android 社区有很多您可能想在您的应用程序; 若要使用的 Java 库本指南介绍如何将 Java 库合并到 Xamarin.Android 应用程序，通过创建绑定库。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 016ac7269f334f6df7fba9635897b9608f459284
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970207"
---
# <a name="binding-a-java-library"></a>绑定 Java 库

_Android 社区有很多您可能想在您的应用程序; 若要使用的 Java 库本指南介绍如何将 Java 库合并到 Xamarin.Android 应用程序，通过创建绑定库。_

## <a name="overview"></a>概述

适用于 Android 的第三方库生态系统是巨大的。 因此，通常最好使用比创建一个新的现有 Android 库。 Xamarin.Android 提供两种方法来使用这些库：

-   创建*绑定库*自动包装与库C#包装器，因此可以调用 Java 代码通过C#的调用。

-   使用*Java 本机接口*(*JNI*) 来直接调用 Java 库代码中的调用。 JNI 是一个编程框架，允许调用并由本机应用程序或库调用的 Java 代码。

本指南将介绍第一个选项： 如何创建*绑定库*，将一个或多个现有的 Java 库包装成可以在应用程序中链接到程序集。 有关使用 JNI 的详细信息，请参阅[使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin.Android 使用来实现绑定*托管可调用包装器*(*MCW*)。 MCW 是托管的代码需要调用 Java 代码时使用的 JNI 桥梁。 子类化 Java 类型和重写虚拟方法在 Java 类型，托管的可调用包装器还提供支持。 同样，只要 Android 运行时 （艺术） 代码想要调用托管的代码，它会通过已知为 Android 可调用包装器 (ACW) 的另一个 JNI 桥。 这[体系结构](~/android/internals/architecture.md)下图中所示：

[![Android JNI 桥体系结构](images/architecture.png)](images/architecture.png#lightbox)

绑定库是包含管理可调用包装器的 Java 类型的程序集。 例如，下面是一个 Java 类型， `MyClass`，我们想要在绑定库自动换行：

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

我们生成一个绑定库，用于以后 **.jar** ，其中包含`MyClass`，我们可以实例化它并从其上调用方法C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

若要创建此绑定库，请使用 Xamarin.Android *Java 绑定库*模板。 生成的绑定项目与 MCW 类创建.NET 程序集 **.jar**文件，并适用于 Android 库项目中嵌入资源。 您也可以为 Android 存档创建绑定库 (。AAR) 文件和 Eclipse Android 库项目。 通过引用生成的绑定库 DLL 程序集，您可以在 Xamarin.Android 项目中重用现有的 Java 库。

当您在绑定库中引用的类型时，必须使用绑定库的命名空间。 通常情况下，添加`using`顶部的指令在C#源文件，它是.NET 命名空间版本的 Java 包名称。 例如，如果您所绑定的 Java 包名称 **.jar**所示：

```csharp
com.company.package
```

然后可使以下`using`顶部的语句在C#源文件访问绑定中的类型 **.jar**文件：

```csharp
using Com.Company.Package;
```


绑定现有 Android 库时，必须要记住以下几点：

* **是否有任何库的外部依赖关系？** &ndash; 必须为 Xamarin.Android 项目中包含所需的 Android 库的任何 Java 依赖项**ReferenceJar**或是**EmbeddedReferenceJar**。 必须将任何本机程序集添加到作为绑定项目**EmbeddedNativeLibrary**。  

* **哪个版本的 Android API does Android 库目标？** &ndash; 不能以"降级"的 Android API 级别;确保，Xamarin.Android 绑定项目面向的相同的 API 级别 （或更高） 为 Android 库。

* **哪些版本的 JDK 用于编译库？** &ndash; 如果使用不同版本的 JDK 比在使用 xamarin.android 生成 Android 库，可能会发生绑定错误。 如果可能，请重新编译使用相同的 Xamarin.Android 安装使用的 jdk 版本的 Android 库。


## <a name="build-actions"></a>生成操作

创建绑定库，当您设置*生成操作*上 **.jar**或。AAR 文件合并到绑定库项目&ndash;每个生成操作确定如何 **.jar**或。将嵌入到 （或引用） AAR 文件绑定库。 以下列表总结了这些生成操作：

* `EmbeddedJar` &ndash; 将嵌入 **.jar**到作为嵌入资源生成的绑定库 DLL。 这是最简单和大多数常用的生成操作。 如果你想使用此选项 **.jar**自动编译成字节代码并打包到绑定库。

* `InputJar` &ndash; 不会嵌入 **.jar**到生成的绑定库。DLL。 绑定库。DLL 会对此有依赖关系 **.jar**在运行时。 使用此选项，如果不希望包括 **.jar**绑定库 （例如，出于许可原因） 中。 如果使用此选项，则必须确保输入 **.jar**可在运行您的应用程序的设备上。

* `LibraryProjectZip` &ndash; 将嵌入。AAR 文件到生成的绑定库。DLL。 它类似于 EmbeddedJar，只不过在绑定中，可以访问资源 （以及代码）。AAR 文件。 如果你想要嵌入使用此选项。AAR 到绑定库。

* `ReferenceJar` &ndash; 指定的引用 **.jar**： 一个引用 **.jar**是 **.jar**到绑定的其中一个 **.jar**或。AAR 文件取决于。 此引用 **.jar**仅用于满足编译时依赖项。 当使用此生成操作，C#绑定不会创建引用 **.jar**并且它不嵌入在生成的绑定库中。DLL。 使用此选项时将使绑定库参考 **.jar**但尚未尚未执行操作。 此生成操作可用于打包多个 **.jar**s （和/或。AARs) 到多个相互依赖的绑定库。

* `EmbeddedReferenceJar` &ndash; 将引用的嵌入 **.jar**到生成的绑定库。DLL。 如果想要创建使用此生成操作C#的这两个输入绑定 **.jar** （或）。AAR) 和所有它的引用 **.jar**(s) 绑定库中。

* `EmbeddedNativeLibrary` &ndash; 将嵌入到本机 **.so**到绑定。 用于此生成操作 **.so**所需的文件 **.jar**文件被绑定。 它可能需要手动加载 **.so**库，然后从 Java 库执行代码。 这是如下所述。

以下指南中的更详细地介绍了操作这些生成。

此外，使用以下生成操作以帮助导入 Java API 文档和其转换为C#XML 文档：

* `JavaDocJar` 用于指向 Javadoc 存档 Jar 符合 Maven 包样式的 Java 库 (通常`FOOBAR-javadoc**.jar**`)。
* `JavaDocIndex` 用于指向`index.html`中的 API 参考文档 HTML 文件。
* `JavaSourceJar` 用于补充`JavaDocJar`，以便首先从源生成 JavaDoc，然后将结果视为`JavaDocIndex`，符合 Maven 的 Java 库包样式 (通常`FOOBAR-sources**.jar**`)。

API 文档应为从 Java8、 Java7 或 Java6 SDK （它们是所有不同的格式），默认 doclet 或 DroidDoc 样式。

## <a name="including-a-native-library-in-a-binding"></a>在绑定中包括本机库

可能有必要包括 **.so**库，请在 Xamarin.Android 绑定的绑定 Java 库的一部分。 Xamarin.Android 已包装的 Java 代码执行时，将无法进行 JNI 调用和错误消息_java.lang.UnsatisfiedLinkError:找不到的本机方法：_ 会在 logcat 出应用程序中显示。

此解决方法是手动加载 **.so**库通过调用`Java.Lang.JavaSystem.LoadLibrary`。 例如假设 Xamarin.Android 项目已共享库**libpocketsphinx_jni.so**绑定项目的生成操作中包含**EmbeddedNativeLibrary**，以下代码片段（使用共享的库之前执行） 将加载 **.so**库：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>调整到 C 的 Java Api&eparsl;

Xamarin.Android 绑定生成器会更改某些 Java 的惯用语言和模式，对应于.NET 的模式。 以下列表介绍了如何将 Java 映射到C#/.NET:

-   _Setter/Getter 方法_在 Java 中都_属性_在.NET 中。

-   _字段_在 Java 中都_属性_在.NET 中。

-   _侦听器/侦听器接口_在 Java 中都_事件_在.NET 中。 回调接口中的方法的参数将由表示`EventArgs`子类。

-   一个_静态嵌套类_在 Java 中是_嵌套类_在.NET 中。

-   _内部类_在 Java 中是_嵌套类_实例构造函数中使用C#。



## <a name="binding-scenarios"></a>绑定方案

以下绑定方案指南可帮助你为将合并到您的应用程序绑定 Java 库 （或库）：

-   [绑定。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是创建用于绑定库的演练 **.jar**文件。

-   [绑定。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是创建用于绑定库的演练。AAR 文件。 阅读此演练，若要了解如何将绑定 Android Studio 库。

-   [绑定 Eclipse 库项目](~/android/platform/binding-java-library/binding-a-library-project.md)是从 Android 库项目中创建绑定库的演练。 阅读此演练，若要了解如何将绑定 Eclipse Android 库项目。

-   [自定义绑定](~/android/platform/binding-java-library/customizing-bindings/index.md)介绍了如何手动修改绑定以解决生成错误并调整所获得的 API，这样就更多"C#-例如"。

-   [绑定疑难解答](~/android/platform/binding-java-library/troubleshooting-bindings.md)列出了常见绑定错误方案，介绍了可能的原因，并提供了解决这些错误的建议。


## <a name="related-links"></a>相关链接

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 元数据](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用本机库](~/android/platform/native-libraries.md)
