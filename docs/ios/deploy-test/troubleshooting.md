---
title: Xamarin.iOS 测试和部署 - 疑难解答
description: 本文档提供了与代码签名和预配、TestFlight 以及将 iOS 应用程序包从 Mac 生成主机复制到 Windows 有关的疑难解答提示。
ms.prod: xamarin
ms.assetid: 65286D09-F74D-4F22-B6CD-D1BCD7FC7992
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/23/2017
ms.openlocfilehash: e57254ce724c5660e53dcd9deaa0f4a27bf91400
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675188"
---
# <a name="xamarinios-testing-and-deployment---troubleshooting"></a>Xamarin.iOS 测试和部署 - 疑难解答

## <a name="code-signing--provisioning"></a>代码签名和设置

iOS 方面的代码签名和设置可能非常麻烦，因此务必要确保代码签名证书和设置配置文件井然有序。

* 大型团队应避免在 Xcode 中使用“修复问题”按钮，如此处所示：

    [![](troubleshooting-images/fixissue.png "“修复问题”对话框")](troubleshooting-images/fixissue.png#lightbox)

    这会创建新的设置配置文件和证书。 在最好的情况下，这会在团队成员每次单击它时创建设置配置文件，从而导致配置文件杂乱无章。 在最坏的情况下，它会吊销公司中其他所有人的证书，从而导致其应用停止工作。

* 使密钥链访问保持组织有序，并删除过期证书和配置文件。 企业证书可持续三年，而其他证书仅持续一年。 证书无法进行续订，因此需要恰好在旧证书过期之前创建新证书。 确保吊销并删除旧证书，以及使用新证书对应用重新签名。

* 安装新设置配置文件时删除旧配置文件。 这意味着 Visual Studio for Mac 不必决定要使用的配置文件。 若要实现此目的，请首先请确保在 Apple 开发人员中心中删除配置文件，然后浏览到“首选项”>“你的帐户”>“查看详细信息...”  。选择设置配置文件，然后单击“在查找工具中显示”  。 这样会显示配置文件在 Mac 文件系统中的位置，随后可以在该位置使用查找程序删除它。

* 确保所有所需证书和对应私钥都可用。 每个团队都需要开发人员证书（用于在自己的设备上安装应用）和分发证书（用于在其他设备上安装）

* 安装新预配配置文件或证书时，重新启动 Xcode 和 Visual Studio for Mac / Visual Studio。

## <a name="testflight"></a>TestFlight

有时，测试并不会完全按照计划顺利进行。  以下步骤可以帮助解决与 TestFlight 有关的问题：

- TestFlight 只能用于面向 iOS 8 及更高版本的应用。

- 必须存在具有 beta 权利的应用商店分发配置文件  。

- “新 iOS 应用提交”  窗口必须包含与应用的 **Info.plist** 完全相同的信息，并且必须填写所有部分。 必须在上传到 TestFlight 之前为应用指定图标。

- 上传新生成时，需要 1 到 5 分钟的时间，直到生成出现在 iTunes Connect 中。

- 必须为每个版本的应用打开 [TestFlight Beta 测试开关](~/ios/deploy-test/testflight.md#beta-testing)。

- 开发人员团队中也作为内部测试员的每个成员都必须打开“内部测试员”  开关。

- 属于或拥有其他 iTunes Connect 帐户的用户不能作为内部测试员。 他们只能作为外部测试员进行添加。

- 内部和外部用户分开进行添加、选择和邀请。 每个列表都必须分开管理。

- Apple 必须审批将分发给外部测试员的每个版本。 如果生成的版本更改，则需要由 Apple 进行新的 beta 审查。 如果生成号更改，则审查是可选的。

- 必须将元数据添加到分发给外部测试员的版本。 这可以通过在“我的应用”>“预发行”  中单击生成号来进行访问。

- 每天只能添加两个生成进行审查。 由于更改版本会强制进行审查，因此这意味着版本号每天只能更改两次。

<a name="Automatically_copy_app_bundles_back_to_Windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>自动将 .app 应用程序包复制回 Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]
