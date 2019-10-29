---
title: 了解 Android API 级别
description: Xamarin 具有几个 Android API 级别设置，可确定应用与多个 Android 版本的兼容性。 本指南说明了这些设置的含义，如何配置这些设置，以及它们在运行时对应用造成的影响。
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: c7bae50b481e9b0473ff97d720f2115e8f7c3d2e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018181"
---
# <a name="understanding-android-api-levels"></a>了解 Android API 级别

_Xamarin 具有几个 Android API 级别设置，可确定应用与多个 Android 版本的兼容性。本指南说明了这些设置的含义，如何配置这些设置，以及它们在运行时对应用造成的影响。_

## <a name="quick-start"></a>快速入门

Xamarin 公开了三个 Android API 级别项目设置：

- [目标框架](#framework)&ndash; 指定要在生成应用程序时使用的框架。 在*编译*时，Xamarin 使用此 API 级别。

- [最低 Android 版本](#minimum)&ndash; 指定你希望应用支持的最早 android 版本。 此 API 级别由 Android 在*运行*时使用。

- [目标 Android 版本](#target)&ndash; 指定应用要在其上运行的 android 版本。 此 API 级别由 Android 在*运行*时使用。

你必须为该 API 级别安装 SDK 平台组件，然后才能为你的项目配置 API 级别。 有关下载和安装 Android SDK 组件的详细信息，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)。

> [!NOTE]
> 从2018年8月开始，Google Play 控制台将要求新应用的目标 API 级别为26（Android 8.0）或更高。
从11月2018开始，需要将现有应用作为目标 API 级别26或更高版本。 有关详细信息，请参阅[在 Google Play 中提高应用程序的安全性和性能](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html)。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

通常，所有三个 Xamarin Android API 级别均设置为相同的值。 在 "**应用程序**" 页上，将 "**使用 Android 版本编译" （目标框架）** 设置为最新稳定 API 版本（或至少具有所需的所有功能的 android 版本）。
在下面的屏幕截图中，目标框架设置为**Android 7.1 （API 级别 25-Nougat）** ：

[默认情况下，使用 Android 版本进行编译![目标 Framework 版本](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

在 " **Android 清单**" 页上，将 "最低 android 版本" 设置为 "**使用 SDK 版本编译**"，并将目标 android 版本设置为与目标 Framework 版本相同的值（在以下屏幕截图中，将目标 android 框架设置为**Android 7.1 （Nougat）** ）：

[![最小值和目标 Android 版本设置为目标框架版本](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

如果要保持与早期版本的 Android 的向后兼容性，请将**最低 android 版本设置为面向**你希望应用支持的最早版本的 android。 （请注意，API 级别14是[Google Play 服务和 Firebase 支持](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)所需的最低 api 级别。）以下示例配置支持从 API 级别14到 API 级别25的 Android 版本：

[使用 API 级别 Nougat （最低 Android 版本设置为 API 级别14）![编译](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

通常，所有三个 Xamarin Android API 级别均设置为相同的值。 将**目标框架**设置为最新的稳定 API 版本（或至少为 Android 版本，该版本具有所需的所有功能）。 若要设置**目标框架**，请在**项目选项**中导航到 "**生成 >" 常规**"。 在下面的屏幕截图中，目标框架设置为**使用最新安装的平台（8.0）** ：

[![目标框架默认使用最新安装的平台](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

可在 "**项目选项**" 中的 "**生成 > android 应用程序**" 下找到最小和目标 Android 版本设置。 将最低 Android 版本设置为**自动使用目标框架版本**，并将目标 Android 版本设置为与目标框架版本相同的值。 在下面的屏幕截图中，目标 Android 框架设置为**Android 8.0 （API 级别26）** 以匹配上述目标框架设置：

[在项目选项中设置目标和框架级别![](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

如果要保持与早期版本 Android 的向后兼容性，请将**最低 android 版本**更改为你希望应用支持的最早版本的 android。 请注意，API 级别14是[Google Play 服务和 Firebase 支持](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html)所需的最低 api 级别。
例如，以下配置支持的 Android 版本早于 API 级别14：

[![最低和目标版本设置为自动使用目标框架版本](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----

如果你的应用支持多个 Android 版本，你的代码必须包括运行时检查，以确保你的应用使用最低 Android 版本设置（有关详细信息，请参阅适用[于 Android 版本的运行时检查](#runtimechecks)）。 如果正在使用或创建库，请参阅下面的[Api 级别和库](#libraries)，获取有关配置库的 API 级别设置的最佳实践。

## <a name="android-versions-and-api-levels"></a>Android 版本和 API 级别

随着 Android 平台发展和发布新 Android 版本，会为每个 Android 版本分配一个唯一的整数标识符（称为*API 级别*）。 因此，每个 Android 版本都对应于一个 Android API 级别。 由于用户在旧版本和最新版本的 Android 上安装应用程序，因此必须设计实际的 Android 应用程序以使用多个 Android API 级别。

### <a name="android-versions"></a>Android 版本

每个版本的 Android 按多个名称进行：

- Android 版本，如**android 9.0**
- 代码（或甜品）名称，如_饼图_
- 相应的 API 级别，如**api 级别 28**

Android 代码名称可能对应于多个版本和 API 级别（如下表中所示），但每个 Android 版本正好对应于一个 API 级别。

此外，Xamarin 还定义了映射到当前已知 Android API 级别的*版本代码*。 下表可以帮助你在 API 级别、Android 版本、代码名称和 Xamarin 版本之间进行转换（在 `Android.OS` 命名空间中定义生成版本代码）：

[!include[](~/android/includes/api-levels.md)]

如此表所示，新 Android 版本会频繁发布 &ndash; 有时每年会有多个版本。 因此，可能运行你的应用的 Android 设备的范围包括各种旧版和较新的 Android 版本。 你如何保证你的应用程序将在多个不同版本的 Android 上一致可靠地运行？ Android 的 API 级别可帮助你管理此问题。

### <a name="android-api-levels"></a>Android API 级别

每个 Android 设备只能在*一个*api 级别运行 &ndash; 此 api 级别保证每个 android 平台版本都是唯一的。 API 级别精确标识应用可以调用的 API 集的版本;它标识作为开发人员编码的清单元素、权限等的组合。 Android 的 API 级别系统可帮助 Android 在设备上安装应用程序之前，确定应用程序是否与 Android 系统映像兼容。

构建应用程序时，它包含以下 API 级别信息：

- 要在其上运行应用程序的 Android*目标*API 级别。

- Android 设备运行应用所需的*最低*android API 级别。 

这些设置用于确保正常运行应用程序所需的功能在安装时在 Android 设备上可用。 如果未运行，则阻止应用在该设备上运行。 例如，如果 Android 设备的 API 级别低于你为应用指定的最小 API 级别，则 Android 设备将阻止用户安装你的应用。

## <a name="project-api-level-settings"></a>项目 API 级别设置

以下各节说明如何使用 SDK 管理器为你要面向的 API 级别准备开发环境，并详细说明如何配置*目标框架*、*最低 Android 版本*和*面向*Xamarin 中的 Android 版本设置。

### <a name="android-sdk-platforms"></a>Android SDK 平台

你必须安装对应于该 API 级别的 Android SDK 平台版本，然后才能在 Xamarin 中选择目标或最小 API 级别。 适用于目标框架的可用选项范围，最低 Android 版本和目标 Android 版本限制为你已安装 Android SDK 版本的范围。 您可以使用 SDK 管理器来验证是否安装了所需的 Android SDK 版本，还可以使用它来添加应用程序所需的任何新的 API 级别。 如果你不熟悉如何安装 API 级别，请参阅[Android SDK 安装程序](~/android/get-started/installation/android-sdk.md)。

<a name="framework" />

### <a name="target-framework"></a>目标 Framework

*目标框架*（也称为 `compileSdkVersion`）是在生成时编译应用的特定 Android Framework 版本（API 级别）。 此设置指定应用程序在运行时*预期*使用的 api，但它不会影响在安装应用程序时实际可使用的 api。 因此，更改目标框架设置不会更改运行时行为。

目标框架标识应用程序链接到的库版本 &ndash; 此设置确定可在应用程序中使用的 Api。 例如，如果要使用 Android 5.0 棒糖形中引入的[NotificationBuilder. SetCategory](xref:Android.App.Notification.Builder.SetCategory*)方法，则必须将目标框架设置为**API 级别21（棒糖）** 或更高版本。 如果将项目的目标框架设置为 api 级别（如**Api 级别19（KitKat））** ，并尝试在代码中调用 `SetCategory` 方法，将会出现编译错误。

建议始终使用*最新*的可用目标框架版本进行编译。 这样做会为你的代码所调用的任何已弃用的 Api 提供有用的警告消息。 当你使用最新的支持库版本时，使用最新的目标 Framework 版本尤为重要，&ndash; 每个库都希望在支持库的最小 API 级别或更高版本上编译应用。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中访问 "目标框架" 设置，请在**解决方案资源管理器**中打开项目属性，然后选择 "**应用程序**" 页：

[项目属性![应用程序页](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

设置目标框架，方法是在 "**使用 Android 版本编译**" 下的下拉菜单中选择一个 API 级别，如下所示。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要访问 "Visual Studio for Mac 中的" 目标框架 "设置，请右键单击项目名称，然后选择"**选项**"。这将打开 "**项目选项**" 对话框。 在此对话框中，导航到 "**生成 >" 常规**"，如下所示：

[项目选项页![生成常规部分](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

通过在**目标框架**右侧的下拉菜单中选择一个 API 级别来设置目标框架，如上所示。

-----

<a name="minimum" />

### <a name="minimum-android-version"></a>最低 Android 版本

*最低 android 版本*（也称为 `minSdkVersion`）是可以安装和运行应用程序的最早版本的 android OS （即最低的 API 级别）。 默认情况下，应用只能安装在匹配目标框架设置或更高版本的设备上;如果 "最低 Android 版本" 设置*低于 "* 目标框架" 设置，你的应用也可以在早期版本的 Android 上运行。 例如，如果将 "目标框架" 设置为 " **android 7.1 （Nougat）** "，并将 "最低 android 版本" 设置为 " **Android 4.0.3 （冰淇淋三明治）** "，则应用可安装在 API 级别15到 api 级别为25的任何平台上（包含）。

虽然你的应用程序可能会在此范围的平台上成功生成并安装，但这并不能保证在所有这些平台上都能成功*运行*。 例如，如果你的应用安装在**android 5.0 （棒糖形）** 上，并且你的代码调用仅在**Android 7.1 （Nougat）** 和更高版本中提供的 API，你的应用程序将收到运行时错误，可能会崩溃。 因此，你的代码必须确保在运行时 &ndash; &ndash; 它只调用运行它的 Android 设备所支持的那些 Api。 换句话说，你的代码必须包括显式运行时检查，以确保你的应用程序仅在最新的设备上使用较新的 Api 以支持它们。
本指南后面的[针对 Android 版本的运行时检查](#runtimechecks)介绍了如何将这些运行时检查添加到你的代码中。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中访问最低 Android 版本设置，请在**解决方案资源管理器**中打开项目属性，然后选择 " **Android 清单**" 页。 在 "**最低 android 版本**" 下的下拉菜单中，可以选择应用程序的最低 android 版本：

[![最低 Android 目标选项设置为使用 SDK 版本进行编译](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

如果选择 "**使用 SDK 版本的编译**"，则最低 Android 版本将与目标框架设置相同。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中访问最低 Android 版本，请右键单击项目名称，然后选择 "**选项**"。这将打开 "**项目选项**" 对话框。 导航到 "**生成 >" Android 应用程序**"。
使用**最低 android 版本**右侧的下拉菜单，可以设置应用程序的最低 android 版本：

[![最低 Android 版本设置为自动使用目标框架版本](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

如果选择 "**自动 &ndash; 使用目标框架版本**，则最低 Android 版本将与目标框架设置相同。

-----

<a name="target" />

### <a name="target-android-version"></a>目标 Android 版本

*目标 Android 版本*（也称为 `targetSdkVersion`）是应用预期在其中运行的 android 设备的 API 级别。 Android 使用此设置来确定是否启用任何兼容行为 &ndash; 这可确保应用继续按预期方式工作。 Android 使用您的应用程序的 "目标 Android 版本" 设置来确定哪些行为更改可以应用于您的应用程序，而不会中断应用程序（这是 Android 提供向前兼容性的方式）。

目标框架和目标 Android 版本虽然具有非常相似的名称，但并不相同。 目标框架设置将目标 API 级别信息传递给 Xamarin 以便在*编译时*使用，而目标 Android 版本将目标 api 级别信息传递到 Android 以在*运行时*使用（当应用程序已在设备上安装并运行）。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中访问此设置，请在**解决方案资源管理器**中打开项目属性，然后选择 " **Android 清单**" 页。 在 "**目标 android 版本**" 下的下拉菜单中，可以选择应用程序的目标 Android 版本：

[使用 SDK 版本将![目标 Android 版本设置为编译](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

建议你将目标 Android 版本显式设置为你用来测试应用程序的最新版本的 Android。 理想情况下，它应设置为最新的 Android SDK 版本 &ndash; 这样，你就可以在执行行为更改之前使用新 Api。 对于大多数开发人员，我们*不*建议将目标 Android 版本设置为**使用 SDK 版本的编译**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中访问此设置，请右键单击项目名称，然后选择 "**选项**"。这将打开 "**项目选项**" 对话框。 导航到 "**生成 >" Android 应用程序**"。 使用 "**目标 android 版本**" 右侧的下拉菜单，可以设置应用程序的目标 android 版本：

[![目标 Android 版本设置为自动使用目标框架版本](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

建议你将目标 Android 版本显式设置为你用来测试应用程序的最新版本的 Android。 理想情况下，应将其设置为最新的可用 Android SDK 版本 &ndash; 这样就可以在执行行为更改之前使用新 Api。 对于大多数开发人员，我们不建议将目标 Android 版本设置为**自动使用目标框架版本**。

-----

通常，目标 Android 版本应受最低 Android 版本和目标框架的限制。 即：

**最低 Android 版本 < = 目标 Android 版本 < = 目标框架**

有关 SDK 级别的详细信息，请参阅 Android 开发人员[使用-SDK](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html)文档。

<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Android 版本的运行时检查

发布每个新版本的 Android 时，会更新框架 API 以提供新功能或替代功能。 在少数例外情况下，早期 Android 版本中的 API 功能将在不进行修改的情况下转发到较新的 Android 版本。 因此，如果你的应用程序在特定的 Android API 级别上运行，则该应用程序通常能够在更高版本的 Android API 级别上运行而无需修改。 但如果你还想要在较早版本的 Android 上运行你的应用，该怎么办？

如果选择的最低 Android*版本低于 "* 目标框架" 设置，则在运行时应用程序可能无法使用某些 api。 但是，你的应用程序仍然可以在以前的设备上运行，但功能会降低。 对于与最低 Android 版本设置相对应的 Android 平台上不可用的每个 API，你的代码必须显式检查 `Android.OS.Build.VERSION.SdkInt` 属性的值，以确定运行应用的平台的 API 级别。 如果 API*级别低于支持*你想要调用的 Api 的最低 Android 版本，则你的代码必须找出一种正确工作方式，而无需进行此 api 调用。

例如，假设我们想要在**android 5.0 棒糖形**（及更高版本）上使用[NotificationBuilder SetCategory](xref:Android.App.Notification.Builder.SetCategory*)方法对通知进行分类，但我们仍希望应用程序在早期版本的 Android 上运行，例如**Android 4.1 Jelly Bean** （其中 `SetCategory` 不可用）。 参考本指南开头的 Android 版本表，可以看到**android 5.0 棒糖形**的生成版本代码 `Android.OS.BuildVersionCodes.Lollipop`。 为了支持较早版本的 Android，其中 `SetCategory` 不可用，我们的代码可以在运行时检测到 API 级别，并仅在 API 级别大于或等于棒糖版本代码时条件下调用 `SetCategory`：

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

在此示例中，我们的应用程序的目标框架设置为**android 5.0 （Api 级别21）** ，其最低 android 版本设置为**ANDROID 4.1 （api 级别16）** 。 由于 `SetCategory` 在 API 级别 `Android.OS.BuildVersionCodes.Lollipop` 和更高版本中可用，因此此示例代码仅在实际可用时才会调用 `SetCategory` &ndash; 它*不*会尝试在 API 级别为16时调用 `SetCategory`、17、18、19或20。 此功能在早期的 Android 版本中减少了，但通知未正确排序（因为这些通知未按类型分类），但仍会发布通知以提醒用户。 应用仍可正常工作，但其功能略有降低。

通常情况下，生成版本检查可帮助代码在运行时决定是否在新的方式与旧方法之间执行操作。 例如:

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

如果在旧的 Android 版本（缺少一个或多个 Api）上运行，则不能通过快速且简单的规则来减少或修改应用程序的功能。 在某些情况下（例如，在上面的 `SetCategory` 示例中），在 API 调用不可用时，可以将其省略。 但在其他情况下，如果检测到 `Android.OS.Build.VERSION.SdkInt` 检测到的应用程序所需的 API 级别小于应用程序获得最佳体验所需的 API 级别，则可能需要实现其他功能。

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>API 级别和库

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

创建 Xamarin 类库项目（如类库或绑定库）时，只能将目标框架设置配置 &ndash; 最低 Android 版本和目标 Android 版本设置不可用。 这是因为没有**Android 清单**页：

[仅!["使用 Android 版本编译" 选项可用](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

创建 Xamarin 类库项目时，没有 " **Android 应用程序**" 页，你可以在其中配置最低 android 版本和目标 android 版本 &ndash; 最低 android 版本和目标 android 版本设置不是即可.
这是因为没有 "**生成 > Android 应用程序**" 页：

[![生成常规页，无最低版本和目标版本选项](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

最小 Android 版本和目标 Android 版本设置不可用，因为生成的库不是独立应用 &ndash; 库可以在任何 Android 版本上运行，具体取决于打包时使用的应用。 您可以指定如何*编译*库，但无法预测库将运行的平台 API 级别。 考虑到这一点，在使用或创建库时应遵循以下最佳做法：

- **使用 android 库时**&ndash; 如果你在应用程序中使用 android 库，请确保将应用程序的目标框架设置设置为至少与库的目标框架设置*相同*的 API 级别。

- **创建 android 库时**&ndash; 如果你要创建一个 android 库供其他应用程序使用，请确保将其 "目标框架" 设置设置为编译所需的最小 API 级别。

建议使用这些最佳实践，以帮助防止库尝试调用在运行时不可用的 API （这可能会导致应用崩溃）。 如果你是库开发人员，则应该尽力限制 API 调用的使用情况，使其成为总体 API 外围应用程序的一个小且完善的子集。 这样做有助于确保库可以安全地在更广泛的 Android 版本中使用。

## <a name="summary"></a>总结

本指南介绍如何使用 Android API 级别跨不同版本的 Android 管理应用兼容性。 其中提供了有关配置 Xamarin Android*目标框架*、*最低 Android 版本*和*目标 android 版本*项目设置的详细步骤。 本指南提供了使用 Android SDK 管理器安装 SDK 包的说明，其中包括如何编写代码以在运行时处理不同 API 级别的示例，并说明了如何在创建或使用 Android 库时管理 API 级别。 它还提供了一个综合列表，该列表将 API 级别与 Android 版本号（如 Android 4.4）、Android 版本名称（如 Kitkat）和 Xamarin 版本代码相关联。

## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [SDK CLI 工具更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [选择 compileSdkVersion、minSdkVersion 和 targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [什么是 API 级别？](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Codenames、标记和生成号](https://source.android.com/source/build-numbers)
