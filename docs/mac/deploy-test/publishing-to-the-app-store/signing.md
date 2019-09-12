---
title: 使用开发人员 ID 对 Xamarin.Mac 应用进行签名
description: 本文档介绍如何使用开发人员 ID 对 Xamarin.Mac 应用进行签名，以便可以在 Mac App Store 之外进行分发。 其中讨论了代码签名选项和生成。
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 925a110025092ce6efa4e4d5f8e80b640b7f3dd1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770091"
---
# <a name="signing-xamarinmac-apps-with-a-developer-id"></a>使用开发人员 ID 对 Xamarin.Mac 应用进行签名

如果开发人员计划直接将应用分发给 macOS 用户，Apple 建议使用开发人员 ID 对应用进行代码签名，以便可在启用“网关守卫”  的 macOS 系统上安装此应用。 如果应用未经签名，**网关守卫**会通过警告消息阻止用户安装（在启动时按住 Ctrl 键可跳过此限制）。

请在 Apple 网站详细阅读[开发者 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)和[在 Mac App Store 外进行分发](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)。

## <a name="code-signing-options"></a>代码签名选项

若要生成应用以直接（而不通过 Mac App Store）部署给用户，请将“签名设置”  设置为使用“开发人员 ID”  。 请务必编辑“发布”  配置。

 [![](signing-images/config02.png "Mac 签名选项")](signing-images/config02.png#lightbox)

## <a name="build"></a>生成

生成前，请确保选择正确的配置，然后在“Mac 生成”设置  中选择创建安装包：

[![](signing-images/config03.png "生成选项")](signing-images/config03.png#lightbox)

生成应用时，会提示开发人员同时使用这两个证书：

 [![](signing-images/image57.png "允许密钥链访问")](signing-images/image57.png#lightbox)

 [![](signing-images/image58.png "允许密钥链访问")](signing-images/image58.png#lightbox)

生成应用程序后，开发人员可右键单击项目，然后选择“打开所在文件夹”  找到包文件（在 `bin/Release` 目录中）。 此包文件包含应用程序的安装程序，因而可直接将应用程序分发给任何 macOS 用户进行安装。

 [![](signing-images/image59.png "在查找器中选择应用包")](signing-images/image59.png#lightbox)

## <a name="related-links"></a>相关链接

- [安装](~//mac/get-started/installation.md)
- [Hello，Mac 示例](~//mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [工具指南：对应用进行代码签名](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
