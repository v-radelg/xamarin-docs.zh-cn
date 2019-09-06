---
title: IOS 11 中的体系结构更改
description: 本文档介绍 iOS 11 中的32位应用的弃用情况。 本文介绍如何将应用程序更新为面向64位体系结构。
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 15cd6139cc83639146e6044d2b791d94ee30f2d9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286349"
---
# <a name="architecture-changes-in-ios-11"></a>IOS 11 中的体系结构更改

在 iOS 11 上，你应该注意的最重要的更改之一是弃用32位对应用的支持，如[Apple 的](https://developer.apple.com/news/?id=06282017b)新闻版本中所述。 所有新应用和现有应用的更新都必须支持64位。 32位应用**将不会**在 iOS 11 中启动。

若要在 Visual Studio for Mac 中更新应用，请使用以下步骤：

1. 右键单击项目名称，打开 "**项目选项**"。
2. 浏览到 " **IOS 生成**" 选项卡。
3. 为**Debug | iPhoneSimulator**和**Release | iPhoneSimulator**将**支持的体系结构**下拉箭头设置为**x86_64** ：

    ![更改模拟器体系结构下拉](architecture-changes-images/image1.png)

4. 对于 iOS 设备，请将配置更改为 "**调试 | iPhone** "，并将 "**支持的体系结构**" 下拉箭头设置为**ARM64**：

    !["更改设备体系结构" 下拉](architecture-changes-images/image2.png)

5. 将配置更改为 "**发布 | iPhone** "，并将 "**支持的体系结构**" 下拉箭头设置为**ARM64**。

有关32位和64位体系结构的详细信息，请参阅[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#ios)指南。

## <a name="related-links"></a>相关链接

- [IOS 11 （Apple）中的新增功能](https://developer.apple.com/ios/)
- [更新了应用商店产品页（Apple）](https://developer.apple.com/app-store/product-page/)
- [更新 iOS 11 应用（WWDC）（视频）](https://developer.apple.com/videos/play/wwdc2017/204/)
