---
title: 在 iTunes Connect 中配置 tvOS 应用
description: 本文提供了 iOS 配置你的应用对 tvOS 的特定配置在 iTunes Connect 中的补充指南。
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f4ef00cfe990de2d5afd461d7a110d32bc4a236
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61413187"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>在 iTunes Connect 中配置 tvOS 应用

_本文提供了 iOS 配置你的应用对 tvOS 的特定配置在 iTunes Connect 中的补充指南。_


除了配置和设置，将需要按照 iOS 进行[iTunes Connect 中配置你的应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南中，本文档介绍如何将所需释放 Xamarin.tvOS 的具体配置在 Apple TV App Store 中的应用程序。

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>添加 tvOS 发行版本

是否要创建新的应用程序要发布的 Apple TV App Store，或将 Apple TV 支持添加到现有的 iOS 应用程序，你将需要创建 iTunes Connect 记录并将其配置使用以下 iOS 指导特定于：

- [创建 iTunes Connect 记录](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [管理应用视频和屏幕快照](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [管理名称、说明、新增功能、关键字和 URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [维护的常规信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

（可选） 你可能还需要：

- [维护 Game Center 信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [维护应用内购买信息](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

所有已完成上述步骤中，打开应用的 iTunes Connect 记录并选择要添加使用左侧边栏的 tvOS 支持：

[![](itunes-connect-images/connect01.png "添加使用左侧边栏的 tvOS 支持")](itunes-connect-images/connect01.png#lightbox)

TvOS 的特定信息屏幕然后将可用于给定 iTunes Connect 记录：

[![](itunes-connect-images/connect02.png "TvOS 的特定信息屏幕")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS 版本信息

从左侧边栏中，选择**1.0 准备提交**tvOS 应用部分下：

[![](itunes-connect-images/connect03.png "tvOS 版本信息")](itunes-connect-images/connect03.png#lightbox)

在此屏幕上提供以下信息：

- 所需的屏幕截图、 说明、 关键字和 Url。
- 如版本号、 版权和年龄分级的常规应用信息。
- 可选的应用内购买。
- 可选 Game Center 排行榜和成就的支持。
- 所需应用审核信息，如联系人、 演示帐户和说明。

一旦您输入所需的信息，请单击**保存**要保存所做的更改的屏幕的右上角的按钮：

[![](itunes-connect-images/connect04.png "tvOS 准备好进行提交的版本信息")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>正在准备提交以供审阅

终于准备好将 Xamarin.tvOS 应用提交到 Apple TV App Store 审阅后，返回到应用的 iTunes Connect 记录，然后单击**提交以供审阅**屏幕的右上角的按钮：

[![](itunes-connect-images/connect05.png "提交以供审阅")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>总结

本文提供在 iTunes Connect 中所需 tvOS 应用发布到 Apple TV App Store 的 tvOS 特定设置的概述。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
