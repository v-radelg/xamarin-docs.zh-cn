---
title: Xamarin.iOS 应用的临时分发
description: 本文档概述了 Ad-Hoc 分发技术，此技术主要用于通过大范围用户来测试 Xamarin.iOS 应用程序。
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: efa3c8cf4d2e64c6d8bccdfe6e6fec9602d17c73
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865856"
---
# <a name="ad-hoc-distribution-for-xamarinios-apps"></a>Xamarin.iOS 应用的临时分发

_本文档概述了 Ad-Hoc 分发技术，此技术主要用于通过大范围用户来测试 Xamarin.iOS 应用程序。_

Xamarin.iOS 应用开发完成后，软件开发生命周期中的下一步是将应用分发给用户以供测试。

iTunes Connect 是一个可用于管理应用测试的选项，[TestFlight](~/ios/deploy-test/testflight.md) 指南中对其进行了详细介绍。 但是，由于 Apple Developer Enterprise Program 的成员无法访问 iTunes Connect，因此 Ad Hoc  分发是测试这些应用的最好方法。

Xamarin.iOS 应用程序可通过 Ad Hoc 分发  进行用户测试，Ad Hoc 分发可在 Apple Developer Program 和 Apple Developer Enterprise Program 中获取，它允许测试最多 100 台 iOS 设备。

Ad Hoc 分发的优点在于，无需 App Store 批准，可通过 Web 服务器或 iTunes 进行无线安装。 但是，每成员资格年只能开发和分发最多 **100** 台设备，并且必须通过它们的 UDID 手动将其添加到 Member Center。 若要深入了解如何添加设备，请访问[设备预配](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice)指南。

Ad Hoc 分发需要应用程序使用包含代码签名信息的 Ad Hoc 配置文件  以及应用程序的标识和可安装应用程序的设备进行设置。

本指南介绍如何对 Ad Hoc 分发进行设置，以及如何分发 Xamarin.iOS 应用。

<a name="setup" />

## <a name="setting-up-for-distribution"></a>设置分发

即使计划发布用于内部开发的 Xamarin.iOS 应用程序，为了方便测试，需要生成一个特定于它的 Ad Hoc 分发配置文件。 此配置文件允许对要发布的应用程序进行数字签名，使其可安装在 iOS 设备上。

下一部分将介绍如何创建分发证书和分发配置文件。

> [!NOTE]
> 只有团队代理和管理员可创建分发证书和预配配置文件。

<a name="createcertificate" />

## <a name="create-a-distribution-certificate"></a>创建分发证书


