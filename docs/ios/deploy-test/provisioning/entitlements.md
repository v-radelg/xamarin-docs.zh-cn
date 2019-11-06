---
title: 在 Xamarin.iOS 中使用权利
description: 权利是授予应用程序的特殊应用功能和安全性权限，这些应用程序经过正确配置可以使用权利。
ms.prod: xamarin
ms.assetid: 8A3961A2-02AB-4228-A41D-06CB4108D9D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/13/2018
ms.openlocfilehash: 43bde3a31a79728548e72ea1d34977f1a131f282
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028540"
---
# <a name="working-with-entitlements-in-xamarinios"></a>在 Xamarin.iOS 中使用权利

_权利是授予给应用程序的特殊应用功能和安全性权限，这些应用程序经过正确配置可以使用权利。_

在 iOS 中，应用在_沙盒_中运行，沙盒提供一套规则，用于限制在应用程序和特定系统资源或用户数据之间的访问。 _权利_用于请求系统扩展沙盒以赋予应用其他功能。

若要扩展应用的功能，则必须在应用的 Entitlements.plist 文件中提供权利。 仅某些功能可扩展，[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南中列出了这些功能，[下文](#entitlement-key-reference)对其进行了。 权利作为键值对传递给系统，通常而言，每个功能只需一个即可。 本指南后面的[权利键引用](#entitlement-key-reference)部分介绍了特定的键和值。
Visual Studio for Mac 和 Visual Studio 提供一个清晰的界面，用于通过 Entitlements.plist 编辑器在 Xamarin.iOS 应用中添加权利。
本指南介绍了 Entitlements.plist 编辑器及其使用方法。 本指南还提供了所有权利的引用，可添加到 iOS 项目以获得各种功能。

## <a name="entitlements-and-provisioning"></a>权利和预配

Entitlements.plist 文件用于指定权利以及对应用程序捆绑包进行签名。

然而，为了确保应用的代码签名正确，需要某些其他设置。 所用的预配配置文件必须包含已启用所需功能的应用 ID。 有关如何执行此操作的信息，请参阅[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

> [!IMPORTANT]
> Entitlements.plist 文件虽有助于填写使用功能的应用程序的正确属性，但不能生成预配配置文件，因为该文件未链接到 Apple 开发人员帐户。 若要部署和分发应用程序，仍需使用开发人员门户生成预配配置文件。

## <a name="set-entitlements-in-a-xamarinios-project"></a>在 Xamarin.iOS 项目中设置权利

定义应用 ID 时，除了选择和配置所需的应用程序服务，还必须通过编辑“Info.plist”  和“Entitlements.plist”  文件在 Xamarin.iOS 项目中配置权利。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中配置权利，请执行以下操作：

1. 在“解决方案资源管理器”  中，双击“Info.plist”  文件，将其打开进行编辑。
2. 在“iOS 应用程序目标”  部分，填写应用程序的名称并输入定义应用 ID 时创建的“捆绑标识符”  ：

    ![](entitlements-images/servicexs01.png "Enter a Bundle Identifier")

3. 保存对“Info.plist”  文件的更改。
4. 在“解决方案资源管理器”  中，双击“Entitlements.plist”  文件，将其打开进行编辑：

    ![](entitlements-images/servicexs02.png "Editing the Entitlements")

5. 选择并配置 Xamarin.iOS 应用程序所需的任何权利，使其匹配创建应用 ID 时定义的设置。
6. 保存对“Entitlements.plist”  文件的更改。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中配置权利，请执行以下操作：

1. 在“解决方案资源管理器”  中，右键单击“Info.plist”  ，选择“打开方式…”  和“属性列表编辑器”  文件将其打开以进行编辑。
2. 在“iOS 应用程序目标”  部分，填写应用程序的名称并输入定义应用 ID 时创建的“捆绑标识符”  ：

    ![](entitlements-images/servicevs01.png "Setting the Bundle Identifier")

3. 保存对“Info.plist”  文件的更改。
4. 在**解决方案资源管理器**中，右键单击“Entitlements.plist”  文件，选择“打开方式…”  和“属性列表编辑器”  将其打开以进行编辑：

    ![](entitlements-images/servicevs02.png "Editing the Entitlements")

    或者，双击“Entitlements.plist”  文件，打开 XML 源编辑器，从而可以设置权利属性和键值，详细信息如下方的[权利键引用](#entitlement-key-reference)部分所述。

5. 选择并配置 Xamarin.iOS 应用程序所需的任何权利，使其匹配创建应用 ID 时定义的设置。
6. 保存对“Entitlements.plist”  文件的更改。

-----

## <a name="adding-a-new-entitlementsplist-file"></a>添加新的 Entitlements.plist 文件

权利通过 Entitlements.plist 文件添加到应用中。 此文件默认情况下包含在 Xamarin.iOS 项目中，但较早的项目中可能会缺失此文件。

若要将 Entitlements.plist 文件添加到 Xamarin.iOS，请执行以下操作：

1. 右键单击项目文件，再依次转到“添加”>“新文件…”  ：

    ![“添加文件”上下文菜单](entitlements-images/image1.png)
2. 在“新文件”对话框中，依次选择  “iOS”>“属性列表”，再将它命名为“Entitlements”：

    ![“新文件”对话框](entitlements-images/image2.png)

## <a name="entitlement-key-reference"></a>权利键引用

权利键可通过 Entitlements.plist 编辑器的“源”窗格进行添加。 使用 Entitlements.plist 编辑器时通常会添加所需的键，现已列出以供参考。

### <a name="wallet"></a>Wallet

- 说明  ：以前称为 Passbook，是一款用于存储和管理凭证的应用。 这些凭证可以是信用卡、购物卡、登机牌或票证。

  - **类型标识符**
    - **键**：com.apple.developer.pass-type-identifiers
    - **字符串**：`$(TeamIdentifierPrefix)*`

- **注意**：
  - 这会使应用允许所有凭证类型。 若要限制应用并仅允许一部分团队凭证类型，请将字符串值设置为 `$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

  其中，pass.$(CFBundleIdentifier) 是[上文](~/ios/platform/passkit.md)创建的凭证 ID

<a name="icloud" />

### <a name="icloud"></a>iCloud

- **说明**：iCloud 向 iOS 用户提供了一种方便简单的方式来存储内容并在设备之间共享内容。 开发人员可采用四种方法使用 iCloud 来为用户提供存储方式：键值存储、UIDocument 存储、CoreData 和使用 CloudKit 直接为单个文件和目录提供存储。 有关这些方面的详细信息，请参阅 iCloud 指南简介。

  - **iCloud 文档和 CloudKit**
    - **键**：com.apple.developer.ubiquity-container-identifiers
    - **字符串**：`$(TeamIdentifierPrefix)$(CFBundleIdentifier)`
  - **iCloud KeyValue 存储**
    - **键**：com.apple.developer.ubiquity-kvstore-identifier
    - **字符串**：`$(TeamIdentifierPrefix)$(CFBundleIdentifier)`

- **注意**：
  - 通过登录到 developer.apple.com 可找到 `$(TeamIdentifierPrefix)` 字符串，访问“会员中心”>“帐户”>“开发人员帐户摘要”  可获取团队 ID（若是单个开发人员则为个人 ID）。 该字符串是一个含有 10 个字符的字符串（例如 A93A5CM278）。
  - `$(CFBundleIdentifier)` 字符串以 `iCloud` 开头，在根据[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南中的步骤创建 iCloud 容器时，设置了该字符串。
  - 为在生成时获得正确的值，可使用和替换 $`(TeamIdentifierPrefix)` 和 `$(CFBundleIdentifier)` 占位符。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

### <a name="app-groups"></a>应用组

- 说明  ：应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。

  - **键**：com.apple.security.application-groups
  - **字符串**：group.$(CFBundleIdentifier)

<a name="apple-pay" />

### <a name="apple-pay"></a>Apple Pay

- 说明  ：通过 Apple Pay，用户可以使用 iOS 设备支付实体商品。
  - **键**：com.apple.developer.in-app-payments
  - **字符串**：merchant.your.mechantid

### <a name="push-notifications"></a>推送通知

- **键**：aps-environment
- **字符串**：`development` 或 `production`

### <a name="siri"></a>Siri

- 说明  ：通过 SiriKit，iOS 应用可使用应用扩展、新的 Intents 和 Intents UI 框架来提供可访问 iOS 设备上的 Siri 和 Maps 应用的服务。 有关详细信息，请参阅 SiriKit 指南简介。
  - **键**：com.apple.developer.siri

### <a name="personal-vpn"></a>个人 VPN

- **键**：com.apple.developer.networking.vpn.api
- **字符串**：allow-vpn

### <a name="keychain-sharing"></a>Keychain 共享

- 说明  ：通过 Keychain 共享，应用开发人员可将存储在设备密钥链中的密码与同一团队开发的其他应用共享。 通过在字符串中传递密钥链访问组标识符可限制访问权限。
  - **键**：keychain-access-groups
  - **字符串**：$(AppIdentifierPrefix) $(CFBundleIdentifier)

### <a name="inter-app-audio"></a>应用间音频

- 说明  ：通过应用间音频，开发人员可对应用间的音频进行流式处理。
  - **键**：inter-app-audio
  - 布尔  ：是

### <a name="associated-domains"></a>关联的域

- 说明  ：应使用此权利传递应作为通用链接处理的关联域。 可实现通用链接以允许在应用和网站间进行深层链接。 应对应用支持的每个域提供一个条目，每个条目应以 `applinks:` 开头
  - **键**：com.apple.developer.associated-domains
  - **字符串**：webcredentials:example.com

### <a name="data-protection"></a>数据保护

- 说明  ：启用数据保护将使用内置加密硬件，以加密格式存储应用中所使用的敏感数据。 默认情况下，保护级别设置为完全保护（文件仅在设备未锁定时可访问）。
  - **键**：com.apple.developer.default-data-protection
  - 字符串  ：NSFileProtectionComplete

### <a name="homekit"></a>HomeKit

- 说明  ：HomeKit 框架提供了一个平台，用于从一台 iOS 设备上设置、配置和管理所有支持的家庭自动化设备。 有关使用 HomeKit 的详细信息，请参阅 HomeKit 指南简介。
  - **键**：com.apple.developer.homekit
  - 布尔  ：是

### <a name="healthkit"></a>HealthKit

- 说明  ：HealthKit 是 iOS 8 中引入的一个框架，可为相关健康信息提供集中、协调和安全的数据存储。 有关使用 HealthKit 的详细信息，请参阅 HealthKit 指南简介。
  - **键**：com.apple.developer.healthkit
  - 布尔  ：是

### <a name="wireless-accessory-configuration"></a>无线附件配置

- 说明  ：使用无线附件配置可允许应用配置 MFi Wi-Fi 附件
  - **键**：com.apple.external-accessory.wireless-configuration
  - 布尔  ：是

### <a name="classkit"></a>ClassKit

- 说明  ：通过 ClassKit，教师可在应用中查看学生在布置的活动中的进度。
  - **密钥**：com.apple.developer.ClassKit-environment
  - **字符串**：`development` 或 `production`

## <a name="summary"></a>总结

本指南介绍了权利以及如何在 Visual Studio for Mac 和 Visual Studio 中使用这些权利。 此外，本指南还提供了每个功能的键值对引用。
