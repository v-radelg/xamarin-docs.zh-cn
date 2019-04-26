---
title: 发布到 Apple TV App Store
description: 本文档介绍如何将应用发布到 Apple TV App Store。 它介绍如何配置、 预配、 生成和提交使用 Xamarin 生成的 tvOS 应用程序。
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b941bcc8588e7fb0377430cca2829ad72ecbc8c6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416598"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>发布到 Apple TV App Store

按顺序将分发到所有 Apple TV 设备的应用程序，Apple 要求应用以通过发布*Apple TV App Store*，使应用商店 tvOS 应用的一站式购物位置。 多种类型的应用的开发人员可以依赖此单点分发的巨大成功采用大写形式。 Apple TV App Store 是一种统包解决方案，提供应用程序开发人员分发和支付系统。

提交到 Apple TV App Store 的应用程序的过程包括：

1. 创建应用 ID，选择“权利”。
2. 创建分发预配配置文件。
3. 使用此配置文件来构建您的应用程序。
4. 通过将应用提交*iTunes Connect*。


在本文中，我们将介绍预配、 生成和提交有关 Apple TV App Store 分发应用程序所需的所有步骤。

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>提交应用程序前的准备

提交到 Apple TV App Store 发布应用程序后，会经历一个审查过程，确保其符合 Apple 的质量和内容的指导原则 Apple 的。 如果应用程序不符合这些指南，Apple 将拒绝此应用程序，此时需要处理 Apple 提出的不符合项，然后重新提交。
因此，如果熟悉这些指南并尽力使应用程序与其相符，应用程序通过 Apple 审核的几率较大。 Apple 相关指南目前[应用商店查看准则](https://developer.apple.com/appstore/resources/approval/guidelines.html)并[新建 Apple TV 的应用程序提交过您准备](https://developer.apple.com/tvos/submit/)。

提交应用时需要注意几点：

1. 请确保应用程序的说明与匹配应用中包含的功能。
2. 测试应用不会在正常使用时崩溃。 这包括支持的所有 Apple TV 设备上的使用情况。


Apple 还会保留 Apple TV App Store 提交提示的列表。 可在[在 App Store 上分发](https://developer.apple.com/appstore/resources/submission/tips.html)中阅读这些列表。

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中配置应用程序

[在 iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)是一套基于 web 工具，可在管理位于 Apple TV App Store 上的 tvOS 应用等。 Xamarin.tvOS 应用将需要进行恰当安装并配置在 iTunes Connect 中，然后可以提交到 Apple 进行审核并从根本上讲，付费或为 Apple TV App Store 中的免费应用发布。

请执行以下操作：

1. 验证 iTunes Connect 的**协议、税款和银行**部分中是否具备恰当的最新协议，以便发布免费或付费的 iOS 应用程序。
2. 创建一个新**iTunes Connect 记录**应用程序，并指定其**显示名称**（如 Apple TV App Store 中所示）。
3. 选择**售价**或指定免费发布应用程序。
4. 提供**App Store 图标**（大图标） 和你在操作中，它支持在 Apple TV 设备上的应用程序的屏幕截图。 请参阅我们[使用的图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)更多详细信息的指南。
5. 提供简洁**说明**的应用程序包括其功能和对最终用户的好处。
6. 提供**类别**，**子类别**，并**关键字**以帮助用户在 Apple TV App Store 中找到你的应用。
7. 提供 Apple 要求的你的网站的**联系人**和**支持人员** URL。
8. 将应用程序的设置**评级**，后者由 Apple TV App Store 上的家长控制。
9. 配置可选的 App Store 技术，如 **Game Center** 和**应用内购买**。

有关更多详细信息，请参阅我们[iTunes Connect 中配置 tvOS 应用](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)文档。

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>准备 App Store 分发

若要将应用发布到 Apple TV App Store，首先需要生成进行分发，这涉及到许多步骤。 以下部分介绍了有关发布准备 Xamarin.tvOS 应用，以便它可以生成并将其提交到 Apple TV App Store 进行审核和发布所需的所有内容。

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>对应用程序服务进行预配

Apple 提供一系列特殊的应用程序服务，也称为权利时为其创建的唯一 ID 可以为 tvOS 应用激活。 无论使用自定义权利，仍需要在 Apple TV App Store 上发布之前创建 Xamarin.tvOS 应用的唯一 ID。

使用 Apple 的基于 Web 的 iOS 预配门户创建应用 ID 和（可选）选择权利，包括以下步骤：

1. 选择**预配** > **开发**。
2. 单击“+”按钮，并为新的应用程序提供“名称”和“捆绑 ID”。
3. 滚动到屏幕的底部，选择任意**应用服务**Xamarin.tvOS 应用将需要的。
4. 单击“继续”按钮，并按照屏幕上的说明创建新的应用程序 ID。

除了选择和配置所需的应用程序服务定义你的应用 ID 时，您还需要通过编辑 Xamarin.tvOS 项目中配置应用程序 ID 和权利`Info.plist`和`Entitlements.plist`文件。

执行以下操作在 Visual Studio for Mac:

1. 在“解决方案资源管理器”中，双击 `Info.plist` 文件，将其打开进行编辑。
2. 在中**tvOS 应用程序目标**部分中，填写你的应用程序的名称并输入**捆绑包标识符**创建时定义应用程序 id。
3. 保存对 `Info.plist` 文件所做的更改。
4. 在“解决方案资源管理器”中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
5. 选择并配置所需的 Xamarin.tvOS 应用，使其匹配时定义应用程序 id。 执行上面的设置权利
6. 保存对 `Entitlements.plist` 文件所做的更改。

有关详细说明，请参阅[为应用程序服务进行预配](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文档。 本文档针对 iOS 编写的而使用相同的步骤 Xamarin.tvOS 应用预配。

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>设置应用程序图标、 启动图像和顶架图像

对于可被包含在 Apple TV App Store 中接受 Apple 的 tvOS 应用程序，它需要正确的图标、 启动和所有它将其运行的 Apple TV 设备 Top Shelf 图像。 你将需要添加将编译到所需的图像资产`Assets.car`文件，并包含在 Xamarin.tvOS 应用的捆绑包，然后将其上载到 iTunes Connect。

有关详细说明，请参阅我们[使用的图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)文档。

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>创建并安装分发配置文件

使用 tvOS*预配配置文件*以控制可以部署特定应用程序生成的方式。 这些文件包含有关用于应用签名的证书、应用程序 ID 和应用安装位置的信息。 对于开发和临时分发，预配配置文件还包括允许部署应用的设备列表。 但是，对于 Apple TV App Store 分发，唯一的证书和应用程序 ID 信息是包括在内，因为公开分发的唯一机制是通过 Apple TV App Store。

使用 Apple 的基于 Web 的 iOS 预配门户的预配包含以下步骤：

1.  选择“预配” > “分发”。
2.  单击 **+** 按钮，然后选择你想要为创建的分发配置文件的类型**Apple TV App Store**。
3.  从要为其创建分发配置文件的下拉列表中，选择“应用程序 ID”。
4.  选择所需应用程序进行签名的证书。
5.  为新的**分发配置文件**输入**名称**，并生成配置文件。
6.  刷新在 Xcode 中可用的配置文件的列表。
7.  选择分发预配配置文件在 Visual Studio for**应用商店**_生成配置_。

有关详细说明，请参阅[创建分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile)和[在 Xamarin.iOS 项目中选择分发配置文件](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。 同样，这两个文档是特定于 iOS 的但为 tvOS 应用使用相同的方法。


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>设置应用程序的生成配置

默认情况下，当创建新的 Xamarin.tvOS 应用，_生成配置_自动创建两个**调试**并**发行**部署。 向 Apple 您将提交你的应用程序在最终生成前，，有一些你将需要对基类进行的修改**版本**配置。

请执行以下操作：

1. 右键单击**项目名称**中**解决方案资源管理器**并选择**选项**打开进行编辑。
2. 如果你正面向特定版本的 tvOS 下, 将其选中**tvOS 生成** > **iOS SDK 版本**。 为 tvOS 支持预览版本，请将此值设置为**默认**。
3. 链接将减小总体应用程序的可分发通过去除未使用的方法、 属性类，等等，在大多数情况下，应保留为默认值**仅链接 framework SDK**。 在某些情况下，例如使用某些特定第三方库时，可能会强制将此值设置为“不链接”，防止删除需要的元素。
4. 寄送 Xamarin.tvOS 应用，你将需要使用 LLVM 优化编译器。 絋粄**使用 LLVM 优化编译器**下选中框**发行**配置。
5. Apple 还所需的 tvOS 应用使用 bitcode。 下再次**发行**配置中，添加`--bitcode=asmonly`到**其他 mtouch 参数**框。
6. **适用于 iOS 优化 PNG 图像文件**应选中复选框，因为这样做有助于进一步减少应用程序的可交付结果大小。
7. *不*应启用调试，因为这将为生成增加不必要的大小。


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>生成并提交可分发文件

与 Xamarin.tvOS 应用正确配置，你现在可以进行的最后一个分发生成的将提交到 Apple 进行审核和发布。

#### <a name="build-your-archive"></a>生成存档

1. 在 Visual Studio for Mac 中选择“发布 | 设备”配置：

    ![](app-store-publishing-images/buildxs01new.png "选择发布配置")
2. 在“生成”菜单中，选择“存档以供发布”：

    [![](app-store-publishing-images/buildxs02new.png "选择“存档以发布”")](app-store-publishing-images/buildxs02new.png#lightbox)
3. 存档创建完成后，将显示“存档”视图：

    [![](app-store-publishing-images/buildxs03new.png "存档视图")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>签名和分发应用

每次生成用于存档的应用程序时，都会自动打开“存档视图”，显示所有已存档的项目（按解决方案分组）。 默认情况下，此视图只显示当前打开的解决方案。 若要查看拥有存档的所有解决方案，请单击“显示所有存档”选项。

建议保留部署到客户（App Store 或企业部署）的存档，以便可在稍后的日期表示生成的任何调试信息。

对应用进行签名并准备分发：

1. 选择**签名并分发...**，如下所示：

    [![](app-store-publishing-images/buildxs04new.png "选择 theSign 和分发...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. 这将打开发布向导。 选择“App Store”分发频道以创建包，再打开应用加载程序：

    [![](app-store-publishing-images/distribute01.png "选择 App Store 分发通道")](app-store-publishing-images/distribute01.png#lightbox)
3. 在预配配置文件屏幕上，选择签名标识和对应的预配配置文件，或使用其他标识重新签名：

    [![](app-store-publishing-images/distribute02.png "选择签名标识和对应的预配配置文件")](app-store-publishing-images/distribute02.png#lightbox)
4. 验证包的详细信息，再单击“发布”以保存 `.ipa` 包：

    [![](app-store-publishing-images/distribute03.png "验证包的详细信息")](app-store-publishing-images/distribute03.png#lightbox)
5. 保存 `.ipa` 后，应用即可通过应用加载程序上传到 iTunes Connect：

    [![](app-store-publishing-images/distribute04.png "上传到 iTunes Connect 通过应用程序加载程序")](app-store-publishing-images/distribute04.png#lightbox)

创建分发生成并将其存档后，现可将应用程序提交到 iTunes Connect。

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>将应用提交到 Apple

分发生成完成后，即可将 iOS 应用程序提交到 Apple 以供审核，然后在 App Store 上发布。


Visual Studio for Mac 中的存档工作流应用程序加载程序，会自动打开后已保存`.ipa`:

2. 选择“提交应用”，单击“选择”按钮：

    [![](app-store-publishing-images/publishvs01.png "选择“提交应用”")](app-store-publishing-images/publishvs01.png#lightbox)

3. 选择前面创建的 zip 或 IPA 文件，单击“确定”按钮。
4. 应用加载程序将验证文件：

    [![](app-store-publishing-images/publishvs02.png "应用程序加载程序验证屏幕")](app-store-publishing-images/publishvs02.png#lightbox)
5. 单击“下一步”按钮，将按照 App Store 的要求验证应用：

    [![](app-store-publishing-images/publishvs03.png "正在验证对应用商店应用程序")](app-store-publishing-images/publishvs03.png#lightbox)
6. 单击“发送”按钮，将应用程序发送到 Apple 以供审核。
7. 文件成功上传后，应用程序加载程序将通知你。

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>iTunes Connect 状态

如果您重新登录到 iTunes Connect，并从可用应用的列表中选择你的应用，iTunes Connect 中的状态现在应显示它是**等待审核**(可能会暂时读取**收到上传**而将其进行处理）：

[![](app-store-publishing-images/image21.png "在 iTunes 中的状态连接显示等待评审")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑难解答

如果您遇到的问题将 Xamarin.tvOS 应用提交到 Apple TV App Store，请参阅我们[故障排除](~/ios/tvos/troubleshooting.md)指南。 它包含可能会遇到的一些已知的问题和如何解决这些 Xamarin.tvOS 中。

<a name="Summary" />

## <a name="summary"></a>总结

本文提供配置、 生成和提交用于 Apple TV App Store 发布的应用的分步指南。 首先，介绍创建并安装分发配置文件所需的步骤。 然后，介绍如何使用 Visual Studio for Mac 创建分发生成。 最后，它演示了如何使用 iTunes Connect 和 Xcode 存档工具提交到 Apple TV App Store 的应用程序。


## <a name="related-links"></a>相关链接

- [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)
- [为新的 Apple TV 准备你的应用程序提交](https://developer.apple.com/tvos/submit/)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常见的应用拒绝](https://developer.apple.com/app-store/review/rejections/)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
