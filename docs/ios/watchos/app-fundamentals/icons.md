---
title: 使用 watchOS 在 Xamarin 中的图标
description: 本文档介绍 watchOS 应用程序和如何设置解决方案来包含这些图标所需的各种图标。
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: 75b5d1f941921a84d96579a4b0d0666ae0c2522d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864984"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>使用 watchOS 在 Xamarin 中的图标

Apple Watch 解决方案需要图标的两个的集：

* 将显示在 iPhone 的 iOS 应用程序图标。
* Apple Watch 和通知屏幕中的监视菜单上的一个圆圈将呈现的图标。 监视应用程序图标还将显示在[Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS 应用程序。

## <a name="apple-watch-icons"></a>Apple Watch 图标

| | | |
|-|-|-|
|iOS 应用图标|将显示在 iPhone 上并启动父应用程序|![iOS 应用图标](icons-images/icon-ios.png)|
|监视应用程序图标|在 Apple Watch 主屏幕上显示|![watchOS 应用图标](icons-images/icon-home.png)|
||将出现在监视通知|![watchOS 通知图标](icons-images/notification-icon.png)|
||将出现在[iOS Apple Watch 应用](~/ios/watchos/app-fundamentals/settings.md)|![iOS Watch 应用图标](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>配置你的解决方案

若要确保将 iOS 应用程序和监视应用程序都显示正确的名称和图标，请按照为每个项目的这些说明操作：

### <a name="ios-app"></a>iOS 应用

请参阅[iOS 应用程序图标指导](~/ios/app-fundamentals/images-icons/app-icons.md)以确保正确配置 iOS 应用的图标。

#### <a name="infoplist"></a>Info.plist

在 watch 应用旁边显示的字符串[Apple Watch 设置应用](~/ios/watchos/app-fundamentals/settings.md)中配置**iOS 应用的 info.plist 文件**。

确认你**Info.plist**已`CFBundleName`键和值 (注意： 这与不同`CFBundleDisplayName`，可以同时具有):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch 应用

一次你[父应用](~/ios/watchos/app-fundamentals/parent-app.md)已经配置了其图标，需要将应用程序图标资产目录添加到 watch 应用。

1. 右键单击监视应用程序项目，然后选择**文件 > 添加 > 新建文件...> iOS > 资产目录**将资产目录添加到项目。

    ![](icons-images/newasset.png "向项目添加资产目录")

2. 双击**AppIcon.appiconset/Contents.json**文件

    ![](icons-images/xcassets-iconset-sml.png "AppIcon 内容")

3. 添加所有 watchOS 映像，在此屏幕截图所示：

    [![](icons-images/appicons-sml.png "添加所有 watchOS 映像，如以下屏幕截图中所示")](icons-images/appicons.png#lightbox)

    请参阅[Apple 的图标准则](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/)（维度也会显示在屏幕上） 的所需大小。 请记住，这些图标将自动剪辑元素以在一个圆周中呈现。

    图标列表如下所示：

    ![](icons-images/xcassets-complete-sml.png "在解决方案资源管理器中的图标列表")

4. 若要确保将资产目录包括在应用中，添加以下键和值添加到**监视应用的 info.plist 文件**:

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

你可以验证图标通过检查配置正确[Apple Watch 设置应用](~/ios/watchos/app-fundamentals/settings.md)在 iPhone 模拟器中，或生成[通知](~/ios/watchos/platform/notifications.md)并确认该图标显示在通知屏幕。

> [!NOTE]
> 图标不能具有 alpha 通道 （应用程序将拒绝在 App Store 提交过程 alpha 通道是否存在）。 您可以检查是否存在 alpha 通道，并将其删除[Mac OS X 上使用预览应用](~/ios/watchos/troubleshooting.md#noalpha)。


## <a name="related-links"></a>相关链接

- [Apple 的 watchOS 图标和图像指南](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
