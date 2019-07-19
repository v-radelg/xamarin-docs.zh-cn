---
title: Xamarin.iOS 应用的内部分发
description: 本文档简要概述了应用程序作为 Apple Enterprise Developer Program 成员身份进行的 In-House 分发。
ms.prod: xamarin
ms.assetid: 9466E51E-303E-466E-85D7-D0525E16BB37
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: a2dc974fba7beb70f1caecf8198181193d32e4c2
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865534"
---
# <a name="in-house-distribution-for-xamarinios-apps"></a>Xamarin.iOS 应用的内部分发

_本文档简要概述了应用程序作为 Apple Enterprise Developer Program 成员身份进行的 In-House 分发。_

在软件开发生命周期中，Xamarin.iOS 应用开发完毕后，下一步就是将其分发给用户。 专有应用可通过 **Apple Developer Enterprise Program** 内部  （之前称作企业）分发，它提供了以下好处：

- 无需提交应用程序供 Apple 审核。
- 可在任意数量的设备上部署一个应用程序
    - 请务必注意，Apple 清楚表明内部应用程序仅限内部使用。

还请务必注意 Enterprise Program：

- 不提供进行分发或测试（包括 TestFlight）的 iTunes Connect 访问权限。
- 会员资格的费用是每年 $299。

所有应用仍需要由 Apple 签名。

<a name="testing" />

## <a name="testing-your-application"></a>测试应用程序

通过 Ad Hoc 分发执行应用程序的测试。 有关测试的详细信息，请按照 [Ad-Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)指南中的步骤操作。 请注意，最多只能测试 100 台设备。

<a name="setup" />

## <a name="getting-set-up-for-distribution"></a>设置分发

与其他 Apple Developer Program 一样，在 Apple Developer Enterprise Program 下，只有团队管理员和代理可创建分发证书和配置文件。

Apple Developer Enterprise Program 证书将持续 3 年，配置文件将在 1 年后过期。

