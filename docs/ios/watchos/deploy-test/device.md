---
title: Apple Watch 设备上的测试
description: 本文档介绍了如何部署使用 Xamarin 生成的 watchOS 应用，以便在实际 Apple Watch 上进行测试。 它讨论了设备、预配配置文件、测试并提供了一些故障排除提示。
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 4b1e232259d7b1816e64298b5c0b8853d8385c20
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283859"
---
# <a name="testing-on-apple-watch-devices"></a>Apple Watch 设备上的测试

完成创建应用程序 Id 和应用程序组所需的[部署步骤](~/ios/watchos/deploy-test/index.md)后（如果需要），请使用此页上的说明执行以下操作：

- 在 Apple 开发人员中心[设置你的设备](#devices)，并
- [创建开发预配配置文件](#profiles)，然后
- [部署和测试](#testing)Apple Watch。

<a name="devices" />

## <a name="devices"></a>设备

在实际 iPhone 或 iPad 上测试 iOS 应用始终需要在开发人员中心注册设备。 设备列表如下所示 (单击加号 **+** 以添加新设备):

![](device-images/devices-sml.png "设备列表如下所示")

监视没有什么不同-你现在需要在将应用部署到 Apple Watch 设备之前添加它。 使用**Xcode**查找监视的 UDID （**Windows > 设备**列表）。 当配对电话连接时，还会显示该手表的信息：

[![](device-images/xcode-devices-sml.png "配对的手表信息")](device-images/xcode-devices.png#lightbox)

知道手表的 UDID 后，将其添加到开发人员中心内的设备列表：

![](device-images/devices-watch-sml.png "设备列表中的手表 UDID")

添加监视设备后，请确保在创建的任何新的或现有的开发或即席预配配置文件中选择该设备：

![](device-images/devices-provisioning.png "可用设备列表")

别忘了编辑现有的预配配置文件以进行下载并重新安装！

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>开发预配配置文件

若要在设备上进行测试，需要在解决方案中为每个应用 ID 创建一个**开发预配配置文件**。

如果你有通配符应用 ID，则*只需要一个配置文件*;但是，如果每个项目都有单独的应用 ID，则每个应用 ID 都需要一个预配配置文件：

![](device-images/provisioningprofile-development.png "开发预配配置文件")

创建所有三个配置文件后，它们将显示在列表中。 请记住下载并安装每个：

![](device-images/provisioningprofiles.png "可用的开发预配配置文件")

可以通过选择 "**生成" > "IOS 捆绑签名**" 屏幕，然后选择 "**发布**" 或 "**调试 iPhone** " 配置来验证 "**项目选项**" 中的预配配置文件。

"**预配配置文件**" 列表将显示所有匹配的配置文件，你应在此下拉列表中看到已创建的匹配配置文件：

![](device-images/options-selectprofile.png "预配配置文件列表")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>监视设备上的测试

配置好设备、应用程序 Id 和预配配置文件后，即可进行测试。

1. 请确保已插入 iPhone，并已将手表与 iPhone 配对。

2. 确保配置设置为 "**发布**" 或 "**调试**"。

3. 确保在 "目标" 列表中选择了 "连接的 iPhone 设备"。

4. 右键单击 iOS 应用程序项目（不是 "监视" 或 "扩展"），然后选择 "**设为启动项目**"。

5. 单击 "**运行**" 按钮（或从 "**运行**" 菜单中选择 "**启动**" 选项）。

6. 解决方案将生成，iOS 应用将部署到 iPhone。
  如果未正确设置 iOS 应用或监视扩展预配，则部署到 iPhone 将会失败。

7. 如果部署成功完成，iPhone 会自动尝试将监视应用发送到配对的监视。 应用图标将显示在 "监视" 屏幕上，其中显示 "循环*安装*进度指示器"。

8. 如果成功安装了 "监视" 应用，则图标将保留在 "监视" 屏幕上-触控它，开始测试应用！


## <a name="troubleshooting"></a>疑难解答

如果在部署过程中出现错误，请使用**视图 > pad > 设备日志**查看有关错误的详细信息。 下面列出了一些错误及其原因：

### <a name="error-mt3001-could-not-aot-the-assembly"></a>错误 MT3001：无法对程序集进行 AOT

在调试模式下生成以部署到 Apple Watch 设备时，可能会出现这种情况。

若要*暂时*解决此问题，请在 "监视扩展**项目选项" > 生成 > watchOS 生成**"窗口中禁用**增量生成**：

[![](device-images/disable-incremental-sml.png "\"增量生成\" 复选框")](device-images/disable-incremental.png#lightbox)

这将在将来的版本中得到修复，之后，可以重新启用增量生成以利用更快的生成时间。


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>在设备上进行调试时，无法启动监视应用

尝试在物理设备上调试监视应用时，仅显示 & 加载 "微调框的图标（最终超时）。 此问题将在将来的版本中得到解决;解决方法是运行发布版本（将不允许调试）。


### <a name="invalid-application-executable-or-application-verification-failed"></a>应用程序可执行文件无效，或者应用程序验证失败

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "应用程序可执行的警报无效")

如果在应用尝试安装后这些消息出现*在 "监视" 屏幕上*，可能会出现以下几个问题：

- 在 Apple 开发人员中心，未将监视设备本身添加为设备。 按照说明[正确配置设备](#devices)。

- 用于测试的开发预配配置文件未包含监视设备;或者，在将手表添加到预配配置文件后，不会重新下载并重新安装它们。 按照说明[正确配置预配配置文件](#profiles)。

- 如果**iOS 设备日志**包含`The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` "监视应用的信息"，则**info.plist**具有错误的**MinimumOSVersion**值。
  这应该是**8.2** -如果你已安装 Xcode 6.3，你可能需要手动编辑要插入的源，并将其设置为8.2。

- 手表应用的**info.plist**不正确地启用了权利（如应用组）。

- 监视应用的**应用 ID**不正确地在开发人员中心中启用了授权（如应用组）。



### <a name="install-never-finished"></a>安装从未完成

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

此错误可能表示 Watch 应用的**info.plist**文件中的键不必要（和无效）。 不应在 Watch 应用中包含用于 iOS 应用或监视扩展的键。

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"等待调试器连接"

如果 "**应用程序输出**" 窗口显示停滞

```csharp
waiting for debugger to connect
```

检查项目中包含的任何 Nuget 是否**依赖于 ""。** 这会自动添加到一些 Microsoft 发布的库，其中包括流行的[Microsoft Http 客户端库](https://www.nuget.org/packages/Microsoft.Net.Http/)。

添加到 .csproj 的**Microsoft.** **.csproj**文件会在部署过程中干扰 iOS 扩展的打包。 您可以跟踪[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912)。
一种可能的解决方法是编辑 .csproj 文件，并手动将它们**移到最后一个元素。**

