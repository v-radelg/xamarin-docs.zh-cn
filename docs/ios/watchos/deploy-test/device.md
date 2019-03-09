---
title: Apple Watch 设备上测试
description: 本文档介绍如何使用 Xamarin 生成用于测试在实际的 Apple Watch 的 watchOS 应用部署。 它讨论了设备预配配置文件，测试，并提供一些故障排除提示。
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 9c15e9205b96a02caa182e47b71c6d36c8bff1aa
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671047"
---
# <a name="testing-on-apple-watch-devices"></a>Apple Watch 设备上测试

已按照后[部署步骤](~/ios/watchos/deploy-test/index.md)若要创建应用 Id 和应用程序组 （如果需要），请使用此页上的说明：

- [设置你的设备](#devices)Apple 开发人员中心，并
- [创建开发预配配置文件](#profiles)，然后
- [部署和测试](#testing)Apple Watch 上。

<a name="devices" />

## <a name="devices"></a>设备

测试真实 iPhone 或 iPad 上的 iOS 应用始终要求要在开发人员中心注册的设备。 设备列表如下所示 (单击加号 **+** 以添加新设备):

![](device-images/devices-sml.png "设备列表如下所示")

监视也是如此-你现在需要向其部署应用之前，添加你的 Apple Watch 设备。 查找使用手表的 UDID **Xcode** (**Windows > 设备**列表)。 配对的电话连接时还会显示所监视的信息：

[![](device-images/xcode-devices-sml.png "配对的监视信息")](device-images/xcode-devices.png#lightbox)

当您知道手表的 UDID，将其添加到开发人员中心中的设备列表：

![](device-images/devices-watch-sml.png "监视的设备列表中的 UDID")

添加后监视设备，请确保选择任何新的或现有开发或临时创建的预配配置文件中：

![](device-images/devices-provisioning.png "可用的设备列表")

别忘了是否编辑现有的预配配置文件以下载并重新安装它 ！

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>开发预配配置文件

生成适用于您需要创建在设备上测试**开发预配配置文件**为你的解决方案中每个应用程序 ID。

如果必须是通配符应用 ID*只有一个预配配置文件，将需要*; 但是，如果您有单独的应用 ID 为每个项目都需要预配配置文件为每个应用程序 ID:

![](device-images/provisioningprofile-development.png "开发配置文件")

一旦您创建所有三个配置文件，它们将显示在列表中。 请记住下载并安装每个：

![](device-images/provisioningprofiles.png "可用的开发预配配置文件")

你可以验证中的预配配置文件**项目选项**通过选择**生成 > iOS 捆绑签名**屏幕，然后选择**发行**或**调试 iPhone**配置。

**预配配置文件**列表将显示所有匹配的配置文件-您应该看到已创建此下拉列表中的匹配配置文件：

![](device-images/options-selectprofile.png "预配配置文件列表")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>监视设备上测试

配置设备、 应用 Id 和预配配置文件后，已准备好进行测试。

1. 请确保你的 iPhone 接通电源，并监视已配对的 iPhone。

2. 请确保配置设置为**发行**或**调试**。

3. 请确保已连接的 iPhone 设备目标列表中选择。

4. 右键单击 iOS 应用程序项目 （不监视或扩展） 并选择**设为启动项目**。

5. 单击**运行**按钮 (或选择**启动**选项**运行**菜单)。

6. 将构建该解决方案和 iOS 应用将部署到 iPhone。
  如果 iOS 应用程序或监视扩展预配未正确设置为 iPhone 部署将失败。

7. 如果部署成功完成，iPhone 将自动尝试将 watch 应用发送到配对的监视。 应用程序的图标将显示与圆监视屏幕上*安装*进度指示器。

8. Watch 应用是否已成功安装，图标将仍保留在监视屏幕中的对它进行修改以开始测试您的应用程序 ！


## <a name="troubleshooting"></a>疑难解答

如果在部署使用过程中出现错误**视图 > 面板 > 设备日志**以查看有关错误的详细信息。 下面列出了一些错误及其原因：

### <a name="error-mt3001-could-not-aot-the-assembly"></a>错误 MT3001:可以不 AOT 的程序集

构建在调试模式下，若要部署到 Apple Watch 设备可能会发生该错误。

向*暂时*解决此问题，请禁用**增量生成**监视扩展中**项目选项 > 生成 > watchOS 生成**窗口：

[![](device-images/disable-incremental-sml.png "增量生成复选框")](device-images/disable-incremental.png#lightbox)

这将在其后增量生成可以重新启用才能利用更快的生成时间的未来版本中修复。


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Watch 应用无法在设备上进行调试时启动

当尝试调试的监视应用图标和加载微调框在物理设备上显示 （并最终超时）。 这将在将来的版本; 中解决一种解决方法是运行发布版本 （这将不允许调试）。


### <a name="invalid-application-executable-or-application-verification-failed"></a>无效的应用程序可执行文件或应用程序验证失败

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "无效的应用程序可执行文件警报")

如果出现这些消息*在监视屏幕*已尝试安装应用后，可能有几个问题：

- 尚未为 Apple 开发人员中心上的设备添加监视设备本身。 按照说明[正确配置设备](#devices)。

- 要用于测试的开发预配配置文件不具有包含; 监视设备或监视项目已添加到预配配置文件后不重新下载并重新安装它们。 按照说明[正确配置预配配置文件](#profiles)。

- 如果**iOS 设备日志**包含`The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3`然后监视应用程序的**Info.plist**错误**MinimumOSVersion**值。
  这应该是**8.2** -如果已安装 Xcode 6.3 可能需要手动编辑源到插入将其设置为 8.2。

- Watch 应用**Entitlements.plist**错误地了授权启用了 （如应用组），它不应具有。

- Watch 应用**应用程序 ID**错误地不应具有开发人员中心中已启用 （如应用组） 的权利。



### <a name="install-never-finished"></a>永远不会安装已完成

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

此错误可能指示 Watch 应用中的不必要 （和无效） 密钥**Info.plist**文件。 不应包括用于监视应用程序中的 iOS 应用或监视扩展的密钥。

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>"正在等待调试程序连接"

如果**应用程序输出**窗口时遇到困难显示

```csharp
waiting for debugger to connect
```

检查是否任何项目中包含了 Nuget 具有依赖关系**Microsoft.Bcl.Build**。 这会自动添加与一些 Microsoft 发布的库，包括热门[Microsoft Http 客户端库](https://www.nuget.org/packages/Microsoft.Net.Http/)。

**Microsoft.Bcl.Build.targets**添加到的文件 **.csproj**可能会影响在部署过程中的 iOS 扩展打包。 你可以跟踪[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912)。
可能的解决方法是编辑.csproj 文件，并手动将其移**Microsoft.Bcl.Build.targets**为最后一个元素。

