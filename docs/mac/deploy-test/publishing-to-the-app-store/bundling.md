---
title: Mac App Store 捆绑
description: 本文档介绍了如何捆绑 Xamarin.Mac 应用以便发布到 Mac App Store。 其中讨论了代码签名选项和生成。
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 129ba01a41f9e5f58802c4d4da65d1662a103adc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770112"
---
# <a name="bundling-for-the-mac-app-store"></a>Mac App Store 捆绑

本部分简单介绍如何使用 Visual Studio for Mac 构建要发布到 Mac App Store 的应用程序。 根据附加功能（例如 iCloud 访问和推送通知），可能需要安装本文未提及的其他组件。

> [!NOTE]
> 开始本部分之前，开发人员必须创建生产预配配置文件才能针对 Mac App Store 进行构建。 请参阅本文档前面的说明，了解如何创建所需的预配配置文件。

## <a name="code-signing-options"></a>代码签名选项

更新代码签名和打包选项前，请将“配置”  更改为“发布”  。 开发人员请务必使用其公司**标识**，并使用先前签名用程序以在 App Store 中发布时所创建的配置文件。

 [![编辑代码签名选项](bundling-images/config02.png "编辑代码签名选项")](bundling-images/config02-large.png#lightbox)

确保已在“Mac 版本”  设置中选中用于创建安装程序包的选项：

[![编辑生成选项](bundling-images/config03.png "编辑生成选项")](bundling-images/config03-large.png#lightbox)

## <a name="build"></a>生成

生成前，请确保已选择“发布”  配置。 开发人员构建应用时，系统会提示同时使用两个证书：

 ![允许应用使用证书](bundling-images/image62.png "允许应用使用证书")

 ![允许应用使用证书](bundling-images/image63.png "允许应用使用证书")

构建应用程序后，开发人员可右键单击项目，然后选择“打开所在文件夹”  查找包文件（在下例中，位于 `bin/x86/AppStore` 目录）。  此包文件包括应用的安装程序，可将其提交到 Apple 以包含在 Mac App Store 中。

 ![在查找器中选择生成包](bundling-images/image64.png "在查找器中选择生成包")

## <a name="related-links"></a>相关链接

- [安装](/visualstudio/mac/installation/)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [开发者 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