1. 浏览到 Apple Developer Member Center 的“证书、标识符和描述文件”  部分。
2. 在“证书”  下选择“生产”  。
3. 单击“+”  按钮创建新证书。
4. 根据你的计划成员身份，在“生产”  标题下，选择“In-House 和 Ad Hoc”  或“App Store 和 Ad Hoc”  ：

   [![](ad-hoc-distribution-images/cert-first-small.png "选择“内部”和“临时”，或“App Store”和“临时”")](ad-hoc-distribution-images/cert-first-large.png#lightbox)

5. 单击“继续”，并按说明通过密钥链访问创建证书签名请求：

   [![](ad-hoc-distribution-images/createcertmanually02.png "通过 Keychain Access 创建证书签名请求")](ad-hoc-distribution-images/createcertmanually02.png#lightbox)

6. 在按照指示创建 CSR 后，单击“继续”，并将 CSR 上传到 Member Center：

   [![](ad-hoc-distribution-images/createcertmanually03.png "将 CSR 上传到 Member Center")](ad-hoc-distribution-images/createcertmanually03.png#lightbox)

7. 单击“生成”创建证书。
8. 最后，下载完成的证书，双击该文件进行安装。
9. 此时，证书应已在计算机上安装，但可能需要[刷新配置文件](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)，确保其在 Xcode 中可见。

或者，可通过 Xcode 中的”首选项”对话框请求证书。 为此，请执行以下步骤：

1. 选择团队，然后单击“管理证书...”  ：[![](ad-hoc-distribution-images/selectteam.png "选择团队")](ad-hoc-distribution-images/selectteam.png#lightbox)

2. 接下来，单击加号 (+) 按钮，然后选择“iOS App Store”   ：[![](ad-hoc-distribution-images/selectcert.png "选择“iOS App Store”")](ad-hoc-distribution-images/selectcert.png#lightbox)

<a name="createprofile" />

## <a name="create-a-distribution-provisioning-profile"></a>创建分发预配配置文件

<a name="createappid" />

### <a name="create-an-app-id"></a>创建 App ID
与创建的任何其他设置配置文件一样，需使用应用程序 ID 来确认分发到用户设备的应用。 如果尚未创建此项，请按照以下步骤创建：


1. 在 [Apple Developer Center](https://developer.apple.com/account/overview.action) 中，浏览到“证书、标识符和描述文件”  部分。 在“标识符”  下选择“应用 ID”  。
2. 单击“+”  按钮并提供“名称”  ，该名称将在门户中标识它。
3. 应用前缀需已设置为你的团队 ID 且不可更改。 选择显式或通配符应用程序 ID，并以反向 DNS 格式输入捆绑 ID，如：
    - **显式**：`com.[DomainName].[AppName]`
    - **通配符**：`com.[DomainName].*`
4. 选择应用要求的任意[应用服务](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)。
5. 单击“继续”  按钮，并按照屏幕上的说明创建新的应用 ID。

拥有创建分发配置文件所需的必要组件后，请按照下面的步骤进行创建：

1. 返回 Apple 预配门户，选择“预配”>“分发”  ：[![](ad-hoc-distribution-images/distribute01.png "选择“预配”>“分发”")](ad-hoc-distribution-images/distribute01.png#lightbox)

2. 单击“+”  按钮，选择要创建为“临时”  的分发配置文件的类型：

    [![](ad-hoc-distribution-images/distribute02.png "创建临时分发类型")](ad-hoc-distribution-images/distribute02.png#lightbox)

3. 单击“继续”  按钮，然后在下拉列表中选择要为其创建分发配置文件的应用程序 ID：

    [![](ad-hoc-distribution-images/distribute03.png "从下拉列表选择“App ID”")](ad-hoc-distribution-images/distribute03.png#lightbox)

4. 单击“继续”  按钮，然后选择签名应用程序时所需的分发证书：

    [![](ad-hoc-distribution-images/distribute04.png "选择签名应用程序时所需的分发证书")](ad-hoc-distribution-images/distribute04.png#lightbox)

5. 单击“继续”  按钮，为新的分发配置文件输入“名称”  ：

    [![](ad-hoc-distribution-images/distribute06.png "输入新的分发配置文件的名称")](ad-hoc-distribution-images/distribute06.png#lightbox)

6. 单击“生成”  按钮，创建新的配置文件并完成该过程。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

可能需要退出 Visual Studio for Mac，并让 Xcode 刷新可用签名标识和预配配置文件列表（按照[在 Xcode 中下载配置文件和证书](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)部分中的说明操作），然后 Visual Studio for Mac 中才会有新的分发配置文件。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

可能需要退出 Visual Studio，并让 Xcode（在生成主机的 Mac 上）刷新可用签名标识和预配配置文件列表（按照[在 Xcode 中下载配置文件和证书](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)部分中的说明操作），然后 Visual Studio 中才会有新的分发配置文件。

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>在 Xamarin.iOS 项目中选择分发配置文件

如果准备就绪可执行 Xamarin.iOS 应用程序的最终生成，请选择上面创建的分发配置文件。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 在 Visual Studio for Mac 中，执行以下操作：

1. 在“解决方案资源管理器”  中，双击项目名称，将其打开进行编辑。
2. 在“配置”  下拉列表中，选择“iOS 应用程序包签名”  和生成类型：

    ![](ad-hoc-distribution-images/releasexs01.png "从“配置”下拉列表选择生成类型")
3. 在大多数情况下，可以保留“签名标识”  和“预配配置文件”  的默认值（即“自动”  ），Visual Studio for Mac 会根据 Info.plist 中的应用程序包标识符选择正确的配置文件：

    ![](ad-hoc-distribution-images/releasexs02.png "“签名标识”和“预配配置文件”设为默认值“自动”")
4. 如果需要，在下拉列表中，选择上面创建的签名标识和分发配置文件：

    ![](ad-hoc-distribution-images/releasexs03.png "选择“签名标识”和“分发配置文件”")
5. 单击“确定”  按钮保存更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
 在 Visual Studio 中，执行以下操作：

1. 在“解决方案资源管理器  ”中，右键单击项目名称，然后选择“属性”  ，将其打开并进行编辑。
2. 在“配置”  下拉列表中，选择“iOS 应用程序包签名”  和生成类型：

    ![](ad-hoc-distribution-images/releasevs01.png "从“配置”下拉列表选择生成类型")
3. 在大多数情况下，可以保留“签名标识”  和“预配配置文件”  的默认值（即“自动”  ），Visual Studio 会根据 Info.plist 中的应用程序包标识符选择正确的配置文件：

    ![](ad-hoc-distribution-images/releasevs02.png "“签名标识”和“预配配置文件”设为默认值“自动”")
4. 如果需要，在下拉列表中，选择上面创建的签名标识和分发配置文件：

    ![](ad-hoc-distribution-images/releasevs03.png "选择“签名标识”和“分发配置文件”")
5. 保存对项目属性所做的更改。

-----

<a name="adhoc" />

## <a name="ad-hoc-distribution"></a>Ad Hoc 分发

虽然 [TestFlight](~/ios/deploy-test/testflight.md) 是一种常用的 Beta 测试和分发方法，但它是 iTunes Connect 的一部分，因此不可供 **Apple Developer Enterprise Program** 的成员使用。

如果 iTunes connect 不可选，那么 Ad Hoc 分发允许开发人员在许多设备上对应用进行 Beta 测试。 Ad Hoc 分发的工作方式与 In-House 分发类似，需要创建 IPA，IPA 可无线分发，或通过 iTunes 手动分发。

<a name="IPA_Creation" />

### <a name="ipa-support-for-ad-hoc-deployment"></a>Ad Hoc 部署的 IPA 支持

完成预配后，可将应用程序打包到名为 IPA  的文件中。 这是一个 zip 文件，内含应用程序以及其他元数据和图标。 IPA 用于将应用程序本地添加到 iTunes，使其可直接同步到配置文件中的设备。

若要深入了解如何创建 IPA，请参阅 [IPA 支持指南](~/ios/deploy-test/app-distribution/ipa-support.md)。

## <a name="summary"></a>总结

本文介绍了 Ad Hoc 分发机制，它用于测试 Xamarin.iOS 应用程序。


## <a name="related-links"></a>相关链接

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支持](~/ios/deploy-test/app-distribution/ipa-support.md)
