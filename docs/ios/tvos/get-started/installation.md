---
title: 在 Xamarin 中安装 tvOS 支持
description: 本文讨论 Xcode 9 和 Xamarin.iOS 11 中的 tvOS 的支持，并提供了有关如何完成设置以开发 tvOS 应用使用 Xamarin 的简要说明。
ms.prod: xamarin
ms.assetid: 0819DC93-A46B-49DC-A566-8E27CAE1B829
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: e84ce04f3745c46ba94845ba2f815c638cf81a74
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61398039"
---
# <a name="installing-tvos-support-in-xamarin"></a>在 Xamarin 中安装 tvOS 支持

> [!TIP]
> 若要开始试用 Xamarin 的预览支持 iOS 12 和 tvOS 12，阅读[iOS 12 入门指南](~/ios/platform/introduction-to-ios12/get-started.md)。

Apple 已发布的 Apple TV 4k 和 tvOS 11。 Apple TV 平台是打开开发人员，使他们能够创建丰富的沉浸式应用程序和通过 Apple tv 设备内置 App Store 发布它们。

Xamarin.iOS 11 及更高版本支持 tvOS 11 使用 Apple 的 Xcode 9 随附 SDK。

- [适用于 Xamarin.iOS 的发行说明](https://docs.microsoft.com/xamarin/ios/release-notes/)
- [Xcode 的发行说明](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html#//apple_ref/doc/uid/TP40001051-CH1-SW876)

## <a name="installation"></a>安装

若要构建使用 Xamarin 的 tvOS 应用程序：

1. **安装最新的 Xcode** –[下载最新版本的 Xcode](https://developer.apple.com/xcode/download/)并将其安装。 而无需安装 Xcode，它将无法生成 Xamarin 应用。 
2. **运行 Xcode** – 安装 Xcode 后, 启动一次才能更新和运行 Visual Studio for mac。 Xcode 将安装 Xamarin 需要一些工具。
3. **安装最新稳定版本的 Xamarin** -更新到最新[稳定的 Xamarin 版本](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel)。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [构建适用于使用 Xamarin （视频） 的 tvOS 应用](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
