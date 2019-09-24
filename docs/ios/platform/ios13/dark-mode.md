---
title: Xamarin 中的深色模式
description: 深色模式是适用于浅色和深色主题的全新系统范围选项。 iOS 用户现在可以选择主题或允许 iOS 动态更改外观。
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: be487ab839e2fb4d21b85719a56dc34303317a5f
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206387"
---
# <a name="dark-mode-in-xamarinios"></a>Xamarin 中的深色模式

对于浅色和深色主题，深色模式是系统范围的选项。 iOS 用户现在可以选择主题或允许 iOS 根据环境和当天的时间动态更改外观。

本文档介绍了暗色模式和在 iOS 13 应用程序中支持的深色模式。

## <a name="requirements"></a>要求

深色模式要求 iOS 13 和 Xcode 11、Xamarin 12.99 和 Visual Studio 2019 或 Visual studio 2019 for Mac 支持 Xcode 11。

## <a name="turning-on-dark-mode"></a>打开深色模式

Apple 在 iOS 13 中提供了一个开发人员菜单，用于在深色模式和浅色模式间切换。 在 iOS 13 模拟器中打开 "**设置**" 并选择 "**开发人员**" 部分，然后滚动到 "**暗外观**" 开关。 此更改将反映在整个模拟器环境中：

![打开深色模式](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>轻型和深色模式的资产

Visual Studio 中的资产目录现在支持每个外观模式的可选图像和颜色：通用、深色和浅色。 以这种方式定义图像和颜色时，iOS 将自动选择相应的图像和颜色。

在 iOS 项目中打开**assets.xcassets**文件并添加新的映像集。 请注意，可以在任何目标解决方案中指定通用、深色和浅色图像。 在下面的屏幕截图中，有一个 "深色" 和 "MicrosoftLogo" 名称的图像：

![轻型和深色模式的资产](dark-mode-images/LightAndDark_AssetCatalog2.png)

**Assets.xcassets**还包含**BackgroundColor**和**TitleColor**的条目，它们是颜色定义。 现在，可以通过在整个应用程序中使用的名称使用这些颜色。 已将**BackgroundColor**分配给视图的背景，并将**TitleColor**分配到标签，如以下屏幕截图所示：

![轻型和深色模式的资产](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>动态系统颜色

Apple 引入了新的语义颜色，它们基于新的深色模式设置动态调整其外观。

## <a name="summary"></a>总结

本文介绍了适用于 iOS 的深色模式，并使用资产目录为每个模式指定图像和颜色。

## <a name="related-links"></a>相关链接

- [深色模式设计准则](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [语义颜色](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
