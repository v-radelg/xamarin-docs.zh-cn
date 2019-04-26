---
title: Android 入门
description: 本文档介绍如何开始使用.NET 嵌入和 Android。 它讨论了安装.NET 嵌入，创建一个 Android 库项目，使用生成的输出中的 Android Studio 项目中和其他注意事项。
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: e60892edfcf73f3e7cada8923e16bcc1be2c203e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60876886"
---
# <a name="getting-started-with-android"></a>Android 入门

除了从要求之外[开始使用 Java](~/tools/dotnet-embedding/get-started/java/index.md)指南还需要：

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更高版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html)使用 Java 1.8

作为概述，我们将：

* 创建C#Android 库项目
* 安装通过 NuGet 的.NET 嵌入
* 在 Android 库程序集上运行.NET 嵌入
* 使用 Android Studio 中的 Java 项目中生成的 AAR 文件

## <a name="create-an-android-library-project"></a>创建一个 Android 库项目

打开 Visual Studio 的 Windows 或 Mac 中，创建一个新的 Android 类库项目，将其命名**你好从 csharp**，并将其保存到 **~/Projects/hello-from-csharp**或 **%USERPROFILE%\Projects\hello 从 csharp**。

添加新的 Android 活动名为**HelloActivity.cs**后, 跟在一个 Android 布局**Resource/layout/hello.axml**。

添加新`TextView`到布局，并更改为充满乐趣的内容的文本。

布局源应如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

在您的活动，请确保您调用`SetContentView`具有新布局：

```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```

> [!NOTE]
> 别忘了`[Register]`属性。 有关详细信息，请参阅[限制](#current-limitations-on-android)。

生成项目。 生成的程序集将保存在`bin/Debug/hello-from-csharp.dll`。

## <a name="installing-net-embedding-from-nuget"></a>.NET 嵌入从 NuGet 安装

请按照这些[说明](~/tools/dotnet-embedding/get-started/install/install.md)来安装和配置.NET 嵌入为你的项目。

命令调用应配置将如下所示：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>在 Android Studio 项目中使用生成的输出

1. 打开 Android Studio 并创建与新的项目**空活动**。
2. 右键单击你**应用程序**模块，然后选择**新建 > 模块**。
3. 选择**导入。JAR /。除了 AAR 包**。
4. 使用目录浏览器查找 **~/Projects/hello-from-csharp/output/hello_from_csharp.aar**然后单击**完成**。

![导入 AAR 到 Android Studio](android-images/androidstudioimport.png)

这会将 AAR 文件复制到名为的新模块**hello_from_csharp**。

![Android Studio 项目](android-images/androidstudioproject.png)

若要使用新模块从你**应用程序**，右键单击并选择**打开模块设置**。 上**依赖项**选项卡上，添加一个新**模块依赖关系**，然后选择 **: hello_from_csharp**。

![Android Studio 中的依赖项](android-images/androidstudiodependencies.png)

在您的活动，将添加一个新`onResume`方法，并使用下面的代码以启动C#活动：

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>程序集压缩 (*重要说明*)

一项进一步更改是必需的.NET 嵌入在 Android Studio 项目中。

打开你的应用**build.gradle**文件，并添加了以下更改：

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin.Android 当前加载.NET 程序集直接从 apk 进行签名，但它需要的程序集不进行压缩。

如果没有此设置，应用将启动时崩溃，并且类似于这样的内容打印到控制台：

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>运行应用

在启动您的应用程序：

![从 helloC#在模拟器中运行的示例](android-images/hello-from-csharp-android.png)

请注意此处发生的情况：

* 我们有C#类， `HelloActivity`，该子类 Java
* 我们有 Android 资源文件
* 在 Android Studio 中使用这些通过 Java

若要运行此示例，以下所有最终 APK 中设置：

* 应用程序启动时配置 Xamarin.Android
* 中包括的.NET 程序集**资产/程序集**
* **AndroidManifest.xml**修改为在C#活动，等等。
* Android 资源和资产从.NET 库
* [Android 可调用包装器](~/android/platform/java-integration/android-callable-wrappers.md)任何`Java.Lang.Object`子类

如果您正在寻找其他演练，请查看以下视频中，该示例演示了嵌入 Charles Petzold [; FingerPaint 演示](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/)Android Studio 项目中：

[![适用于 Android 的 Embeddinator 4000](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 Java 1.8

截至撰写本文时，最佳选择是使用 Android Studio 3.0 ([在此处下载](https://developer.android.com/studio/index.html))。

若要启用在您应用的模块的 Java 1.8 **build.gradle**文件：

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

此外可以看一看[Android Studio 测试项目](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle)的更多详细信息。 

如果想要使用 Android Studio 2.3.x 稳定，必须将启用不推荐使用的 Jack 工具链：

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>在 Android 上的当前限制

现在，如果您子类`Java.Lang.Object`，Xamarin.Android 将生成而不是.NET 嵌入 Java 存根 （stub） （Android 可调用包装器）。 因此，必须遵循相同的规则，用于导出C#为 Xamarin.Android 的 java。 例如：

```csharp
[Register("mono.embeddinator.android.ViewSubclass")]
public class ViewSubclass : TextView
{
    public ViewSubclass(Context context) : base(context) { }

    [Export("apply")]
    public void Apply(string text)
    {
        Text = text;
    }
}
```

* `[Register]` 需将映射到所需的 Java 包名称
* `[Export]` 需使方法成为对 Java 可见

我们可以使用`ViewSubclass`在 Java 中如下所示：

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

详细了解[使用 Xamarin.Android 的 Java 集成](~/android/platform/java-integration/index.md)。

## <a name="multiple-assemblies"></a>多个程序集

嵌入的单个程序集是一种简单;但是，它是更有可能会有多个C#程序集。 很多时候将 NuGet 包，例如 Android 支持库或 Google Play 服务的更加复杂的事情上具有依赖项。

这将导致一个进退两难的局面，因为.NET 嵌入需要到如最终 AAR 包含许多类型的文件：

* Android 资产
* Android 资源
* Android 本机库
* Android 的 java 源

很可能不希望从 Android 支持库或 Google Play Services 这些文件包含到你 AAR，但会在 Android Studio 中使用来自 Google 的官方版本。

下面是建议的方法：

* 传递.NET 嵌入自己拥有的任何程序集 （具有源代码），并且想要调用从 Java
* 传递.NET 嵌入 Android 资产、 本机库或中的资源所需的任何程序集
* 添加 Android 等的 Java 依赖项在 Android Studio 中支持库或 Google Play Services

因此，可能是您的命令：

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

你应从 NuGet 中排除任何内容，除非您了解它包含 Android 资产、 资源等，您需要在 Android Studio 项目中。 此外可以忽略不需要调用从 Java 和链接器的依赖项_应_包括你的库所需的部件。

若要添加在 Android Studio 中，所需的任何 Java 依赖项在**build.gradle**文件可能如下所示：

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>其他阅读材料

* [在 Android 上的回调](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与到开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [错误代码和说明](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>相关链接

- [天气示例 (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
