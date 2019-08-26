---
title: Fastlane for iOS – Sigh
description: 本文档介绍了 fastlane 的 sigh 命令，该命令用于创建、更新和修复所有 Xamarin.iOS 生成配置的预配配置文件。
ms.prod: xamarin
ms.assetid: CD17276F-2C8C-4A46-A54C-DD532EBD5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: bb2fd504cccb7aa81831288762ba623824e52554
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526511"
---
# <a name="fastlane-for-ios-sigh"></a>Fastlane for iOS – Sigh

> [!IMPORTANT]
> Fastlane 建议使用 [ `match`](~/ios/deploy-test/provisioning/fastlane/match.md) 来生成和维护预配配置文件。 只有想要完全控制代码签名并充分了解相关信息，才可直接使用 sigh。

## <a name="overview"></a>概述

按照传统惯例，设备预配由开发团队的成员通过 Xcode 或在 Apple 开发人员门户网站上执行。 它包含以下几个步骤：

- 请求开发证书
- 将设备添加到门户
- 创建应用 ID
- 创建预配配置文件
- 下载配置文件和证书

上述每个步骤包含需要解决的问题，具体取决于正在开发的应用程序的类型。 不论是手动还是通过 Xcode 设置设备以供开发，都可以在[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南中找到所需的步骤详细信息。

本指南介绍 fastlane 工具作为使用 Xcode 的替代方法，并说明下列内容：

- [什么是 Sigh](#whatissigh)
- [创建应用程序 ID](#appid)
- [添加新设备](#newdevices)
- [使用 Sigh](#using)
- [附加选项](#options)

> [!NOTE]
> 如果现有的应用 ID 与应用的捆绑 ID 相匹配，并且如果开发人员门户中存在设备，则可以忽略[创建应用程序 ID](#appid) 和[添加设备](#newdevices)上的步骤。 在这种情况下，跳转到[使用 Sigh](#using)开始使用。

## <a name="installation"></a>安装

有关安装 Fastlane 的信息，请参阅 [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation) 指南介绍。

<a name="whatissigh" />

## <a name="what-is-sigh"></a>什么是 Sigh

Sigh 提供一个终端接口，可用于为所有配置创建和续订预配配置文件：开发、应用商店分发、临时分发和企业分发。 此外，它还提供了一种直接下载和修复预配配置文件的方法。

<a name="appid" />

## <a name="creating-an-app-id"></a>创建应用 ID

可以使用以下命令创建应用程序 ID：

```
fastlane produce -u your@appleid.com -a com.company.appname --skip_itc
```

其中，`com.company.appname` 是应用的捆绑 ID，在 Xamarin.iOS 应用程序的 Info.plist 文件中可找到此捆绑 ID，如下所示：

[![](sigh-images/fastlane-image5.png "Xamarin.iOS 应用程序的 Info.plist 文件")](sigh-images/fastlane-image5.png#lightbox)

唯一的应用程序 ID 必须是反向 DNS 样式字符串。 创建此 ID 后，请记录下来，因为稍后在本指南中使用 sigh 时需要使用它。

如果需要在 [iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) 上创建应用，请从以上命令中删除 `--skip_itc` 标志。

<a name="newdevices" />

## <a name="adding-new-devices"></a>添加新设备

若要通过命令行将单个设备添加到开发人员门户，请输入以下命令：

```bash
fastlane run register_device name:"Adam iPhone" udid:"abcdeg1234567"
```

若要添加多个设备，请使用 `register_devices` 命令：

```bash
    register_devices(
        devices: {
            "iPhone 6" => "1234567890123456789012345678901234567890",
            "iPad Air 2" => "abcdefghijklmnopqrstvuwxyzabcdefghijklmn"
         }
    )
```

<a name="using" />

## <a name="using-sigh"></a>使用 Sigh

若要开始使用 sigh 实用程序，请在终端中输入以下命令：

```bash
fastlane sigh
```

默认情况下，这将创建[应用商店分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)预配配置文件。 若要设置设备进行开发，请传递 `--development` 标志：

```bash
fastlane sigh --development
```

fastlane 提示时，请输入你的 Apple ID 用户名。 如果首次使用 fastlane，可能还会要求输入密码。 否则，将从密钥链拉取密码环境变量。

如果 Apple ID 关联多个团队，此处将显示这些团队。 选择你想要使用的团队所对应的数字：

[![](sigh-images/fastlane-image2.png "选择想要使用的团队")](sigh-images/fastlane-image2.png#lightbox)

通过使用以下方式也可以将团队 ID 传递到 CLI：

```bash
fastlane sigh -l 2TU993NY9J
```

输入应用的[应用程序 ID](#appid)。 请记住，这应该与应用的 Info.plist 中的捆绑标识符相匹配。

连接到你的帐户的所有设备都将添加到你的预配配置文件。

然后 Fastlane 将创建、下载和安装适合你的预配配置文件。

如果浏览开发人员中心，则可以查看新创建的预配配置文件，如下所示：

[![](sigh-images/fastlane-image10.png "查看新创建的预配配置文件")](sigh-images/fastlane-image10.png#lightbox)

默认情况下，sigh 将在当前文件夹中存储预配配置文件。 若要更改输出目录，请编辑 `output_path`，或执行以下操作：

```bash
fastlane sigh -o "~/Library/MobileDevice/Provisioning Profiles"
```

<a name="options" />

## <a name="sigh-additional-options"></a>Sigh 附加选项

使用 sigh 时，可使用下列选项提供额外支持：

- 若要下载所有预配配置文件，请使用：

    ```bash
    fastlane sigh download_all
    ```

- 若要将特定签名标识用于预配配置文件，请使用：

    ```bash
    fastlane sigh -c "Amy cert"
    ```
    
    其中，`Amy cert` 是代码签名标识名。


## <a name="related-links"></a>相关链接

- [fastlane - sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme)
