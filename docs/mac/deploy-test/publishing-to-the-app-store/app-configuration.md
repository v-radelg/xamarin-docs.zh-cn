---
title: Mac 应用配置
description: 本文档介绍了如何配置 Xamarin.Mac 应用以进行发布。 其中讨论了应用程序设置、签名设置和生成设置。
ms.prod: xamarin
ms.assetid: fea66a34-1581-4cd6-b714-3fbff215a542
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 7db7edd4d8143f4e456f59b97de51e3c1d202dc0
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489396"
---
# <a name="mac-app-configuration"></a>Mac 应用配置

## <a name="mac-app-configuration"></a>Mac 应用配置

在 Visual Studio for Mac 中右键单击 Mac 应用程序项目，然后选择“选项”  。

### <a name="application-settings"></a>应用程序设置

若要更改 Xamarin.Mac 应用的应用程序设置，请在“解决方案板”  中双击“Info.plist”  文件：

![选择 info.plist 文件](app-configuration-images/config04.png "选择 info.plist 文件")

随即显示该应用的可用选项：

 [![编辑 info.plist 文件](app-configuration-images/config01.png "编辑 info.plist 文件")](app-configuration-images/config01-large.png#lightbox)

运行通过 Xamarin.Mac 创建的 Mac 应用程序具有以下系统要求：

- 运行 Mac OS X 10.7 或更高版本的 Mac 计算机。

### <a name="signing-settings"></a>签名设置

通过“项目选项”  对话框的“Mac 签名”  部分，开发人员可对 Xamarin.Mac 应用进行签名，以供测试、自行发布或通过 Apple App Store 发布：

[![Mac 签名编辑器](app-configuration-images/config02.png "Mac 签名窗口")](app-configuration-images/config02-large.png#lightbox)

编译应用时，从此处选择“标识”、“配置文件”和用于对应用进行签名的任何自定义授权。 开发人员可根据需要对用于在其他 Mac 上安装应用的安装程序进行签名。

### <a name="build-settings"></a>生成设置

成功编译应用后，开发人员可通过“项目选项”  对话框的“Mac 生成”  部分选择 Xamarin.Mac 应用的体系结构、控制应用支持的 macOS 版本，并根据需要创建安装包：

 [![编辑生成设置](app-configuration-images/config03.png "编辑生成设置")](app-configuration-images/config03-large.png#lightbox)

## <a name="related-links"></a>相关链接

- [安装](/visualstudio/mac/installation/)
- [Hello，Mac 示例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上分发应用](https://developer.apple.com/devcenter/mac/checklist/)
- [开发人员 ID 和网关守卫](https://developer.apple.com/resources/developer-id/)
