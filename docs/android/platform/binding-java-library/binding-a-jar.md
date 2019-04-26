---
title: 绑定 .JAR
description: 本演练提供了从 Android 创建 Xamarin.Android Java 绑定库的分步说明。JAR 文件。
ms.prod: xamarin
ms.assetid: 93F1D5C5-E2AF-46EA-8460-485A0860C176
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: 3c84b29807fd4a181ed867095645005bf9ba4df0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957238"
---
# <a name="binding-a-jar"></a>绑定 .JAR

_本演练提供了从 Android 创建 Xamarin.Android Java 绑定库的分步说明。JAR 文件。_

## <a name="overview"></a>概述

Android 社区还提供了许多可能想要使用应用程序中的 Java 库。 采用通常封装这些 Java 库。JAR （Java 存档文件） 格式，但您可以将打包。它在 JAR *Java 绑定库*，以便其功能是适用于 Xamarin.Android 应用。 Java 绑定库的目的是使中的 Api。JAR 文件提供给C#代码中通过自动生成的代码包装器。

Xamarin 工具可以生成绑定库从一个或多个输入。JAR 文件。 绑定库 (。DLL 程序集） 包含以下元素： 

-   原始内容。JAR 文件。

-   托管可调用包装 (MCW)，这是C#类型对应的 Java 类型中的自动换行。JAR 文件。

生成的 MCW 代码使用 JNI （Java 本机接口） 将 API 调用转发到基础。JAR 文件。 可以为任何创建绑定库。最初针对与 Android （请注意 Xamarin 工具中当前不支持非 Android Java 库的绑定） 一起使用的 JAR 文件。 您还可以选择用于生成绑定库而不包括的内容。JAR 文件，以便在 DLL 具有依赖项。JAR 在运行时。

在本指南中，我们将逐步完成创建一个绑定库的基础知识。JAR 文件。 我们将所有内容会直接的示例演示了&ndash;，即没有自定义或绑定的调试需要的。 
[创建绑定使用元数据](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)提供了绑定进程不是完全自动和一定量的手动干预是必需的更高级方案的示例。 Java 库绑定，一般情况下 （其中一个基本代码示例） 的概述，请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。 

 
## <a name="walkthrough"></a>演练

在下面的演练中，我们将创建一个绑定库，用于[看毕加索会如何](http://square.github.io/picasso/)，流行的 Android。提供了映像加载和缓存功能的 JAR。 我们将使用以下步骤来绑定**看毕加索会如何 2.x.x.jar** Xamarin.Android 项目中创建新的.NET 程序集，我们可以使用： 

1. 创建新的 Java 绑定库项目。

2. 添加。JAR 文件复制到项目。

3. 设置为相应的生成操作。JAR 文件。

4. 选择目标框架。支持 JAR。

5. 生成绑定库。

一旦我们创建绑定库，我们将开发一个小型的 Android 应用程序演示我们调用绑定库中的 Api 的能力。 在此示例中，我们想要访问的方法**看毕加索会如何 2.x.x.jar**:

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

我们生成一个绑定库，用于以后**看毕加索会如何 2.x.x.jar**，我们可以调用这些方法从C#。 例如：

```csharp
using Com.Squareup.Picasso;
...
Picasso.With (this)
    .Load ("http://mydomain.myimage.jpg")
    .Into (imageView);

```


### <a name="creating-the-bindings-library"></a>创建绑定库

在开始执行以下步骤之前, 请下载[看毕加索会如何 2.x.x.jar](http://repo1.maven.org/maven2/com/squareup/picasso/picasso/2.5.2/picasso-2.5.2.jar)。

首先，创建一个新的绑定库项目。 在 Visual Studio for Mac 或 Visual Studio 中，创建一个新的解决方案并选择*Android 绑定库*模板。 （在本演练中的屏幕截图使用 Visual Studio 中，但 Visual Studio for Mac 是非常相似。）将解决方案命名**JarBinding**: 

[![创建 JarBinding 库项目](binding-a-jar-images/01-new-bindings-library-sml.w157.png)](binding-a-jar-images/01-new-bindings-library.w157.png#lightbox)

该模板包含**Jar**文件夹在其中添加你。JAR(s) 到绑定库项目。 右键单击**Jar**文件夹，然后选择**添加 > 现有项**: 

[![添加现有项](binding-a-jar-images/02-add-existing-item-sml.png)](binding-a-jar-images/02-add-existing-item.png#lightbox)

导航到**看毕加索会如何 2.x.x.jar**前面下载的文件，选择它并单击**添加**: 

[![选择 jar 文件，然后单击添加](binding-a-jar-images/03-select-jar-file-sml.png)](binding-a-jar-images/03-select-jar-file.png#lightbox)

确认**看毕加索会如何 2.x.x.jar**文件已成功添加到项目： 

[![Jar 添加到项目](binding-a-jar-images/04-jar-added-sml.png)](binding-a-jar-images/04-jar-added.png#lightbox)

创建 Java 绑定库项目时，必须指定是否。JAR 是嵌入到绑定库中或单独打包。 若要执行此操作，您指定以下值之一*生成操作*: 

-   **EmbeddedJar** &ndash; 。绑定库中，将嵌入 JAR。

-   **InputJar** &ndash; 。将独立于绑定库保留 JAR。

通常情况下，使用**EmbeddedJar**生成操作，以便。JAR 自动打包到绑定库中。 这是最简单的选项&ndash;中的 Java 字节码。JAR 转换为 Dex 字节码，以及 （以及托管的可调用包装） 嵌入到 APK。 如果你想要保留。JAR 独立于绑定库，则可以使用**InputJar**选项; 但是，您必须确保。JAR 文件位于运行您的应用程序的设备上。 

生成操作设置为**EmbeddedJar**: 

[![选择 EmbeddedJar 生成操作](binding-a-jar-images/05-embeddedjar-sml.png)](binding-a-jar-images/05-embeddedjar.png#lightbox)

接下来，打开项目属性来配置*目标框架*。 如果。JAR 使用任何 Android Api，将目标框架设置为 API 级别。需要 JAR。 通常情况下，开发人员的。JAR 文件将指示哪个 API 级别 （或级别） 的。与兼容 JAR。 (有关目标框架设置和在常规的 Android API 级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。)

设置目标 API 级别绑定库 （在此示例中，我们将使用 API 级别 19）： 

[![目标 API 级别设置为 API 19](binding-a-jar-images/06-set-target-framework-sml.png)](binding-a-jar-images/06-set-target-framework.png#lightbox)


最后，生成绑定库。 尽管可能会显示某些警告消息，但绑定库项目应能成功生成，并生成输出。在以下位置的 DLL:**JarBinding/bin/Debug/JarBinding.dll**
    


### <a name="using-the-bindings-library"></a>使用绑定库

若要使用这个。DLL 在 Xamarin.Android 应用中，执行以下步骤：

1.  添加到绑定库的引用。

2.  对进行调用。JAR 通过托管的可调用包装器。 

在以下步骤中，我们将创建一个使用绑定库下载并显示的图像的最小应用`ImageView`;"繁重的工作"，可以驻留在的代码。JAR 文件。 

首先，创建使用绑定库的新 Xamarin.Android 应用程序。 右键单击解决方案并选择**添加新项目**; 将新项目命名**BindingTest**。 我们要在与绑定库相同的解决方案中创建此应用程序，为了简化本演练中;但是，使用绑定库应用程序，而是驻留在另一种解决方案： 

[![添加新 BindingTest 项目](binding-a-jar-images/07-add-new-project-sml.w157.png)](binding-a-jar-images/07-add-new-project.w157.png#lightbox)

右键单击**引用**的节点**BindingTest**项目，然后选择**添加引用...**:

[![直接添加引用](binding-a-jar-images/08-add-reference.png)](binding-a-jar-images/08-add-reference.png#lightbox)

检查**JarBinding**前面创建的项目并单击**确定**:

[![选择 JarBinding 项目](binding-a-jar-images/09-choose-jar-binding-sml.png)](binding-a-jar-images/09-choose-jar-binding.png#lightbox)

打开**引用**的节点**BindingTest**项目，然后确认**JarBinding**引用不存在： 

[![在引用下显示 JarBinding](binding-a-jar-images/10-references-shows-jarbinding-sml.png)](binding-a-jar-images/10-references-shows-jarbinding.png#lightbox)

修改**BindingTest**布局 (**Main.axml**)，以便它具有单个`ImageView`:

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

添加以下`using`语句**MainActivity.cs** &ndash;这样就可以轻松地访问的方法的基于 Java 的`Picasso`驻留在绑定库中的类：

```csharp
using Com.Squareup.Picasso;
```

修改`OnCreate`方法，以便使用`Picasso`类来从 URL 加载图像并将其显示在`ImageView`: 

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

编译并运行**BindingTest**项目。 应用程序将启动，并后一个短暂的延迟 （具体取决于网络条件），它应下载并显示类似于以下屏幕截图的映像：

[![屏幕截图的 BindingTest 运行](binding-a-jar-images/11-result-sml.png)](binding-a-jar-images/11-result.png#lightbox)

祝贺你！ 您已成功绑定 Java 库。JAR 并将 Xamarin.Android 应用中使用。
 
 
## <a name="summary"></a>总结

在本演练中，我们将创建第三方绑定库。JAR 文件，添加到最小化测试应用，绑定库，然后运行应用程序以验证我们C#代码可以调用 Java 代码驻留在中。JAR 文件。 



## <a name="related-links"></a>相关链接

- [生成 Java 绑定库 （视频）](https://university.xamarin.com/classes#10090)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
