---
title: 为不同屏幕创建资源
ms.prod: xamarin
ms.assetid: 3D17DE45-115C-7192-5685-44F8EEE07DCC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/28/2018
ms.openlocfilehash: 63cbe556783ffe22512ff5312817d522120bd15e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670566"
---
# <a name="creating-resources-for-varying-screens"></a>为不同屏幕创建资源

Android 本身在设备上运行多个不同，每个都拥有各种分辨率、 屏幕大小和屏幕密度。 Android 将执行缩放和调整大小以使应用程序在这些设备上运行，但这可能会导致非最佳用户体验。 例如，图像可能会显示模糊，或者它们可能会按预期在视图上定位。


## <a name="concepts"></a>概念

几个术语和概念非常重要，若要了解为支持多个屏幕。

- **屏幕大小**&ndash;用于显示你的应用程序的物理空间量

- **屏幕密度**&ndash;在屏幕上任何给定区域中的像素数。 典型单位是度量的每英寸点数 (dpi)。

- **解决方法**&ndash;在屏幕上的像素为单位的总数。 开发应用程序时, 解析不可与屏幕大小和密度一样重要。

- **密度无关像素 (dp)** &ndash;虚拟单位度量值以允许布局设计为独立的密度。 此公式用于将分发点转换成屏幕像素：

    px &equals; dp &times; dpi &divide; 160

- **方向**&ndash;屏幕的方向被视为可横向宽度大于高度时。 与此相反，纵向方向为时屏幕的高度大于宽度。 如用户旋转设备，可以在应用程序的生存期内更改方向。

请注意，这些概念的前三个相互关联&ndash;提高分辨率不增加密度会增加屏幕大小。 但是如果增加的密度和解决方法，那么屏幕大小可以保持不变。 屏幕大小、 密度和解决方法之间的关系使变得复杂屏幕支持快速。

为了帮助应对这种复杂性，Android 框架倾向于使用*密度无关的像素 (dp)* 对于屏幕的布局。 通过使用密度无关的像素，UI 元素将显示以用户在具有不同密度的屏幕上具有相同的物理大小。


## <a name="supporting-various-screen-sizes-and-densities"></a>支持不同屏幕大小和密度

Android 处理大部分工作来呈现的每个屏幕配置正确的布局。 但是，有一些执行的操作，可以帮助解决问题的系统。

使用而不是在布局中的实际像素密度无关像素已足够在大多数情况下，以确保密度独立性。
Android 将在运行时为适当大小缩放绘图。
但是，有可能，缩放将导致位图，以显示模糊。 若要解决此问题，提供针对不同密度的备用资源。 当设计用于多个分辨率和屏幕密度的设备，它将证明管理更轻松开始使用更高的分辨率或密度映像然后再减少。


### <a name="declare-the-supported-screen-size"></a>声明支持的屏幕大小

