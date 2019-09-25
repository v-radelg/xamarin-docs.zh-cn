---
title: 为不同屏幕创建资源
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 6db927409e07b97ef5b7b1e7f54b6bcbdc60e115
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249665"
---
# <a name="creating-resources-for-varying-screens"></a>为不同屏幕创建资源

Android 本身在许多不同的设备上运行，每个设备的分辨率、屏幕大小和屏幕密度都多种多样。 Android 将执行缩放和调整大小操作，使应用程序可以在这些设备上工作，但这可能会导致用户体验不佳。 例如，图像可能会变得模糊，或在视图上以预期的方式放置。

## <a name="concepts"></a>概念

需要理解几个术语和概念，以支持多个屏幕。

- **屏幕大小**&ndash;用于显示应用程序的物理空间量

- **屏幕密度**&ndash;屏幕上任何给定区域的像素数。 典型度量单位是每英寸点数（dpi）。

- **解决方法**&ndash;屏幕上的总像素数。 开发应用程序时，解决方法并不像屏幕大小和密度那么重要。

- **与密度无关的像素（dp）** &ndash;用于实现布局独立于密度设计的虚拟度量单位。 此公式用于将 dp 转换为屏幕像素：

    px &equals; dp &times; dpi 160&divide;

- **方向**&ndash;当屏幕的宽度大于高度时，屏幕的方向被视为横向。 相反，当屏幕的高度大于宽度时，纵向方向就是。 在用户旋转设备时，在应用程序的生存期内，方向可能会改变。

请注意，这些概念中的前三个概念是&ndash;相互关联的，增加分辨率而不增加密度将增加屏幕大小。 但是，如果同时增加了密度和分辨率，则屏幕大小会保持不变。 屏幕大小、密度和分辨率之间的这种关系会使屏幕支持速度变得更加复杂。

为帮助应对这种复杂性，Android 框架倾向于对屏幕布局使用与*密度无关的像素（dp）* 。 通过使用密度独立像素，用户可在具有不同密度的屏幕上向用户显示相同的物理大小。

## <a name="supporting-various-screen-sizes-and-densities"></a>支持各种屏幕尺寸和密度

Android 会处理大部分工作，以便为每个屏幕配置正确呈现布局。 但是，可以采取一些措施来帮助系统完成操作。

在大多数情况下，在大多数情况下使用与密度无关的像素，而不是实际像素，以确保密度独立性。
Android 会在运行时将绘图扩展到适当大小。
但是，缩放可能会导致位图显示模糊。 若要解决此问题，请为不同的密度提供备用资源。 为多个分辨率和屏幕密度设计设备时，从较高的分辨率或密度图像开始，然后向下减小更容易。

### <a name="declare-the-supported-screen-size"></a>声明支持的屏幕大小

