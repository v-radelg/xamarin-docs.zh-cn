---
title: Xamarin.iOS 应用的免费预配
description: 本文档介绍 Xamarin.iOS 开发人员如何在物理设备上测试其应用，而无需注册需要付费的 Apple 开发人员计划。
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 07/16/2018
ms.openlocfilehash: 533ab09a73116402e39da65933f26b8f3065357c
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855063"
---
# <a name="free-provisioning-for-xamarinios-apps"></a>Xamarin.iOS 应用的免费预配

免费预配允许 Xamarin.iOS 开发人员将应用部署到 iOS 设备并对其进行测试，无需成为 Apple 开发者计划的一员。
如果模拟测试有用并且方便实施，请在 iOS 物理设备上测试应用以验证它们在实际内存、存储和网络连接受到约束的情况下正常运行，这一点也至关重要。

使用免费预配将应用部署到设备：

- 使用 Xcode 创建必需的签名标识（开发人员证书和私钥）并预配配置文件（包含显式应用 ID 和已连接的 iOS 设备的 UDID）。
- 使用在 Visual Studio for Mac 或 Visual Studio 2019 中通过 Xcode 创建的签名标识和预配配置文件部署 Xamarin.iOS 应用程序。

> [!IMPORTANT]
> [自动预配](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)允许 Visual Studio for Mac 或 Visual Studio 2019 自动设置设备进行开发者测试。 但是，自动预配与免费预配不兼容。 若要使用自动预配，必须具有付费 Apple 开发者计划帐户。

## <a name="requirements"></a>要求

使用免费预配将 Xamarin.iOS 应用程序部署到设备：

- 正在使用的 Apple ID 不得连接 Apple 开发者计划。
- Xamarin.iOS 应用必须使用显式应用 ID，而不是通配符应用 ID。
- Xamarin.iOS 应用中使用的捆绑包标识符必须唯一，且不能曾在其他应用中使用过。 使用免费预配的任何捆绑包标识符都不能再次使用。
- 如果已分配某应用，将无法通过免费预配对该应用进行部署。
- 如果应用使用应用程序服务，则需要创建配置文件，如[设备预配](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)指南中详述。 

