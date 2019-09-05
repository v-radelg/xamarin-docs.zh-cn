---
title: Fastlane for iOS - Match
description: 本文档介绍了 fastlane 的 match 命令，该命令用于创建和维护 iOS 开发的代码签名证书和预配配置文件。
ms.prod: xamarin
ms.assetid: C4A2A67E-0643-4CED-B1A9-79D65054F3CA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 8991ddc55069fad8c5f023f35ece0926f0f7e5b8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285727"
---
# <a name="fastlane-for-ios---match"></a>Fastlane for iOS - Match

按照传统惯例，设备预配已经由开发团队的成员通过 Xcode 或在 Apple 开发人员门户网站上执行。 它包含以下几个步骤：

- 请求开发证书
- 将设备添加到门户
- 创建应用 ID
- 创建预配配置文件
- 下载配置文件和证书

不论是通过手动还是通过 Xcode 设置设备以进行开发所需的步骤详细信息，都可以在[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南中找到。

本指南介绍如何将 fastlane 工具用作 Xcode 的替代方法。

## <a name="installation"></a>安装

有关安装 Fastlane 的信息，请参阅 [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation) 指南介绍。

<a name="whatismatch" />

## <a name="what-is-match"></a>什么是 Match？

Match 负责创建和维护代码签名证书和预配配置文件，使 iOS 开发团队可以在所有开发人员中共享一个代码签名标识。

在向 App Store 部署应用、执行 beta 版本测试，或在设备上安装应用时，开发团队的每个成员都有其自己的签名标识。 这可能会导致标识和配置文件发生冲突，并需要手动创建、轮换和管理配置文件和应用程序 ID。

但是，match 会创建和维护所有证书和配置文件，并将它们存储在私有 GIT 存储库。 这样，团队中的所有开发人员都可以访问和使用这些凭据。 另外，这样还额外提高了证书的安全性：除了存储在私有 GIT 存储库，还可以使用[密码](#passphrase)加密。 通过将代码签名项目存储在存储库中，团队代理和管理员可以根据需要更新和轮换证书，这意味着将新证书分发到每个开发人员将花费更少的时间。

> [!IMPORTANT]
> match 当前不支持内部企业配置文件。

<a name="initializing" />

## <a name="initializing-your-project-with-match"></a>使用 Match 初始化项目

如果你是团队管理员，通过 github.com 或 bitbucket.com 创建私有 GIT 存储库时，请确保将所有团队成员作为参与者添加到存储库。

通过使用终端，将目录更改为项目目录并运行：

```
fastlane match init
```

出现提示时，请输入 GIT 存储库的 URL：

 [![](match-images/fastlane-image7.png "输入 git 存储库的 URL")](match-images/fastlane-image7.png#lightbox)

然后可以找到此 URL，通过单击 github.com 上的“克隆或下载”  按钮可复制此 URL，如下所示：

[![](match-images/fastlane-image6.png "github.com 上的“克隆”或“下载”按钮下的 URL")](match-images/fastlane-image6.png#lightbox)

初始化项目将创建 Matchfile 文本文件，该文件可以进行编辑，从而将环境变量传递给 Match 工具。 Matchfile 示例如下所示：

[![](match-images/fastlane-image8.png "matchfile 示例")](match-images/fastlane-image8.png#lightbox)

<a name="running" />

## <a name="running-match"></a>运行 Match

> [!NOTE]
> fastlane 建议首次运行match 之前，应考虑使用 [match nuke 命令](#using)清除现有配置文件和证书。

根据所需的环境，可以使用以下任一命令创建新证书和预配配置文件，并将其存储在新的 GIT 存储库：

```
fastlane match appstore

fastlane match adhoc

fastlane match development
```

除了创建新的证书和配置文件，使用以下任一命令还可以将以下项添加或更新（如果已存在）到 GIT 存储库：

- 证书文件夹
- 配置文件文件夹
- 附带基本说明的自述文件
- match 版本

[![](match-images/fastlane-image9.png "git 存储库中的项目结构")](match-images/fastlane-image9.png#lightbox)

预配配置文件安装在 `~/Library/MobileDevice/Provisioning Profiles` 中。 证书和私钥直接安装在密钥链中。

<a name="using" />

### <a name="using-the-nuke-command"></a>使用 `nuke` 命令

如果证书比较杂乱，可以通过以下命令使用 `nuke` 吊销每种环境的证书和配置文件：

```
fastlane match nuke
```

若要撤销特定环境的所有证书和预配配置文件：

```
fastlane match nuke development
```

 or

```
fastlane match nuke distribution
```

Fastlane 将在进行删除前确认将要删除的文件。

<a name="passphrase" />

### <a name="passphrase"></a>密码

首次运行 `match` 时，需要设置 GIT 存储库的密码。 请务必记住此密码，因为在不同计算机上运行 match 时，将需要此密码。 这又增加了一层安全保障：每个文件将使用 OpenSSL 加密。 后来在新的计算机上每次运行 `match` 时，系统都将提示输入此密码 – 初次输入后，密码将添加到本地密钥链。

若要将密码设置为使用环境变量加密配置文件，请使用 `MATCH_PASSWORD`。

<a name="options" />

## <a name="additional-options"></a>附加选项

使用 match 时，可使用下列选项提供额外支持：

- 将 `-–help` 标志用于所有的可用命令列表：

    ```
    fastlane match cert --help
    ```

- 使用 `-–verbose` 标志增加输出的详细程度：

    ```
    fastlane match --development --verbose
    ```

- 如果开发人员门户上的设备计数已更改，请使用 `--force_for_new_devices` 标识强制预配配置文件进行续订”

    ```
    fastlane match development --force_for_new_devices
    ```

## <a name="related-links"></a>相关链接

- [fastlane - match](https://github.com/fastlane/fastlane/blob/master/match/README.md)
