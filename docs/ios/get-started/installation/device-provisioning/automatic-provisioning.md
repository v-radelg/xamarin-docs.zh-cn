---
title: Xamarin.iOS 的自动预配
description: Xamarin.iOS 成功安装后，iOS 开发的下一步是 iOS 设备预配。 本指南介绍如何使用自动签名请求开发证书和配置文件。
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 4d4c9980dd4c7be4ed18f1b6ce015d73b3caf836
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855167"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Xamarin.iOS 的自动预配

_Xamarin.iOS 成功安装后，iOS 开发的下一步是 iOS 设备预配。 本指南介绍如何使用自动签名请求开发证书和配置文件。_

## <a name="requirements"></a>要求

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

- Visual Studio for Mac 7.3 或更高版本
- Xcode 9 或更高版本

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

- Visual Studio 2019
- 或者 Visual Studio 2017 版本 15.7（或更高版本）

必须与具备以下条件的 Mac 生成主机配对：

- Xcode 10 或更高版本

-----

## <a name="enabling-automatic-signing"></a>启用自动签名

开始自动签名进程前，应确保 Apple ID 已添加到 Visual Studio 中，如 [Apple 帐户管理](~/cross-platform/macios/apple-account-management.md)指南所述。 一旦添加了 Apple ID，便可以使用任意相关联的团队。 此操作可以指定团队的证书、配置文件和其他 ID。 团队 ID 还可用于创建预配配置文件中包含的应用 ID 的前缀。 Apple 可以通过此团队 ID 验证你的身份是否和你所述一致。

> [!IMPORTANT]
> 在开始之前，请确保登录 [iTunes Connect](https://itunesconnect.apple.com/) 或 [appleid.apple.com](https://appleid.apple.com) 以检查是否已接受最新的 Apple 帐户策略。 如果系统提示，请完成这些步骤以接受 Apple 的任何新帐户协议。 如果不接受自 2018 年 5 月起的隐私协议，那么在尝试预配设备时，将看到以下警报之一：
> ```
> Unexpected authentication failure. Reason: {
> "authType" : "sa"
> }
> ```
> or
> ```
> Authentication Service Is Unavailable
> ```

若要自动签名应用以在 iOS 设备上开发，请执行以下操作：

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开 iOS 项目。

2. 打开 Info.plist 文件。

3. 在“签名”部分中，选择“自动设置”：

    ![团队选择器下拉列表](automatic-provisioning-images/image2.png)

4. 从“团队”下拉列表中选择团队。

6. 几秒后，便会创建签名证书和设置配置文件：

    ![成功创建证书和配置文件](automatic-provisioning-images/image5.png)

    如果自动签名失败，则“自动签名板”将显示错误的原因。

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

1. 按照[与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南所述，将 Visual Studio 2019 与 Mac 配对。

2. 在解决方案资源管理器中，右键单击项目名称，然后选择“属性”。 然后，导航至“iOS 捆绑签名”选项卡。

3. 选择“自动预配”方案：

    ![自动方案的选择](automatic-provisioning-images/prov4.png)

4. 从“团队”组合框中选择团队，然后开始自动签名进程。

    ![团队选择](automatic-provisioning-images/prov3.png)

4. 这将启动自动签名进程。 然后，Visual Studio 尝试生成应用 ID、预配配置文件和签名标识来将这些项目用于签名。 可在生成输出中查看生成进程：

    ![显示项目生成的生成输出](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>触发自动预配

启用自动签名时，如果发生以下情况，Visual Studio for Mac 会在必要时更新项目：

* iOS 设备插入到 Mac
    - 这会自动检查设备是否已在 Apple 开发人员门户中注册。 如果没有，则会添加该设备并生成包含该设备的新的预配配置文件。
* 应用的捆绑 ID 发生更改
    - 这会更新应用 ID。 创建包含此应用 ID 的新预配配置文件。
* 一个受支持的功能在 Entitlements.plist 文件中启用。
    - 此功能将添加到应用 ID，并生成具有更新后的应用 ID 的新预配配置文件。
    - 当前并非所有功能均受支持。 有关受支持功能的详细信息，请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

## <a name="wildcard-app-ids"></a>通配符应用 ID

从 Visual Studio for Mac 7.6 开始，自动预配将默认尝试创建并使用通配符应用 ID 和预配配置文件，而不是基于 Info.plist 中指定的捆绑包标识符的显式应用 ID。 通配符应用 ID 可减少 Apple 开发人员门户中要维护的配置文件和 ID 的数量。

在某些情况下，应用的权利需要显式应用 ID。 以下权利不支持通配符应用 ID：

- 应用组
- 关联的域
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- 热点
- 应用内购买
- 多路径
- NFC
- 个人 VPN
- 推送通知
- 无线附件配置

若应用使用其中一种权利，Visual Studio for Mac 将尝试创建显式（而不是通配符）应用 ID。

> [!NOTE]
> 使用通配符应用 ID 的自动配置目前仅适用于 Visual Studio for Mac。

## <a name="related-links"></a>相关链接

- [免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [应用分发](~/ios/deploy-test/app-distribution/index.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
- [Apple - 应用分发指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-Simplified-iOS-Provisioning-in-Visual-Studio-with-fastlane/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
