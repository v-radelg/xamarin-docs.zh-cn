---
title: 使用 Xamarin.iOS 中的功能
description: 向应用程序添加功能通常需要其他预配设置。 本指南将介绍所有功能所需的设置。
ms.prod: xamarin
ms.assetid: 98A4676F-992B-4593-8D38-6EEB2EB0801C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: 6014bb0889c84c35edc991011bae46065d4ff8d7
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287100"
---
# <a name="working-with-capabilities-in-xamarinios"></a>使用 Xamarin.iOS 中的功能

_向应用程序添加功能通常需要其他预配设置。本指南将介绍所有功能所需的设置。_

Apple 向开发人员提供了一些功能  ，这些功能通常称为应用服务  ，是扩展功能和拓宽 iOS 应用作用范围的一种方式。 通过这些功能，开发人员可向其应用添加更深入的平台功能集成，例如：从应用、其他设备服务（例如 Siri）等启动货币交易。
这些功能可用于 Xamarin.iOS 项目。 服务的完整列表如下所述：

- 应用组
- 关联的域
- 数据保护
- Game Center
- HealthKit
- HomeKit
- 无线附件配置
- iCloud
- 应用内购买
- 应用间音频
- Apple Pay
- Wallet
- 推送通知
- 个人 VPN
- Siri
- 映射
- 后台模式
- Keychain 共享
- 网络扩展
- 热点配置
- 多路径
- NFC 标记读取

可通过 Visual Studio for Mac 和 Visual Studio 2019 或在 Apple 开发人员门户中手动启动这些功能。 Wallet、Apple Pay 和 iCloud 等某些功能需要应用 ID 的其他配置。

本指南介绍如何通过 Visual Studio 自动启用和手动使用开发人员中心在应用程序中启用每个应用服务，包括可能需要的任何其他设置。 

## <a name="adding-app-services"></a>添加应用服务

若要使用这些功能，应用必须具有一个有效的预配配置文件，其中包含启用了正确服务的应用 ID。 可在 Visual Studio for Mac 和 Visual Studio 2019 中自动或在 Apple 开发人员中心手动创建此预配配置文件。

本节介绍如何使用 Visual Studio 的自动预配或开发人员中心来启用大多数功能。 Wallet、iCloud、Apple Pay 和应用组等功能需要其他设置。 在接下来的指南中将详细介绍这些内容。