务必注意，不可续订已过期的证书，而需要将过期的证书替换为新的证书，如[下面](#certificate)所详述。

<a name="certificate" />

## <a name="creating-a-distribution-certificate"></a>创建分发证书

1. 浏览到 Apple Developer Member Center 的“证书、标识符和描述文件”  部分。
2. 在“证书”  下选择“生产”  。
3. 单击“+”  按钮创建新证书。
4. 在“生产”  标题下，选择“In-House 和 Ad Hoc”  ：

   [![](in-house-distribution-images/createcertmanually01.png "选择“内部”和“临时”")](in-house-distribution-images/createcertmanually01.png#lightbox)

5. 单击“继续”，并按说明通过密钥链访问创建证书签名请求：

   [![](in-house-distribution-images/createcertmanually02.png "通过 Keychain Access 创建证书签名请求")](in-house-distribution-images/createcertmanually02.png#lightbox)

6. 按指示创建 CSR 后，单击“继续”并将 CSR 上传到 Member Center：

   [![](in-house-distribution-images/createcertmanually03.png "将 CSR 上传到 Member Center")](in-house-distribution-images/createcertmanually03.png#lightbox)

7. 单击“生成”创建证书。
8. 下载已完成的证书，并双击该文件进行安装。
9. 此时，计算机上应安装了证书，但可能需要刷新配置文件，确保其在 Xcode 中可见。

或者，可通过 Xcode 中的”首选项”对话框请求证书。 为此，请执行以下步骤：

1. 选择团队，再单击“查看详细信息”  ：

   [![](in-house-distribution-images/selectteam.png "选择团队")](in-house-distribution-images/selectteam.png#lightbox)

2. 然后，单击“iOS 分发证书”  旁边的“创建”  按钮：

   [![](in-house-distribution-images/selectcert.png "创建“iOS 分发证书”")](in-house-distribution-images/selectcert.png#lightbox)

3. 接下来，单击加号 (+) 按钮，然后选择“iOS App Store”   ：

   [![](in-house-distribution-images/selectcert.png "选择“iOS App Store”")](in-house-distribution-images/selectcert.png#lightbox)

<a name="profile" />

## <a name="creating-a-distribution-provisioning-profile"></a>创建分发配置文件

<a name="appid" />

### <a name="creating-an-app-id"></a>创建应用 ID

与创建的其他任何配置文件一样，需要应用 ID 来标识将分发到用户设备的应用。 如果尚未创建此项，请按照以下步骤创建：


1. 在 [Apple Developer Center](https://developer.apple.com/account/overview.action) 中，浏览到“证书、标识符和描述文件”  部分。 在“标识符”  下选择“应用 ID”  。
2. 单击“+”  按钮并提供“名称”  ，该名称将在门户中标识它。
3. 应用前缀需已设置为你的团队 ID 且不可更改。 选择显式或通配符应用程序 ID，并以反向 DNS 格式输入捆绑 ID，如：显式  ：com.[DomainName].[AppName] 通配符  ：com.[DomainName].*
4. 选择应用要求的任意[应用服务](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)。
5. 单击“继续”  按钮，按照屏幕上的说明创建新的应用程序 ID。

拥有创建分发配置文件所需的必要组件后，请按照下面的步骤进行创建：

1. 返回 Apple 预配门户，选择“预配”   > “分发”  ：

   [![](in-house-distribution-images/distribute01.png "选择“预配”>“分发”")](in-house-distribution-images/distribute01.png#lightbox)

2. 单击“+”  按钮，选择要创建为“In-House”  的分发配置文件类型：

   [![](in-house-distribution-images/distribute02.png "创建内部分发配置文件")](in-house-distribution-images/distribute02.png#lightbox)

3. 单击“继续”  按钮，然后在下拉列表中选择要为其创建分发配置文件的应用程序 ID：

   [![](in-house-distribution-images/distribute03.png "从下拉列表选择“App ID”")](in-house-distribution-images/distribute03.png#lightbox)

4. 单击“继续”  按钮，然后选择签名应用程序时所需的分发证书：

   [![](in-house-distribution-images/distribute04.png "选择签名应用程序时所需的分发证书")](in-house-distribution-images/distribute04.png#lightbox)

5. 单击“继续”  按钮，为新的分发配置文件输入“名称”  ：

   [![](in-house-distribution-images/distribute06.png "输入新的分发配置文件的名称")](in-house-distribution-images/distribute06.png#lightbox)

6. 单击“生成”  按钮，创建新的配置文件并完成该过程。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 可能需要退出 Visual Studio for Mac，并让 Xcode 刷新可用签名标识和预配配置文件列表（按照[请求签名标识](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)部分中的说明操作），然后 Visual Studio for Mac 中才会有新的分发配置文件。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

可能需要退出 Visual Studio，并让 Xcode（位于生成主机的 Mac 上）刷新可用签名标识和配置文件列表（按照[请求签名标识](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)部分中的说明操作），然后 Visual Studio 中才会有新的分发配置文件可用。

-----

<a name="inhouse" />

## <a name="distributing-your-app-in-house"></a>实现应用的 In-House 分发

在 Apple Developer Enterprise Program 中，被许可方是负责分发应用程序的人员，其需遵循 Apple 设置的[指南](http://adcdownload.apple.com/Documentation/License_Agreements__Apple_Developer_Enterprise_Program/Apple_Developer_Program_Enterprise_Agreement_20150608.pdf)。

可使用各种不同的方法安全地分发应用，例如：

- 通过 iTunes 本地分发
- MDM 服务器
- 安全的内部 Web 服务器
- 电子邮件

若要使用以上任一方式分发应用，必须先创建一个 IPA 文件，如下一节中所述。


### <a name="creating-an-ipa-for-in-house-deployment"></a>创建 IPA 进行 In-House部署

完成预配后，可将应用程序打包到名为 IPA  的文件中。 这是一个 zip 文件，内含应用程序以及其他元数据和图标。 IPA 用于将应用程序本地添加到 iTunes，使其可直接同步到配置文件中的设备。

若要深入了解如何创建 IPA，请参阅 [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)指南。


## <a name="summary"></a>总结

本文简要概述了 Xamarin.iOS 应用程序的 In-House 分发。

## <a name="related-links"></a>相关链接

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Ad Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)
