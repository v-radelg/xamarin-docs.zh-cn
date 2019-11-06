---
title: Xamarin.Mac 中的证书和标识符
description: 本指南介绍如何创建发布 Xamarin.Mac 应用时所需的证书和标识符。
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 59c3372350caf3939a4e40ba2999ffb490d4a1f7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030082"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Xamarin.Mac 中的证书和标识符

_本指南介绍如何创建发布 Xamarin.Mac 应用时所需的证书和标识符。_

## <a name="certificates-and-identifiers"></a>证书和标识符

请访问 [Apple 开发人员成员中心](https://developer.apple.com)，配置用于开发的 Mac。 主菜单如下所示：

[![Apple Developer Member Center](certificates-identifiers-images/devcenter01.png "Apple Developer Member Center")](certificates-identifiers-images/devcenter01-large.png#lightbox)

单击“证书、标识符和描述文件”  链接：

[![选择“证书、标识符和配置文件”](certificates-identifiers-images/devcenter02.png "选择“证书、标识符和配置文件”")](certificates-identifiers-images/devcenter02-large.png#lightbox)

接着，单击“Mac 应用”  部分下的“证书链接”  ：

[![选择“证书”链接](certificates-identifiers-images/devcenter03.png "选择“证书”链接")](certificates-identifiers-images/devcenter03-large.png#lightbox)

单击“所有”  链接，然后单击 **+** 按钮：

[![选择“所有”并添加新项](certificates-identifiers-images/certif01.png "选择“所有”并添加新项")](certificates-identifiers-images/certif01-large.png#lightbox)

如果需要，从此处下载“中间证书”  （全球开发人员关系证书颁发机构和开发人员 ID 证书颁发机构）。 但是，这些应由 Xcode 自动为开发人员设置。

本部分的其余部分提供以下四个部分的演练，以完成 Mac 开发人员帐户设置。

- **注册 Mac 应用程序 ID** - 开发人员需对编写的每个应用程序遵循这些步骤。
- **注册 macOS 系统** - 仅在添加要测试的计算机时需要。
- **创建证书** - 仅在设置证书时以及之后续订时需要进行一次。
- **创建配置文件** - 对于每个编写的新应用程序，以及在添加新系统时，开发人员需要遵循这些步骤。

单击此页左上角的“概述”  链接，随时返回到此菜单。

### <a name="register-mac-app-id"></a>注册 Mac 应用 ID

开发人员需要为编写的每个应用程序注册应用程序 ID。 使用以下步骤，为名为“MacWriter”的基本应用示例创建条目。

1. 输入“应用程序 ID 说明”  ，并选择应用程序所需的任何“应用程序服务”  ：

    [![输入说明和应用服务](certificates-identifiers-images/devcenter04.png "输入说明和应用服务")](certificates-identifiers-images/devcenter04-large.png#lightbox)
2. 为应用输入“捆绑 ID”  ，并单击“继续”  按钮：

    [![输入捆绑 ID](certificates-identifiers-images/devcenter05.png "输入捆绑 ID")](certificates-identifiers-images/devcenter05-large.png#lightbox)
3. 验证信息，并单击“提交”  按钮：

    [![验证信息](certificates-identifiers-images/devcenter06.png "验证信息")](certificates-identifiers-images/devcenter06-large.png#lightbox)

某些“应用程序服务”  可能需要进一步配置（例如，iCloud）。 如果是这种情况，选择刚创建的新应用程序 ID 并单击“编辑”  按钮：

[![编辑新的应用 ID](certificates-identifiers-images/devcenter07.png "编辑新的应用 ID")](certificates-identifiers-images/devcenter07-large.png#lightbox)

若要配置 iCloud 服务，单击“编辑”  按钮：

[![配置 iCloud 服务](certificates-identifiers-images/devcenter08.png "配置 iCloud 服务")](certificates-identifiers-images/devcenter08-large.png#lightbox)

在此处，开发人员可以配置将要使用的数据库：

[![配置数据库](certificates-identifiers-images/devcenter09.png "配置数据库")](certificates-identifiers-images/devcenter09-large.png#lightbox)

### <a name="register-macos-systems"></a>注册 macOS 系统

若要创建用于测试的配置文件，开发人员需要注册 Mac 计算机。 最多可注册 100 台计算机用于测试 Mac 应用。

在 Mac 开发人员中心，从“设备”  部分选择“所有”  ，然后单击 **+** 按钮：

[![添加新计算机](certificates-identifiers-images/devcenter10.png "添加新计算机")](certificates-identifiers-images/devcenter10-large.png#lightbox)

输入要添加的计算机的“名称”  和“UUID”  ，然后单击“继续”  按钮。 检查信息，然后单击“注册”  按钮：

[![输入新计算机的信息](certificates-identifiers-images/devcenter11.png "输入新计算机的信息")](certificates-identifiers-images/devcenter11-large.png#lightbox)

### <a name="create-certificates"></a>创建证书

使用“证书”部分创建用于对 Mac 应用程序进行签名的不同类型的证书：

[![创建新的证书](certificates-identifiers-images/certif01.png "创建新证书")](certificates-identifiers-images/certif01-large.png#lightbox)

证书有三大类型：

- **Mac 开发证书** - 对于常规应用开发可选，但如果开发人员计划使用 iCloud 或推送通知等功能则必需。 开发人员需要开发证书才能创建允许他们访问这些功能的预配配置文件。
- **Mac App Store** - 开发人员需要一个用于应用的证书，还需要一个用于安装程序的证书。
- **开发人员 ID** - 选择在 Mac App Store 外分配时用于应用和安装程序的证书。

以下部分提供创建上述每种证书类型的示例。

#### <a name="mac-development-certificate"></a>Mac 开发证书

如前所述，只有使用 macOS 功能（例如 iCloud 或推送通知）时，才必需 Mac 应用开发证书。

执行以下操作新建开发证书：

1. 选择“Mac 开发”  单选按钮，然后单击“继续”  ：

     [![添加开发证书](certificates-identifiers-images/certif02.png "添加开发证书")](certificates-identifiers-images/certif02-large.png#lightbox)
2. 下一屏幕介绍如何使用密钥链访问来创建要上传的证书签名请求文件：

    [![密钥链访问上传屏幕](certificates-identifiers-images/certif03.png "密钥链访问上传屏幕")](certificates-identifiers-images/certif03-large.png#lightbox)
3. 请选择有意义的证书公用名，以便之后创建最后一个证书时易于识别。 请记住保存文件的位置，以便在下一步中找到：

    ![导出证书](certificates-identifiers-images/image12.png "导出证书")
4. 证书请求文件（扩展名 `.certSigningRequest`）本地保存在 Mac 上。 请记住其保存位置（默认位置是桌面），下一步需要选择它：

    [![上传证书文件](certificates-identifiers-images/image13.png "上传证书文件")](certificates-identifiers-images/image13-large.png#lightbox)
5. 单击“下载”  获取证书，双击将其安装在“密钥链”  中：

    [![下载开发证书](certificates-identifiers-images/image15.png "下载开发证书")](certificates-identifiers-images/image15-large.png#lightbox)
6. 单击“下载”  获取证书，双击将其安装在“密钥链”  中。 **开发人员证书实用程序**会如下显示证书：

    [![开发人员证书实用工具](certificates-identifiers-images/image16.png "开发人员证书实用工具")](certificates-identifiers-images/image16-large.png#lightbox)
7. 它还会如下显示在“密钥链”  中：

    ![密钥链访问中的证书](certificates-identifiers-images/image17.png "密钥链访问中的证书")

如前所述，只有在开发人员实现 macOS 功能（例如 iCloud 或推送通知）时，才必需开发人员证书。 还要求创建“开发配置文件”  ，测试 Mac App Store 应用将需要该文件。

#### <a name="mac-app-store-certificates"></a>Mac App Store 证书

若要在 App Store 上发布应用，需要创建用于对应用程序和 Mac 安装程序包进行签名的“Mac App Store”  证书。

1. 选择“Mac App Store”  作为证书类型，然后单击“继续”  按钮：

    [![创建 App Store 证书](certificates-identifiers-images/certif04.png "创建 App Store 证书")](certificates-identifiers-images/certif04-large.png#lightbox)
2. 选择要创建的证书的类型（需要将发布到 App Store 的其中一种类型）：

    [![选择证书类型](certificates-identifiers-images/certif05.png "选择证书类型")](certificates-identifiers-images/certif05-large.png#lightbox)
3. 下一页解释如何使用“密钥链访问”  生成证书请求文件。 按照说明操作：

    [![生成密钥链请求](certificates-identifiers-images/certif06.png "生成密钥链请求")](certificates-identifiers-images/certif06-large.png#lightbox)
4. 选择描述性“公用名”  - 例如使名称中包含文本“App Store Application”：

    ![输入描述性名称](certificates-identifiers-images/image20.png "输入描述性名称")
5. 证书请求文件（扩展名 `.certSigningRequest`）本地保存在 Mac 上。 请记住其保存位置（默认位置是桌面）：

    [![保存证书](certificates-identifiers-images/image21.png "保存证书")](certificates-identifiers-images/image21-large.png#lightbox)
6. 单击“下载”  获取证书，双击将其安装在“密钥链”  中：

    [![下载 App Store 证书](certificates-identifiers-images/image23.png "下载 App Store 证书")](certificates-identifiers-images/image23-large.png#lightbox)
7. 单击“继续”  ，然后严格按照相同步骤为安装程序  下载另一个证书：

    [![选择安装程序](certificates-identifiers-images/image24.png "选择安装程序")](certificates-identifiers-images/image24-large.png#lightbox)
8. 选择描述性“公用名”  - 例如使名称中包含文本“AppStore Installer”：

    ![设置证书名称](certificates-identifiers-images/image25.png "设置证书名称")
9. 证书请求文件（扩展名 `.certSigningRequest`）本地保存在 Mac 上。 请记住其保存位置（默认位置是桌面）：

    [![上传证书](certificates-identifiers-images/image26.png "上传证书")](certificates-identifiers-images/image26-large.png#lightbox)

    [![下载分发证书](certificates-identifiers-images/image28.png "下载分发证书")](certificates-identifiers-images/image28-large.png#lightbox)
10. 单击“下载”  获取证书，双击将其安装在“密钥链”  中。 “开发人员证书实用程序”会如下显示证书：

    [![开发人员证书实用工具](certificates-identifiers-images/image29.png "开发人员证书实用工具")](certificates-identifiers-images/image29-large.png#lightbox)
11. 这两个新证书此时会出现在“密钥链”  中：

    [![密钥链访问中的证书](certificates-identifiers-images/image30.png "密钥链访问中的证书")](certificates-identifiers-images/image30-large.png#lightbox)

#### <a name="developer-id-certificates"></a>开发人员 ID 证书

若要自行发布 Xamarin.Mac 应用程序（而非通过 Apple App Store 发布），开发人员需要开发人员 ID 证书对应用进行签名，从而进行发布和安装。

请执行以下操作：

1. 在“证书”  部分，先单击 **+** 按钮，然后选择“开发人员 ID”  单选按钮：

    [![添加开发人员 ID](certificates-identifiers-images/certif07.png "添加开发人员 ID")](certificates-identifiers-images/certif07-large.png#lightbox)
2. 单击“继续”  按钮，然后选择要创建的开发人员 ID 的类型：

    [![选择开发人员 ID 类型](certificates-identifiers-images/certif08.png "选择开发人员 ID 类型")](certificates-identifiers-images/certif08-large.png#lightbox)
3. 需要两个证书，一个用来对应用程序本身进行签名，另一个用来对应用程序的安装程序进行签名。 为这些密钥命名证书请求时要注意：使用包含 `Application` 和 `Installer` 文本的描述性名称，便于之后进行区分。
4. 下一个屏幕提供有关如何创建证书的详细说明，单击“继续”  按钮：

    [![如何创建证书](certificates-identifiers-images/certif09.png "如何创建证书")](certificates-identifiers-images/certif09-large.png#lightbox)
5. 选择描述性“公用名” - 例如使名称中包含文本“Developer ID Application”  ：

    ![输入证书的名称](certificates-identifiers-images/image33.png "输入证书的名称")
6. 证书请求文件（扩展名 `.certSigningRequest`）本地保存在 Mac 上。 请记住其保存位置（默认位置是桌面）：

    [![上传证书](certificates-identifiers-images/certif10.png "上传证书")](certificates-identifiers-images/certif10-large.png#lightbox)

    [![下载开发人员 ID](certificates-identifiers-images/certif11.png "下载开发人员 ID")](certificates-identifiers-images/certif11-large.png#lightbox)
7. 单击“下载”  获取证书，双击将其安装在“密钥链”  中。
8. 单击“继续”  ，然后严格按照相同步骤为安装程序  下载另一个证书。
9. 选择描述性“公用名” - 例如使名称中包含文本“Developer ID Installer”  ：

    ![输入公用名](certificates-identifiers-images/image38.png "输入公用名")
10. 证书请求文件（扩展名 `.certSigningRequest`）本地保存在 Mac 上。 请记住其保存位置（默认位置是桌面）：

    [![上传证书](certificates-identifiers-images/certif10.png "上传证书")](certificates-identifiers-images/certif10-large.png#lightbox)
11. 证书此时可供下载 - 单击“下载”  按钮，再单击“完成”  ：

    [![下载证书](certificates-identifiers-images/certif11.png "下载证书")](certificates-identifiers-images/certif11-large.png#lightbox)
12. 单击“下载”  获取证书，双击将其安装在“密钥链”  中。 **开发人员证书实用程序**会如下显示证书：

    [![开发人员证书实用工具](certificates-identifiers-images/certif12.png "开发人员证书实用工具")](certificates-identifiers-images/certif12-large.png#lightbox)
13. “密钥链”  会显示以下各项：

    [![密钥链访问中的证书](certificates-identifiers-images/image43.png "密钥链访问中的证书")](certificates-identifiers-images/image43-large.png#lightbox)

## <a name="related-links"></a>相关链接

- [安装](/visualstudio/mac/installation/)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
