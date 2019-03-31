---
title: WatchOS 应用部署到应用商店
description: 本文档介绍如何部署使用到 App Store 的 Xamarin 生成的 watchOS 应用。 它介绍了分发预配配置文件和 iTunes Connect，并还提供了一些故障排除提示。
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: bf86759832a1aba0ccc1c144981af6ea4eae8670
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677789"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>WatchOS 应用部署到应用商店

> [!IMPORTANT]
> 请确保查看[Apple Watch 工具包提交指南](https://developer.apple.com/app-store/watch/)，并查看[故障排除](#troubleshooting)部分，了解可能会遇到任何问题。

- 请确保您有：
  - [**分发预配配置文件**](#provisioning)创建为你的项目。
  - **部署目标**(`MinimumOSVersion`) 为 iOS 父应用程序设置为**8.2**或更早版本 （不支持 8.3）。

- 在中[ **iTunes Connect**](#iTunes_Connect):

  - 创建你的 iOS 应用程序条目 (或添加**新版本**向现有应用程序)。
  - 添加监视图标和屏幕截图。

- 然后在[Visual Studio for Mac](#xamarin_studio) （不，目前支持 Visual Studio）：

  - 右键单击 iOS 应用程序并选择**设为启动项目**。
  - 将更改为**App Store**配置。
  - 使用**存档**功能创建应用程序存档。

- 最后，切换到[Xcode 6.2 +](#xcode)

  - 转到**窗口 > 管理器**，然后选择**存档**。
  - 从列表中选择的应用程序和存档。
  - （可选）**验证...** 存档。
  - **提交...** 存档并遵循的步骤将上载到 iTunes 连接用于审查和批准。

阅读下面这些项与相关的特定使用技巧。 请参阅[故障排除](#troubleshooting)部分中如果遇到问题。

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>分发预配配置文件

生成适用于您需要创建的应用商店部署**分发预配配置文件**为你的解决方案中每个应用程序 ID。

如果必须是通配符应用 ID*只有一个预配配置文件，将需要*; 但是，如果您有单独的应用 ID 为每个项目都需要预配配置文件为每个应用程序 ID:

![](appstore-images/provisioningprofile-distribution-sml.png "应用商店分发配置文件")

一旦您创建所有三个配置文件，它们将显示在列表中。 请记住下载并安装每个 （通过双击它）：

![](appstore-images/provisioningprofiles-sml.png "可用的配置文件的列表")

你可以验证中的预配配置文件**项目选项**通过选择**生成 > iOS 捆绑签名**屏幕，然后选择**AppStore | iPhone**配置。

**预配配置文件**列表将显示所有匹配的配置文件-您应该看到已创建此下拉列表中的匹配配置文件。

![](appstore-images/options-selectprofile-sml.png "IOS 捆绑签名对话框")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>在 iTunes Connect

请按照[应用分发概述](~/ios/deploy-test/app-distribution/index.md)，特别是：

- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

在 iTunes Connect 中配置该应用，别忘了添加的监视图标和屏幕截图：

![](appstore-images/itunesconnect-watch-sml.png "监视图标和在 iTunes Connect 中的屏幕截图")

图标文件应 1024 x 1024 像素，并且将在显示时应用于它的循环掩码。 该图标不应具有 alpha 通道。

至少一个屏幕截图是必需的可能会提交最多五个。
它们应为 312 x 390 像素，并演示中的监视应用。
42 mm 监视模拟器可用于获取在此大小的屏幕截图。


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 请确保 iOS 应用程序是启动项目。 如果没有，请右键单击以将其设置：

  ![](appstore-images/xs-startup.png "设置启动项目")

2. 选择**AppStore**生成配置：

  ![](appstore-images/xs-appstore.png "AppStore 生成配置")

3. 选择**生成 > 存档**菜单项来启动存档过程：

  ![](appstore-images/xs-archive.png "生成菜单")

你还可以选择**视图 > 存档...** 菜单项以查看之前创建的存档。

  ![](appstore-images/xs-archives-sml.png "存档视图")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode 将自动显示在 Visual Studio for mac。 创建的存档

1. 启动 Xcode，然后选择**窗口 > 管理器**:

  ![](appstore-images/xc-organizer.png "窗口菜单")

2. 切换到**存档**选项卡上，选择使用 Visual Studio 创建用于 Mac 的存档：

  ![](appstore-images/xc-archives.png "存档选项卡")

3. 可以选择**验证...** 存档，然后选择**提交...** 将应用上传到 iTunes Connect。

4. （如果你属于多个），请选择开发团队，然后确认提交：

  ![](appstore-images/xc-submit1.png "开发团队部分")

5. 请访问 iTunes Connect 再次以查看已上传二进制文件。 转到您的应用程序配置页，然后选择**预发行版**从顶部菜单中，若要查看**生成**列表：

  [![](appstore-images/itc-prerelease-sml.png "在 iTunes Connect 中应用配置页")](appstore-images/itc-prerelease.png#lightbox)

可在提交以供审核的应用**版本**页。 请参阅[iOS 应用分发概述](~/ios/deploy-test/app-distribution/index.md)有关详细信息。


## <a name="troubleshooting"></a>疑难解答

以下是提交到 App Store，以及为解决这些问题可以采取的步骤时可能遇到一些错误。

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>存档菜单选项不是 Visual Studio for Mac 中可见

请按照[上述步骤](#xamarin_studio)配置用于存档解决方案。 如果您不能正确设置启动项目，请确保首先生成配置设置以调试或发布之前尝试更改启动项目。 然后将设置生成配置回**AppStore**。

### <a name="invalid-icon"></a>“无效”图标

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

请按照[删除 alpha 通道的说明](~/ios/watchos/troubleshooting.md)从您的图标。

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion 不匹配

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

-IOS 应用、 监视扩展和 Watch 应用-在解决方案中的所有项目应都使用相同的版本号。 编辑每个**Info.plist**文件以使版本号与完全匹配。

### <a name="missing-icons"></a>缺少图标

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

按照中的说明[使用图标](~/ios/watchos/app-fundamentals/icons.md)将所有所需的映像添加到监视应用程序项目。

### <a name="missing-icon"></a>缺少图标。

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

确保您有最新版本的 Visual Studio for Mac 和，您**AppIcon.appiconset**包含一组完整的映像。 如果你仍看到此错误，查看的源**Contents.json**以确认它包含所有所需图像的一项。 或者，你可确保您使用的最新版本的 Xamarin，一旦删除并重新创建**AppIcon.appiconset**。

> [!IMPORTANT]
> 有在 Visual Studio for Mac 的监视图标支持是一个已知的 bug： 需要为 88 x 88 像素的图像**29x29@3x**映像 （它应是 87 x 87 像素为单位）。


无法为 Mac-编辑图像资产在 Xcode 中解决此问题在 Visual Studio 中或手动编辑**Contents.json**文件 (以匹配[此示例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。



### <a name="invalid-watchkit-support"></a>无效的 WatchKit 支持

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

此消息可能出现期间验证和提交，或在自动电子邮件中向 iTunes Connect 显然成功上传后。
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> 您必须**存档**Visual Studio for Mac，然后切换到 Xcode 6.2 + 验证并上传到 iTunes Connect 中的应用。


使用稳定 Xamarin 通道和 Xcode 6.2 +。



### <a name="invalid-provisioning-profile"></a>无效的预配配置文件

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**分发预配配置文件**必须提供用于监视应用解决方案中的所有三个项目： iOS 应用程序、 监视扩展和 Watch 应用中的任一显式 （三个配置文件） 或通过单个通配符配置文件。 检查 iOS 开发人员中心中存在的预配配置文件和已下载并将其添加到你的 mac。

### <a name="invalid-code-signing-entitlements"></a>无效的代码签名授权

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

请确保预配配置文件是在 Apple 开发人员中心，正确设置和已下载并安装它们。 此外检查 Visual Studio 中的 Mac 的属性窗口为每个项目设置。

### <a name="invalid-architecture"></a>无效的体系结构

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

你只能添加 Watch 应用[Unified API （64 位）](~/cross-platform/macios/unified/index.md) Xamarin.iOS 应用程序。
右键单击 iOS 应用项目，然后转到**选项 > 生成 > iOS 生成 > 高级选项卡**并确保**支持的体系结构**AppStore iPhone 的配置包括**ARM64** （例如。 **ARMv7 + ARM64**)。

### <a name="this-bundle-is-invalid"></a>此捆绑包是无效的。

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

父 iOS 应用程序必须具有设置为"8.2"或更低 MinimumOSVersion。

### <a name="non-public-api-usage"></a>非公共 API 使用情况

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

请确保使用最新版本 Xcode 和 Xamarin 的工具。
你的代码不应访问任何非公共 Api。

### <a name="build-error-mt5309"></a>生成错误 MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

此错误可能是你拥有重命名从 Xcode 安装的结果**Xcode.app**。 例如，将发生此错误，如果重命名您的安装与**XCode 6.2.app**。



## <a name="related-links"></a>相关链接

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
