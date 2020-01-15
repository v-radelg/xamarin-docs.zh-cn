---
title: Xamarin.Mac 中的证书和标识符
description: 本指南介绍如何创建发布 Xamarin.Mac 应用时所需的证书和标识符。
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2ea3516c1fb89c8c9b9cc3694d7c95ccd87e9d41
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489452"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Xamarin.Mac 中的证书和标识符

_本指南介绍如何创建发布 Xamarin.Mac 应用时所需的证书和标识符。_

## <a name="setup"></a>安装

请访问 [Apple 开发人员成员中心](https://developer.apple.com)，配置用于开发的 Mac。 单击“帐户”  链接并登录。 主菜单如下所示：

> [!div class="mx-imgBorder"]
> [![Apple Developer Member Center](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

单击“证书、标识符和配置文件”  按钮（或靠近“证书”  标题的加号按钮）：

> [!div class="mx-imgBorder"]
> [![选择“证书、ID 和配置文件”](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

选择证书类型，然后单击“继续”  ：

> [!div class="mx-imgBorder"]
> [![选择“证书”链接](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

如果需要，可以从此处下载“中间证书”  （全球开发人员关系证书颁发机构和开发人员 ID 证书颁发机构）（页面底部的最后一项）。 但是，这些应由 Xcode 自动为开发人员设置。

本部分的其余部分介绍与 Mac 开发人员相关的部分：

- **注册 Mac 应用程序 ID** - 开发人员需对编写的每个应用程序遵循这些步骤。
- **注册 macOS 系统** - 仅在添加要测试的计算机时需要。
- **创建证书** - 仅在设置证书时以及之后续订时需要进行一次。
- **创建配置文件** - 对于每个编写的新应用程序，以及在添加新系统时，开发人员需要遵循这些步骤。

## <a name="register-mac-app-id"></a>注册 Mac 应用 ID

需要为每个应用程序注册应用 ID。 请按照以下步骤来创建条目：

1. 按“+”（加号）或“注册应用 ID”  ：

    > [!div class="mx-imgBorder"]
    > [![应用 ID 入门](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. 选择“应用 ID” 

    > [!div class="mx-imgBorder"]
    > [![应用 ID 入门](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. 输入“说明”  ，并选择应用程序所需的任何“应用服务”  ：a. 平台应为“macOS”  a. 选择“说明”  （仅在此门户中使用）a. 输入“捆绑 ID”  ，它应与“Info.plist”匹配  a. 选择应用需要的功能

    > [!div class="mx-imgBorder"]
    > [![输入说明和应用服务](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    按“继续”  检查所做的选择。

1. 如果信息正确，请单击“注册”  以完成安装：

    > [!div class="mx-imgBorder"]
    > [![查看输入的数据](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. 验证信息，并单击“提交”  按钮：

    > [!div class="mx-imgBorder"]
    > ![验证信息](certificates-identifiers-images/appid05.png)

某些“应用程序服务”  可能需要进一步配置（例如，iCloud）。 如果是这种情况，选择刚创建的新应用程序 ID 并单击“编辑”  按钮：

> [!div class="mx-imgBorder"]
> [![编辑新的应用 ID](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

若要配置 iCloud 服务，单击“编辑”  按钮：

> [!div class="mx-imgBorder"]
> [![配置 iCloud 服务](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>注册 macOS 设备

若要创建用于测试的配置文件，开发人员需要注册 Mac 计算机。 最多可注册 100 台计算机进行测试。

1. 在 Mac 开发人员中心，从“设备”  部分选择“所有”  ，然后单击 **+** 按钮：

    > [!div class="mx-imgBorder"]
    > [![添加新计算机](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. 输入要添加的计算机的“名称”  和“UUID”  ，然后单击“继续”  按钮。 检查信息，然后单击“注册”  按钮：

    > [!div class="mx-imgBorder"]
    > [![输入新计算机的信息](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. 查看并确认输入的数据：

    > [!div class="mx-imgBorder"]
    > [![输入新计算机的信息](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>创建证书

使用“证书”部分创建用于对 Mac 应用程序进行签名的不同类型的证书：

> [!div class="mx-imgBorder"]
> [![创建新的证书](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

与 macOS 开发相关的证书有五种主要类型：

- **Mac 开发** - 对于常规应用开发可选，但如果开发人员计划使用 iCloud 或推送通知等功能则必需。 开发人员需要开发证书才能创建允许他们访问这些功能的预配配置文件。
- **Mac 应用分发** - 开发人员需要一个用于应用的证书，还需要一个用于安装程序的证书。
- **Mac 安装程序分发** - 开发人员需要一个用于应用的证书，还需要一个用于安装程序的证书。
- **开发人员 ID 安装程序** - 在 Mac App Store 外分发时用于安装程序的证书。
- **开发人员 ID 应用程序** - 在 Mac App Store 外分发时用于应用的证书。

以下部分提供创建其中一些证书类型的示例。

### <a name="mac-development-certificate"></a>Mac 开发证书

如前所述，只有使用 macOS 功能（例如 iCloud 或推送通知）时，才必需 Mac 开发证书。

执行以下操作新建开发证书：

1. 选择“Mac 开发”  单选按钮，然后单击“继续”  ：

    > [!div class="mx-imgBorder"]
    > [![添加开发证书](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. 上传“证书签名请求”  。 证书请求文件（扩展名 `.certSigningRequest`）本地保存在 Mac 上。 单击“选择文件”  以选择证书请求，然后按“继续”  。

    > [!div class="mx-imgBorder"]
    > [![上传证书请求文件](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    访问[了解详细信息 >](https://help.apple.com/developer-account/#/devbfa00fef7) 链接，以获取有关如何使用“密钥链访问”  创建证书请求文件的说明。

1. 按“下载”  以获取证书文件，然后双击它进行安装：

    > [!div class="mx-imgBorder"]
    > [![下载证书文件](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

如前所述，只有在开发人员实现 macOS 功能（例如 iCloud 或推送通知）时，才必需开发人员证书。 还要求创建“开发配置文件”  ，测试 Mac App Store 应用将需要该文件。

### <a name="mac-app-store-certificates"></a>Mac App Store 证书

若要在 App Store 上发布应用，需要两个证书：

- “Mac 应用分发”  证书，用于对应用程序进行签名；以及 
- “Mac 安装程序分发”证书  ，用于对安装程序进行签名。

> [!TIP]
> 为这些密钥命名证书请求时要注意：使用包含 `Application` 和 `Installer` 文本的描述性名称，便于之后进行区分。

首先，创建安装程序证书：

1. 选择“Mac 安装程序分发”  作为证书类型，然后单击“继续”  按钮：

    > [!div class="mx-imgBorder"]
    > [![创建 App Store 证书](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. 下一页解释如何使用“密钥链访问”  生成证书请求文件。 按照说明操作：

    > [!div class="mx-imgBorder"]
    > [![上传证书请求](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    访问[了解详细信息 >](https://help.apple.com/developer-account/#/devbfa00fef7) 链接，以获取有关如何使用“密钥链访问”  创建证书请求文件的说明。 请记住选择一个证书名称来反映证书的类型  （应用程序或安装程序）。

1. 单击“下载”  获取证书，双击将其安装在“密钥链”  中：

    > [!div class="mx-imgBorder"]
    > [![下载 App Store 证书](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**按照 Mac 应用分发证书的相同步骤进行操作。**

![Mac 应用分发证书](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>开发人员 ID 证书

若要自行发布 Xamarin.Mac 应用程序（而非通过 Apple App Store 发布），需要以下两种证书：

- “开发人员 ID 安装程序”  证书，用于对应用程序进行签名；以及 
- “开发人员 ID 应用程序”  证书，用于对安装程序进行签名。

> [!TIP]
> 为这些密钥命名证书请求时要注意：使用包含 `Application` 和 `Installer` 文本的描述性名称，便于之后进行区分。

创建、下载和安装证书后，它们将显示在“密钥链访问”  中：

[密钥链访问证书列表](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>相关链接

- [安装](/visualstudio/mac/installation/)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [开发者 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
