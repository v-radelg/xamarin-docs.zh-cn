---
title: tvOS 应用分发概述
description: 本文档概述了可用于 Xamarin.tvOS 应用的分发技巧，并充当有关该主题的更详细文档的指针。
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0a40d50d02008439e81d5db19bcda0647203e2da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414400"
---
# <a name="tvos-app-distribution-overview"></a>tvOS 应用分发概述

_本文档概述了可用于 Xamarin.tvOS 应用的分发技巧，并充当有关该主题的更详细文档的指针。_


后 Xamarin.tvOS 应用开发完毕，软件开发生命周期的下一步是将其分发给用户，如下面的关系图的突出显示部分中所示：


[![软件开发生命周期概述](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple 提供了以下的方式来分发 tvOS 应用支持的 Xamarin.tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**In-House（企业）**](#In-House-Distribution) 
2. [**Ad Hoc**](#Ad_Hoc_Distribution) 

所有这些情况都要求使用相应的配置文件预配应用程序。 配置文件是一种文件，其中包含了代码签名信息，以及应用程序标识和预期分发机制。 对于非 App Store 分发，该文件还包含可部署应用的设备的相关信息。

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Apple TV App Store 分发

这是 tvOS 应用程序将分发给使用者在 Apple TV 设备上的主要方式。 所有提交到 Apple TV App Store 的应用需要 Apple 审批。

应用通过名为 iTunes Connect 的门户提交到 App Store。 请参阅我们[iTunes Connect 中配置你的应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南以获取有关以下主题的信息：

- 管理协议、 税款和银行。
- 创建 iTunes Connect 记录。
- 管理应用视频和屏幕快照。
- 管理应用程序名称、 说明、 最新内容、 关键字和 Url。
- 维护常规应用信息。
- 维护 Game Center 信息。
- 维护应用审核信息。
- 维护定价信息。
- 维护应用内购买信息。
- 查看应用程序评论。

而不是专门为 tvOS 编写，本文档介绍如何设置和使用此门户来准备应用以便在 Apple TV App Store 中发布。 许多相同的步骤所需是否设置了 iOS 或 tvOS 应用。

完成所有上面列出的步骤操作后，请参阅我们[iTunes Connect 中配置 tvOS 应用](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)添加需要的 tvOS 应用程序特定信息。

务必注意，只有属于 **Apple 开发人员计划**的开发人员有权访问 iTunes Connect。 **Apple 开发人员企业计划**成员没有访问权限。

如果您遇到的问题将 Xamarin.tvOS 应用提交到 Apple TV App Store，请参阅我们[故障排除](~/ios/tvos/troubleshooting.md)指南。 它包含可能会遇到的一些已知的问题和如何解决这些 Xamarin.tvOS 中。

有关详细信息，请访问[发布到 Apple TV App Store](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md)指南。

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>In-House 分发

In-House 分发（有时称为企业分发）允许 **Apple 开发人员企业计划**成员在内部向同一组织内的其他成员分发应用。 In-House 分发的优势在于无需 App Store 审核，且没有可安装应用程序的设备数量限制。 但是，值得注意的是，**Apple 开发人员企业计划**成员**没有**权限访问 iTunes Connect，因此许可证持有人负责分发应用。

有关设置以及如何 In-House 分发你的应用程序的详细信息，请参阅[In-House 分发指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 本文档是特定于 iOS 的但同样的技术用于 tvOS 应用。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Ad Hoc 分发

Xamarin.tvOS 应用可以进行用户测试通过 ad hoc 分发可**Apple Developer Program**，并**Apple Developer Enterprise Program**，并允许最多 100 个 Apple TV 设备若要进行测试。 ITunes Connect 不是一个选项时，ad hoc 分发的最佳用例是贵公司内的分发。

有关设置以及如何 In-House 分发您的应用程序的详细信息，请参阅[Ad Hoc 分发指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同样，本文档是特定于 iOS 的但同样的技术用于 tvOS 应用。

<a name="Summary" />

## <a name="summary"></a>总结

本文提供可用于 Xamarin.tvOS 应用分发机制的简要概述。 它引入了 Apple TV App Store 中，Ad Hoc 和 In-house 部署，并提供更多详细信息的链接。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