> [!IMPORTANT]
> 并非所有功能都可通过自动预配进行添加和管理。 以下列表包含受支持的功能：
>
>- HealthKit 
>- HomeKit 
>- 个人 VPN 
>- 无线附件配置 
>- 应用间音频 
>- SiriKit 
>- 热点 
>- 网络扩展 
>- NFC 标记读取
>- 多路径 
>
>当前不支持推送通知、Game Center、应用内购买、映射、Keychain 共享、关联域和数据保护功能。 若要添加这些功能，请使用手动预配并遵循[开发人员中心](#devcenter)部分中的步骤。

## <a name="using-the-ide"></a>使用 IDE

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

功能添加到 Visual Studio for Mac 中的“Entitlements.plist”  。 若要添加功能，请使用以下步骤：

1. 打开 iOS 应用程序的“Info.plist”文件，从组合框选择“自动预配”方案和“团队”    。 如果需要帮助，请遵循[自动预配](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)指南中的步骤：

    ![自动管理签名选项](images/manage-signing.png)

2. 打开“Entitlements.plist”文件并选择需要添加的功能  ：

    ![将功能添加到 entitlements.plist 文件](images/image17.png)

    选择一个功能执行两项操作：
    - 将该功能添加到应用 ID
    - 将权利键/值对添加到 Entitlements.plist 文件。

    执行这些任务后，Visual Studio for Mac 将通过显示以下成功消息通知用户：

    ![将功能添加到 entitlements.plist 文件](images/image18.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

功能随即添加到“Entitlements.plist”  。 若要在 Visual Studio 2019 中添加功能，请使用以下步骤：

1. 按照[与 Mac 配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南所述，将 Visual Studio 2019 与 Mac 配对。

2. 通过选择“项目”>“预配属性...”打开“预配选项” 

3. 从组合框选择“自动预配”方案和“团队”   。 如果需要帮助，请遵循[自动预配](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)指南中的步骤：

    ![自动管理签名选项](images/manage-signing-vs.png)

4. 打开“Entitlements.plist”文件并选择需要添加的功能  。 保存该文件。

    保存“Entitlement.plist”会实现两项操作  ：

    - 将该功能添加到应用 ID
    - 将权利键/值对添加到 Entitlements.plist 文件。

-----


<a name="devcenter" />

## <a name="using-the-developer-center"></a>使用开发人员中心

使用开发人员中心需要两步：创建应用 ID，然后使用此应用 ID 创建一个预配配置文件。 下面详细介绍了这些步骤。

### <a name="creating-an-app-id-with-an-app-service"></a>创建具有应用服务的应用 ID

1. 在 Mac 上（如果使用 Windows 计算机，则为生成主机 Mac）浏览到 [Apple 开发人员中心](https://developer.apple.com/account)并登录。
2. 选择“证书、标识符和配置文件”  ：

    ![Apple 开发人员中心](images/image5.png)

3. 在“标识符”  下，选择“应用 ID”  ：

    ![开发人员中心中的应用 ID 选择](images/image6.png)

4. 按右上角的“+”  按钮来创建新的应用 ID。
5. 输入应用 ID 描述，选择显式应用 ID，然后按 `com.domain.appname` 格式输入捆绑 ID。 此捆绑 ID 应与项目中的捆绑 ID 匹配：

    ![添加应用 ID 详细信息](images/image7.png)

6. 在“应用服务”  下，选择应用中所需的服务：

    ![应用服务选择页面](images/image8.png)

7. 按“继续”  。
8. 输入应用 ID。 每个服务将处于以下状态之一：“已启用”、“已禁用”或“可配置”，如下所示    。 如果为“已启用”  ，则可在预配配置文件中使用。 如果为“可配置”  ，则此功能需要其他设置。 其他步骤见于后续部分中的详细说明。

    ![应用 ID 确认](images/image9.png)

9. 单击“注册”  ，然后单击“完成”  。 新创建的应用 ID 应显示在 iOS 应用 ID 列表中。


<a name="provisioningprofile" />

### <a name="creating-a-provisioning-profile"></a>创建预配配置文件

现在创建一个含有此应用 ID 的配置文件。 请执行以下步骤：

1. 在 Apple 开发人员中心中，浏览到“预配配置文件”>“所有”  ：

    ![预配配置文件部分](images/image10.png)

2. 按右上角的“+”  按钮来创建一个新的预配配置文件。
3. 选择所需的预配配置文件类型，然后单击“继续”  ：

    ![预配配置文件选择](images/image11.png)

4. 在下拉列表下，选择上述步骤中创建的应用 ID，然后按“继续”  ：

    ![应用 ID 选择](images/image12.png)

5. 选择用于签名应用的证书，然后按“继续”  ：

    ![证书选择](images/image13.png)

6. 选择要包含在此配置文件中的设备，然后按“继续”  ：

    ![选择预配配置文件的设备](images/image14.png)

7. 命名此配置文件，以便于识别，然后按“继续”  生成配置文件：

    ![命名预配配置文件](images/image15.png)

8. 按“下载”  按钮进行下载，然后在“查找器”中双击此文件以安装此预配配置文件。

9. 如果使用 Visual Studio，请确保选中“手动预配”选项  。

10. 在 Visual Studio for Mac / Visual Studio 中，浏览到“项目选项”>“捆绑包签名”  ，并将预配配置文件设置为刚才创建的预配配置文件：

    ![Visual Studio for Mac 项目选项](images/image16.png)

> [!IMPORTANT]
> 此外，可能还需要在 Entitlement.plist 文件中设置授权密钥，在 Info.plist 文件中设置隐私密钥。 有关这些权利的详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。

<a name="nextsteps" />

## <a name="next-steps"></a>后续步骤

在服务器端启用功能后，仍需执行一些步骤才可让应用使用该功能。 下表列出了可能需要采取的其他步骤：

- 在应用中使用框架命名空间。
- 将所需权利添加到应用。 有关所需权利以及添加方法的详细信息，请参阅[权利简介](~/ios/deploy-test/provisioning/entitlements.md)指南。

<a name="troubleshooting" />

## <a name="troubleshooting-capabilities"></a>疑难解答功能

下表详细列出了一些可能会阻碍使用已启用的应用服务开发应用的常见问题。

- 确保创建了正确的 ID 并在 Apple 开发人员门户的“证书、ID 和配置文件”部分注册了 ID。 
- 确保“服务”已添加到应用 ID（或扩展 ID），并且该服务已配置为使用 Apple 开发人员门户的“证书、ID 和配置文件”中所创建的上述应用组/商户 ID/容器。 
- 确保已安装配置文件和应用 ID，且应用的“Info.plist”（在 Xamarin 项目中）正在使用上述配置的某个应用 ID。 
- 确保应用的 **Entitlements.plist** 文件（在 Xamarin 项目中）启用了正确的服务。
- 确保 info.plist 中设置了正确的隐私密钥
- 在应用的“iOS 捆绑包签名”中，确保“自定义权利”设置为“Entitlements.plist”。    对“调试”和“iOS 模拟器”生成来说，这不是默认设置  。

<a name="summary" />

## <a name="summary"></a>总结

本指南解释了功能（或应用服务），并介绍了如何在 Apple 开发人员中心的 Visual Studio 中启用这些功能  。 此外，本指南还详细介绍了如何设置更为复杂的服务（例如 Wallet、iCloud、Apple Pay 和应用组）。 最后，本指南还介绍了设置的后续步骤以及简单的故障排除选项。
