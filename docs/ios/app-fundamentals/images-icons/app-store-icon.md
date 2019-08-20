---
title: Xamarin 中的应用商店图标
description: 本文档介绍如何使用资产目录来管理 Xamarin iOS 应用程序的应用商店图标。 以前, 应用商店图标通过 iTunes Connect 进行管理。
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 984ef59a4571379f7b2969ed4f15674f8819e4e4
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620330"
---
# <a name="app-store-icons-in-xamarinios"></a>Xamarin 中的应用商店图标

在 Xcode 9 之前, 所有应用商店图标都是通过 iTunes Connect 添加的。 但这并不是这样。 现在, 应用商店图标必须作为项目捆绑包的一部分包含, 并添加到资产目录中。 Apple 将拒绝不包含应用商店图标的应用。

应用商店图标是向用户应用程序的人脸, 因此它必须易于记忆, 并以较小的大小显示。 易记的图标是干净、简单且具有高度辨识度的。

Apple 建议在设计应用程序图标时遵照以下准则：

- 确保图标适合应用程序。
- 创建与应用程序的设计一致的简单图标。
- 图标中避免使用文字。
- 从整体思考：单个应用图标用于所有商店区域。

App Store 中显示的应用图标必须是 1024 x 1024 像素的图像。  Apple 已声明，资产目录中的应用存储图标不能是透明的，也不能包含 alpha 通道。

有关详细信息, 请参阅 Apple 的[IOS 人体学接口指导原则](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)。

## <a name="adding-an-app-store-icon"></a>添加应用商店图标

应用商店图标现在应由资产目录提供。 

若要添加应用商店图标, 请执行以下操作:

1. 在项目的**assets.xcassets**文件中找到**AppIcon**图像集。 
    - 所有新项目都应附带一个包含 AppIcon 图像集的**assets.xcassets**文件。
    - 若要添加新的资产目录, 请右键单击项目, 然后选择 "**添加 > 新文件 > 资产目录**"。
    - 若要添加新的应用图标图像集, 请在 "图标集" 区域中右键单击, 然后选择 "**应用程序图标" & 启动 > 新应用程序图标的图像**:

    ![添加新的图像集选项](app-store-icon-images/image1.png)

2. 滚动到列表中的 "**应用商店**" 图标:

    ![App Store 图标](app-store-icon-images/image2.png)

3. 单击该图标, 浏览到 1024 x 1024 像素的图像。 保存资产目录。




## <a name="related-links"></a>相关链接

- [管理具有资产目录的图标](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
