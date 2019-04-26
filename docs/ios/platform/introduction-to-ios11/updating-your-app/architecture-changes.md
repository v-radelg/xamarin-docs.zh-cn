---
title: IOS 11 中的体系结构更改
description: 本文档介绍了不推荐使用的 iOS 11 中的 32 位应用程序。 介绍了如何更新应用程序面向 64 位体系结构。
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: b7d1df6ed2a8bd480025681ebcbe48acd7592564
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169329"
---
# <a name="architecture-changes-in-ios-11"></a>IOS 11 中的体系结构更改

应注意的 ios 11 的最大变化之一就是不推荐使用的应用程序，如中所述的 32 位支持[Apple 的](https://developer.apple.com/news/?id=06282017b)按释放。 所有新应用程序和更新到现有应用程序必须支持 64 位。 32 位应用程序**将不启动**iOS 11 中。

若要更新你的应用在 Visual Studio 中的 Mac，请使用以下步骤：

1. 右键单击项目名称以打开**项目选项**。
2. 浏览到**iOS 生成**选项卡。
3. 设置**支持的体系结构**下拉列表**x86_64**有关**调试 | iPhoneSimulator**并**版本 | iPhoneSimulator**:

    ![更改模拟器体系结构下拉列表](architecture-changes-images/image1.png)

4. 对于 iOS 设备，将配置更改为**调试 | iPhone**并设置**支持的体系结构**下拉列表**ARM64**:

    ![更改设备体系结构下拉列表](architecture-changes-images/image2.png)

5. 将配置更改为**发布 | iPhone**并设置**支持的体系结构**下拉列表**ARM64**。

32 位和 64 位体系结构的详细信息，请参阅[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#ios)指南。

## <a name="related-links"></a>相关链接

- [What's New iOS 11 (Apple) 中](https://developer.apple.com/ios/)
- [更新的应用商店产品页 (Apple)](https://developer.apple.com/app-store/product-page/)
- [将应用更新适用于 iOS 11 (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