声明的屏幕大小可确保仅受支持的设备可以下载应用程序。 这可以通过设置[支持屏幕](https://developer.android.com/guide/topics/manifest/supports-screens-element.html)中的元素**AndroidManifest.xml**文件。 此元素用于指定应用程序支持何种屏幕大小。 如果在应用程序可以正确地放置其布局以填满屏幕支持被视为给定的屏幕。 通过使用此清单元素，该应用程序将不会显示在[ *Google Play* ](https://play.google.com/)适用于不符合屏幕规范的设备。 但是，应用程序仍将具有不受支持的屏幕，设备上运行，但两种布局可能出现的模糊和像素化。

中的声明支持的屏幕 sixes **Properites/AndroidManifest.xml**解决方案的文件：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Android 清单](resources-for-varying-screens-images/01-android-manifest-sml.w1581.png)](resources-for-varying-screens-images/01-android-manifest.w1581.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Android 清单](resources-for-varying-screens-images/01-android-manifest-sml.m761.png)](resources-for-varying-screens-images/01-android-manifest.m761.png#lightbox)

-----

编辑**AndroidManifest.xml**包括[支持屏幕](https://developer.android.com/guide/topics/manifest/supports-screens-element.html):

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

### <a name="provide-alternate-layouts-for-different-screen-sizes"></a>针对不同屏幕大小提供备用布局


备用布局可自定义视图的特定的屏幕大小、 更改的位置或组件 UI 元素的大小。

从 API 级别 13 (Android 3.2) 开始，屏幕大小将不推荐使用而改为使用 sw*N*dp 限定符。 此新限定符声明需要给定布局空间量。 建议用于运行在 Android 3.2 或更高版本的应用程序应使用这些较新的限定符。

例如，如果布局所需的最小 700 屏幕宽度的分发点，备用布局会转的文件夹中**布局 sw700dp**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![700 dp 屏幕宽度的布局文件夹](resources-for-varying-screens-images/03-layout-sw700dp-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![700 dp 屏幕宽度的布局文件夹](resources-for-varying-screens-images/03-layout-sw700dp-xs.png)

-----


作为一般准则，下面是一些数字为各种设备：

- **典型的电话** &ndash; 320 dp： 典型的电话

- **5"平板电脑 /"tweener"设备** &ndash; 480 dp： 例如 Samsung 记录

- **7"平板电脑** &ndash; 600 dp： 如 Barnes &amp; Noble 四处

- **10 个"平板电脑** &ndash; 720 dp： 如 Motorola Xoom

应用程序的目标 API 级别最多 12 (Android 3.1) 布局应放入目录使用限定符**小型**/**正常**/**大型** /**加大**作为不同屏幕大小所提供的大多数设备的泛化。 例如，在下图中，有四个不同的屏幕大小的备用资源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![适用于四个屏幕大小的备用资源](resources-for-varying-screens-images/04-layout-large-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![适用于四个屏幕大小的备用资源](resources-for-varying-screens-images/04-layout-large-xs.png)

-----

以下是较旧的 pre-API 级别 13 屏幕大小限定符如何与密度无关的像素进行比较的比较：

- 426 dp x 320 dp 是**小**

- 470 dp x 320 dp 是**正常**

- 640 dp x 480 dp 是**大型**

- 960 dp x 720 dp 是**加大**

较新屏幕的大小在 API 级别 13 中的限定符并设置具有优先权要高于 API 级别 12 和更低的较旧屏幕限定符。
对于跨应用程序，将旧的和新的 API 级别，它可能有必要创建备用资源使用这两个集的限定符，如以下屏幕截图中所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![使用这两个限定符的备用资源](resources-for-varying-screens-images/05-both-qualifiers-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![使用这两个限定符的备用资源](resources-for-varying-screens-images/05-both-qualifiers-xs.png)

-----



### <a name="provide-different-bitmaps-for-different-screen-densities"></a>针对不同屏幕密度提供不同的位图

虽然 Android 缩放根据设备的位图，位图本身可能不完美地增加或减少： 它们可能会变得模糊。 提供适用于屏幕密度的位图将缓解此问题。

例如下, 图是可能发生的布局和外观问题的示例时密度-指定未提供资源。

![不带密度资源的屏幕截图](resources-for-varying-screens-images/06-density-not-provided.png)

这与使用特定于密度的资源设计的布局：

![使用特定于密度的资源的屏幕截图](resources-for-varying-screens-images/07-density-specific-resources.png)


### <a name="create-varying-density-resources-with-android-asset-studio"></a>Android Asset Studio 创建不同的密度资源

这些位图不同密度的创建可能会有点令人厌倦。 在这种情况下，Google 已创建一个在线实用程序，这样可以减少一些涉及与名为这些位图创建内容的麻烦[ **Android Asset Studio**](https://romannurik.github.io/AndroidAssetStudio/)。

[![Android Asset Studio](resources-for-varying-screens-images/08-android-asset-studio-sml.png)](resources-for-varying-screens-images/08-android-asset-studio.png#lightbox)

此网站将创建一个映像，从而针对四个通用屏幕密度的位图的帮助。 Android Asset Studio 将然后创建一些自定义位图，并可以稍后要下载为 zip 文件。


## <a name="tips-for-multiple-screens"></a>多个屏幕的提示

Android 上创作多个设备上，运行，屏幕大小和屏幕密度的组合可能会感到不知所措。 以下提示可帮助最大程度减少支持各种设备所需的工作量：

- **仅设计和开发为所需**&ndash;有许多不同设备，但某些极少数外观造型，可能需要花费大量精力来设计和开发的存在。 [**屏幕大小和密度**](https://developer.android.com/resources/dashboard/screens.html)仪表板是提供数据的屏幕大小/屏幕密度矩阵分解的 Google 提供的页。 此明细提供有关如何支持屏幕上的开发工作的见解。

- **使用分发点而不是像素**-像素会变得麻烦作为屏幕密度的更改。 请不要对像素值。 避免以 dp （密度无关像素为单位） 支持的像素为单位。

- **避免** [AbsoluteLayout](https://developer.xamarin.com/api/type/Android.Widget.AbsoluteLayout/)
  **只要可能**&ndash;它在 API 级别 3 (Android 1.5) 中已弃用，并且会导致脆弱的布局。 不应使用。 相反，尝试使用如下所示更灵活的布局小组件[ **LinearLayout**](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)， [ **RelativeLayout**](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)，或新[**GridLayout**](https://developer.xamarin.com/api/type/Android.Widget.GridLayout/)。

- **选取一个为默认布局方向**&ndash;例如，而不是提供的备用资源**布局 land**并**布局端口**，放置的资源在横向**布局**，以及为纵向的资源**布局端口**。

- **使用高度和宽度的 LayoutParams** -在 XML 布局文件中，定义 UI 元素时 Android 应用程序中使用**wrap_content**和**fill_parent**值将为获得更大成功确保正确查看跨不同设备比使用像素或密度无关的单位。 这些维度值导致 Android 来相应地缩放位图资源。 鉴于相同原因，密度无关的单位是最适合保留有关何时指定边距和填充的 UI 元素。


## <a name="testing-multiple-screens"></a>测试多个屏幕

Android 应用程序必须针对所有受支持的配置进行测试。 理想情况下应在实际设备自身上测试设备，但在许多情况下这是不可能或可行。
在这种情况下，每个设备配置为使用仿真程序和 Android 虚拟设备安装程序将很有用。

Android SDK 提供了一些仿真器外观可用于创建 Avd 将复制大小、 密度和多个设备的分辨率。
许多硬件供应商同样提供为其设备的外观。

另一种方法是使用第三方测试服务的服务。
这些服务将需要对 apk 进行，许多不同设备上运行，然后提供应用程序的工作方式的反馈。
