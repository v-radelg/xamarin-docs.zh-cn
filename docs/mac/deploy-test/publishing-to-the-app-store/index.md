---
title: 将 Xamarin.Mac 应用发布到 Mac App Store
description: 本文档介绍如何使用 Visual Studio for Mac 部署 Xamarin.Mac 应用。 其中介绍了如何设置 Mac 开发人员帐户、如何创建代码签名证书以及如何使用这些证书来生成可直接分发或通过 Mac App Store 分发的 Mac 应用。
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 84a35389276fed031f6bea116c1e4f37a63469a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769873"
---
# <a name="publishing-xamarinmac-apps-to-the-mac-app-store"></a>将 Xamarin.Mac 应用发布到 Mac App Store

## <a name="overview"></a>概述

可以两种不同的方式分发 Xamarin.Mac 应用：

- **开发人员 ID** – 使用开发人员 ID 签名的应用程序可在 App Store 之外分发，但网关守卫会识别并允许安装次应用程序。
- **Mac App Store** – 应用必须具有安装程序包，且应用和其安装程序都必须经过签名，以便提交到 Mac App Store。

本文档介绍如何使用 Visual Studio for Mac 和 Xcode设置 Apple 开发者帐户和对每个部署类型配置 Xamarin.Mac 项目。

## <a name="mac-developer-program"></a>Mac 开发人员计划

加入 [Mac 开发人员计划](https://developer.apple.com/devcenter/mac/)时，开发人员可选择以个人或公司身份加入，如下方屏幕截图所示：

[![Apple 开发人员门户](images/image1.png "Apple 开发人员门户")](images/image1-large.png#lightbox)

选择适合自身情况的正确注册类型。

> [!NOTE]
> 此处所作的选择会影响到在配置开发人员帐户时一些屏幕出现的方式。 本文档中的说明和屏幕截图适用于**个人**开发人员帐户。 在**公司**开发人员帐户中，部分选项仅**团队管理员**用户可用。

### <a name="certificates-and-identifiersmacdeploy-testpublishing-to-the-app-storecertificates-identifiersmd"></a>[证书和标识符](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

本指南介绍如何创建发布 Xamarin.Mac 应用时所需的证书和标识符。

### <a name="create-provisioning-profilemacdeploy-testpublishing-to-the-app-storeprofilesmd"></a>[创建设置配置文件](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

本指南介绍如何创建发布 Xamarin.Mac 应用所需的设置配置文件。

### <a name="mac-app-configurationmacdeploy-testpublishing-to-the-app-storeapp-configurationmd"></a>[Mac 应用配置](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

本指南介绍配置要发布的 Xamarin.Mac 应用。

### <a name="sign-with-developer-idmacdeploy-testpublishing-to-the-app-storesigningmd"></a>[使用开发人员 ID 签名](~/mac/deploy-test/publishing-to-the-app-store/signing.md)

本指南介绍使用开发人员 ID 对 Xamarin.Mac 应用进行签名，以便进行发布。

### <a name="bundle-for-mac-app-storemacdeploy-testpublishing-to-the-app-storebundlingmd"></a>[Mac App Store 捆绑](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

本指南介绍将要发布的 Xamarin.Mac 应用捆绑到 Mac App Store。

### <a name="upload-to-mac-app-storemacdeploy-testpublishing-to-the-app-storeuploadingmd"></a>[上传到 Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

本指南介绍将要发布的 Xamarin.Mac 应用上传到 Mac App Store。

## <a name="related-links"></a>相关链接

- [安装](/visualstudio/mac/installation/)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
