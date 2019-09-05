---
title: 在 Xamarin 中使用 watchOS 应用组
description: 本文档介绍应用组及其在 watchOS 应用程序中的用法。 本文介绍了如何配置应用组、预配要求、info.plist 注意事项和部署。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: c75db8bd29b7a57c46610abdd5e4024938fc9e1b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280332"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>在 Xamarin 中使用 watchOS 应用组


应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。 应用组可以用于如下所示的数据：

- Apple Watch[设置](~/ios/watchos/app-fundamentals/settings.md)。
- 共享[使用 nsuserdefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)。
- 共享[文件](~/ios/watchos/app-fundamentals/parent-app.md#files)。

## <a name="configure-an-app-group"></a>配置应用组

通过[应用组](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)配置共享位置，这是在 [iOS 开发中心](https://developer.apple.com/devcenter/ios/)的**证书、标识符和描述文件**部分配置的。 还必须在每个项目的“Entitlements.plist”中引用此值。

### <a name="provisioning"></a>预配

应用组将具有标识符，该标识符通常是带有`group.`前缀的包 ID。 例如，我们可以使用捆绑 ID `com.xamarin.WatchSettings`和应用组。 `group.com.xamarin.WatchSettings`

[![](app-groups-images/app-group-sml.png "使用 WatchSettings 和应用组 WatchSettings 中的捆绑包 ID。")](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

并配置预配配置文件，在**info.plist**中**启用应用程序组**，并输入所选的 ID：

[![](app-groups-images/entitlements-sml.png "配置 info.plist 并输入 ID")](app-groups-images/entitlements.png#lightbox)


### <a name="deployment"></a>部署

请确保在[部署](~/ios/watchos/deploy-test/index.md#App_Groups)设置中正确配置应用组。


有关详细信息，请参阅[应用程序组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。


## <a name="related-links"></a>相关链接

- [Apple 的包含应用程序与共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Apple 的应用组文档](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
