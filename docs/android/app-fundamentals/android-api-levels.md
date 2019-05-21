---
title: 了解 Android API 级别
description: Xamarin.Android 已确定与多个版本的 Android 应用程序的兼容性的多个 Android API 级别设置。 本指南介绍了这些设置的含义、 如何配置它们，以及有何影响它们对您的应用程序在运行时。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 1f88525fefb83c92d5e5dda2176d3622bb67c78d
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924955"
---
# <a name="understanding-android-api-levels"></a>了解 Android API 级别

_Xamarin.Android 已确定与多个版本的 Android 应用程序的兼容性的多个 Android API 级别设置。本指南介绍了这些设置的含义、 如何配置它们，以及有何影响它们对您的应用程序在运行时。_


## <a name="quick-start"></a>快速入门

Xamarin.Android 提供了三个 Android API 级别的项目设置：

-   [目标框架](#framework)&ndash;指定要在构建您的应用程序使用哪个框架。 在使用此 API 级别*编译*时间通过 Xamarin.Android。

-   [最低 Android 版本](#minimum)&ndash;指定您希望在应用程序支持的最旧的 Android 版本。 在使用此 API 级别*运行*android 时间。

-   [目标 Android 版本](#target)&ndash;指定你的应用 Android 版设计用于上运行。 在使用此 API 级别*运行*android 时间。

可以为你的项目配置 API 级别之前，必须安装该 API 级别的 SDK 平台组件。 有关下载和安装 Android SDK 组件的详细信息，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)。

> [!NOTE]
> 从 2018 年 8 月开始，Google Play 控制台将需要新的应用程序目标 API 级别 26 (Android 8.0) 或更高版本。
现有应用程序将需要目标 API 级别 26 或更高版本开始，在 2018 年 11 月。 有关详细信息，请参阅[改善应用程序安全性和性能在即将年的 Google Play 上](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

通常情况下，所有三个 Xamarin.Android API 级别设置为相同的值。 上**应用程序**页上，将**使用 Android 版本 （目标框架） 编译**到最新的稳定 API 版本 （或最小值，包含所有所需的功能的 Android 版本）。
在以下屏幕截图中，目标框架设置为**Android 7.1 (API 级别 25-Nougat)**:

[![目标框架版本默认为使用 Android 版本编译](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

上**Android 清单**页上，将最低 Android 版本设置为**使用编译使用 SDK 版本**并将目标 Android 版本设置为相同的值 （在下面的示例的目标框架版本屏幕截图中，目标 Android 框架设置为**Android 7.1 (Nougat)**):

[![最小值和目标 Android 版本设置为目标框架版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果你想要保持与早期的 Android 版本的向后兼容性，设置**到目标的最低 Android 版本**到所需应用程序以支持 Android 的最早版本。 (请注意，API 级别 14 是所需的最低 API 级别[Google Play 服务和 Firebase 支持](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。)下面的示例配置支持 Android API 级别 14 通过 API 级别 25 的版本：

[![使用 API 级别 25 编译 Nougat，最低 Android 版本设置为 API 级别 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

通常情况下，所有三个 Xamarin.Android API 级别设置为相同的值。 设置**目标框架**到最新的稳定 API 版本 （或最小值，包含所有所需的功能的 Android 版本）。 若要设置**目标框架**，导航到**生成 > 常规**中**项目选项**。 在以下屏幕截图中，目标框架设置为**使用最新安装的平台 (8.0)**:

[![默认为使用最新安装的平台的目标框架](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

可在最小值和目标 Android 版本设置下找到**生成 > Android 应用程序**中**项目选项**。 最低 Android 版本设置为**自动-使用目标框架版本**并将目标 Android 版本设置为与目标框架版本相同的值。 在以下屏幕截图中，目标 Android 框架设置为**Android 8.0 （API 级别 26）** 以匹配目标框架设置更高版本：

[![在项目选项中设置的目标和框架级别](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果你想要保持与早期的 Android 版本的向后兼容性，更改**最低 Android 版本**到所需应用程序以支持 Android 的最早版本。 请注意，API 级别 14 是所需的最低 API 级别[Google Play 服务和 Firebase 支持](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)。
例如，下面的配置早 API 级别 14 支持 Android 版本：

[![最小值和目标版本设置为自动-使用目标框架版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


如果您的应用程序支持多个 Android 版本，你的代码必须包含运行时检查以确保您的应用程序可使用的最低 Android 版本设置 (请参阅[运行时检查的 Android 版本](#runtimechecks)下面有关详细信息)。 如果要使用或创建一个库，请参阅[API 级别和库](#libraries)下面有关最佳实践配置 API 级别设置的库。



## <a name="android-versions-and-api-levels"></a>Android 版本和 API 级别

在 Android 平台的发展和新的 Android 版本的发布时，每个 Android 版本分配唯一整数标识符，称为*API 级别*。 因此，每个 Android 版本对应于单个的 Android API 级别。 由于用户安装应用程序的较旧也为最新版本的 Android 上，必须设计实际的 Android 应用程序可以使用多个 Android API 级别。


### <a name="android-versions"></a>Android 版本

每个版本的 Android 会由多个名称：

-   Android 版本，如**Android 9.0**
-   代码 （或餐后甜点） 名称，例如_饼图_
-   相应的 API 级别，如**API 级别 28**

Android 的代码名称可能对应于多个版本和 API 级别 （如在下表中所示），但每个 Android 版本对应于一个 API 级别。

此外，定义 Xamarin.Android*生成版本代码*，它将映射到当前已知的 Android API 级别。 下表可以帮助你进行 API 级别、 Android 版本、 代码名称和 Xamarin.Android 生成版本代码之间转换 (生成版本代码中定义`Android.OS`命名空间):

[!include[](~/android/includes/api-levels.md)]

此表所示，新的 Android 版本的发布频率&ndash;有时多个版本每年。 因此，可能运行你的应用的 Android 设备的通用包括的各种较旧和较新的 Android 版本。 如何保证您的应用程序将许多不同版本的 Android 上运行一致而可靠地？ Android API 级别可帮助你管理此问题。


### <a name="android-api-levels"></a>Android API 级别

每个 Android 设备运行在完全*一个*API 级别&ndash;此 API 级别保证是唯一的每个 Android 平台版本。 API 级别精确标识您的应用程序可以调用; 的 API 集的版本它标识作为开发人员的清单元素、 权限、 编码的对等组合。 Android 的系统的 API 级别可帮助确定应用程序是否与在设备上安装应用程序之前的 Android 系统映像兼容的 Android。

当生成应用程序时，它包含以下 API 级别信息：

-   *目标*的生成应用上运行的 Android API 级别。

-   *最小*Android 设备必须运行你的应用的 Android API 级别。 

这些设置用于确保在安装时正常运行应用程序所需的功能也可在 Android 设备上。 如果没有，阻止该设备上运行应用。 例如，如果 Android 设备的 API 级别低于您指定为你的应用的最低 API 级别，在 Android 设备将阻止用户安装您的应用程序。


## <a name="project-api-level-settings"></a>项目 API 级别设置

以下各节说明如何使用 SDK 管理器的 API 级别，你想要为目标，跟如何配置的详细说明准备开发环境*目标框架*，*最小值Android 版本*，并*目标 Android 版本*在 Xamarin.Android 中的设置。


### <a name="android-sdk-platforms"></a>Android SDK 平台

可以在 Xamarin.Android 中选择目标或最低 API 级别之前，必须到该 API 级别安装相对应的 Android SDK 平台版本。 目标框架、 最低 Android 版本和目标 Android 版本的可用选项的范围被限制为已安装的范围的 Android SDK 版本。 可以使用 SDK 管理器以验证安装了所需的 Android SDK 版本，并可用来添加任何新的 API 级别所需的应用程序。 如果您不熟悉如何安装 API 级别，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)。

<a name="framework" />

### <a name="target-framework"></a>目标 Framework

*目标框架*(也称为`compileSdkVersion`) 是在生成时的编译你的应用程序的特定 Android 框架版本 （API 级别）。 此设置指定哪些 Api 应用*需要*时运行，但它不起作用的 Api 是实际可供您的应用程序在安装时使用。 因此，更改目标框架设置不会更改运行时行为。

目标框架标识你的应用程序针对链接的库版本&ndash;此设置确定可以在应用中使用哪些 Api。 例如，如果你想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/)在 Android 5.0 Lollipop 中引入的方法，必须将目标框架设置为**API Level 21 (Lollipop)** 或更高版本。 如果将你的项目的目标框架设置为 API 级别等**API 级别 19 (KitKat)** 尝试调用`SetCategory`在代码中的方法，将收到编译错误。

我们建议始终编译与*最新*可用的目标框架版本。 执行此操作提供有用的警告消息可能由代码调用任何已弃用的 api。 使用最新支持库版本时，使用最新的目标框架版本则尤其&ndash;每个库要求您的应用程序进行编译时支持该库的最低 API 级别或更高版本。 


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要访问 Visual Studio 中的目标框架设置，打开项目属性中的**解决方案资源管理器**，然后选择**应用程序**页：

[![应用程序项目属性页](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

通过选择下的下拉列表菜单中的 API 级别设置目标框架**使用 Android 版本编译**如上所示。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要访问 Mac 在 Visual Studio 中的目标框架设置，请右键单击项目名称并选择**选项**; 这将打开**项目选项**对话框。 在此对话框中，导航到**生成 > 常规**如下所示：

[![生成项目选项页的常规部分](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

通过选择右侧的下拉列表菜单中的 API 级别设置目标框架**目标框架**如上所示。

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>最低 Android 版本

*最低 Android 版本*(也称为`minSdkVersion`) 是最早版本的 Android OS （即最低 API 级别），可以安装和运行你的应用程序。 默认情况下，应用仅可匹配目标框架设置的设备上已安装或更高版本;如果最低 Android 版本设置为*较低*高于目标框架设置中，您的应用程序还可以在早期版本的 Android 上运行。 例如，如果目标框架设置为**Android 7.1 (Nougat)** 并将最低 Android 版本设置为**Android 4.0.3 (Ice Cream Sandwich)**，可以在 API 级别 15 从任何平台上安装了应用为 API 级别 25，非独占。

尽管您的应用程序可能会成功生成，并在此系列的平台上安装，但这并不保证将成功*运行*所有这些平台上。 例如，如果您的应用程序安装在**Android 5.0 (Lollipop)** 和你的代码调用一个 API，仅适用于**Android 7.1 (Nougat)** 和更高版本，您的应用程序将获取运行时错误和可能崩溃。 因此，必须确保你的代码&ndash;在运行时&ndash;，它调用仅支持的 Android 设备上运行的 Api。 换而言之，你的代码必须包含显式运行时检查，以确保你的应用仅在不够高，无法支持它们的设备上使用较新的 Api。
[运行时检查的 Android 版本](#runtimechecks)本指南中，将在后面介绍了如何将这些运行时检查添加到你的代码。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要访问 Visual Studio 中的最低 Android 版本设置，打开项目属性中的**解决方案资源管理器**，然后选择**Android 清单**页。 下的下拉列表菜单中**最低 Android 版本**可以为应用程序选择最低 Android 版本：

[![最低 Android 版本设置为使用 SDK 版本编译的目标选项](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果选择**使用编译使用 SDK 版本**，最低 Android 版本将作为目标框架设置相同。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要访问的 Mac 在 Visual Studio 中的最低 Android 版本，请右键单击项目名称并选择**选项**; 这将打开**项目选项**对话框。 导航到**生成 > Android 应用程序**。
使用右侧的下拉列表菜单**最低 Android 版本**，可以为你的应用程序设置最低 Android 版本：

[![最低 Android 版本设置为自动-使用目标框架版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果选择**自动&ndash;使用目标框架版本**，最低 Android 版本将作为目标框架设置相同。

-----


<a name="target" />

### <a name="target-android-version"></a>目标 Android 版本

*目标 Android 版本*(也称为`targetSdkVersion`) 是 API 级别的 Android 设备的应用应运行在其中。 Android 使用此设置来确定是否启用任何兼容性行为&ndash;这可确保您的应用程序继续按你期望的方式。 Android 使用您的应用程序的目标 Android 版本设置来判断哪些行为更改可应用于您的应用程序而不会破坏它 （这是 Android 如何提供向前兼容性）。

目标框架和目标 Android 版本，具有非常相似的名称，而不是相同的操作。 目标框架设置目标 API 级别将信息传递给 Xamarin.Android 以供*编译时*，而目标 Android 版本目标 API 级别将信息传递给 Android 以便在使用*运行时*（应用程序的设备上安装并运行）。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要访问 Visual Studio 中的此设置，打开项目属性中的**解决方案资源管理器**，然后选择**Android 清单**页。 下的下拉列表菜单中**目标 Android 版本**可以为应用程序中选择目标 Android 版本：

[![目标 Android 版本进行编译使用 SDK 版本](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

我们建议您显式设置为最新版本的用于测试你的应用的 Android 目标 Android 版本。 理想情况下，它应设置为最新的 Android SDK 版本&ndash;这样即可使用新的 Api 之前通过行为更改的工作。 对于大多数开发人员，我们*不这样做*建议目标 Android 版本设置成**使用编译使用 SDK 版本**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要访问的 Mac 在 Visual Studio 中的此设置，请右键单击项目名称并选择**选项**; 这将打开**项目选项**对话框。 导航到**生成 > Android 应用程序**。 使用右侧的下拉列表菜单**目标 Android 版本**，可以为你的应用程序设置目标 Android 版本：

[![目标 Android 版本设置为自动-使用目标框架版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

我们建议您显式设置为最新版本的用于测试你的应用的 Android 目标 Android 版本。 理想情况下，它应设置为最新可用的 Android SDK 版本&ndash;这样即可使用新的 Api 之前通过行为更改的工作。 对于大多数开发人员，我们不建议将目标 Android 版本设置为**自动-使用目标框架版本**。

-----

一般情况下，目标 Android 版本应受最低 Android 版本和目标框架。 即：

**最低 Android 版本 < = 目标 Android 版本 < = 目标框架**

有关 SDK 级别的详细信息，请参阅 Android 开发人员[使用 sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html)文档。


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>运行时检查的 Android 版本

发布 Android 每个新版本后，框架 API 会更新以提供新或替代功能。 几个例外情况之外，从早期的 Android 版本的 API 功能将会传递到较新的 Android 版本，无需修改即可。 因此，如果您的应用程序在特定 Android API 级别上运行，它通常会能够无需修改即可在更高版本的 Android API 级别上运行。 但如果你还想要更早版本的 Android 上运行你的应用？

如果您选择的最低 Android 版本*较低*与您的目标框架设置，某些 Api 可能不能供您在运行时的应用程序。 但是，早期在设备上，但具有降低的功能，仍可运行您的应用程序。 对于不是与你的最低 Android 版本设置相对应的 Android 平台上可用的每个 API，你的代码必须显示检查的值`Android.OS.Build.VERSION.SdkInt`属性来确定应用程序运行的平台的 API 级别。 如果 API 级别*较低*支持 API 的最低 Android 版本比你想要调用，则你的代码必须找到一种方法，才能正常运行而无需进行此 API 调用。

例如，让我们假设我们想要使用[NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/)方法以运行时对通知进行分类**Android 5.0 Lollipop** （及更高版本），但我们仍然希望对我们的应用程序在早期版本的 Android 上运行，如**Android 4.1 Jelly Bean** (其中`SetCategory`不可用)。 在本指南的开始处的 Android 版本表中引用，我们看到的生成版本代码**Android 5.0 Lollipop**是`Android.OS.BuildVersionCodes.Lollipop`。 若要支持较旧版本的 Android where`SetCategory`是不可用，我们的代码可以检测在运行时的 API 级别和有条件地调用`SetCategory`API 级别时才大于或等于棒糖形生成版本代码：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此示例中，我们的应用程序的目标框架设置为**Android 5.0 (API 级别 21)** 并且其最低 Android 版本设置为**Android 4.1 (API 级别为 16)**。 因为`SetCategory`目前在 API 级别`Android.OS.BuildVersionCodes.Lollipop`和更高版本，此示例代码将调用`SetCategory`仅当有实际可用&ndash;它将*不*尝试调用`SetCategory`时 API级别为 16、 17、 18、 19 或 20。 仅的范围内通知不正确 （因为它们不按类型分类） 排序，但仍发布通知来提醒用户，这些以前的 Android 版本缩减功能。 我们的应用仍然正常工作，但其功能会略有降低。

一般情况下，生成版本检查可帮助你决定在运行时之间执行这样的操作与旧方法的新方法的代码。 例如：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

没有快速而简单规则，说明如何减少或缺少一个或多个 Api 的较旧 Android 版本上运行时修改应用程序的功能。 在某些情况下 (例如，在`SetCategory`上面示例中)，只需省略 API 调用时不可用。 但是，在其他情况下，您可能需要为时实现替代功能`Android.OS.Build.VERSION.SdkInt`被检测出是早于 API 级别，您的应用程序需要以提供其最佳体验。

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>API 级别和库

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

创建 Xamarin.Android 类库项目 （如类库或绑定库） 时，可以配置的目标框架设置&ndash;最低 Android 版本和目标 Android 版本设置不可用。 这是因为没有任何**Android 清单**页：

[![仅使用 Android 版本选项编译可用](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如果创建 Xamarin.Android 类库项目，则没有**Android 应用程序**页可以配置最低 Android 版本和目标 Android 版本&ndash;最低 Android 版本和目标Android 版本设置不可用。
这是因为没有任何**生成 > Android 应用程序**页：

[![生成不包含最小值和目标版本选项常规页](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

最低 Android 版本和目标 Android 版本设置不可用因为所得的库不是一款独立应用&ndash;库可以运行任何 Android 版本，具体取决于它打包在一起的应用。 您可以指定如何在库将成为*编译*，但无法预测哪个平台 API 级别将在上运行库。 明确这一点后，使用或创建库时，应遵循以下最佳实践：

-   **使用 Android 库时**&ndash;如果你正在使用 Android 库应用程序中，请务必设置将设置为 API 级别，它应用的目标框架*至少高达*目标框架库的设置。

-   **创建 Android 库时**&ndash;如果您创建 Android 库为使用其他应用程序，请务必将其目标框架设置为编译所需的最低 API 级别。

这些最佳实践建议以帮助防止这种情况，一个库尝试调用不是在运行时 （这可能导致应用崩溃） 提供的 API。 如果您是库的开发人员，您应尽可能限制你使用的 API 调用的总的 API 外围应用的小型和获得广泛认可的子集。 这样做有助于确保您的库可以安全地用在更广范围的 Android 版本之间。


## <a name="summary"></a>总结

本指南说明了如何使用 Android API 级别来跨不同版本的 Android 管理应用程序兼容性。 它提供的详细的步骤用于配置 Xamarin.Android*目标框架*，*最低 Android 版本*，并*目标 Android 版本*项目设置。 它提供了有关使用 Android SDK 管理器安装 SDK 包的说明包含有关如何编写代码来处理在运行时，不同 API 级别的示例，并介绍了如何管理创建或使用 Android 库时的 API 级别。 它还提供了与 Android 版本数字 （如 Android 4.4)、 Android 版本名称 （例如 Kitkat) 和 Xamarin.Android 生成版本代码的 API 级别的完整列表。


## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [选取 compileSdkVersion、 minSdkVersion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [什么是 API 级别？](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [用、 标记和内部版本号](https://source.android.com/source/build-numbers)
