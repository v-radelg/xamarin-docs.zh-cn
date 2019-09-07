---
title: 使用 Xamarin 中的 watchOS 图标
description: 本文档介绍 watchOS 应用程序所需的各种图标，以及如何设置解决方案以包含这些图标。
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/26/2018
ms.openlocfilehash: e0bf9ec1553e6638398695157a11242b9885b168
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768102"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>使用 Xamarin 中的 watchOS 图标

Apple Watch 解决方案需要两组图标：

- 将出现在 iPhone 上的 iOS 应用程序图标。
- Apple Watch 将在 "监视" 菜单和通知屏幕上的圆圈中呈现的图标。 "监视应用" 图标也会显示在[Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS 应用中。

## <a name="apple-watch-icons"></a>Apple Watch 图标

| | | |
|-|-|-|
|iOS 应用程序图标|出现在 iPhone 上并启动父应用程序|![iOS 应用程序图标](icons-images/icon-ios.png)|
|"监视应用" 图标|显示在 Apple Watch 主屏幕上|![watchOS 应用图标](icons-images/icon-home.png)|
||显示在监视通知上|![watchOS 通知图标](icons-images/notification-icon.png)|
||出现在[iOS Apple Watch 应用](~/ios/watchos/app-fundamentals/settings.md)|![iOS 监视应用图标](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>配置解决方案

若要确保 iOS 应用和监视应用都显示正确的名称和图标，请针对每个项目执行以下说明：

### <a name="ios-app"></a>iOS 应用

请参阅[Ios 应用程序图标指南](~/ios/app-fundamentals/images-icons/app-icons.md)，以确保正确配置 ios 应用的图标。

#### <a name="infoplist"></a>Info.plist

" [Apple Watch 设置" 应用](~/ios/watchos/app-fundamentals/settings.md)中的 "监视" 应用旁边显示的字符串在**iOS 应用的 info.plist**中配置。

确认你的**info.plist**具有`CFBundleName`密钥和值（注意：这不同于`CFBundleDisplayName`，你可以同时拥有两者）：

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch 应用

[父应用](~/ios/watchos/app-fundamentals/parent-app.md)配置图标后，需要将应用程序图标资产目录添加到 watch 应用。

1. 右键单击 "监视" 应用项目，然后选择 "**文件" > "添加 > 新建文件 ..."> iOS > 资产目录**将资产目录添加到项目。

    ![](icons-images/newasset.png "向项目添加资产目录")

2. 双击**appicons.appiconset/内容 json**文件

    ![](icons-images/xcassets-iconset-sml.png "AppIcon 内容")

3. 添加所有 watchOS 图像，如以下屏幕截图所示：

    [![](icons-images/appicons-sml.png "添加所有 watchOS 映像，如以下屏幕截图所示")](icons-images/appicons.png#lightbox)

    请参阅 Apple 的所需大小的[图标准则](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/)（还会在屏幕上显示维度）。 请记住，这些图标会自动裁剪，以在圆形中呈现。

    图标列表应类似于：

    ![](icons-images/xcassets-complete-sml.png "解决方案资源管理器中的图标列表")

4. 若要确保在应用中包含资产目录，请将以下键和值添加到**Watch 应用的信息中。 info.plist**：

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

可以通过检查 iPhone 模拟器中的 " [Apple Watch 设置" 应用程序](~/ios/watchos/app-fundamentals/settings.md)，或生成[通知](~/ios/watchos/platform/notifications.md)并确认图标显示在通知屏幕上，来验证是否已正确配置图标。

> [!NOTE]
> 图标不能有 alpha 通道（如果存在 alpha 通道，应用将在应用商店提交期间被拒绝）。 可以检查 alpha 通道是否存在，并[使用 Mac OS X 上的预览应用](~/ios/watchos/troubleshooting.md#noalpha)删除它。

## <a name="related-links"></a>相关链接

- [Apple 的 watchOS 图标 & 图像指南](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
