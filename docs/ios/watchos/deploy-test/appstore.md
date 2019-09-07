---
title: 将 watchOS 应用部署到 App Store
description: 本文档介绍如何将用 Xamarin 生成的 watchOS 应用部署到 App Store。 它会查看分发配置文件和 iTunes Connect，还会提供一些故障排除提示。
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 7b80573a728e1868254b5a89254ebc385b3baa12
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768078"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>将 watchOS 应用部署到 App Store

> [!IMPORTANT]
> 务必查看[Apple 的 "观看工具包" 提交指南](https://developer.apple.com/app-store/watch/)，并查看[故障排除](#troubleshooting)部分，了解你可能遇到的任何问题。

- 请确保：
  - 为你的项目创建的[**分发配置文件**](#provisioning)。
  - IOS 父应用程序`MinimumOSVersion`的**部署目标**（）设置为**8.2**或更早版本（不支持8.3）。

- 在[**ITunes Connect**](#iTunes_Connect)中：

  - 创建 iOS 应用条目（或将**新版本**添加到现有应用）。
  - 添加监视图标和屏幕截图。

- 然后[Visual Studio for Mac](#xamarin_studio) （目前不支持 Visual Studio）：

  - 右键单击 "iOS 应用"，然后选择 "**设为启动项目**"。
  - 更改为**应用商店**配置。
  - 使用**存档**功能创建应用程序存档。

- 最后，切换到[Xcode 6.2 +](#xcode)

  - 中转到 **> 组织**程序的窗口，然后选择 "**存档**"。
  - 从列表中选择应用程序和存档。
  - 同时**验证 ...** 存档。
  - **提交...** 存档并遵循的步骤将上载到 iTunes 连接用于审查和批准。

阅读与以下各项相关的特定提示。 如果遇到问题，请参阅[故障排除](#troubleshooting)部分。

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>分发预配配置文件

若要生成应用商店部署，需要为解决方案中的每个应用 ID 创建**分发预配配置文件**。

如果你有通配符应用 ID，则*只需要一个配置文件*;但是，如果每个项目都有单独的应用 ID，则每个应用 ID 都需要一个预配配置文件：

![](appstore-images/provisioningprofile-distribution-sml.png "App Store 分发配置文件")

创建所有三个配置文件后，它们将显示在列表中。 请记住，下载并安装每个（通过双击此项）：

![](appstore-images/provisioningprofiles-sml.png "可用配置文件列表")

可以通过选择 "**生成 > IOS 捆绑签名**" 屏幕并选择 " **AppStore | iPhone** " 配置来验证 "**项目选项**" 中的预配配置文件。

"**预配配置文件**" 列表将显示所有匹配的配置文件，你应在此下拉列表中看到已创建的匹配配置文件。

![](appstore-images/options-selectprofile-sml.png "IOS 捆绑签名对话框")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes 连接

请特别参阅[应用分发概述](~/ios/deploy-test/app-distribution/index.md)：

- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

在 iTunes Connect 中配置应用时，请不要忘记添加 "监视" 图标和屏幕截图：

![](appstore-images/itunesconnect-watch-sml.png "ITunes Connect 中的 \"监视\" 图标和屏幕截图")

图标文件应为1024x1024 像素，并将在显示时应用圆形掩码。 图标不应具有 alpha 通道。

至少需要一个屏幕截图，最多可以提交5个屏幕截图。
它们应为312x390 像素，并演示监视应用的操作。
可以使用 42mm watch 模拟器以这种大小拍摄屏幕截图。

<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 确保 iOS 应用是启动项目。 如果不是，请右键单击以设置它：

   ![](appstore-images/xs-startup.png "设置启动项目")

2. 选择 " **AppStore**生成配置"：

   ![](appstore-images/xs-appstore.png "AppStore 生成配置")

3. 选择 "**生成 > 存档**" 菜单项以启动存档过程：

   ![](appstore-images/xs-archive.png "\"生成\" 菜单")

你还可以选择 "**查看 > 存档 ...** " 菜单项以查看之前创建的存档。

  ![](appstore-images/xs-archives-sml.png "存档视图")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode 会自动显示 Visual Studio for Mac 中创建的存档。

1. 启动 Xcode 并选择 " **Window > 组织**程序"：

   ![](appstore-images/xc-organizer.png "\"窗口\" 菜单")

2. 切换到 "**存档**" 选项卡，然后选择已创建的存档 Visual Studio for Mac：

   ![](appstore-images/xc-archives.png "\"存档\" 选项卡")

3. 选择 "**验证**"，然后选择 "**提交 ...** "，将应用上传到 iTunes Connect。

4. 选择开发团队（如果你属于多个），然后确认提交：

   ![](appstore-images/xc-submit1.png "\"开发团队\" 部分")

5. 再次访问 iTunes Connect，查看上传的二进制文件。 请访问应用的 "配置" 页，然后从顶部菜单中选择 "**预发行**"，查看 "**生成**" 列表：

   [![](appstore-images/itc-prerelease-sml.png "ITunes Connect 中的 \"应用\" 配置页")](appstore-images/itc-prerelease.png#lightbox)

然后，你可以在 "**版本**" 页上提交要审批的应用。 有关详细信息，请参阅[iOS 应用分发概述](~/ios/deploy-test/app-distribution/index.md)。

## <a name="troubleshooting"></a>疑难解答

下面是在提交到 App Store 时可能会遇到的一些错误，以及解决这些错误的步骤。

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Visual Studio for Mac 中不显示 "存档" 菜单选项

按照[上述步骤](#xamarin_studio)配置解决方案以进行存档。 如果无法正确设置启动项目，请确保先将生成配置设置为 "调试" 或 "发布"，然后再尝试更改启动项目。 然后将生成配置重新设置为**AppStore**。

### <a name="invalid-icon"></a>“无效”图标

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

按照说明从图标中[删除 alpha 通道](~/ios/watchos/troubleshooting.md)。

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion 不匹配

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

解决方案中的所有项目-iOS 应用、监视扩展和监视应用-应使用相同的版本号。 编辑每个**info.plist**文件，使版本号完全匹配。

### <a name="missing-icons"></a>缺少图标

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

按照使用[图标](~/ios/watchos/app-fundamentals/icons.md)中的说明将所有所需的映像添加到 "监视应用程序" 项目。

### <a name="missing-icon"></a>缺少图标

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

请确保具有最新版本的 Visual Studio for Mac，并且**appicons.appiconset**包含一组完整的映像。 如果仍看到此错误，请查看**内容**的源，以确认它是否包含所有所需映像的条目。 或者，在确保使用最新版本的 Xamarin 后，请删除并重新创建**appicons.appiconset**。

> [!IMPORTANT]
> Visual Studio for Mac 的 "监视" 图标支持中有一个已知 bug：它需要 **29x29@3x** 图像的88x88 像素图像（应为87x87 像素）。

无法在 Visual Studio for Mac 中解决此问题-请在 Xcode 中编辑图像资产，或者手动编辑**内容 json**文件（以与[本示例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)匹配）。

### <a name="invalid-watchkit-support"></a>WatchKit 支持无效

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

成功上传后，可能会在验证和提交期间出现此消息，或从 iTunes Connect 自动发送电子邮件。
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> 必须 Visual Studio for Mac 中**存档**应用，然后切换到 Xcode 6.2 +，验证并上传到 iTunes Connect。

使用稳定的 Xamarin 通道和 Xcode 6.2 +。

### <a name="invalid-provisioning-profile"></a>无效的预配配置文件

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

必须为 "监视应用" 解决方案中的所有三个项目提供**分发预配配置文件**： iOS 应用、监视扩展和监视应用-显式（三个配置文件）或通过单个通配符配置文件。 检查 iOS 开发人员中心中是否存在预配配置文件，以及是否已下载并将其添加到 Mac 中。

### <a name="invalid-code-signing-entitlements"></a>代码签名权利无效

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

确保预配配置文件在 Apple 开发人员中心正确设置，并且已下载并安装这些配置文件。 同时，检查它们是否在每个项目 Visual Studio for Mac 的 "属性" 窗口中设置。

### <a name="invalid-architecture"></a>体系结构无效

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

只能添加监视应用[Unified API （64位）](~/cross-platform/macios/unified/index.md) Xamarin iOS 应用。
右键单击 iOS 应用程序项目，然后选择 " **> 生成 > IOS 生成 > 高级" 选项卡**，并确保 AppStore 配置**支持的体系结构**包括**ARM64** （例如 **ARMv7 + ARM64**）。

### <a name="this-bundle-is-invalid"></a>此绑定无效。

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

你的父 iOS 应用程序必须将 MinimumOSVersion 设置为 "8.2" 或更早版本。

### <a name="non-public-api-usage"></a>非公共 API 使用情况

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

确保使用最新版本的 Xcode 和 Xamarin 工具。
你的代码不应访问任何非公共 Api。

### <a name="build-error-mt5309"></a>生成错误 MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

此错误很可能是由于你已将 Xcode 安装重命名为**Xcode**。 例如，如果将安装重命名为**XCode 6.2**，则会发生此错误。

## <a name="related-links"></a>相关链接

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
