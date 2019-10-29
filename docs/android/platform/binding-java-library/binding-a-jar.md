---
title: 绑定 .JAR
description: 本演练提供了从 Android 创建 Xamarin Android Java 绑定库的分步说明。JAR 文件。
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 59969abae739db1d9035ec31738c39a3912f47ae
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027774"
---
# <a name="binding-a-jar"></a>绑定 .JAR

_本演练提供了从 Android 创建 Xamarin Android Java 绑定库的分步说明。JAR 文件。_

## <a name="overview"></a>概述

Android 社区提供很多你可能想要在应用中使用的 Java 库。 这些 Java 库通常打包在中。JAR （Java 存档）格式，但你可以打包。将其 JAR 到*Java 绑定库*中，以便将其功能提供给 Xamarin Android 应用。 Java 绑定库的用途是使中的 Api 成为。可通过自动生成C#的代码包装程序的代码使用的 JAR 文件。

Xamarin 工具可以从一个或多个输入生成绑定库。JAR 文件。 绑定库（。DLL 程序集）包含以下内容： 

- 原始的内容。JAR 文件。

- 托管的可调用包装器（MCW） C# ，是在中包装相应的 Java 类型的类型。JAR 文件。

生成的 MCW 代码使用 JNI （Java 本机接口）将 API 调用转发到基础。JAR 文件。 可以为任何创建绑定库。最初针对于 Android 使用的 JAR 文件（请注意，Xamarin 工具目前不支持非 Android Java 库的绑定）。 你还可以选择生成绑定库，而不包括的内容。JAR 文件，以便 DLL 依赖于。运行时的 JAR。