声明屏幕大小可确保只有受支持的设备才能下载应用程序。 这是通过在**androidmanifest.xml**文件中设置[支持-网屏](https://developer.android.com/guide/topics/manifest/supports-screens-element.html)元素来完成的。 此元素用于指定应用程序支持的屏幕大小。 如果应用程序可以将其布局正确地放置在填充屏幕上，则将给定屏幕视为受支持。 使用此清单元素，应用程序将不会显示在不满足屏幕规范的设备[*Google Play*](https://play.google.com/)中。 但是，应用程序仍将在具有不受支持的屏幕的设备上运行，但这些布局可能看起来比较模糊和像素化。

支持的屏幕 sixes 在解决方案的**属性/androidmanifest.xml**文件中声明：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Android 清单](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Android 清单](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

编辑**androidmanifest.xml**以包含[支持-屏幕](https://developer.android.com/guide/topics/manifest/supports-screens-element.html)：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="HelloWorld.HelloWorld">
      <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="27" />
      <supports-screens android:resizable="true"
                        android:smallScreens="true"
                        android:normalScreens="true"
                        android:largeScreens="true" />
      <application android:allowBackup="true"
                   android:icon="@mipmap/ic_launcher"
                   android:label="@string/app_name"
                   android:roundIcon="@mipmap/ic_launcher_round"
                   android:supportsRtl="true" android:theme="@style/AppTheme">
  </application>
</manifest>
```

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>提供不同屏幕大小的替代布局

使用替代布局可以自定义特定屏幕大小的视图，并更改组件 UI 元素的位置或大小。

从 API 级别13（Android 3.2）开始，屏幕大小将在使用 sw*N*dp 限定符时弃用。 此新限定符声明给定布局需要的空间量。 建议在 Android 3.2 或更高版本上运行的应用程序应使用这些较新的限定符。

例如，如果布局需要屏幕宽度的最小 700 dp，则备用布局会处于文件夹**布局-sw700dp**：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![700 dp 屏幕宽度的布局文件夹](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![700 dp 屏幕宽度的布局文件夹](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----

作为指导原则，以下是各种设备的一些数字：

- **典型电话**&ndash; 320 dp：典型电话

- **5 个 "绘图板/" tweener "设备**&ndash; 480 dp：例如 Samsung 注释

- **7**600 dp：例如 Barnes &amp; Noble Nook &ndash;

- **10 个 "平板电脑**&ndash; 720 dp：例如 Motorola Xoom

对于面向最多为12（Android 3.1）的 API 级别的应用程序，这些布局应在使用限定符**小型**/**普通**/**大型**/**加大**的目录中，作为大多数设备中提供的各种屏幕大小。 例如，在下图中，有四种不同屏幕大小的备用资源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![四个屏幕大小的备用资源](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![四个屏幕大小的备用资源](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

下面比较了较旧的预 API 级别13屏幕大小限定符与与密度无关的像素之间的比较：

- 426 dp x 320 dp 为**小**

- 470 dp x 320 dp**正常**

- 640 dp x 480 dp 太**大**

- 960 dp x 720 dp 为**加大**

API 级别13和更高版本中更新的屏幕大小限定符的优先级高于 API 级别12和更低版本的旧屏幕限定符。
对于将跨越旧的和新的 API 级别的应用程序，可能需要使用这两组限定符创建备用资源，如以下屏幕截图所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![具有两个限定符的备用资源](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![具有两个限定符的备用资源](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----

### <a name="provide-different-bitmaps-for-different-screen-densities"></a>为不同的屏幕密度提供不同的位图

尽管 Android 会根据需要为设备缩放位图，但位图本身可能无法完美地缩放：它们可能会变得模糊或模糊。 提供适用于屏幕密度的位图会减轻此问题。

例如，下面的图像是一个布局和外观问题的示例，在未提供密度指定资源时，可能会出现这种问题。

![无密度资源的屏幕截图](resources-for-varying-screens-images/06-density-not-provided.png)

将此与使用特定于密度的资源设计的布局进行比较：

![具有特定于密度的资源的屏幕截图](resources-for-varying-screens-images/07-density-specific-resources.png)

### <a name="create-varying-density-resources-with-android-asset-studio"></a>用 Android 资产工作室创建不同的密度资源

创建各种密度的位图可能有点枯燥。 因此，Google 创建了一个联机实用工具，可减少创建这些称为[**Android 资产工作室**](https://romannurik.github.io/AndroidAssetStudio/)的位图所涉及的一些麻烦。

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

此网站通过提供一个图像帮助创建面向四个常见屏幕密度的位图。 然后，Android 资产工作室将创建包含某些自定义项的位图，并允许将它们下载为 zip 文件。

## <a name="tips-for-multiple-screens"></a>多屏幕提示

Android 在非常惊人数量的设备上运行，屏幕大小和屏幕密度的组合似乎非常大。 以下提示可帮助最大限度地减少支持各种设备所需的工作量：

- **仅针对所需的内容进行设计和开发**&ndash;这里有很多不同的设备，但有些设备存在极少的外形因素，可能需要大量精力来设计和开发。 "[**屏幕大小" 和 "密度**](https://developer.android.com/resources/dashboard/screens.html)" 仪表板是 Google 提供的一页，提供有关屏幕大小/屏幕密度矩阵细目的数据。 此细目提供了有关如何在支持屏幕上进行开发工作的见解。

- **使用 DPs 而不是像素**-当屏幕密度发生变化时，像素会变得很麻烦。 不要将像素值硬编码。 避免将像素用于 dp （与密度无关的像素）。

- **避免**[AbsoluteLayout](xref:Android.Widget.AbsoluteLayout)只要有可能&ndash; ，它会在 API 级别3（Android 1.5）中弃用，并将产生脆弱布局。 
   不应使用此方法。 相反，尝试使用更灵活的布局小组件，例如[**LinearLayout**](xref:Android.Widget.LinearLayout)、 [**RelativeLayout**](xref:Android.Widget.RelativeLayout)或新的[**GridLayout**](xref:Android.Widget.GridLayout)。

- **选取一个布局方向作为默认布局**例如，将资源置于布局中并将纵向资源置于布局端口，而不是提供备用资源布局-土地和布局端口。 &ndash;

- **使用 LayoutParams 作为高度和宽度**-在 XML 布局文件中定义 UI 元素时，使用**wrap_content**和**fill_parent**值的 Android 应用程序将获得更多成功，确保不同设备上的外观与使用像素或密度无关的单位。 这些维度值会使 Android 根据需要缩放位图资源。 出于此原因，在指定 UI 元素的边距和填充时，最好保留与密度无关的单位。

## <a name="testing-multiple-screens"></a>测试多个屏幕

必须针对受支持的所有配置对 Android 应用程序进行测试。 理想情况下，应在实际设备上测试设备，但在许多情况下，这是不可能的或不切实际的。
在这种情况下，对每个设备配置使用的模拟器和 Android 虚拟设备设置将很有用。

Android SDK 提供了一些模拟器外观，可以使用它来创建 Avd，以便复制多个设备的大小、密度和分辨率。
许多硬件供应商同样提供其设备的外观。

另一种方法是使用第三方测试服务的服务。
这些服务将采用 APK，在许多不同的设备上运行它，然后提供应用程序的工作方式反馈。
