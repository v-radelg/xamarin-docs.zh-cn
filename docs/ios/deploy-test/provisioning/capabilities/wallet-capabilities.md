---
title: Xamarin.iOS 中的钱包功能
description: 向应用程序添加功能通常需要其他预配设置。 本指南将介绍钱包功能所需的设置。
ms.prod: xamarin
ms.assetid: BD9475E6-F586-488C-93D4-8A2A1629B99B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 1844136bb62930341cf00ec871fdd2ff8ff8fe1f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762730"
---
# <a name="wallet-capabilities-in-xamarinios"></a>Xamarin.iOS 中的钱包功能

_向应用程序添加功能通常需要其他预配设置。本指南将介绍钱包功能所需的设置。_

Wallet 是一款用于存储和显示条形码和其他内容的应用，允许用户直接从设备显示票证、登机牌和优惠券。 此信息存储在“凭证”  中。 例如，登机牌或单个票证将是单一凭证。 

开发人员可以多种方式使用 Wallet：

* 若要创建凭证，则无需生成应用程序。 Passfile 是一个包含一些 JSON 文件和可选元数据文件的压缩存档。 若要准备，则需要[凭证类型 ID](~/ios/platform/passkit.md)和[凭证证书](~/ios/platform/passkit.md)。 此信息稍后会在 JSON 文件中声明。 有关预配 Passfile 的详细信息，请参阅 [PassKit 简介](~/ios/platform/passkit.md)指南。

* 同时编写了用于分发凭证的配套应用。 此外，它们还可以创建、编辑和更新凭证，并将凭证添加到 Wallet 应用。 影院应用是此类应用的一个典型示例 - 用户通过应用购买票证后，此票证可直接从该应用添加到 Wallet。 若要使用配套应用，预配配置文件应包含具有“钱包”功能的应用 ID，可以通过以下步骤进行设置。 应用还必须包含所需的权利。

* 中间应用为不直接操作凭证的应用。 除接收凭证以及可让用户选择将其添加到 Wallet 外，这些应用极少与凭证互动。 这些应用程序虽不需要任何特殊预配或权利，但会使用来自 PassKit 框架的一些方法。

## <a name="developer-center"></a>开发人员中心

若要创建用于 Wallet 的新预配配置文件，请执行以下操作：

1. 浏览到 Apple 开发人员门户的[“证书、标识符和配置文件”](https://developer.apple.com/account/ios/certificate/)部分。
2. 在“标识符”下，浏览到“应用 ID”   ： 
    
    ![应用 ID 选择](wallet-capabilities-images/image17.png)

3. 单击页面右上角的“+”  图标。
4. 通过向新应用 ID 提供“名称  ”和捆绑标识符来注册新的应用 ID。 （请注意，此捆绑标识符必须与项目中的捆绑 ID 匹配）：
   
    ![添加应用 ID 详细信息](wallet-capabilities-images/image18.png)

5. 从服务列表中选择“Wallet”  应用服务：
    
    ![选择服务屏幕](wallet-capabilities-images/image19.png)

6. 按“继续”  ，然后按“注册”  来创建应用 ID。

如有必要，可编辑现有应用 ID 以添加 Wallet 功能。

现在此应用 ID 可用于生成或再生成新的预配配置文件，正如[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南中所述：

![使用新创建的应用 ID 创建预配配置文件](wallet-capabilities-images/image20.png)

有关使用 Wallet 的详细信息，请参阅以下指南：

* [PassKit 简介](~/ios/platform/passkit.md)

## <a name="next-steps"></a>后续步骤

下表列出了可能需要采取的其他步骤：

* 在应用中使用框架命名空间。
* 将所需权利添加到应用。 有关所需权利以及添加方法的详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在应用的“iOS 捆绑包签名”中，确保“自定义权利”设置为“Entitlements.plist”。    对“调试”和“iOS 模拟器”生成来说，这不是默认设置  。

如果遇到与应用服务有关的问题，请参考主要指南的[故障排除](~/ios/deploy-test/provisioning/capabilities/index.md)部分。
