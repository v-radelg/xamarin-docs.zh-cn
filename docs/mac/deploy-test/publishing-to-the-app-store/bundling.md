---
title: Mac App Store 捆绑
description: 本文档介绍了如何捆绑 Xamarin.Mac 应用以便发布到 Mac App Store。 其中讨论了代码签名选项和生成。
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7d2cd650dd22cd64d506d5d17a5ae0c34b44fc2b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487177"
---
# <a name="bundling-for-the-mac-app-store"></a>Mac App Store 捆绑

本部分简单介绍如何使用 Visual Studio for Mac 构建要发布到 Mac App Store 的应用程序。 根据附加功能（例如 iCloud 访问和推送通知），可能需要安装本文未提及的其他组件。

> [!NOTE]
> 开始本部分之前，开发人员必须创建生产预配配置文件才能针对 Mac App Store 进行构建。 请参阅创建所需的预配配置文件的[配置文件说明](profiles.md)。

## <a name="code-signing-options"></a>代码签名选项

更新代码签名和打包选项前，请将“配置”  更改为“发布”  。 开发人员请务必使用其公司**标识**，并使用先前签名用程序以在 App Store 中发布时所创建的配置文件。

[![编辑代码签名选项](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

确保已在“Mac 版本”  设置中选中用于创建安装程序包的选项：

[![编辑生成选项](bundling-images/build.png "编辑生成选项")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>生成

生成前，请确保已选择“发布”  配置。 开发人员构建应用时，系统会提示两次（同时使用应用程序证书和安装程序证书）  ：

![允许应用使用证书将出现两次](bundling-images/perms02.png)

构建应用程序后，开发人员可右键单击项目，然后选择“在查找器中展现”  查找包文件（在下例中，位于 `bin/Release/AppStore` 目录）。  此包文件包括应用的安装程序，可将其提交到 Apple 以包含在 Mac App Store 中。

> [!div class="mx-imgBorder"]
> ![在查找器中选择生成包](bundling-images/path.png)

## <a name="related-links"></a>相关链接

- [安装](/visualstudio/mac/installation/)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
