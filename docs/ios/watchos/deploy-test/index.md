---
title: 部署和测试使用 Xamarin 的 watchOS 应用
description: 本文档介绍如何部署和测试使用 Xamarin 生成的 watchOS 应用。 它提供的部署清单，讨论显式和通配符应用 Id，并介绍了应用组。
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7d626b8a968835813d87c93e3cead57a00c14000
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293343"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>部署和测试使用 Xamarin 的 watchOS 应用

## <a name="deployment-checklist"></a>部署核对清单

是否要部署到测试监视，或上载到 App Store，需要完成此页上的步骤：

- 在中**iOS 开发人员中心**:
  - [应用 Id](#App_IDs)已创建。
  - [应用组](#App_Groups)配置 （如果需要）。
  - 分发预配配置文件创建

- 在你的解决方案：

  - 验证是否[捆绑包 Id 和项目引用](~/ios/watchos/get-started/installation.md)设置。
  - 检查您的图标是否[正确配置](~/ios/watchos/app-fundamentals/icons.md)。
  - 检查所有项目中的捆绑包版本数字匹配项。
  - 配置**Entitlements.plist**应用组 （如果需要）。

* 然后按照说明：
  - [用于测试，将部署到 Apple Watch](~/ios/watchos/deploy-test/device.md)，或
  - [将上传到应用商店](~/ios/watchos/deploy-test/appstore.md)。

<a name="App_IDs"/>

## <a name="app-ids"></a>应用 Id

如中所述[安装说明进行操作](~/ios/watchos/get-started/installation.md)，Watch 应用中的所有三个项目具有相关程序包 Id，如：

- Xamarin.iOS 统一项目- `com.xamarin.WatchKitCatalog`
- WatchKit 扩展项目- `com.xamarin.WatchKitCatalog.watchkitextension`
- 监视应用程序项目- `com.xamarin.WatchKitCatalog.watchkitapp`

所有三个项目需要匹配分发预配配置文件，使用显式应用 Id 对于每个或通配符应用 id。

### <a name="explicit-app-ids"></a>显式应用 Id

创建**应用程序 ID**为每个项目的捆绑 ID （这将在 iOS 开发人员中心上如下所示）：

![IOS 开发人员中心中的捆绑 Id](images/appids-specific-sml.png)

当创建或配置应用 Id，请记住启用你的应用需要的特定功能。 这可能包括推送通知和应用程序组。

将需要为每个应用程序 id。 创建分发预配配置文件

### <a name="wildcard-app-id"></a>通配符应用 ID

或者，可以创建一个通配符**应用程序 ID**相匹配所有三个项目，如`com.xamarin.*`。

请注意某些功能不能用于是通配符应用 ID （例如推送通知）。 如果您的应用程序需要这些功能应创建显式应用 Id。

对于分发，将仅需要创建一个分发预配配置文件的通配符应用 id。

<a name="App_Groups" />

## <a name="app-groups"></a>应用组

应用组可用于监视扩展你的 iOS 应用之间共享数据。 您应确保你的解决方案具有：

- 配置**应用程序组**Apple 开发人员门户中**证书、 标识符和配置文件**部分。

- 已启用**应用组**(并提供**应用组 ID**) 中*同时*iOS 应用和监视扩展**应用 ID**和**Entitlements.plist**。

### <a name="certificates-identifiers--profiles"></a>证书、 标识符和配置文件

若要使用应用组，创建中的条目**应用组**屏幕。 在下面的示例中的组名与常用的应用 Id，但具有相同的反向 DNS 样式`group.`前缀 （这是必需）：

![标识符](images/appgroups-new-sml.png)

然后，应用程序组将出现在列表中：

![标识符列表](images/appgroups-setup-sml.png)

它在中创建组后，可以将引用你**应用程序 ID**配置。 请记住将其包含这两个 iOS 应用程序和监视扩展**应用 Id**。

![可用的配置](images/appgroups-sml.png)

不要**不**启用应用组在 Apple Watch 应用 id。 不需要监视本身上启用。

### <a name="entitlementsplist"></a>Entitlements.plist

（例如一些应用功能。 应用组） 要求您将您的权利。
双击以编辑**Entitlements.plist**在这些项目中的文件：

- iOS 应用程序项目
- 监视扩展项目

.![Entitlements.plist 编辑器](images/entitlements-plist-sml.png)

不要**不**启用 Watch 应用项目中的权利。 不需要监视本身上启用。

## <a name="related-links"></a>相关链接

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
