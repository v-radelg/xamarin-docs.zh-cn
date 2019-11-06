---
title: 绑定 .AAR
description: 本演练提供了从 Android 创建 Xamarin Android Java 绑定库的分步说明。AAR 文件。
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 103720c8cb47b1ac4cfe5cfadeb6b18828318ad3
ms.sourcegitcommit: 5a23c66f81853884480aca666d649a56d68c01cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618534"
---
# <a name="binding-an-aar"></a>绑定 .AAR

_本演练提供了从 Android 创建 Xamarin Android Java 绑定库的分步说明。AAR 文件。_

## <a name="overview"></a>概述

*Android 存档（。AAR）* file 是 Android 库的文件格式。
无.AAR 文件是一个。包含以下内容的 ZIP 存档：

- 已编译的 Java 代码
- 资源 Id
- 资源
- 元数据（例如，活动声明、权限）

在本指南中，我们将逐步介绍为单一的绑定库创建基础知识。AAR 文件。 有关一般情况下的 Java 库绑定概述（带有基本的代码示例），请参阅[绑定 Java 库](~/android/platform/binding-java-library/index.md)。

> [!IMPORTANT]
> 绑定项目只能包含一个。AAR 文件。 如果为。AAR 依赖于其他。AAR，则这些依赖项应包含在其自己的绑定项目中，然后引用这些依赖项。 请参阅[Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。

## <a name="walkthrough"></a>演练

我们将为示例 Android 存档文件创建一个绑定库，该文件是在 Android Studio， [aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true)中创建的。 此.AAR 包含一个 `TextCounter` 类，该类具有用于计算字符串中元音和辅音数目的静态方法。 此外， **textanalyzer aar**包含一个图像资源，用于帮助显示计数结果。

我们将使用以下步骤从中创建绑定库。AAR 文件：

1. 创建新的 Java 绑定库项目。

2. 添加单个。AAR 文件。 绑定项目只能包含一个。AAR.

3. 为设置相应的生成操作。AAR 文件。

4. 选择一个目标框架。AAR 支持。

5. 生成绑定库。

一旦创建了绑定库，我们将开发一个小型 Android 应用程序，该应用程序会提示用户输入文本字符串，然后调用。用于分析文本的 AAR 方法，从中检索图像。AAR，并显示与图像一起显示的结果。

示例应用将访问 textanalyzer 的 `TextCounter` 类 **。 aar**：

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

此外，此示例应用将检索并显示打包在 textanalyzer 中的图像资源 **。 aar**：

[![Xamarin 猴子映像](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

此图像资源驻留在**textanalyzer. aar**中的**res//猴子。**

### <a name="creating-the-bindings-library"></a>创建绑定库

在开始执行以下步骤之前，请下载示例[textanalyzer. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 存档文件：

1. 从 "Android 绑定库" 模板开始创建一个新的绑定库项目。 你可以使用 Visual Studio for Mac 或 Visual Studio （下面的屏幕截图显示 Visual Studio，但 Visual Studio for Mac 非常相似）。 将解决方案命名为**AarBinding**：

    [![创建 AarBindings 项目](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. 该模板包含一个**jar**文件夹，可在其中添加。AAR 绑定库项目。 右键单击 " **jar** " 文件夹，然后选择 "**添加 > 现有项**：

    [![添加现有项](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. 导航到之前下载的**textanalyzer**文件，选择它，然后单击 "**添加**"：

    [![添加 textanalayzer. aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. 验证**textanalyzer aar**文件是否已成功添加到项目中：

    [添加了 textanalyzer aar 文件 ![](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. 将**textanalyzer**的生成操作设置为 "`LibraryProjectZip`"。 在 Visual Studio for Mac 中，右键单击 " **aar** " 以设置生成操作。 在 Visual Studio 中，可在 "**属性**" 窗格中设置生成操作：

    [![将 aar 生成操作设置为 LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. 打开项目属性以配置*目标框架*。 如果为。AAR 使用任何 Android Api，将目标框架设置为的 API 级别。AAR 期望。 （有关目标框架设置和一般 Android API 级别的详细信息，请参阅[了解 ANDROID Api 级别](~/android/app-fundamentals/android-api-levels.md)。）

    设置绑定库的目标 API 级别。 在此示例中，我们可以自由使用最新的平台 API 级别（API 级别23），因为我们的**textanalyzer**不依赖 Android api：

    [![将目标级别设置为 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. 生成绑定库。 绑定库项目应成功生成并生成输出。DLL 位于以下位置： **AarBinding/bin/Debug/AarBinding**

### <a name="using-the-bindings-library"></a>使用绑定库

使用此。DLL，必须首先添加对绑定库的引用。 使用以下步骤：

1. 我们要在与绑定库相同的解决方案中创建此应用程序，以简化本演练。 （使用绑定库的应用程序也可以驻留在不同的解决方案中。）创建新的 Xamarin Android 应用：右键单击该解决方案，然后选择 "**添加新项目**"。 将新项目命名为**BindingTest**：

    [![创建新的 BindingTest 项目](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. 右键单击 " **BindingTest** " 项目的 "**引用**" 节点，然后选择 "**添加引用 ...** "：

    [![单击 "添加引用"](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. 选择前面创建的**AarBinding**项目，然后单击 **"确定"** ：

    [![检查 AAR 绑定项目](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. 打开**BindingTest**项目的 "**引用**" 节点，验证是否存在**AarBinding**引用：

    ["引用" 下列出 ![AarBinding](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

若要查看绑定库项目的内容，可以双击该引用以在**对象浏览器**中打开它。 您可以查看 `Com.Xamarin.Textcounter` 命名空间的映射内容（从 Java `com.xamarin.textanalyzezr` 包映射），并且您可以查看 `TextCounter` 类的成员：

[![查看对象浏览器](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上面的屏幕截图突出显示了该示例应用程序将调用的两个 `TextAnalyzer` 方法： `NumConsonants` （包装基础 Java `numConsonants` 方法）和 `NumVowels` （包装基础 Java `numVowels` 方法）。

### <a name="accessing-aar-types"></a>访问.AAR 类型

添加指向绑定库的应用的引用后，可以在中访问 Java 类型。AAR 访问C#类型（感谢C#包装）。 C#应用代码可以调用 `TextAnalyzer`方法，如以下示例中所示：

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上面的示例中，我们将在 `TextCounter` 类中调用静态方法。 但是，还可以实例化类和调用实例方法。 例如，如果你的。AAR 将名为 `Employee` 的类包装 `buildFullName`实例方法，可以实例化 `MyClass`，并使用它，如下所示：

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

以下步骤将代码添加到应用程序，以便它提示用户输入文本，使用 `TextCounter` 分析文本，然后显示结果。

将**BindingTest**布局（**main.axml**）替换为以下 XML。 此布局提供文本输入的 `EditText`，以及用于启动元音和辅音计数的两个按钮：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

将**MainActivity.cs**的内容替换为以下代码。 如本示例中所示，按钮事件处理程序调用包装在中的 `TextCounter` 方法。AAR 并使用 toast 来显示结果。 请注意绑定库的命名空间的 `using` 语句（在本例中为 `Com.Xamarin.Textcounter`）：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

编译并运行**BindingTest**项目。 此应用将开始，并在左侧显示屏幕截图（`EditText` 用一些文本进行初始化，但你可以点击它进行更改）。 点击 "**计数元音**" 时，toast 将显示元音的数目，如下所示：

[运行 BindingTest 的 ![屏幕快照](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

尝试点击**COUNT 辅音**按钮。 此外，您还可以修改文本行并再次点击这些按钮以测试不同的元音和辅音计数。

### <a name="accessing-aar-resources"></a>访问.AAR 资源

Xamarin 工具合并了**R**数据。AAR 到应用的**资源**类中。 因此，您可以访问。使用与访问项目**资源**路径中的资源相同的方式，从布局（和代码隐藏）中 AAR 资源。

若要访问图像资源，请使用**资源。** 在中打包的映像的名称。AAR. 例如，可以在中引用 **.png** 。使用 `@drawable/image`的 AAR 文件：

```xml
<ImageView android:src="@drawable/image" ... />
```

你还可以访问驻留在中的资源布局。AAR. 为此，请使用**资源. 布局**中打包的布局的名称。AAR. 例如:

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer. aar**示例包含位于**res/可绘制/猴子**的图像文件。 让我们访问此映像资源，并将其用于示例应用：

编辑**BindingTest**布局（**main.axml**），并将 `ImageView` 添加到 `LinearLayout` 容器末尾。 此 `ImageView` 显示 **\@可绘制/猴子**的图像;此映像将从 textanalyzer 的资源部分加载 **。 aar**：

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

编译并运行**BindingTest**项目。 点击 "**辅音**" 时，应用将启动并在左侧 &ndash; 显示屏幕截图，结果显示在右侧：

[显示辅音计数的 ![BindingTest](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

祝贺你！ 已成功绑定 Java 库。AAR!

## <a name="summary"></a>总结

在本演练中，我们创建了的绑定库。AAR 文件，将绑定库添加到最小测试应用，并运行应用以验证C#代码是否可以调用驻留在中的 Java 代码。AAR 文件。
此外，我们还扩展了应用程序以访问和显示驻留在中的图像资源。AAR 文件。

## <a name="related-links"></a>相关链接

- [生成 Java 绑定库（视频）](https://university.xamarin.com/classes#10090)
- [绑定 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [AarBinding （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573-一个项目无法绑定多个 aar 文件](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
