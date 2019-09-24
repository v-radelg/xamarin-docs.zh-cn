---
title: 安装步骤-通过 Apple for Xamarin 进行登录
description: 根据移动应用程序目标的不同平台，用 Apple 安装程序登录。
ms.prod: xamarin
ms.assetid: 8F712802-395B-469B-B5BE-C927AD1A8391
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: c1f75f4872d787e261ab6bbac3624e31538c6ff0
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206539"
---
# <a name="setup-sign-in-with-apple-for-xamarinforms"></a>安装通过 Apple for Xamarin 进行登录。 Forms

本指南介绍了设置跨平台应用程序以使用 Apple 进行登录高级操作所需的一系列步骤。 Apple 开发人员门户中的 Apple 安装程序是直接的，而在 Android 和 Apple 之间创建安全关系则需要执行其他步骤。 

## <a name="apple-developer-setup"></a>Apple 开发人员设置

你需要在 Apple 开发人员门户的 "[证书、标识符 & 配置文件](https://developer.apple.com/account/resources/)" 部分中解决一些设置步骤，然后才能在应用程序中使用 Apple 的登录。

### <a name="apple-sign-in-domain"></a>Apple 登录域

在 "*证书、标识符 & 配置文件*" 部分的[更多](https://developer.apple.com/account/resources/services/list)部分中注册域名并将其与 Apple 验证。

![更多部分](sign-in-images/readme-signin-domain-configure.png)

添加域，并单击 "**注册**"。

![注册域表单](sign-in-images/readme-signin-domain-more.png)

> [!NOTE]
> 如果你看到关于域不符合 SPF 规范的错误，则需要将 SPF DNS TXT 记录添加到域并等待其传播，然后再继续：SPF TXT 的外观可能如下所示：`v=spf1 a a:myapp.com -all`

接下来，你将需要通过单击 "**下载**" 以检索`apple-developer-domain-association.txt`该文件，然后将其`.well-known`上传到域网站的文件夹来验证域的所有权。

文件上传并可访问后，可以单击 "验证" 以使用 Apple 验证域所有权。 `.well-known/apple-developer-domain-association.txt`

> [!NOTE]
> Apple 将验证的`https://`所有权。 确保设置了 SSL，并通过安全的 URL 访问该文件。

在继续操作之前成功完成此过程。

## <a name="setup-your-app-id"></a>设置应用 ID

在 "[标识符](https://developer.apple.com/account/resources/identifiers/list)" 部分中，创建一个新的标识符，然后选择 "**应用 id**"。 如果你已经有一个应用程序 ID，请选择对其进行编辑。

![创建新的应用 ID](sign-in-images/readme-appid-create.png)

**使用 Apple 启用登录**。 您很可能需要使用 "**启用为主应用 ID** " 选项。

![使用 Apple 启用登录](sign-in-images/readme-appid-signin.png)

保存您的应用程序 ID 更改。

## <a name="create-a-service-id"></a>创建服务 ID

在 "[标识符](https://developer.apple.com/account/resources/identifiers/list/serviceId)" 部分中，创建一个新的标识符，然后选择 "**服务 id**"。

![创建新的服务 ID](sign-in-images/readme-serviceid-create.png)

为服务 ID 指定说明和标识符。  此标识符将是你`ServerId`的。  请确保启用**Apple 的登录**。

继续下一步之前，请单击启用的 "_通过 Apple 登录_" 选项旁的 "**配置**"。

在配置面板中，确保选择了正确的**主应用 ID** 。

接下来，选择以前配置的**Web 域**。

最后，添加一个或多个**返回 url**。  以后`redirect_uri`使用的任何内容都必须在此处严格注册。  输入时，请确保`http://` URL `https://`中包含或。

> [!NOTE]
> 出于测试目的，不能使用`127.0.0.1`或`localhost`，但可以`local.test`使用其他域，如。  如果选择执行此操作，则可以编辑计算机的`hosts`文件，将此虚构域解析为本地 IP 地址。

![配置 Apple 登录](sign-in-images/readme-serviceid-configure.png)

完成后，保存所做的更改。

## <a name="create-a-key-for-your-services-id"></a>为服务 ID 创建密钥

在 "[密钥](https://developer.apple.com/account/resources/authkeys/list)" 部分中，创建一个新**密钥**。

为密钥命名，并**使用 Apple 启用登录**。

![创建新密钥](sign-in-images/readme-key-create.png)

单击 "_在 Apple 登录_" 旁的 "**配置**"。

确保选择了正确的**主应用 ID** ，并单击 "**保存**"。

单击 "**继续**"，然后单击 "**注册**" 以创建新密钥。

接下来，你将只有一个机会下载刚刚生成的密钥。  单击 **“下载”** 。

![下载密钥](sign-in-images/readme-key-download.png)

此外，请在此步骤中记下**密钥 ID** 。 `KeyId`稍后将对此进行使用。

你将下载一个`.p8`密钥文件。  可以在记事本或 VSCode 中打开此文件以查看文本内容。  它们应该如下所示：

```
-----BEGIN PRIVATE KEY-----
MIGTAgEAMBMGBasGSM49AgGFCCqGSM49AwEHBHkwdwIBAQQg3MX8n6VnQ2WzgEy0
Skoz9uOvatLMKTUIPyPCAejzzUCgCgYIKoZIzj0DAQehRANCAARZ0DoM6QPqpJxP
JKSlWz0AohFhYre10EXPkjrih4jTm+b0AeG2BGuoIWd18i8FimGDgK6IzHHPsEqj
DHF5Svq0
-----END PRIVATE KEY-----
```

命名此密钥`P8FileContents`并将其保存在安全的位置。 将此服务集成到移动应用程序时将使用它。

## <a name="summary"></a>总结

本文介绍了使用 Apple 进行登录所需的步骤，以便在 Xamarin 中使用。

## <a name="related-links"></a>相关链接

- [用 Apple 准则登录](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
  