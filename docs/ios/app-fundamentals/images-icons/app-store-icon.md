---
title: 在 Xamarin.iOS 应用商店图标
description: 本文档介绍如何使用资产目录来管理 Xamarin.iOS 应用程序的 App Store 图标。 以前，应用商店图标被管理与 iTunes Connect。
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 53e25ae9f4650254f2aaaa03dc8727fae674c9f0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258075"
---
# <a name="app-store-icons-in-xamarinios"></a>在 Xamarin.iOS 应用商店图标

在 Xcode 9 之前通过 iTunes Connect 添加了所有的应用商店图标。 但是，这不再是这种情况。 应用商店图标现在必须包含为项目程序包的一部分并添加资产目录中。 Apple 将拒绝不包含应用商店图标的应用。

App Store 图标是给用户，因此它必须是便于记忆和显示在小尺寸下也应用程序的人脸。 易记的图标是干净、简单且具有高度辨识度的。

Apple 建议在设计应用程序图标时遵照以下准则：

- 确保图标适合应用程序。
- 创建与应用程序的设计一致的简单图标。
- 图标中避免使用文字。
- 从整体思考：单个应用图标用于所有商店区域。

App Store 中显示的应用图标必须是 1024 x 1024 像素的图像。  Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

有关详细信息，请参阅 Apple [iOS 人机接口指南 》](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)。

## <a name="adding-an-app-store-icon"></a>添加 App Store 图标

应用商店图标现在应由资产目录提供。 

添加 App Store 图标，请执行以下操作：

1. 找到**AppIcon**映像设置**Assets.xcassets**在项目文件。 
    - 所有新项目应具有**Assets.xcassets**文件，其中包含 AppIcon 图像集。
    - 若要添加新的资产目录，请右键单击项目，然后选择**添加 > 新文件 > 资产目录**。
    - 若要添加新应用程序图标图像集，请右键单击在图标集区域中，选择**应用图标和启动图像 > 新应用程序图标**:
    
    ![添加新图像的 set 选项](app-store-icon-images/image1.png)

2. 滚动到**App Store**列表中的图标：

    ![App Store 图标](app-store-icon-images/image2.png)

3. 单击图标，并浏览为 1024 x 1024 像素的图像。 保存资产目录。




## <a name="related-links"></a>相关链接

- [使用资产目录管理图标](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
