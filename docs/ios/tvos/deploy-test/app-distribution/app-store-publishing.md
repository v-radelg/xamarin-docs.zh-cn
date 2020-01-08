---
title: 发布到 Apple TV App Store
description: 本文档介绍如何将应用发布到 Apple TV App Store。 本文介绍如何配置、预配、生成和提交使用 Xamarin 构建的 tvOS 应用程序。
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a3d0962eb8253ca72b6afce77036e02a7aa59b94
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607888"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>发布到 Apple TV App Store

若要将应用程序分发到所有 Apple TV 设备，Apple 要求通过*APPLE Tv App store*发布应用，使应用存储 tvOS 应用的一站式购物位置。 许多类型的应用程序的开发人员可以在这一单点分发的巨大成功上获得资本。 Apple TV App Store 是一个全包式解决方案，它为应用程序开发人员提供了分发和支付系统。

将应用程序提交到 Apple TV 应用商店的过程包括：

1. 创建应用 ID，选择“权利”。
2. 创建分发预配配置文件。
3. 使用此配置文件构建你的应用程序。
4. 通过*ITunes Connect*提交你的应用。

在本文中，我们将介绍预配、生成和提交 Apple TV 应用商店分发应用所需的所有步骤。

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>提交应用程序前的准备

将应用提交到 Apple TV 应用商店后，它会经历 Apple 的审核过程，以确保其符合 Apple 的质量和内容指导原则。 如果应用程序不符合这些指南，Apple 将拒绝此应用程序，此时需要处理 Apple 提出的不符合项，然后重新提交。
因此，如果熟悉这些指南并尽力使应用程序与其相符，应用程序通过 Apple 审核的几率较大。 Apple 指南适用于[应用商店审查准则](https://developer.apple.com/appstore/resources/approval/guidelines.html)，并[为新的 Apple TV 准备应用提交](https://developer.apple.com/tvos/submit/)。

提交应用时需要注意几点：

1. 请确保应用的说明与应用中包含的功能匹配。
2. 测试应用不会在正常使用时崩溃。 这包括每个支持的 Apple TV 设备上的使用情况。

Apple 还会维护 Apple TV 应用商店提交提示的列表。 可在[在 App Store 上分发](https://developer.apple.com/appstore/resources/submission/tips.html)中阅读这些列表。

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中配置应用程序

[ITunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)是一套基于 web 的工具，用于在 Apple TV 应用商店上管理 tvOS 应用。 你的 Xamarin tvOS 应用需要在 iTunes Connect 中正确设置和配置，然后才能提交到 Apple 进行审核并最终在 Apple TV 应用商店中发布以供销售或免费应用。

请执行下列操作：

1. 验证 iTunes Connect 的**协议、税款和银行**部分中是否具备恰当的最新协议，以便发布免费或付费的 iOS 应用程序。
2. 为应用程序创建新的**ITunes Connect 记录**，并指定其**显示名称**（如 Apple TV 应用商店中所示）。
3. 选择**售价**或指定免费发布应用程序。
4. 在应用程序所支持的 Apple TV 设备上，为应用程序提供应用**商店图标**（大图标）和屏幕截图。 有关更多详细信息，请参阅[使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)指南。
5. 为应用程序提供清晰、简洁的**说明**，包括应用程序的功能和对最终用户的好处。
6. 提供**类别**、**子类别**和**关键字**，帮助用户在 Apple TV 应用商店中查找应用。
7. 提供 Apple 要求的你的网站的**联系人**和**支持人员** URL。
8. 设置应用程序的**分级**，这由 Apple 电视应用商店上的家长控制使用。
9. 配置可选的 App Store 技术，如 **Game Center** 和**应用内购买**。

有关更多详细信息，请参阅[ITunes Connect 文档中的配置 TvOS 应用](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)。

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>准备 App Store 分发

若要将应用发布到 Apple TV App Store，首先需要生成它以进行分发，这涉及到许多步骤。 以下各节介绍了准备用于发布的 tvOS 应用程序所需的一切内容，以便可以生成该应用程序并将其提交到 Apple TV 应用商店，以便查看和发布。

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>对应用程序服务进行预配

Apple 提供一系列特殊应用程序服务（也称为权利），可以在为 tvOS 应用程序创建唯一 ID 时为其激活。 无论你使用的是自定义权利，你仍需要为你的 tvOS 应用创建一个唯一 ID，然后才能将其发布到 Apple TV 应用商店。

使用 Apple 的基于 Web 的 iOS 预配门户创建应用 ID 和（可选）选择权利，包括以下步骤：

1. 选择 "**预配** > **开发**"。
2. 单击“+”按钮，并为新的应用程序提供“名称”和“捆绑 ID”。
3. 滚动到屏幕底部，选择 tvOS 应用程序所需的任何**应用服务**。
4. 单击“继续”按钮，并按照屏幕上的说明创建新的应用程序 ID。

除了在定义应用 ID 时选择并配置所需的应用程序服务之外，还需要通过编辑 `Info.plist` 和 `Entitlements.plist` 文件在 tvOS 项目中配置应用程序 ID 和权利。

Visual Studio for Mac 中执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 在 " **TvOS 应用程序目标**" 部分，填写应用程序的名称并输入定义应用程序 ID 时创建的**捆绑标识符**。
3. 保存对 `Info.plist` 文件所做的更改。
4. 在“解决方案资源管理器”中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
5. 选择并配置 tvOS 应用所需的权利，使其与定义应用 ID 时执行的设置匹配。
6. 保存对 `Entitlements.plist` 文件所做的更改。

有关详细说明，请参阅[为应用程序服务进行预配](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services)文档。 虽然本文档是针对 iOS 编写的，但也可以使用相同的步骤来设置 tvOS 应用。

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>设置应用图标、启动图像和顶架图像

要使 Apple 接受 tvOS 应用以将其包含在 Apple TV 应用商店中，它需要对其运行的所有 Apple TV 设备使用正确的图标、启动和顶架图像。 需要添加所需的图像资产，这些资源将编译到 `Assets.car` 文件中，并在上传到 iTunes Connect 之前包含在 tvOS 应用的包中。

有关详细说明，请参阅[使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>创建并安装分发配置文件

tvOS 使用*预配配置文件*来控制如何部署特定的应用程序版本。 这些文件包含有关用于应用签名的证书、应用程序 ID 和应用安装位置的信息。 对于开发和临时分发，预配配置文件还包括允许部署应用的设备列表。 但是，对于 Apple TV 应用商店分发，仅包括证书和应用 ID 信息，因为公共分发的唯一机制是通过 Apple TV App Store。

使用 Apple 的基于 Web 的 iOS 预配门户的预配包含以下步骤：

1. 选择“预配” > “分发”。
2. 单击 **+** 按钮，然后选择你想要为创建的分发配置文件的类型**Apple TV App Store**。
3. 从要为其创建分发配置文件的下拉列表中，选择“应用程序 ID”。
4. 选择对应用程序进行签名所需的证书。
5. 为新的**分发配置文件**输入**名称**，并生成配置文件。
6. 刷新 Xcode 中的可用配置文件列表。
7. 在 Visual Studio 中为**应用商店**_生成配置_选择分发预配配置文件。

有关详细说明，请参阅[创建分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile)和[在 Xamarin.iOS 项目中选择分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。 同样，这两个文档都是特定于 iOS 的，但使用相同的技术来实现 tvOS 应用。

<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>设置应用程序的生成配置

默认情况下，当你创建新的 tvOS 应用时，将自动为**调试**和**发布**部署创建_生成配置_。 在执行要提交到 Apple 的应用的最终版本之前，需要对基本**版本**配置进行一些修改。

请执行下列操作：

1. 右键单击 "**解决方案资源管理器**" 和 "选择"**选项**中的**项目名称**以将其打开以进行编辑。
2. 如果以特定版本的 tvOS 为目标，请在 " **TvOS Build** > **iOS SDK 版本**" 下选择该版本。 对于 tvOS 支持的预览版本，请将此值设置为 "**默认**值"。
3. 链接可以减少未使用的方法、属性、类等，从而减少应用程序的可分发的总大小，并且在大多数情况下，只应将其保留为默认值**Link FRAMEWORK SDK**。 在某些情况下，例如使用某些特定第三方库时，可能会强制将此值设置为“不链接”，防止删除需要的元素。
4. 若要交付 tvOS 应用，需要使用 LLVM 优化编译器。 确保在 "**发布**" 配置下选中 "**使用 LLVM 优化编译器**" 框。
5. Apple 还要求 tvOS 应用使用 bitcode。 再次在**发布**配置下，将 `--bitcode=asmonly` 添加到 "**其他 mtouch 参数**" 框中。
6. 应检查 "**为 IOS 优化 PNG 图像文件**" 复选框，因为这将有助于进一步降低应用的可交付结果大小。
7. *不*应启用调试，因为这将为生成增加不必要的大小。

<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>生成并提交可分发文件

正确配置 Xamarin. tvOS 应用后，便可以执行最终的分发版本，并将其提交到 Apple 进行审核和发布。

#### <a name="build-your-archive"></a>生成存档

1. 在 Visual Studio for Mac 中选择“发布 | 设备”配置：

    ![](app-store-publishing-images/buildxs01new.png "Select the Release configuration")
2. 在“生成”菜单中，选择“存档以供发布”：

    [![](app-store-publishing-images/buildxs02new.png "Select Archive for Publishing")](app-store-publishing-images/buildxs02new.png#lightbox)
3. 存档创建完成后，将显示“存档”视图：

    [![](app-store-publishing-images/buildxs03new.png "The Archives view")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>签名和分发应用

每次生成用于存档的应用程序时，都会自动打开“存档视图”，显示所有已存档的项目（按解决方案分组）。 默认情况下，此视图只显示当前打开的解决方案。 若要查看拥有存档的所有解决方案，请单击“显示所有存档”选项。

建议保留部署到客户（App Store 或企业部署）的存档，以便可在稍后的日期表示生成的任何调试信息。

对应用进行签名并准备分发：

1. 选择 "**签名并分发 ...** "，如下所示：

    [![](app-store-publishing-images/buildxs04new.png ", Select theSign and Distribute...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. 这将打开发布向导。 选择“App Store”分发频道以创建包，再打开应用加载程序：

    [![](app-store-publishing-images/distribute01.png "Select the App Store distribution channel")](app-store-publishing-images/distribute01.png#lightbox)
3. 在 "预配配置文件" 屏幕上，选择签名标识和对应的配置文件，或者用其他标识重新签名：

    [![](app-store-publishing-images/distribute02.png "Select the signing identity and corresponding provisioning profile")](app-store-publishing-images/distribute02.png#lightbox)
4. 验证包的详细信息，再单击“发布”以保存 `.ipa` 包：

    [![](app-store-publishing-images/distribute03.png "Verify the details of the package")](app-store-publishing-images/distribute03.png#lightbox)
5. 保存 `.ipa` 后，应用即可通过应用加载程序上传到 iTunes Connect：

    [![](app-store-publishing-images/distribute04.png "Uploaded to iTunes Connect via the Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

创建分发生成并将其存档后，现可将应用程序提交到 iTunes Connect。

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>将应用提交到 Apple

分发生成完成后，即可将 iOS 应用程序提交到 Apple 以供审核，然后在 App Store 上发布。

保存 `.ipa`后，Visual Studio for Mac 中的存档工作流将自动打开应用程序加载程序：

1. 选择“提交应用”，单击“选择”按钮：

    [![](app-store-publishing-images/publishvs01.png "Select Deliver Your App")](app-store-publishing-images/publishvs01.png#lightbox)

2. 选择前面创建的 zip 或 IPA 文件，单击“确定”按钮。
3. 应用加载程序将验证文件：

    [![](app-store-publishing-images/publishvs02.png "The Application Loader validation screen")](app-store-publishing-images/publishvs02.png#lightbox)
4. 单击“下一步”按钮，将按照 App Store 的要求验证应用：

    [![](app-store-publishing-images/publishvs03.png "The application being validated against the App Store")](app-store-publishing-images/publishvs03.png#lightbox)
5. 单击“发送”按钮，将应用程序发送到 Apple 以供审核。
6. 文件成功上传后，应用程序加载程序将通知你。

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>iTunes Connect 状态

如果重新登录到 iTunes Connect，并从可用应用列表中选择应用，iTunes Connect 中的状态现在应显示为 "正在**等待审核**" （它可能会在处理过程中暂时读取 "**上载接收**"）：

[![](app-store-publishing-images/image21.png "The status in iTunes Connect showing Waiting for Review")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>故障排除

如果在将 tvOS 应用提交到 Apple TV 应用商店时遇到问题，请参阅[故障排除](~/ios/tvos/troubleshooting.md)指南。 它包含一些你可能会遇到的已知问题，以及如何在 tvOS 中解决这些问题。

<a name="Summary" />

## <a name="summary"></a>摘要

本文介绍了如何配置、生成和提交 Apple TV App Store 发布的应用。 首先，介绍创建并安装分发配置文件所需的步骤。 接下来，本演练逐步介绍如何使用 Visual Studio for Mac 来创建分发版本。 最后，该示例演示了如何使用 iTunes Connect 和 Xcode 存档工具将应用程序提交到 Apple TV App Store。

## <a name="related-links"></a>相关链接

- [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)
- [为新的 Apple TV 准备应用提交](https://developer.apple.com/tvos/submit/)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常见的应用拒绝](https://developer.apple.com/app-store/review/rejections/)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