在本指南中，我们将逐步介绍为单一的绑定库创建基础知识。JAR 文件。 我们将通过一个示例来说明，其中一切都是正确的 &ndash; 即，无需对绑定进行自定义或调试。 
[使用元数据创建绑定](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供了一个更高级的方案示例，其中的绑定过程并不完全自动，并且需要一定数量的手动干预。 有关一般情况下的 Java 库绑定概述（带有基本的代码示例），请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。 

## <a name="walkthrough"></a>演练

在下面的演练中，我们将创建[毕加索](https://square.github.io/picasso/)的绑定库，这是一个流行的 Android。提供图像加载和缓存功能的 JAR。 我们将使用以下步骤绑定**picasso-2**以创建可在 Xamarin Android 项目中使用的新 .net 程序集： 

1. 创建新的 Java 绑定库项目。

2. 添加。JAR 文件。

3. 为设置相应的生成操作。JAR 文件。

4. 选择一个目标框架。JAR 支持。

5. 生成绑定库。

一旦创建了绑定库，我们将开发一个小型 Android 应用程序，用于演示在绑定库中调用 Api 的能力。 在此示例中，我们想要访问**picasso-2**的方法：

```java
package com.squareup.picasso

public class Picasso
{ 
    ...
    public static Picasso with (Context context) { ... };
    ...
    public RequestCreator load (String path) { ... };
    ...
}
```

生成**picasso-2**的绑定库后，我们可以从C#调用这些方法。 例如:

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```

### <a name="creating-the-bindings-library"></a>创建绑定库

在开始执行以下步骤之前，请下载[picasso-2](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先，创建一个新的绑定库项目。 在 Visual Studio for Mac 或 Visual Studio 中，创建一个新的解决方案，然后选择 " *Android 绑定库*" 模板。 （本演练中的屏幕截图使用 Visual Studio，但 Visual Studio for Mac 非常相似。）将解决方案命名为**JarBinding**： 

[![创建 JarBinding 库项目](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

该模板包含一个**jar**文件夹，可在其中添加。到绑定库项目的 JAR。 右键单击 " **jar** " 文件夹，然后选择 "**添加 > 现有项**： 

[![添加现有项](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

导航到之前下载的**picasso-2**文件，选择它，然后单击 "**添加**"： 

[![选择 "jar 文件"，然后单击 "添加"](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

验证**picasso-2**文件是否已成功添加到项目中： 

[添加到项目的![Jar](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

创建 Java 绑定库项目时，必须指定是否JAR 将嵌入到绑定库中或单独打包。 为此，你可以指定以下*生成操作*之一： 

- **EmbeddedJar** &ndash;。JAR 将嵌入到绑定库中。

- **InputJar** &ndash;。JAR 将与绑定库保持分离。

通常，使用**EmbeddedJar**生成操作，以便。JAR 自动打包到绑定库中。 这是中 &ndash; Java 字节码最简单的选项。JAR 转换为 Dex 字节码，并嵌入到 APK 中（以及托管的可调用包装器）。 如果要保留，则为。JAR 与绑定库分离，你可以使用**InputJar**选项;但是，必须确保。JAR 文件在运行应用的设备上可用。 

将生成操作设置为**EmbeddedJar**： 

[![选择 EmbeddedJar 生成操作](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

接下来，打开项目属性以配置*目标框架*。 如果为。JAR 使用任意 Android Api，将目标框架设置为的 API 级别。JAR 需要。 通常，的开发人员。JAR 文件将指示的 API 级别（或级别）。JAR 与兼容。 （有关目标框架设置和一般 Android API 级别的详细信息，请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。）

设置绑定库的目标 API 级别（在本示例中，我们使用的是 API 级别19）： 

[![目标 API 级别设置为 API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)

最后，生成绑定库。 尽管可能会显示某些警告消息，但绑定库项目应成功生成并生成输出。DLL 位于以下位置： **JarBinding/bin/Debug/JarBinding**

### <a name="using-the-bindings-library"></a>使用绑定库

使用此。DLL，请执行以下操作：

1. 添加对绑定库的引用。

2. 调用。JAR 通过托管的可调用包装器。 

在下面的步骤中，我们将创建一个使用绑定库下载和显示 `ImageView`中的图像的最小应用;"重提升" 由驻留在中的代码完成。JAR 文件。 

首先，创建一个使用绑定库的新 Xamarin 应用程序。 右键单击该解决方案，然后选择 "**添加新项目**";将新项目命名为**BindingTest**。 我们要在与绑定库相同的解决方案中创建此应用程序，以便简化本演练;但是，使用绑定库的应用程序可以改为驻留在不同的解决方案中： 

[![添加新的 BindingTest 项目](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

右键单击 " **BindingTest** " 项目的 "**引用**" 节点，然后选择 "**添加引用 ...** "：

[![右添加引用](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

检查前面创建的**JarBinding**项目，然后单击 **"确定"** ：

[![选择 JarBinding 项目](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

打开**BindingTest**项目的 "**引用**" 节点，并验证**JarBinding**引用是否存在： 

[![JarBinding 显示在 "引用" 下](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

修改**BindingTest**布局（**main.axml**），使其具有单个 `ImageView`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <ImageView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/imageView" />
</LinearLayout>
```

将以下 `using` 语句添加到**MainActivity.cs** &ndash; 这样就可以轻松地访问绑定库中基于 Java 的 `Picasso` 类的方法：

```csharp
using Com.Squareup.Picasso;
```

修改 `OnCreate` 方法，使其使用 `Picasso` 类从 URL 加载图像并在 `ImageView`中显示该图像： 

```csharp
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
        ImageView imageView = FindViewById<ImageView>(Resource.Id.imageView);

        // Use the Picasso jar library to load and display this image:
        Picasso.With (this)
            .Load ("http://i.imgur.com/DvpvklR.jpg")
            .Into (imageView);
    }
}
```

编译并运行**BindingTest**项目。 应用将启动，并在短暂延迟后（取决于网络条件），应下载并显示类似于以下屏幕截图的图像：

[正在运行的 BindingTest 的![屏幕快照](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

祝贺你！ 已成功绑定 Java 库。JAR 并在你的 Xamarin Android 应用中使用它。

## <a name="summary"></a>总结

在本演练中，我们创建了第三方的绑定库。JAR 文件，将绑定库添加到最小测试应用程序，然后运行该应用程序，验证C#代码是否可以调用驻留在中的 Java 代码。JAR 文件。 

## <a name="related-links"></a>相关链接

- [生成 Java 绑定库（视频）](https://university.xamarin.com/classes#10090)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
