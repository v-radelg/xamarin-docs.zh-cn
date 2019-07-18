---
title: 在 iTunes Connect 中配置应用
description: 本文介绍在 iTunes Connect 中设置和维护 Xamarin.iOS 应用程序使其可在 App Store 上分发所需的步骤。
ms.prod: xamarin
ms.assetid: 74587317-4b15-4904-9582-dcd914827cbc
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 24cf255d5cf9269a7d0d485ad8a88abe6842b07a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865610"
---
# <a name="configuring-an-app-in-itunes-connect"></a>在 iTunes Connect 中配置应用

> [!IMPORTANT]
> Apple [已表明](https://developer.apple.com/ios/submit/)，从 2019 年 3 月开始，提交到 App Store 的所有应用和更新必须是使用 iOS 12.1 SDK 或更高版本构建的，包含在 Xcode 10.1 或更高版本中。
> 应用还应支持 iPhone XS 和 12.9 英寸 iPad Pro 屏幕大小。

iTunes Connect 是一系列基于 Web 的工具，它可在 App Store 上管理 iOS 应用程序等。 Xamarin.iOS 应用程序需要在 iTunes Connect 中进行恰当安装并配置，然后才能提交到 Apple 进行审核并最终在 App Store 作为付费或免费应用发布。

iTunes Connect 可以用于以下任务：

- 设置应用程序的名称（在 App Store 中显示）。
- 提供在支持的 iOS 设备上运行应用程序的屏幕快照或视频。
- 提供应用程序的简明说明，包括其功能和对最终用户的好处。
- 提供类别和子类别，有助于用户在 App Store 中查找应用。
- 提供可进一步帮助用户查找应用的关键字。
- 提供（Apple 要求的）你的网站的联系人和支持人员 URL。
- 设置应用程序的评级，用于在 App Store 上通知家长控制。
- 选择售价或指定发布免费应用程序。
- 配置可选的 App Store 技术，如 Game Center 和应用内购买。

此外，应用还应拥有具有吸引力的高分辨率图稿，以供 Apple 在 App Store 中使用此图稿。 有关详细信息，请参阅 Apple 的 [iTunes Connect 开发人员指南](https://developer.apple.com/support/itunes-connect/)。

## <a name="managing-agreements-tax-and-banking"></a>管理协议、税款和银行

iTunes Connect 的**协议、税款和银行**部分提供有关 iTunes 开发人员付款和税款代扣所需的财务信息，以及跟踪你与 Apple 签订的任何协议的状态。 在 App Store 上发布（免费或付费）iOS 应用程序之前，需具备适当的协议，并且同意对现有协议所做的任何修改。

[![](itunesconnect-images/agreement01.png "管理协议、税款和银行")](itunesconnect-images/agreement01.png#lightbox)

从这里，可以：

- 提供**团队代理**并定义 iTunes Connect 帐户的其他用户角色，例如**管理员**或**财务**。
- 注册并维护**协定**，协定允许组织在 App Store 中分发应用程序。
- 指定**法人**名称（即 App Store 中的销售商名称），此名称用于匹配与组织关联的协定、银行信息和税款信息。
- 若要通过 App Store 销售应用程序，请提供**银行**和**税款**信息。

同样，_必须_正确设置、使用此信息并保持最新状态，才能将要审核和发布的 iOS 应用程序提交到 iTunes Connect。 有关详细信息，请参阅 Apple 的 [Managing Agreements, Tax and Banking](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/ManagingContractsandBanking.html#//apple_ref/doc/uid/TP40011225-CH21-SW1)（管理协议、税款和银行）文档。

<a name="creating" />

## <a name="creating-an-itunes-connect-record"></a>创建 iTunes Connect 记录

需要先在 iTunes Connect 中创建应用程序的记录，然后才可将 Xamarin.iOS 应用程序上传到 iTunes Connect，通过 App Store 进行分发。 此记录包括应用程序的所有信息（这些信息将根据需要以尽可能多的语言在 App Store 中显示）和通过分发流程管理应用所需的所有信息。 此外，此记录还将用于配置 App Store 技术，如 iAd App Network 或 Game Center。

若要将 iOS 应用程序添加到 iTunes Connect，你需要是“团队代理”或具有“管理员”或“技术”角色的用户    。

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  ：

    [![](itunesconnect-images/add01.png "单击“我的应用”")](itunesconnect-images/add01.png#lightbox)
2. 单击左上角的“+”  ，再选择“新建 iOS 应用”  ：

    [![](itunesconnect-images/add02.png "添加“新建 iOS 应用”")](itunesconnect-images/add02.png#lightbox)
3. iTunes Connect 将显示“新建 iOS 应用”  对话框：

    [![](itunesconnect-images/add03.png "“新建 iOS 应用”对话框")](itunesconnect-images/add03.png#lightbox)
4. 输入应用程序在 App Store 中显示的**名称**和**版本号**。
5. 选择“主要语言”  。
6. 输入 **SKU** 号，它是用于跟踪应用程序的唯一固定标识符。 它不会对最终用户显示，应用创建后，_无法_对它进行更改。
7. 预配应用程序时，为在开发人员中心中创建的应用程序选择**捆绑 ID**。 在 Visual Studio for Mac 或 Visual Studio 中对分发捆绑进行签名时，需要使用此相同的捆绑 ID。 有关详细信息，请参阅[创建分发配置文件](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)和[在 Xamarin.iOS 项目中选择分发配置文件](~/ios/get-started/installation/device-provisioning/index.md)文档。
8. 单击“创建”  按钮，为应用程序生成新的 iTunes Connect 记录。

新应用程序将在 iTunes Connect 中创建，你可填写必需信息，例如说明、定价、类别、评级等：

[![](itunesconnect-images/add04.png "新的应用程序将在 iTunes Connect 中创建")](itunesconnect-images/add04.png#lightbox)

<a name="managing" />

## <a name="managing-app-videos-and-screenshots"></a>管理应用视频和屏幕快照

若要在 App Store 中成功营销 iOS 应用程序，最重要的元素之一是提供一套出色的屏幕快照或视频预览。 使用运行中的应用程序的实际视图，突出显示用户交互并展示其独特功能。 使用应用程序预览视频，为用户提供使用应用程序的大致感受。

对于拍摄屏幕快照，Apple 提供了以下建议：

- 优化屏幕快照，使其在应用程序支持的 iOS 设备上提供最佳展示，并确保内容清晰。
- 请勿给 iOS 设备图像中的屏幕快照配框。
- 使用完整屏幕显示应用程序的实际视图，不要在屏幕快照周围添加图形或边框。
- 务必记住从屏幕快照中删除状态栏，iTunes Connect 希望屏幕快照的尺寸排除该区域。
- 尽可能使用真实、高分辨率的 Retina iOS 硬件（而不是在 iOS 模拟器中）拍摄屏幕快照。
- 如果没有可用的应用预览视频，则会将第一张屏幕快照作为搜索结果，在 iPhone 和 iPad 上的 App Store 中显示，因此，请将最佳屏幕快照放在第一位。
- 使用所有五张屏幕快照介绍应用程序，同时突出显示应用程序的精彩瞬间。

Apple 要求提供应用程序支持的每种屏幕大小和分辨率的屏幕快照和视频。 此外，基于支持的方向，应提供纵向和横向版本。

目前要求使用以下屏幕大小和分辨率：

[!include[](~/ios/includes/table-itunesconnect.md)]

### <a name="editing-screenshots-in-itunes-connect"></a>在 iTunes Connect 中编辑屏幕快照

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  。
2. 单击应用程序的**图标**。
3. 选择“版本”  选项卡。
4. 滚动到“屏幕快照”  部分。
5. 选择“图像大小”  ，再拖入相应图像（每个屏幕大小最多 5 张）：

    [![](itunesconnect-images/screenshot01.png "选择“图像大小”并拖入所需图像")](itunesconnect-images/screenshot01.png#lightbox)
6. 为所有所需的屏幕大小重复此操作。
7. 单击屏幕顶部的“保存”  按钮，保存更改。

> [!NOTE]
> 注意:如果屏幕截图或应用预览视频与应用程序中的当前功能不匹配，Apple 将拒绝你的提交。

<a name="metadata" />

## <a name="managing-name-description-whats-new-keywords-and-urls"></a>管理名称、说明、新增功能、关键字和 URL

此“iTunes Connect 应用程序记录”部分介绍应用程序的本地化信息、功能、对新版本所做的修改、用于搜索的关键字以及 iAd 支持和任何支持 URL。

### <a name="app-name"></a>应用程序名称

选择可反映应用程序功能的描述性应用程序名称。 尽可能使其简洁明了。 应用程序的名称十分重要，它可影响用户搜索和发现的方式，因此请使用简单易记的名称。 请特别注意在 iOS 设备（iPad、iPhone 和 iPod touch）上查看时，名称的显示方式。

Apple 建议在选择应用程序名称时遵照以下准则：

- 确保名称简短易记。
- 确保名称不违反第三方的版权或商标。
- 确保名称符合应用程序的功能。
- 在适当情况下，为外国市场提供本地化名称。

### <a name="description"></a>说明

应用程序及其功能的说明应简洁明了且信息丰富。 前几行最重要，这关系到用户的第一印象，以及是否能吸引用户。 描述应用程序的特点和相较于类似应用的优势。

Apple 建议在编写应用程序说明时遵照以下准则：

- 包括一个或两个简要的起始段落以及主要功能的简短项目符号列表。
- 在适当情况下，为外国市场提供本地化说明。
- 如果可能，仅在最后包括用户评论、称赞或推荐。
- 使用换行符和项目符号来增强可读性。
- 注意应用说明在每种设备的 App Store 中的显示方式，确保说明中最重要的句子容易被看到。

### <a name="whats-new"></a>新增功能

上传新版应用程序时，“此版本的新增功能”  字段将可用，请完整地仔细填写此字段。

Apple 建议在填写新增功能信息时遵照以下准则：

- 添加鼓励用户更新的消息。
- 按照重要性的顺序列出项目，指出更改内容和 bug 修复。
- 使用简单可信的语言（而不是技术术语）说明更改内容。

### <a name="keywords"></a>关键字

与应用程序功能相关的周密、策略性关键字有助于用户在 App Store 中搜索时轻松找到你的应用程序。 此外，如果应用程序提供 iAd 广告，在选择应用中目标广告时，iAd App Network 将使用关键字。

对于选择关键字，Apple 提供了以下建议：

- 不要使用竞争性的应用名称、公司或产品名称或者注册商标名称。
- 不要使用通用字词或术语。
- 避免使用不适当或令人反感的术语或无关的词，如名人的名字。
- 在适当情况下，请为外国市场提供本地化关键字。

### <a name="urls"></a>URL

Apple 要求开发人员提供其网站的链接，用于支持用户可能遇到的应用程序相关问题。 他们还要求提供应用程序隐私策略的链接（隐私策略同样必须托管在你的网站上）。

（可选）提供托管在你的网站上的营销信息的链接，此链接用于提供比 App Store 中更详细的应用程序相关信息。

### <a name="editing-name-description-whats-new-keywords-and-urls-in-itunes-connect"></a>在 iTunes Connect 中编辑“名称”、“说明”、“新增功能”、“关键字”和“URL”

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  。
2. 单击应用程序的**图标**。
3. 选择“版本”  选项卡。
4. 滚动到“名称”  部分。
5. 填写所有必填信息：

    [![](itunesconnect-images/name01.png "在 iTunes Connect 中编辑“名称”、“说明”、“新增功能”、“关键字”和“URL”")](itunesconnect-images/name01.png#lightbox)
6. 单击屏幕顶部的“保存”  按钮，保存更改。

> [!IMPORTANT]
> 注意:如果名称、说明、新增功能、关键字或 URL 与应用程序中的当前功能不匹配，Apple 将拒绝你的提交。

<a name="general" />

## <a name="maintaining-general-app-information"></a>维护常规应用信息

“iTunes Connect 应用程序记录”这一部分提供应用程序的唯一 ID（由 Apple 分配）、应用程序所属的类别、评级、版权以及发布此应用程序的公司相关信息。

### <a name="app-icon"></a>应用图标

> [!IMPORTANT]
>  无法再通过 iTunes Connect 提交应用图标。 它们必须通过项目的 Assets.xcassets  文件中的 AppIcon  映像集提交。 有关详细信息，请参阅[应用商店图标](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

应用图标是面向用户的应用程序的门脸，必须易记且在小尺寸下也能正常显示。 易记的图标是干净、简单且具有高度辨识度的。

Apple 建议在设计应用程序图标时遵照以下准则：

- 确保图标适合应用程序。
- 创建与应用程序的设计一致的简单图标。
- 图标中避免使用文字。
- 从整体思考：单个应用图标用于所有商店区域。

App Store 中显示的应用图标必须是 1024 x 1024 像素的图像。

有关详细信息，请参阅 Apple 的 [iOS Human Interface Guidelines](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556)（iOS 人机接口指南）和 [General App Information](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Appendices/Properties.html#//apple_ref/doc/uid/TP40011225-CH26-SW7)（常规应用信息）文档中“大型应用图标”部分的说明

### <a name="app-id"></a>应用 ID

这是在创建 iTunes Connect 记录时，Apple 分配给应用程序的唯一标识号。 调用 Apple 提供的基于 Web 的界面（包括网站中的 App Store 信息）时，可使用此标识号。

### <a name="version-number"></a>版本号

这是应用程序的当前活动版本，此版本在 App Store 中向用户显示。

### <a name="category-and-sub-category"></a>类别和子类别

应用程序的可发现性的一个重要方面是它在 App Store 中所属的类别。 通过类别，用户可浏览应用集合并找到其感兴趣的应用。 通过 iTunes Connect，可在定义应用程序时分配最多两个不同类别。 确保认真选择可最佳描述应用程序主要功能的类别。

### <a name="ratings"></a>评级

App Store 中的所有应用程序都必须评级。 此评级基于应用程序的类型和内容，用于通知家长控制和限制儿童访问。 定义应用程序时，iTunes Connect 将提供一个内容说明列表，用于确定应用程序中内容出现的频率。 这些选择会转换为评级，在 App Store 中显示。

创建适用于儿童的应用程序时，App Store 有一个针对 11 岁及以下儿童的特殊类别。 即使应用程序并非特别针对儿童，这也有助于客户根据提供的适当内容评级来做出正确选择。

> [!IMPORTANT]
> 注意:如果发现淫秽、色情、攻击或诽谤性内容，Apple 将拒绝应用程序提交。

### <a name="copyright-and-company-information"></a>版权和公司信息

Apple 允许提供应用程序的版权信息，并要求提供发布应用程序的公司的相关信息，例如，公司地址和联系信息（发布到韩国 App Store 的应用程序必须提供）。 根据需要，此信息将在 App Store 中显示。

### <a name="editing-general-app-information-in-itunes-connect"></a>在 iTunes Connect 中编辑常规应用信息

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  。
2. 单击应用程序的**图标**。
3. 选择“版本”  选项卡。
4. 滚动到“常规应用信息”  部分。
5. 填写所有必填信息：

    [![](itunesconnect-images/general01.png "在 iTunes Connect 中编辑“常规应用信息”")](itunesconnect-images/general01.png#lightbox)
6. 单击“评级”  旁边的“编辑”  按钮，设置评级信息：

    [![](itunesconnect-images/general02.png "编辑评级")](itunesconnect-images/general02.png#lightbox)
7. 单击屏幕顶部的“保存”  按钮，保存更改。

> [!NOTE]
> 注意:如果类别或评级与应用程序中的当前功能不匹配，Apple 将拒绝你的提交。

<a name="game-center" />

## <a name="maintaining-game-center-information"></a>维护 Game Center 信息

对于支持 Apple Game Center 的 iOS 游戏应用程序，如果应用程序是跨网络连接的多人游戏，可为用户提供类似玩家排名和游戏中成绩的信息。

### <a name="editing-game-center-information-in-itunes-connect"></a>在 iTunes Connect 中编辑 Game Center 信息

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  。
2. 单击应用程序的**图标**。
3. 选择“版本”  选项卡。
4. 滚动到“Game Center”  部分。
5. 将“Game Center”  部分旁的开关切换到“开”  的位置。
6. 填写所有必填信息：

    [![](itunesconnect-images/gamecenter01.png "在 iTunes Connect 中编辑 Game Center 信息")](itunesconnect-images/gamecenter01.png#lightbox)
7. 单击屏幕顶部的“保存”  按钮，保存更改。

使用“Game Center”  选项卡激活 Game Center，并维护此应用的任何可用**排名**或**成绩**：

[![](itunesconnect-images/gamecenter02.png "激活 Game Center")](itunesconnect-images/gamecenter02.png#lightbox)

[![](itunesconnect-images/gamecenter03.png "维护此应用程序的所有可用“排行榜”或“成绩”")](itunesconnect-images/gamecenter03.png#lightbox)

## <a name="maintaining-app-review-information"></a>维护应用审核信息

本部分提供将审核应用程序的 Apple 人员的必需信息，如联系信息（如果技术人员有任何问题）、可能需要的任何演示帐户以及可帮助测试员成功完成应用审核的任何注释。

### <a name="editing-app-review-information-in-itunes-connect"></a>在 iTunes Connect 中编辑应用审核信息

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  。
2. 单击应用程序的**图标**。
3. 选择“版本”  选项卡。
4. 滚动到“应用审核信息”  部分。
5. 填写所有必填信息：

    [![](itunesconnect-images/review01.png "在 iTunes Connect 中编辑应用审核信息")](itunesconnect-images/review01.png#lightbox)
6. 选择希望如何将成功审核的应用发布到 App Store：

    [![](itunesconnect-images/review02.png "在 iTunes Connect 中编辑发布信息")](itunesconnect-images/review02.png#lightbox)
7. 单击屏幕顶部的“保存”  按钮，保存更改。


## <a name="maintaining-pricing-information"></a>维护定价信息

如果计划发布付费应用程序，需要通过选择 Apple 的一个可用定价层设置售价和给定价格的生效日期。 例如，截至编写本文时，**第 1 层**定价如下所示：

[![](itunesconnect-images/price01.png "维护定价信息")](itunesconnect-images/price01.png#lightbox)

### <a name="educational-discount"></a>教育优惠

如果希望以优惠价向一次购买多个副本的教育机构提供应用程序，请选中此框。 可在最新的**付费应用程序协议**中找到折扣的详细信息，必须签署此协议才能向教育客户提供此应用程序。

### <a name="custom-business-to-business-application"></a>自定义企业到企业应用程序

设置为“自定义企业到企业应用程序”  的应用程序仅面向 iTunes Connect 中指定的**批量购买计划**客户，并且仅在适用区域中可用（例如，美国的批量购买计划客户必须使用美国的 App Store 批量购买计划商业版）。

自定义企业到企业应用程序不面向教育机构或普通 App Store 客户。 若要深入了解 *App Store 批量购买计划商业版*，请访问 Apple 的[常见问题解答](http://vpp.itunes.apple.com/faq)页面。 若要深入了解客户如何注册**批量购买计划**，请访问 Apple 的[部署计划](http://enroll.vpp.itunes.apple.com)页面。

### <a name="editing-pricing-information-in-itunes-connect"></a>在 iTunes Connect 中编辑定价信息

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中执行以下操作：

1. 单击“我的应用”  。
2. 单击应用程序的**图标**。
3. 选择“定价”  选项卡：

    [![](itunesconnect-images/price02.png "在 iTunes Connect 中编辑定价信息")](itunesconnect-images/price02.png#lightbox)
4. 选择“可用日期”  。
5. 从“价格层”  下拉列表中，选择所需的价格。
6. （可选）启用“教育折扣”  。
7. （可选）将应用程序定义为“自定义企业到企业应用程序”  。
8. 单击“保存”  按钮保存更改。

<a name="iap" />

## <a name="maintaining-in-app-purchase-information"></a>维护应用内购买信息

如果计划销售应用程序中的虚拟应用内产品（例如，新的游戏级别或应用程序功能），请使用此部分创建并维护这些购买项。

[![](itunesconnect-images/inapp01.png "维护应用内购买信息")](itunesconnect-images/inapp01.png#lightbox)

若要深入了解如何在 Xamarin.iOS 应用程序中使用应用内购买，请参阅[应用内购买](~/ios/platform/in-app-purchasing/index.md)文档。

## <a name="viewing-application-reviews"></a>查看应用程序评论

应用程序发布到 App Store 之后，购买或免费下载应用程序的用户可撰写应用的评论，并评定星级。 使用此部分查看这些评论。 例如:

[![](itunesconnect-images/reviews01.png "查看应用程序评论")](itunesconnect-images/reviews01.png#lightbox)

## <a name="summary"></a>总结

本文介绍如何使用 iTunes Connect 准备 Xamarin.iOS 应用程序，进而发布到 App Store，以及如何维护在 App Store 中显示的应用程序的所有信息。

## <a name="related-links"></a>相关链接

- [使用图像](~/ios/app-fundamentals/images-icons/index.md)
- [iOS 应用开发工作流指南：分发应用程序](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 审核指南](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [iTunes Connect 开发人员指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/About.html#//apple_ref/doc/uid/TP40011225-CH1-SW1)
