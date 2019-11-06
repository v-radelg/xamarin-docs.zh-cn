---
title: Fastlane for iOS – Cert
description: 本文档介绍了 fastlane，该工具可自动完成 iOS 应用程序预配过程中的许多工作：请求证书、将设备添加到 Apple 开发人员门户、创建应用 ID 等。
ms.prod: xamarin
ms.assetid: 900FA6FF-F3C9-4D35-993E-B0D88E6B1883
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: ba0348ff0cf6dc394f67b3c5779fd49eb852673f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028526"
---
# <a name="fastlane-for-ios--cert"></a>Fastlane for iOS – Cert

> [!IMPORTANT]
> Fastlane 建议使用 [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) 工具来生成和维护证书。 只有想要完全控制代码签名并充分了解相关信息，才可直接使用 `cert`。 使用此操作下载最新的代码签名标识。

## <a name="overview"></a>概述

按照传统惯例，设备预配由开发团队的成员通过 Xcode 或在 Apple 开发人员门户网站上执行。 它包含以下几个步骤：

- 请求开发证书
- 将设备添加到门户
- 创建应用 ID
- 创建预配配置文件
- 下载配置文件和证书

上述每个步骤包含需要解决的问题，具体取决于正在开发的应用程序的类型。 不论是手动还是通过 Xcode 设置设备以供开发，都可以在[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南中找到所需的步骤详细信息。

本指南介绍 fastlane 工具作为使用 Xcode 的替代方法，并说明下列内容：

- [什么是 cert？](#whatiscert)
- [使用 cert](#using)
- [附加选项](#options)

## <a name="installation"></a>安装

有关安装和更新 Fastlane 的信息，请参阅 [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation) 指南介绍。

<a name="whatiscert" />

## <a name="what-is-cert"></a>什么是 cert？

Cert 提供终端界面，用于创建新的代码签名标识（通常称为开发人员_证书_），适用于开发和分发环境。

<a name="using" />

## <a name="using-cert"></a>使用 cert

若要使用 cert 实用程序，请在终端 CLI 中输入以下命令：

```
fastlane cert
```

默认情况下，这将创建分发证书。 若要创建开发证书，请传递 `--development` 标志：

```
fastlane cert --development
```

Cert 将提示输入 Apple ID 和密码，因此请立即输入：

[![](cert-images/fastlane-image1.png "cert will prompt for your Apple ID and password")](cert-images/fastlane-image1.png#lightbox)

> [!IMPORTANT]
> 首次输入密码时，密码会保存到本地 macOS 密钥链。 或者，如果不希望在密钥链中存储密码，也可以使用环境变量来存储用户名和密码，或者可以使用 `export fastlane_DONT_STORE_PASSWORD=1`。 有关使用 Fastlane 管理凭据的详细信息，请参阅 Fastlane 的[凭据管理器指南](https://github.com/fastlane/fastlane/blob/master/credentials_manager/README.md)。

通过使用以下命令，还可以将 Apple ID 作为参数传递：

```
fastlane cert -u myemailadress@domain.com
```

如果 Apple ID 关联多个团队，此处将显示这些团队。 选择你想要使用的团队所对应的数字：

[![](cert-images/fastlane-image2.png "Select the team that you wish to use")](cert-images/fastlane-image2.png#lightbox)

通过使用以下标志也可以传递团队 ID：

```
fastlane cert -l 2TU993NY9J
```

Fastlane 将会检查本地计算机上是否已安装了可用的签名证书，并且是否会使用此证书。

如果没有签名证书，cert 将：

- 创建新的私钥和签名请求
- 生成、下载和安装证书
- 将证书和私钥导入密钥链

如果已达到允许帐户使用的最大签名标识数目，将引发异常。 如果想要创建新的签名标识，必须通过开发人员中心手动撤销其中一个现有证书，然后重试。

> [!NOTE]
> 如果私钥链中尚不存在现有签名标识，则 Fastlane 无法从开发人员中心下载它们。 这是因为私钥仅存在于计算机上或在导出 (*.p12) 版本的证书中，但绝不会在开发人员中心。

<a name="options" />

## <a name="additional-options"></a>附加选项

使用 cert 时，可使用下列选项提供额外支持：

- 将 `-–help` 标志用于所有的可用命令列表：

    ```
    fastlane cert --help
    ```

- 使用 `-–verbose` 标志增加输出的详细程度

    ```
    fastlane cert --development --verbose
    ```

## <a name="related-links"></a>相关链接

- [fastlane – cert](https://github.com/fastlane/fastlane/blob/master/cert/README.md)
