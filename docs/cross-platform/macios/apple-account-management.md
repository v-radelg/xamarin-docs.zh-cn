---
title: Apple 帐户管理
description: 本文档介绍如何在 Visual Studio for Mac 和 Visual Studio 2019 中使用 Apple 帐户管理功能。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: 81f161442b33eee94f32c506947ed029fd40aadb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016347"
---
# <a name="apple-account-management"></a>Apple 帐户管理

Apple 帐户管理界面提供了查看与 Apple ID 关联的所有开发团队的方法。 它还允许您通过显示计算机上安装的_签名标识_和_预配配置文件_的列表，查看有关每个团队的更多详细信息。

Apple ID 的身份验证是在命令行上通过[fastlane](https://fastlane.tools/)进行的。 若要成功进行身份验证，必须在计算机上安装 fastlane。 [Fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)指南中详细介绍了有关 fastlane 以及如何安装它的详细信息。

Apple 帐户对话框允许你执行以下操作：

- **创建和管理证书**
- **创建和管理预配配置文件**

本指南介绍了如何执行此操作的相关信息。

> [!NOTE]
> Xamarin 的 Apple 帐户管理工具仅显示有关付费 Apple 开发人员帐户的信息。 若要了解如何在没有付费 Apple 开发人员帐户的设备上测试应用，请参阅[适用于 Xamarin 应用的免费预配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

你还可以使用 iOS 自动预配工具来自动创建和管理你的签名标识、应用 Id 和预配配置文件。 有关使用这些功能的详细信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="requirements"></a>要求

Apple 帐户管理可在 Visual Studio for Mac、Visual Studio 2019 和 Visual Studio 2017 （版本15.7 及更高版本）上找到。

若要使用此功能，你必须具有 Apple 开发人员帐户。 有关 Apple 开发人员帐户的详细信息，请访问[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

- 确保已连接到 internet。 这是因为，fastlane 直接与 Apple 开发人员门户进行通信。
- 请确保已[安装 fastlane 工具](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)。
- 确保具有[https://download.fastlane.tools](https://download.fastlane.tools)的最新 fastlane 工具。
- 在开始之前，请确保接受[开发人员门户](https://developer.apple.com/account/)中的任何用户许可协议。

## <a name="adding-an-apple-developer-account"></a>添加 Apple 开发人员帐户

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 若要打开 "帐户管理" 对话框 **> Apple 开发者帐户 "> 首选项，请参阅 Visual Studio 首选项**：

    ![Apple 开发人员帐户选项](apple-account-management-images/image1.png)

2. 按下 " **+** " 按钮以显示 "登录" 对话框，如下所示： 

    ![fastlane 对话框。](apple-account-management-images/image2.png)

3. 输入你的 Apple ID 和密码，并单击 "**登录**" 按钮。 这会将你的凭据保存在此计算机上的 secure 密钥链中。 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)用于安全地处理凭据并将其传递给 Apple 的开发人员门户。

4. 在 "警报" 对话框中选择 "**始终允许**"，以允许 Visual Studio 使用您的凭据：

    ![始终允许警报对话框](apple-account-management-images/image4.png)

5. 成功添加帐户后，你将看到你的 Apple ID 和你的 Apple ID 所属的任何团队。

    ![添加了帐户的 Apple 开发人员帐户对话框](apple-account-management-images/image5.png)

6. 选择任意团队并按**查看详细信息 ...** 按钮。 这会显示计算机上安装的所有签名标识和预配配置文件的列表：

    ![查看计算机上的签名标识和预配配置文件的详细信息屏幕](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 开始向 Visual Studio 2019 添加 Apple ID 之前，请确保你的开发环境已[配对到 Mac 生成主机](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 若要打开 "帐户管理" 窗口，请在 " **Xamarin > Apple 帐户**" 中，参阅 "工具" > > 选项：

    ![Apple 帐户选项屏幕](apple-account-management-images/prov1.png)

1. 选择 "**添加**" 按钮，并输入你的 Apple ID 和密码：

    ![用户名和密码对话框](apple-account-management-images/prov1a.png)

1. 成功添加帐户后，你将看到你的 Apple ID 和你的 Apple ID 所属的任何团队。

1. 选择任意团队并按**查看详细信息 ...** 按钮。 这会显示计算机上安装的所有签名标识和预配配置文件的列表：

    ![用户名和密码对话框](apple-account-management-images/prov2.png)

-----

## <a name="managing-signing-identities-and-provisioning-profiles"></a>管理签名标识和预配配置文件

"团队详细信息" 对话框显示按类型组织的签名标识列表。 "**状态**" 列建议你证书是否为： 

- **有效**–签名标识（证书和私钥）已安装在计算机上，并且未过期。

- **不在密钥链中**-Apple 的服务器上有有效的签名标识。 若要在计算机上安装此程序，必须从另一台计算机中导出它。 无法从 Apple 开发人员门户下载签名标识，因为它不包含私钥。

- **缺少私钥**-在密钥链中安装了不带私钥的证书。

- 已**过期**–证书已过期。 你应将其从密钥链中删除。

  ![团队详细信息对话框信息](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>创建签名标识

若要创建新的签名标识，请选择 "**创建证书**" 下拉按钮，然后选择所需的类型。 如果您具有正确的权限，则几秒钟后将显示一个新的签名标识。

如果下拉中的某个选项灰显并未选择，则表示你没有正确的团队权限来创建此类证书。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![创建证书选项](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![创建证书选项](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>下载预配配置文件

"团队详细信息" 对话框还显示连接到开发人员帐户的所有配置文件的列表。 可以通过按 "**下载所有配置文件**" 按钮将所有预配配置文件下载到本地计算机

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![下载预配配置文件部分](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![下载预配配置文件部分](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS 捆绑签名

若要了解如何将应用部署到设备，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="troubleshooting"></a>疑难解答

### <a name="view-details-dialog-is-empty"></a>"查看详细信息" 对话框为空

目前这是一个已知问题，与 bug [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)相关。 请确保使用的是最新稳定版本的 Visual Studio for Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>如果在帐户中遇到日志记录问题，请尝试以下操作：

- 打开密钥链应用程序，在 "类别" 下选择 "*密码*"。 搜索 `deliver.`，并删除所有条目。

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>添加帐户时出错。 请使用特定于应用程序的密码登录 "

这是因为在你的帐户上启用了双因素身份验证。 请确保使用的是最新稳定版本的 Visual Studio for Mac

### <a name="failed-to-create-new-certificate"></a>未能创建新证书
"已达到此类型证书的限制"

!["证书限制" 对话框](apple-account-management-images/image10.png)

已生成允许的最大证书数。 若要解决此问题，请浏览到[Apple 开发人员中心](https://developer.apple.com/account/ios/certificate/distribution)，并撤消其中一个生产证书。

## <a name="known-issues"></a>已知问题

- 默认情况下，分发预配配置文件将面向应用商店。 应手动创建内部配置文件或临时配置文件。
