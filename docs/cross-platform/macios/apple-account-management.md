---
title: Apple 帐户管理
description: 本文档介绍如何在 Visual Studio 中使用的 Apple 帐户管理功能，适用于 Mac 和 Visual Studio 2019。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 8617d6e0c0930f581c45dbb461dfcb5d85a2becc
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855050"
---
# <a name="apple-account-management"></a>Apple 帐户管理

Apple 帐户管理界面提供了一种方法，若要查看与 Apple id。 关联的所有开发团队 它还允许您通过显示的列表中查看有关每个团队的更多详细信息_签名标识_并_预配配置文件_在计算机上安装的。

你的 Apple ID 进行身份验证执行包含的命令行[fastlane](https://fastlane.tools/)。 必须为你要成功进行身份验证在计算机上安装 fastlane。 中详细介绍了 fastlane 以及如何安装它的详细信息[fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)指南。

Apple 帐户对话框允许你执行以下操作：

* **创建和管理证书**
* **创建和管理预配配置文件**

在本指南中介绍了如何执行此操作的信息。

> [!NOTE]
> Apple 帐户管理的 Xamarin 的工具仅显示有关付费 Apple 开发人员帐户的信息。 若要了解如何测试付费的 Apple 开发人员帐户的情况下设备上的应用，请参阅[Xamarin.iOS 应用的免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

此外可以使用 iOS 自动预配工具自动创建和管理签名标识、 应用 Id 和预配配置文件。 有关使用这些功能的详细信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="requirements"></a>要求

Visual Studio for Mac，Visual Studio 2019 和 Visual Studio 2017 （版本 15.7 及更高版本） 上提供了 Apple 帐户管理。

必须具有 Apple 开发人员帐户才能使用此功能。 Apple 开发人员帐户的详细信息现已推出[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

- 确保已连接到 internet。 这是因为 fastlane 直接与 Apple 开发人员门户进行通信。
- 请确保您具有[安装 fastlane 工具](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)。
- 确保已从最新的 fastlane 工具[ https://download.fastlane.tools ](https://download.fastlane.tools)。
- 在开始之前，请务必接受中的任何用户许可协议[开发人员门户](https://developer.apple.com/account/)。

## <a name="adding-an-apple-developer-account"></a>添加 Apple 开发人员帐户

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 若要打开帐户管理对话框，请转到**Visual Studio > 首选项 > Apple 开发人员帐户**:

    ![Apple 开发人员帐户选项](apple-account-management-images/image1.png)

2. 按**+** 按钮以显示登录对话框中，如下所示： 

    ![fastlane 对话框。](apple-account-management-images/image2.png)

4. 输入你的 Apple ID 和密码，然后单击**Sign In**按钮。 这将在此计算机上将你的凭据保存在安全密钥链中。 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)用于安全地处理你的凭据，并将其传递给 Apple 的开发人员门户。
 
5. 选择**始终允许**在警报对话框以允许 Visual Studio 以使用你的凭据：

    ![始终允许警报对话框](apple-account-management-images/image4.png)

6. 已成功添加你的帐户，可以看到你的 Apple ID 和你的 Apple ID 是一部分的任何团队。

    ![包含添加的帐户的 Apple 开发人员帐户对话框](apple-account-management-images/image5.png)

7. 选择任何团队，然后按**查看详细信息...** 按钮。 这将显示的所有签名标识和预配配置文件安装在你的计算机上的列表：

    ![视图的详细信息屏幕显示签名标识和预配配置文件在计算机上](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 开始将你的 Apple ID 添加到 Visual Studio 2019 之前，请确保您的开发环境是否[到 Mac 生成主机配对](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 若要打开帐户管理窗口，请转到**工具 > 选项 > Xamarin > Apple 帐户**:

    ![Apple 帐户选项屏幕](apple-account-management-images/prov1.png)

1. 选择**添加**按钮，然后输入你的 Apple ID 和密码：

    ![用户名和密码的对话框](apple-account-management-images/prov1a.png)

1. 已成功添加你的帐户，可以看到你的 Apple ID 和你的 Apple ID 是一部分的任何团队。
 
1. 选择任何团队，然后按**查看详细信息...** 按钮。 这将显示的所有签名标识和预配配置文件安装在你的计算机上的列表：

    ![用户名和密码的对话框](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>管理签名标识和预配配置文件

团队详细信息对话框中显示签名标识，按类型整理的列表。 **状态**列建议您在该证书是否： 

* **有效**– 在计算机上安装的签名标识 （证书和私钥），并且它尚未过期。

* **不在密钥链**– Apple 的服务器上没有有效的签名标识。 若要在计算机上安装此文件，它必须导出从另一台计算机。 无法从 Apple 开发人员门户下载的签名标识，因为它将包含私钥。

* **私钥已丢失**– 在 keychain 中安装证书没有私钥。

* **过期**– 证书已过期。 应从密钥链删除。

  ![团队详细信息对话框信息](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>创建签名标识

若要创建新的签名标识，请选择**Create Certificate**下拉按钮，然后选择所需的类型。 如果您具有正确权限的新签名标识将显示几秒钟后。

如果下拉列表中的一个选项为灰显未选定的这意味着没有正确的团队的权限创建此类型的证书。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![创建证书选项](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![创建证书选项](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>下载预配配置文件

团队详细信息对话框中还显示连接到你的开发人员帐户的所有预配配置文件的列表。 您可以通过按所有预配配置文件下载到本地计算机**下载所有配置文件**按钮

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![下载预配配置文件部分](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![下载预配配置文件部分](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS 捆绑签名

有关将应用部署到设备的信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="troubleshooting"></a>疑难解答

### <a name="view-details-dialog-is-empty"></a>视图的详细信息对话框为空

这是当前的已知的问题，与 bug 相关[#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)。 请确保 Mac 使用 Visual Studio 的最新稳定版本

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>如果您遇到的问题在你的帐户登录，请尝试以下：

* 打开密钥链应用程序，并在类别下选择*密码*。 搜索`deliver.`，并删除所有条目。

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>"添加帐户时出错。 请使用特定于应用的密码登录"

这是因为在你的帐户上启用双因素身份验证。 请确保 Mac 使用 Visual Studio 的最新稳定版本

### <a name="failed-to-create-new-certificate"></a>无法创建新证书
"已达到此类型的证书的限制"

![限制证书对话框](apple-account-management-images/image10.png)

已生成证书允许的最大数目。 若要解决此问题，浏览到[Apple 开发人员中心](https://developer.apple.com/account/ios/certificate/distribution)和撤销生产证书之一。

## <a name="known-issues"></a>已知问题

* 默认情况下，分发预配配置文件将面向应用商店。 应手动创建内部配置文件或临时配置文件。