请参阅本文档的[限制](#limitations)部分，详细了解与免费预配关联的限制，并参阅[应用分发指南](~/ios/deploy-test/app-distribution/index.md)，详细了解分发 iOS 应用程序。

## <a name="testing-on-device-with-free-provisioning"></a>通过免费预配在设备上进行测试

请按照以下步骤，通过免费预配测试 Xamarin.iOS 应用。

### <a name="use-xcode-to-create-a-signing-identity-and-provisioning-profile"></a>使用 Xcode 创建签名标识和预配配置文件

1. 如果没有 Apple ID，请[创建一个](https://appleid.apple.com)。
2. 打开 Xcode 并浏览到“Xcode”>“首选项”。
3. 在“帐户”下，使用 **+** 按钮添加现有 Apple ID。 它应类似于下面的屏幕截图：

    ![Xcode 首选项 - 帐户](free-provisioning-images/launchapp1.png "Xcode Preferences – Accounts")

4. 关闭 Xcode 首选项
5. 插入要在其中部署应用的 iOS 设备。
6. 在 Xcode 中创建新项目。 选择“文件”>“新建”>“项目”，然后选择“单一视图应用”。
7. 在新项目对话框中，将“团队”设置为刚添加的 Apple ID。 在下拉列表中，该内容类似于你的姓名（个人团队）：

    ![创建新的应用](free-provisioning-images/launchapp2.png "Create a new app")

8. 创建新项目后，选择以 iOS 设备（而不是模拟器）为目标的 Xcode 生成方案。

    ![选择 Xcode 生成方案](free-provisioning-images/xcodescheme.png "Select an Xcode build scheme")

9. 在 Xcode 的项目导航器中，选择应用的顶级节点，打开应用的项目设置。
10. 在“常规”>“标识”下，确保捆绑包标识符完全匹配 Xamarin.iOS 应用的捆绑包标识符。

    ![设置捆绑包标识符](free-provisioning-images/launchapp5.png "Set a bundle identifier")

    > [!IMPORTANT]
    > Xcode 将仅为显式应用 ID 创建预配配置文件，并且它必须与 Xamarin.iOS 应用的应用 ID 完全相同。
    > 如果不同，将无法使用免费预配部署 Xamarin.iOS 应用。

11. 在“部署信息”下，确保部署目标与已连接的 iOS 设备上安装的 iOS 版本相同或低于该版本。
12. 在“签名”下，选择“自动管理签名”，并从下拉列表中选择团队：

    ![自动管理签名](free-provisioning-images/launchapp6.png "Automatically manage signing")

    Xcode 将自动生成预配配置文件和签名标识。 可通过单击预配配置文件旁的信息图标查看该文件：

    ![查看预配配置文件](free-provisioning-images/launchapp7.png "View the provisioning profile")

    > [!TIP]
    > 如果 Xcode 尝试生成预配配置文件时失败，请确保该 Xcode 当前所选的生成方案面向连接的 iOS 设备而不是模拟器。

13. 若要在 Xcode 中测试，通过单击运行按钮将空应用程序部署到设备。

### <a name="deploy-your-xamarinios-app"></a>部署 Xamarin.iOS 应用

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 通过 USB 或[以无线方式](~/ios/deploy-test/wireless-deployment.md)将 iOS 设备连接到 Mac 生成主机。
2. 在 Visual Studio for Mac 的“Solution Pad”中，双击“Info.plist”。
3. 在“签名”中，选择“手动预配”。
4. 单击“iOS 捆绑包签名...” 按钮。
5. 对于“配置”，选择“调试”。
6. 对于“平台”，选择“iPhone”。
7. 选择由 Xcode 创建的签名标识。
8. 选择由 Xcode 创建的预配配置文件。

    ![设置签名标识和预配配置文件](free-provisioning-images/launchapp8.png "Set the signing identity and provisioning profile")

    > [!TIP]
    > 如果看不到签名标识或正确的预配配置文件，可能需要重启 Visual Studio for Mac。

9. 单击“确定”保存，并关闭“项目选项”。
10. 选择 iOS 设备并运行应用。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 请确保 Visual Studio 2019 或 Visual Studio 2017 已[与 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 通过 USB 或[以无线方式](~/ios/deploy-test/wireless-deployment.md)将 iOS 设备连接到 Mac 生成主机。
3. 在 Visual Studio 2019 或 Visual Studio 2017 的“解决方案资源管理器”中，右键单击 Xamarin.iOS 项目，然后选择“属性”。
4. 导航至“iOS 捆绑包签名”。
5. 对于“配置”，选择“调试”。
6. 对于“平台”，选择“iPhone”。
7. 选择“手动预配”。
8. 选择由 Xcode 创建的签名标识。
9. 选择由 Xcode 创建的预配配置文件。
    
    ![设置签名标识和预配配置文件](free-provisioning-images/setprofile-w157.png "Set the signing identity and provisioning profile")

    > [!TIP]
    > Xcode 创建此签名标识和预配配置文件并将其存储在 Mac 生成主机上。 由于 Visual Studio 2019 或 Visual Studio 2017 已与 Mac 生成主机[配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，因此可访问该标识和配置文件。 如果未列出，建议重启 Visual Studio 2019 或 Visual Studio 2017。

10. 保存并关闭项目属性。
11. 选择 iOS 设备并运行应用。

-----

## <a name="limitations"></a>限制

Apple 施加了一些限制，限制什么时候、以什么方式才能使用免费预配在 iOS 设备上运行应用程序，确保你只能部署到你自己的设备：

- 还限制了对 iTunes Connect 的访问，因此，未对免费预配应用程序的开发人员提供某些服务（如发布到 App Store 和 TestFlight）。 要通过 Ad Hoc 和 In-House 方式进行分配，需要 Apple 开发人员帐户（企业或个人）。
- 通过免费预配创建的配置文件一周后过期，签名标识一年后过期。 
- 由于 Xcode 仅为显式应用 ID 创建预配配置文件，因此对于每个希望安装的应用，都需遵循[上述](#testing-on-device-with-free-provisioning)说明。
- 不可通过免费预配对大多数应用程序服务进行预配。 这包括 Apple Pay、Game Center、iCloud、应用内购买、推送通知和电子钱包。 Apple 在[支持的功能 (iOS)](https://help.apple.com/developer-account/#/dev21218dfd6) 指南中提供了完整的功能列表。 若要预配应用，将其用于应用程序服务，请访问[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

## <a name="summary"></a>总结

本指南探讨了使用免费预配在 iOS 设备上安装应用程序的优点和限制。 并通过分步演示，介绍了如何使用免费预配安装 Xamarin.iOS 应用。

## <a name="related-links"></a>相关链接

- [设备预配](~/ios/get-started/installation/device-provisioning/index.md)
- [对应用程序服务进行预配](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)
