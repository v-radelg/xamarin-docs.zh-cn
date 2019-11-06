---
title: Fastlane for iOS 介绍
description: 本指南介绍了可用于对 iOS 应用程序进行代码签名的各种 fastlane 工具 其中介绍了如何更新、安装和使用 fastlane 工具。
ms.prod: xamarin
ms.assetid: 8202C57D-22FF-4224-A5B1-AAEF12B7C106
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 0af85c1c27d2b329d81cc680a0fc4c075d4a86dd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028607"
---
# <a name="introduction-to-fastlane-for-ios"></a>Fastlane for iOS 介绍

Fastlane 是一个开源项目，旨在简化 iOS 和 Android 应用混乱冗长的发布过程。 它包括的几个实用程序，每个实用程序负责处理应用发布的特定方面，例如：

- [Deliver](https://github.com/fastlane/fastlane/tree/master/deliver#readme) – 管理屏幕快照、元数据和应用程序捆绑，并将其上传到 iTunes Connect。
- [Produce](https://github.com/fastlane/fastlane/tree/master/produce#readme) – 在 iTunes Connect 和开发人员门户（通常称为 AppID）中创建应用。 它还包括对应用组和应用程序服务的支持。
- [Pem](https://github.com/fastlane/fastlane/tree/master/pem#readme) – 创建和管理推送通知预配配置文件。
- [Gym](https://github.com/fastlane/fastlane/tree/master/gym#readme) – 可用于生成 iOS 应用程序并进行签名。 （Xamarin 应用已使用 MSBuild 生成应用并进行签名和存档）
- [Cert](https://github.com/fastlane/fastlane/tree/master/cert#readme) – 创建和管理代码签名证书 
- [Sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme) – 创建和管理预配配置文件
- [Match](https://github.com/fastlane/fastlane/tree/master/match#readme) – 创建和维护证书和配置文件并将它们存储在 GIT 存储库中，以便它们可以跨开发团队同步。

可以按以下不同的方式使用 Fastlane：通过终端命令、通过基于文件的方式，或通过将环境变量用于持续集成生成。 

本指南专用于处理针对 iOS 应用开发的设备设置，重点介绍了 cert  、sigh  和 match  实用工具。 

提供的内容可用作帮助应用分发的 springboard，包括完全自动化持续集成服务器上的过程。 但是，有必要记住 Fastlane 是生成 Xcode 项目支持工具的第三方，因此某些工具或命令（如 `fastlane init`）可能不会如期处理 csproj 文件。 有关使用 Fastlane 和其他工具或者有关使用 Fastlane 发布 Android 的详细信息，请参阅 [https://fastlane.tools/](https://fastlane.tools/)

<a name="Installation" />

## <a name="installation"></a>安装

1. 请确保 macOS 计算机上安装了 Xcode 命令行工具。 若要安装工具，请在终端中使用命令 `xcode-select --install`。 如果已安装了这些工具，将显示以下错误：

    ```bash
    error: command line tools are already installed, use "Software Update" to install updates
    ```

2. 从 [https://download.fastlane.tools](https://download.fastlane.tools) 下载 Fastlane 工具。 

    > [!NOTE]
    > 可以使用 `brew cask install fastlane` 从 Homebrew 安装 fastlane 工具，也可以使用 `sudo gem install fastlane –NV` 通过 Rubygems（2.0 或更高版本）安装。 但是，使用安全程序可以确保提供正确的依赖项。 

3. 通过解压缩文件安装 fastlane，然后双击 `install` 可执行文件。 如果收到错误通知“无法打开文件，因为它来自无法识别的开发人员”，请按“确定”，然后执行以下操作：
    - 按住 Ctrl 并单击 `install` 可执行文件。 此时，将显示以下对话框：

     ![](images/fastlane-image12.png "The install dialog")

    - 按“确定”开始安装 fastlane 工具

4. 终端将通过下面所示的对话框显示提示。 按 `y`：

   ![](images/fastlane-image13.png "The Terminal prompt")

5. 首次使用 fastlane 之前，请运行 `which fastlane`。 路径应类似于： 

    ```bash
    /Users/[user]/.fastlane/bin
    ```

6. 如果路径与上述路径匹配，表明你已准备就绪。

     否则，请执行以下操作：在 macOS 上，使用以下命令打开 `.bash_profile`，这是一个主目录中隐藏的纯文本文件：

    ```bash
    open ~/.bash_profile
    ```

7. 添加下面的 PATH 环境变量并保存： 

    ```bash
    export PATH="$HOME/.fastlane/bin:$PATH"
    ```

8. 再次运行 `which fastlane`，以确认路径类似 `/Users/[user]/.fastlane/bin`

## <a name="updating-fastlane"></a>更新 Fastlane

Fastlane 是一个非常活跃的开放源代码项目，会定期推出新版本。 当新版本的 Fastlane 可用时，将建议你运行 fastlane 命令的时间：

[![](images/fastlane-image0.png "The fast lane update prompt")](images/fastlane-image0.png#lightbox)

若要更新为新版本的 fastlane，请从[此处](https://download.fastlane.tools)下载最新的包并双击安装包来运行它：

[![](images/fastlane-image0a.png "Running the install package")](images/fastlane-image0a.png#lightbox)

## <a name="contents"></a>内容

本系列指南提供了一些工具，以供 fastlane 对 iOS 应用进行代码签名时使用，以做好开发或分发准备。 目前介绍的这些工具有：

- [cert](~/ios/deploy-test/provisioning/fastlane/cert.md)
- [sigh](~/ios/deploy-test/provisioning/fastlane/sigh.md)
- [match](~/ios/deploy-test/provisioning/fastlane/match.md)

Cert 和 Sigh 负责在本地计算机上创建和管理签名证书和预配配置文件。 Match 将此过程又更推进一步。 它创建和管理证书和预配配置文件并将它们存储在 GIT 存储库，这样，开发团队的所有成员就可以访问它们。 通读每个部分，找出它们的工作原理以及使用方法。

## <a name="using-fastlane-tools-with-xamarin"></a>通过 Xamarin 使用 Fastlane 工具

利用 Fastlane 创建签名标识和预配配置文件之后，应可直接在 Visual Studio for Mac 中设置绑定签名选项，前提是证书和私钥都位于 macOS 密钥链中且预配配置文件位于文件夹 `~/Library/MobileDevice/Provisioning Profiles` 中。

若要设置 Xamarin.iOS 应用程序的代码签名选项，请右键单击项目名称，选择“项目选项”>“生成”>“iOS 捆绑签名”  显式设置签名标识和设预配配置文件，如下所示：

[![](images/fastlane-image11.png "Set the Signing Identity and Provisioning Profile explicitly")](images/fastlane-image11.png#lightbox)

## <a name="related-links"></a>相关链接

- [fastlane 文档](https://fastlane.tools/)
- [fastlane 代码签名文档](https://docs.fastlane.tools/codesigning/getting-started/)
- [代码签名指南](https://codesigning.guide/)
